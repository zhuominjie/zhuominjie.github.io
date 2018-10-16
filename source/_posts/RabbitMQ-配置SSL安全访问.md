---
title: 'RabbitMQ:配置SSL安全访问'
categories: RabbitMQ
abbrlink: 348c654f
date: 2018-09-10 19:06:22
tags:
keywords:
description:
---

## 一、生成 SSL 证书
SSL(Secure Sockets Layer 安全套接层)，及其继任者`传输层安全`（Transport Layer Security，TLS）是为网络通信提供安全及数据完整性的一种安全协议。`TLS`与`SSL`在传输层对网络连接进行加密，一般可分为`单向认证`和`双向认证`。`RabbitMQ` 采用的是双向认证方式。

#### 1.创建目录

```
# mkdir testca
# cd testca
# mkdir certs private
# chmod 700 private
# echo 01 > serial
# touch index.txt
```
<!--more-->
#### 2.在该目录下，生成`openssl.cnf`文件，内容如下：

```
[ ca ]
default_ca = testca

[ testca ]
dir = .
certificate = $dir/cacert.pem
database = $dir/index.txt
new_certs_dir = $dir/certs
private_key = $dir/private/cakey.pem
serial = $dir/serial

default_crl_days = 7
default_days = 365
default_md = sha256

policy = testca_policy
x509_extensions = certificate_extensions

[ testca_policy ]
commonName = supplied
stateOrProvinceName = optional
countryName = optional
emailAddress = optional
organizationName = optional
organizationalUnitName = optional
domainComponent = optional

[ certificate_extensions ]
basicConstraints = CA:false

[ req ]
default_bits = 2048
default_keyfile = ./private/cakey.pem
default_md = sha256
prompt = yes
distinguished_name = root_ca_distinguished_name
x509_extensions = root_ca_extensions

[ root_ca_distinguished_name ]
commonName = hostname

[ root_ca_extensions ]
basicConstraints = CA:true
keyUsage = keyCertSign, cRLSign

[ client_ca_extensions ]
basicConstraints = CA:false
keyUsage = digitalSignature
extendedKeyUsage = 1.3.6.1.5.5.7.3.2

[ server_ca_extensions ]
basicConstraints = CA:false
keyUsage = keyEncipherment
extendedKeyUsage = 1.3.6.1.5.5.7.3.1
```

#### 3.颁发自己的 CA 证书

```
# openssl req -x509 -config openssl.cnf -newkey rsa:2048 -days 365 -out cacert.pem -outform PEM -subj /CN=MyTestCA/ -nodes
# openssl x509 -in cacert.pem -out cacert.cer -outform DER
```

#### 4.生成服务器证书

```
# cd ..
# ls
testca
# mkdir server
# cd server
# openssl genrsa -out key.pem 2048
# openssl req -new -key key.pem -out req.pem -outform PEM -subj /CN=$(hostname)/O=server/ -nodes
# cd ../testca
# openssl ca -config openssl.cnf -in ../server/req.pem -out ../server/cert.pem -notext -batch -extensions server_ca_extensions
# cd ../server
# openssl pkcs12 -export -out keycert.p12 -in cert.pem -inkey key.pem -passout pass:MySecretPassword
```

#### 5.生成客户端证书

```
# cd ..
# ls
server testca
# mkdir client
# cd client
# openssl genrsa -out key.pem 2048
# openssl req -new -key key.pem -out req.pem -outform PEM -subj /CN=$(hostname)/O=client/ -nodes
# cd ../testca
# openssl ca -config openssl.cnf -in ../client/req.pem -out ../client/cert.pem -notext -batch -extensions client_ca_extensions
# cd ../client
# openssl pkcs12 -export -out keycert.p12 -in cert.pem -inkey key.pem -passout pass:MySecretPassword
```

后续需要要使用的文件分别是：`testca/cacert.pem`、`server/cert.pem`、`server/key.pem`、`client/cert.pem`、`client/key.pem`、`client/keycert.p12`

#### 6.番外
此外，生成证书的过程也有git上的大神写好的`shell`脚本可以使用，项目地址为：
https://github.com/Berico-Technologies/CMF-AMQP-Configuration

可直接通过git命令进行clone到本地（最好新建个文件夹AMQPSSL）：

```
git clone https://github.com/Berico-Technologies/CMF-AMQP-Configuration.git  
```
在clone后的项目目录中，doc文件夹下有详细的说明shell脚本如何使用。

1. 确保已经安装好了openssl，具体安装方法请自行google；

切换到 CMF-AMQP-Configuration/ssl 文件夹，运行

```
sh setup_ca.sh MyRabbitMQCA
```

名称定义为”MyRabbitMQCA”，这个名字可以自行指定，用于在证书中显示证书颁发机构名。

2. 生成服务器证书

```
sh make_server_cert.sh rabbitmq-server rabbit
```
一个参数是服务器名，第二个参数是密码。

3. 生成客户端证书

```
sh create_client_cert.sh rabbit-client rabbit
```

第一个参数是客户端名称，第二个参数是密码。

执行完以上步骤之后，会在ssl目录下生成：ca、server、client三个文件夹。

## 二、配置 RabbitMQ 服务器

#### 1.配置RabbitMQ环境

Mac上RabbitMQ的配置文件通常位于`/usr/local/etc/rabbitmq`，在该目录下创建`rabbitmq.conf`和`advanced.config`两个文件，并修改`rabbitmq-env.conf`内容如下：

```
CONFIG_FILE=/usr/local/etc/rabbitmq/rabbitmq
RABBITMQ_ADVANCED_CONFIG_FILE=//usr/local/etc/rabbitmq/advanced
#NODE_IP_ADDRESS=127.0.0.1
NODENAME=rabbit@localhost
```
#### 2.基本配置 rabbitmq.conf 修改

```
listeners.ssl.default=5671
  
ssl_options.cacertfile=/usr/local/etc/rabbitmq/ca/cacert.pem
ssl_options.certfile=/usr/local/etc/rabbitmq/ca/cert.pem
ssl_options.keyfile=/usr/local/etc/rabbitmq/ca/key.pem
ssl_options.verify=verify_peer
ssl_options.fail_if_no_peer_cert=false
```

#### 3.高级配置 advanced.config 修改

```
%% list allowed ciphers
[ 
 {ssl, [{versions, ['tlsv1.2', 'tlsv1.1']}]},
 {rabbit, [
           {ssl_listeners, [5671]},
           {ssl_options, [{cacertfile,"/usr/local/etc/rabbitmq/ca/cacert.pem"},
                          {certfile,  "/usr/local/etc/rabbitmq/ca/cert.pem"},
                          {keyfile,   "/usr/local/etc/rabbitmq/ca/key.pem"},
                          {versions, ['tlsv1.2', 'tlsv1.1']},
                          %% This list is just an example!
                          %% Not all cipher suites are available on all machines.
                          %% Cipher suite order is important: preferred suites
                          %% should be listed first.
                          %% Different suites have different security and CPU load characteristics.
                          {ciphers,  [                                 {ecdhe_ecdsa,aes_256_gcm,null,sha384},
                                      {ecdhe_rsa,aes_256_gcm,null,sha384},
                                      {ecdhe_ecdsa,aes_256_cbc,sha384,sha384},
                                      {ecdhe_rsa,aes_256_cbc,sha384,sha384},
                                      {ecdh_ecdsa,aes_256_gcm,null,sha384},
                                      {ecdh_rsa,aes_256_gcm,null,sha384},
                                      {ecdh_ecdsa,aes_256_cbc,sha384,sha384},
                                      {ecdh_rsa,aes_256_cbc,sha384,sha384},
                                      {dhe_rsa,aes_256_gcm,null,sha384},
                                      {dhe_dss,aes_256_gcm,null,sha384},
                                      {dhe_rsa,aes_256_cbc,sha256},
                                      {dhe_dss,aes_256_cbc,sha256},
                                      {rsa,aes_256_gcm,null,sha384},
                                      {rsa,aes_256_cbc,sha256},
                                      {ecdhe_ecdsa,aes_128_gcm,null,sha256},
                                      {ecdhe_rsa,aes_128_gcm,null,sha256},
                                      {ecdhe_ecdsa,aes_128_cbc,sha256,sha256},
                                      {ecdhe_rsa,aes_128_cbc,sha256,sha256},
                                      {ecdh_ecdsa,aes_128_gcm,null,sha256},
                                      {ecdh_rsa,aes_128_gcm,null,sha256},
                                      {ecdh_ecdsa,aes_128_cbc,sha256,sha256},
                                      {ecdh_rsa,aes_128_cbc,sha256,sha256},
                                      {dhe_rsa,aes_128_gcm,null,sha256},
                                      {dhe_dss,aes_128_gcm,null,sha256},
                                      {dhe_rsa,aes_128_cbc,sha256},
                                      {ecdh_rsa,aes_128_gcm,null,sha256}
                                     ]},
                            {fail_if_no_peer_cert,true}
                         ]}
          ]}
].
```
## 三、配置 Java 客户端
使用JDK自带的keytool工具，可以导入秘钥，进行证书管理。在Java的代码中，通过实例化 `Trust Manager`实现客户端的认证。

```
keytool -import -alias server1 -file /path/to/server/certificate.pem -keystore /path/to/rabbitstore
```
上面的命令将导入`server/certificate.pem`到`rabbitstore`，并且与`server1`关联。keytool在确认证书是否被信任的过程会要求设置密码保护。

下面展示了Java如何使用`Key Manager`和`Trust Manager`进行客户端认证。

```
import java.io.FileInputStream;
import java.security.KeyStore;

import javax.net.ssl.KeyManagerFactory;
import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManagerFactory;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import com.rabbitmq.client.GetResponse;


public class Example2 {
    
     public static void main(String[] args) throws Exception
     {
       char[] keyPassphrase = "MySecretPassword".toCharArray();
       KeyStore ks = KeyStore.getInstance("PKCS12");
       
       ks.load(new FileInputStream(Class.class.getResource("/").getPath() + "keycert.p12"), keyPassphrase);

       KeyManagerFactory kmf = KeyManagerFactory.getInstance("SunX509");
       kmf.init(ks, keyPassphrase);

       char[] trustPassphrase = "rabbitstore".toCharArray();
       KeyStore tks = KeyStore.getInstance("JKS");
       tks.load(new FileInputStream(Class.class.getResource("/").getPath() + "rabbitstore"), trustPassphrase);

       TrustManagerFactory tmf = TrustManagerFactory.getInstance("SunX509");
       tmf.init(tks);

       SSLContext c = SSLContext.getInstance("TLSv1.1");
       c.init(kmf.getKeyManagers(), tmf.getTrustManagers(), null);

       ConnectionFactory factory = new ConnectionFactory();
       factory.setHost("");
       factory.setPort(5671);
       
       factory.setUsername("");
       factory.setPassword("");
       
       factory.useSslProtocol(c);

       Connection conn = factory.newConnection(); 
       Channel channel = conn.createChannel();

       channel.queueDeclare("rabbitmq-java-test", false, true, true, null);
       channel.basicPublish("", "rabbitmq-java-test", null, "Hello, World".getBytes());


       GetResponse chResponse = channel.basicGet("rabbitmq-java-test", false);
       if(chResponse == null) {
           System.out.println("No message retrieved");
       } else {
           byte[] body = chResponse.getBody();
           System.out.println("Recieved: " + new String(body));
       }


       channel.close();
       conn.close();
   }
}
```

## 四、配置有效性检查
#### 1.证书有效性校验
打开一个终端，输入以下命令启动服务端：

```
openssl s_server -accept 8443 -cert server/cert.pem -key server/key.pem -CAfile testca/cacert.pem
```
在另外一个终端，输入以下命令启动客户端：

```
openssl s_client -connect localhost:8443 -cert client/cert.pem -key client/key.pem -CAfile testca/cacert.pem
```
如果生产的证书是有效的，将会在客户端返回连接成功的消息，结尾如下所示：

```
Verify return code: 0 (ok)
```

#### 2.验证RabbitMQ配置有效性
**查看日志**

在RabbitMQ启动的控制台，可以看到日志的位置如下：

```
Logs: /usr/local/var/log/rabbitmq/rabbit@localhost.log
      /usr/local/var/log/rabbitmq/rabbit@localhost_upgrade.log
```

查看日志的内容，可以看到如下内容，说明`TLS`配置已经生效：

```
started SSL Listener on [::]:5671
```

**手动建立openssl连接**

```
openssl s_client -connect localhost:5671 -cert client/cert.pem -key client/key.pem -CAfile testca/cacert.pem
```

如果连接成功，会看到和前面`8443`端口连接相同的结束信息，并且会在`RabbitMQ`的日志中查看到如下消息：

```
accepting AMQP connection <0.223.0> (127.0.0.1:58954 -> 127.0.0.1:5671)
```

## 五、异常处理

#### 1.客户端证书密码错误
如果在`Java`客户端中，导入`keycert.p12`证书的密码设置错误，控制台会返回如下异常。

```
java.io.IOException: failed to decrypt safe contents entry: javax.crypto.BadPaddingException: Given final block not properly padded
```

#### 2.客户端和服务器的加密套协商失败

使用`openssl`或者`Java`客户端进行远程连接时，会使用`openssl`或者`JDK`的加密套，但是，`RabbitMQ`的服务端中，默认的加密套（cipher suite）不一定会有与之一致的加密算法，这将在**握手**阶段就发生协商加密套失败，客户端的错误返回如下：

```
javax.net.ssl.SSLHandshakeException: Received fatal alert: insufficient_security
```

查看`RabbitMQ`的日志，可以看到详细的错误如下：

```
TLS server: In state hello at tls_handshake.erl:197 generated SERVER ALERT: Fatal - Insufficient Security - no_suitable_ciphers
```

可以知道是握手阶段的加密套协商失败，因此，可以通过`wireshark`抓包，查看客户端发出请求所包含的加密套。我的Java客户端加密套如下所示：

{% qnimg cipher-suite.png %}

从图中，可以看到`OpenSSL`定义的加密套名称，我选择其中的一个，在github上找到对应的`Erlang`名称，添加到`RabbitMQ`的`advance.config`中，重启服务之后，就能够完成握手了。


## 附件

|OpenSSL	| IANA 	| Erlang |
| --------   | :-----:  | :-----:  |
|TLS_PSK_WITH_NULL_SHA	|PSK-NULL-SHA	|{psk, null, sha, default_prf}
|TLS_DHE_PSK_WITH_NULL_SHA	|DHE-PSK-NULL-SHA|	{dhe_psk, null, sha, default_prf}
|TLS_RSA_PSK_WITH_NULL_SHA	|RSA-PSK-NULL-SHA	|{rsa_psk, null, sha, default_prf}
|TLS_RSA_WITH_AES_128_CBC_SHA	|AES128-SHA	|{rsa, aes_128_cbc, sha, default_prf}
|TLS_DHE_DSS_WITH_AES_128_CBC_SHA	|DHE-DSS-AES128-SHA	|{dhe_dss, aes_128_cbc, sha, default_prf}
|TLS_DHE_RSA_WITH_AES_128_CBC_SHA	|DHE-RSA-AES128-SHA	|{dhe_rsa, aes_128_cbc, sha, default_prf}
|TLS_DH_anon_WITH_AES_128_CBC_SHA	|ADH-AES128-SHA	|{dh_anon, aes_128_cbc, sha, default_prf}
|TLS_RSA_WITH_AES_256_CBC_SHA	|AES256-SHA	|{rsa, aes_256_cbc, sha, default_prf}
|TLS_DHE_DSS_WITH_AES_256_CBC_SHA	|DHE-DSS-AES256-SHA	|{dhe_dss, aes_256_cbc, sha, default_prf}
|TLS_DHE_RSA_WITH_AES_256_CBC_SHA|	DHE-RSA-AES256-SHA	|{dhe_rsa, aes_256_cbc, sha, default_prf}
|TLS_DH_anon_WITH_AES_256_CBC_SHA	|ADH-AES256-SHA	|{dh_anon, aes_256_cbc, sha, default_prf}
|TLS_RSA_WITH_AES_128_CBC_SHA256|	AES128-SHA256	|{rsa, aes_128_cbc, sha256, default_prf}
|TLS_RSA_WITH_AES_256_CBC_SHA256	|AES256-SHA256	|{rsa, aes_256_cbc, sha256, default_prf}
|TLS_DHE_DSS_WITH_AES_128_CBC_SHA256|	DHE-DSS-AES128-SHA256	|{dhe_dss, aes_128_cbc, sha256, default_prf}
|TLS_DHE_RSA_WITH_AES_128_CBC_SHA256|	DHE-RSA-AES128-SHA256	|{dhe_rsa, aes_128_cbc, sha256, default_prf}
|TLS_DHE_DSS_WITH_AES_256_CBC_SHA256|	DHE-DSS-AES256-SHA256	|{dhe_dss, aes_256_cbc, sha256, default_prf}
|TLS_DHE_RSA_WITH_AES_256_CBC_SHA256	|DHE-RSA-AES256-SHA256	|{dhe_rsa, aes_256_cbc, sha256, default_prf}
|TLS_DH_anon_WITH_AES_128_CBC_SHA256|	ADH-AES128-SHA256	|{dh_anon, aes_128_cbc, sha256, default_prf}
|TLS_DH_anon_WITH_AES_256_CBC_SHA256|	ADH-AES256-SHA256|	{dh_anon, aes_256_cbc, sha256, default_prf}
|TLS_PSK_WITH_RC4_128_SHA	|PSK-RC4-SHA	|{psk, rc4_128, sha, default_prf}
|TLS_PSK_WITH_3DES_EDE_CBC_SHA	|PSK-3DES-EDE-CBC-SHA	|{psk, '3des_ede_cbc', sha, default_prf}
|TLS_PSK_WITH_AES_128_CBC_SHA|	PSK-AES128-CBC-SHA	|{psk, aes_128_cbc, sha, default_prf}
|TLS_PSK_WITH_AES_256_CBC_SHA	|PSK-AES256-CBC-SHA	|{psk, aes_256_cbc, sha, default_prf}
|TLS_DHE_PSK_WITH_RC4_128_SHA	|DHE-PSK-RC4-SHA	{dhe_psk, rc4_128, sha, default_prf}
|TLS_DHE_PSK_WITH_3DES_EDE_CBC_SHA	|DHE-PSK-3DES-EDE-CBC-SHA	{dhe_psk, '3des_ede_cbc', sha, default_prf}
|TLS_DHE_PSK_WITH_AES_128_CBC_SHA|	DHE-PSK-AES128-CBC-SHA	{dhe_psk, aes_128_cbc, sha, default_prf}
|TLS_DHE_PSK_WITH_AES_256_CBC_SHA	|DHE-PSK-AES256-CBC-SHA	{dhe_psk, aes_256_cbc, sha, default_prf}
|TLS_RSA_PSK_WITH_RC4_128_SHA	|RSA-PSK-RC4-SHA	|{rsa_psk, rc4_128, sha, default_prf}
|TLS_RSA_PSK_WITH_3DES_EDE_CBC_SHA	|RSA-PSK-3DES-EDE-CBC-SHA|	|{rsa_psk, '3des_ede_cbc', sha, default_prf}
|TLS_RSA_PSK_WITH_AES_128_CBC_SHA|	RSA-PSK-AES128-CBC-SHA	|{rsa_psk, aes_128_cbc, sha, default_prf}
|TLS_RSA_PSK_WITH_AES_256_CBC_SHA	|RSA-PSK-AES256-CBC-SHA	|{rsa_psk, aes_256_cbc, sha, default_prf}
|TLS_RSA_WITH_AES_128_GCM_SHA256	AES128-GCM-SHA256	|{rsa, aes_128_gcm, null, sha256}
|TLS_RSA_WITH_AES_256_GCM_SHA384	AES256-GCM-SHA384	|{rsa, aes_256_gcm, null, sha384}
|TLS_DHE_RSA_WITH_AES_128_GCM_SHA256	DHE-RSA-AES128-GCM-SHA256	|{dhe_rsa, aes_128_gcm, null, sha256}
|TLS_DHE_RSA_WITH_AES_256_GCM_SHA384	DHE-RSA-AES256-GCM-SHA384	|{dhe_rsa, aes_256_gcm, null, sha384}
|TLS_DH_RSA_WITH_AES_128_GCM_SHA256	DH-RSA-AES128-GCM-SHA256	|{dh_rsa, aes_128_gcm, null, sha256}
|TLS_DH_RSA_WITH_AES_256_GCM_SHA384	DH-RSA-AES256-GCM-SHA384	|{dh_rsa, aes_256_gcm, null, sha384}
|TLS_DHE_DSS_WITH_AES_128_GCM_SHA256	DHE-DSS-AES128-GCM-SHA256	|{dhe_dss, aes_128_gcm, null, sha256}
|TLS_DHE_DSS_WITH_AES_256_GCM_SHA384	DHE-DSS-AES256-GCM-SHA384	|{dhe_dss, aes_256_gcm, null, sha384}
|TLS_DH_DSS_WITH_AES_128_GCM_SHA256	DH-DSS-AES128-GCM-SHA256	|{dh_dss, aes_128_gcm, null, sha256}
|TLS_DH_DSS_WITH_AES_256_GCM_SHA384	DH-DSS-AES256-GCM-SHA384	|{dh_dss, aes_256_gcm, null, sha384}
|TLS_DH_anon_WITH_AES_128_GCM_SHA256	ADH-AES128-GCM-SHA256	|{dh_anon, aes_128_gcm, null, sha256}
|TLS_DH_anon_WITH_AES_256_GCM_SHA384	ADH-AES256-GCM-SHA384	|{dh_anon, aes_256_gcm, null, sha384}
|TLS_PSK_WITH_AES_128_GCM_SHA256	PSK-AES128-GCM-SHA256	|{psk, aes_128_gcm, null, sha256}
|TLS_PSK_WITH_AES_256_GCM_SHA384	PSK-AES256-GCM-SHA384	|{psk, aes_256_gcm, null, sha384}
|TLS_DHE_PSK_WITH_AES_128_GCM_SHA256	DHE-PSK-AES128-GCM-SHA256	|{dhe_psk, aes_128_gcm, null, sha256}
|TLS_DHE_PSK_WITH_AES_256_GCM_SHA384	DHE-PSK-AES256-GCM-SHA384	|{dhe_psk, aes_256_gcm, null, sha384}
|TLS_RSA_PSK_WITH_AES_128_GCM_SHA256	RSA-PSK-AES128-GCM-SHA256	|{rsa_psk, aes_128_gcm, null, sha256}
|TLS_RSA_PSK_WITH_AES_256_GCM_SHA384	RSA-PSK-AES256-GCM-SHA384	|{rsa_psk, aes_256_gcm, null, sha384}
|TLS_PSK_WITH_AES_128_CBC_SHA256	PSK-AES128-CBC-SHA256	|{psk, aes_128_cbc, sha256, default_prf}
|TLS_PSK_WITH_AES_256_CBC_SHA384	PSK-AES256-CBC-SHA384	|{psk, aes_256_cbc, sha384, default_prf}
|TLS_PSK_WITH_NULL_SHA256	PSK-NULL-SHA256	|{psk, null, sha256, default_prf}
|TLS_PSK_WITH_NULL_SHA384	PSK-NULL-SHA384	|{psk, null, sha384, default_prf}
|TLS_DHE_PSK_WITH_AES_128_CBC_SHA256	DHE-PSK-AES128-CBC-SHA256	|{dhe_psk, aes_128_cbc, sha256, default_prf}
|TLS_DHE_PSK_WITH_AES_256_CBC_SHA384	|DHE-PSK-AES256-CBC-SHA384	|{dhe_psk, aes_256_cbc, sha384, default_prf}
|TLS_DHE_PSK_WITH_NULL_SHA256	|DHE-PSK-NULL-SHA256	|{dhe_psk, null, sha256, default_prf}
|TLS_DHE_PSK_WITH_NULL_SHA384	|DHE-PSK-NULL-SHA384	|{dhe_psk, null, sha384, default_prf}
|TLS_RSA_PSK_WITH_AES_128_CBC_SHA256|	RSA-PSK-AES128-CBC-SHA256	|{rsa_psk, aes_128_cbc, sha256, default_prf}
|TLS_RSA_PSK_WITH_AES_256_CBC_SHA384|	RSA-PSK-AES256-CBC-SHA384	|{rsa_psk, aes_256_cbc, sha384, default_prf}
|TLS_RSA_PSK_WITH_NULL_SHA256	|RSA-PSK-NULL-SHA256	|{rsa_psk, null, sha256, default_prf}
|TLS_RSA_PSK_WITH_NULL_SHA384	|RSA-PSK-NULL-SHA384	|{rsa_psk, null, sha384, default_prf}
|TLS_ECDH_ECDSA_WITH_NULL_SHA	|ECDH-ECDSA-NULL-SHA	|{ecdh_ecdsa, null, sha, default_prf}
|TLS_ECDH_ECDSA_WITH_RC4_128_SHA	|ECDH-ECDSA-RC4-SHA	|{ecdh_ecdsa, rc4_128, sha, default_prf}
|TLS_ECDH_ECDSA_WITH_3DES_EDE_CBC_SHA	|ECDH-ECDSA-DES-CBC3-SHA	|{ecdh_ecdsa, '3des_ede_cbc', sha, default_prf}
|TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA	|ECDH-ECDSA-AES128-SHA	|{ecdh_ecdsa, aes_128_cbc, sha, default_prf}
|TLS_ECDH_ECDSA_WITH_AES_256_CBC_SHA	|ECDH-ECDSA-AES256-SHA	|{ecdh_ecdsa, aes_256_cbc, sha, default_prf}
|TLS_ECDHE_ECDSA_WITH_NULL_SHA	|ECDHE-ECDSA-NULL-SHA	|{ecdhe_ecdsa, null, sha, default_prf}
|TLS_ECDHE_ECDSA_WITH_RC4_128_SHA	|ECDHE-ECDSA-RC4-SHA	|{ecdhe_ecdsa, rc4_128, sha, default_prf}
|TLS_ECDHE_ECDSA_WITH_3DES_EDE_CBC_SHA	|ECDHE-ECDSA-DES-CBC3-SHA	|{ecdhe_ecdsa, '3des_ede_cbc', sha, default_prf}
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA	|ECDHE-ECDSA-AES128-SHA	|{ecdhe_ecdsa, aes_128_cbc, sha, default_prf}
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA	|ECDHE-ECDSA-AES256-SHA	|{ecdhe_ecdsa, aes_256_cbc, sha, default_prf}
|TLS_ECDH_RSA_WITH_NULL_SHA	|ECDH-RSA-NULL-SHA	|{ecdh_rsa, null, sha, default_prf}
|TLS_ECDH_RSA_WITH_RC4_128_SHA	|ECDH-RSA-RC4-SHA	|{ecdh_rsa, rc4_128, sha, default_prf}
|TLS_ECDH_RSA_WITH_3DES_EDE_CBC_SHA	|ECDH-RSA-DES-CBC3-SHA	|{ecdh_rsa, '3des_ede_cbc', sha, default_prf}
|TLS_ECDH_RSA_WITH_AES_128_CBC_SHA	|ECDH-RSA-AES128-SHA	|{ecdh_rsa, aes_128_cbc, sha, default_prf}
|TLS_ECDH_RSA_WITH_AES_256_CBC_SHA	|ECDH-RSA-AES256-SHA	|{ecdh_rsa, aes_256_cbc, sha, default_prf}
|TLS_ECDHE_RSA_WITH_NULL_SHA	|ECDHE-RSA-NULL-SHA	|{ecdhe_rsa, null, sha, default_prf}
|TLS_ECDHE_RSA_WITH_RC4_128_SHA	|ECDHE-RSA-RC4-SHA	|{ecdhe_rsa, rc4_128, sha, default_prf}
|TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA	ECDHE-RSA-DES-CBC3-SHA	{ecdhe_rsa, '3des_ede_cbc', sha, default_prf}
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA	ECDHE-RSA-AES128-SHA	{ecdhe_rsa, aes_128_cbc, sha, default_prf}
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA	ECDHE-RSA-AES256-SHA	{ecdhe_rsa, aes_256_cbc, sha, default_prf}
|TLS_ECDH_anon_WITH_NULL_SHA	AECDH-NULL-SHA	{ecdh_anon, null, sha, default_prf}
|TLS_ECDH_anon_WITH_RC4_128_SHA	AECDH-RC4-SHA	{ecdh_anon, rc4_128, sha, default_prf}
|TLS_ECDH_anon_WITH_3DES_EDE_CBC_SHA	AECDH-DES-CBC3-SHA	{ecdh_anon, '3des_ede_cbc', sha, default_prf}
|TLS_ECDH_anon_WITH_AES_128_CBC_SHA	AECDH-AES128-SHA	{ecdh_anon, aes_128_cbc, sha, default_prf}
|TLS_ECDH_anon_WITH_AES_256_CBC_SHA	AECDH-AES256-SHA	{ecdh_anon, aes_256_cbc, sha, default_prf}
|TLS_SRP_SHA_WITH_3DES_EDE_CBC_SHA	SRP-3DES-EDE-CBC-SHA	{srp_anon, '3des_ede_cbc', sha, default_prf}
|TLS_SRP_SHA_RSA_WITH_3DES_EDE_CBC_SHA	SRP-RSA-3DES-EDE-CBC-SHA	{srp_rsa, '3des_ede_cbc', sha, default_prf}
|TLS_SRP_SHA_DSS_WITH_3DES_EDE_CBC_SHA	SRP-DSS-3DES-EDE-CBC-SHA	{srp_dss, '3des_ede_cbc', sha, default_prf}
|TLS_SRP_SHA_WITH_AES_128_CBC_SHA	SRP-AES-128-CBC-SHA	{srp_anon, aes_128_cbc, sha, default_prf}
|TLS_SRP_SHA_RSA_WITH_AES_128_CBC_SHA	SRP-RSA-AES-128-CBC-SHA	|{srp_rsa, aes_128_cbc, sha, default_prf}
|TLS_SRP_SHA_DSS_WITH_AES_128_CBC_SHA	SRP-DSS-AES-128-CBC-SHA	|{srp_dss, aes_128_cbc, sha, default_prf}
|TLS_SRP_SHA_WITH_AES_256_CBC_SHA	|SRP-AES-256-CBC-SHA	|{srp_anon, aes_256_cbc, sha, default_prf}
|TLS_SRP_SHA_RSA_WITH_AES_256_CBC_SHA	|SRP-RSA-AES-256-CBC-SHA	|{srp_rsa, aes_256_cbc, sha, default_prf}
|TLS_SRP_SHA_DSS_WITH_AES_256_CBC_SHA	|SRP-DSS-AES-256-CBC-SHA	|{srp_dss, aes_256_cbc, sha, default_prf}
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256	|ECDHE-ECDSA-AES128-SHA256	|{ecdhe_ecdsa, aes_128_cbc, sha256, sha256}
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384	ECDHE-ECDSA-AES256-SHA384	{ecdhe_ecdsa, aes_256_cbc, sha384, sha384}
|TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA256	ECDH-ECDSA-AES128-SHA256	{ecdh_ecdsa, aes_128_cbc, sha256, sha256}
|TLS_ECDH_ECDSA_WITH_AES_256_CBC_SHA384	ECDH-ECDSA-AES256-SHA384	{ecdh_ecdsa, aes_256_cbc, sha384, sha384}
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256	ECDHE-RSA-AES128-SHA256	{ecdhe_rsa, aes_128_cbc, sha256, sha256}
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384	ECDHE-RSA-AES256-SHA384	{ecdhe_rsa, aes_256_cbc, sha384, sha384}
|TLS_ECDH_RSA_WITH_AES_128_CBC_SHA256	ECDH-RSA-AES128-SHA256	{ecdh_rsa, aes_128_cbc, sha256, sha256}
|TLS_ECDH_RSA_WITH_AES_256_CBC_SHA384	ECDH-RSA-AES256-SHA384	{ecdh_rsa, aes_256_cbc, sha384, sha384}
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256	ECDHE-ECDSA-AES128-GCM-SHA256	{ecdhe_ecdsa, aes_128_gcm, null, sha256}
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384	ECDHE-ECDSA-AES256-GCM-SHA384	{ecdhe_ecdsa, aes_256_gcm, null, sha384}
|TLS_ECDH_ECDSA_WITH_AES_128_GCM_SHA256	|ECDH-ECDSA-AES128-GCM-SHA256	|{ecdh_ecdsa, aes_128_gcm, null, sha256}
|TLS_ECDH_ECDSA_WITH_AES_256_GCM_SHA384	|ECDH-ECDSA-AES256-GCM-SHA384	|{ecdh_ecdsa, aes_256_gcm, null, sha384}
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256	|ECDHE-RSA-AES128-GCM-SHA256	|{ecdhe_rsa, aes_128_gcm, null, sha256}
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384	|ECDHE-RSA-AES256-GCM-SHA384	|{ecdhe_rsa, aes_256_gcm, null, sha384}
|TLS_ECDH_RSA_WITH_AES_128_GCM_SHA256	ECDH-RSA-AES128-GCM-SHA256	|{ecdh_rsa, aes_128_gcm, null, sha256}
|TLS_ECDH_RSA_WITH_AES_256_GCM_SHA384	ECDH-RSA-AES256-GCM-SHA384	|{ecdh_rsa, aes_256_gcm, null, sha384}
|TLS_ECDHE_PSK_WITH_RC4_128_SHA	ECDHE-PSK-RC4-SHA	|{ecdhe_psk, rc4_128, sha, default_prf}
|TLS_ECDHE_PSK_WITH_3DES_EDE_CBC_SHA	ECDHE-PSK-3DES-EDE-CBC-SHA	{ecdhe_psk, '3des_ede_cbc', sha, default_prf}
|TLS_ECDHE_PSK_WITH_AES_128_CBC_SHA	ECDHE-PSK-AES128-CBC-SHA	{ecdhe_psk, aes_128_cbc, sha, default_prf}
|TLS_ECDHE_PSK_WITH_AES_256_CBC_SHA	ECDHE-PSK-AES256-CBC-SHA	{ecdhe_psk, aes_256_cbc, sha, default_prf}
|TLS_ECDHE_PSK_WITH_AES_128_CBC_SHA256	ECDHE-PSK-AES128-CBC-SHA256	{ecdhe_psk, aes_128_cbc, sha256, default_prf}
|TLS_ECDHE_PSK_WITH_AES_256_CBC_SHA384	|ECDHE-PSK-AES256-CBC-SHA384|	{ecdhe_psk, aes_256_cbc, sha384, default_prf}
|TLS_ECDHE_PSK_WITH_NULL_SHA256	|ECDHE-PSK-NULL-SHA256	|{ecdhe_psk, null, sha256, default_prf}
|TLS_ECDHE_PSK_WITH_NULL_SHA384	|ECDHE-PSK-NULL-SHA384	|{ecdhe_psk, null, sha384, default_prf}
|TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256	|ECDHE-RSA-CHACHA20-POLY1305	|{ecdhe_rsa, chacha20_poly1305, null, sha256}
|TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256	|ECDHE-ECDSA-CHACHA20-POLY1305	|{ecdhe_ecdsa, chacha20_poly1305, null, sha256}
|TLS_DHE_RSA_WITH_CHACHA20_POLY1305_SHA256	|DHE-RSA-CHACHA20-POLY1305	|{dhe_rsa, chacha20_poly1305, null, sha256}

映射表：
https://github.com/rabbitmq/rabbitmq-website/issues/453
加密套概念：
https://blog.helong.info/blog/2015/01/23/ssl_tls_ciphersuite_intro/
加密协议：
https://www.iana.org/assignments/tls-parameters/tls-parameters.xhtml#tls-parameters-4
openssl配置说明：
http://www.jinbuguo.com/linux/openssl_install.html