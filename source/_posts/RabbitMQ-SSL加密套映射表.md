---
title: 'RabbitMQ:SSL加密套映射表'
categories: RabbitMQ
tags:
  - RabbitMQ
  - AMQP
  - 消息队列
keywords:
  - RabbitMQ
  - AMQP
  - 消息队列
abbrlink: bcc0ef8
date: 2018-09-10 19:06:22
description:
---
关于加密套中，OpenSSL、IANA和Erlang的映射关系

<!--more-->

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
|TLS_DHE_PSK_WITH_RC4_128_SHA	|DHE-PSK-RC4-SHA	|{dhe_psk, rc4_128, sha, default_prf}
|TLS_DHE_PSK_WITH_3DES_EDE_CBC_SHA	|DHE-PSK-3DES-EDE-CBC-SHA	|{dhe_psk, '3des_ede_cbc', sha, default_prf}
|TLS_DHE_PSK_WITH_AES_128_CBC_SHA|	DHE-PSK-AES128-CBC-SHA	|{dhe_psk, aes_128_cbc, sha, default_prf}
|TLS_DHE_PSK_WITH_AES_256_CBC_SHA	|DHE-PSK-AES256-CBC-SHA	|{dhe_psk, aes_256_cbc, sha, default_prf}
|TLS_RSA_PSK_WITH_RC4_128_SHA	|RSA-PSK-RC4-SHA	|{rsa_psk, rc4_128, sha, default_prf}
|TLS_RSA_PSK_WITH_3DES_EDE_CBC_SHA	|RSA-PSK-3DES-EDE-CBC-SHA	|{rsa_psk, '3des_ede_cbc', sha, default_prf}
|TLS_RSA_PSK_WITH_AES_128_CBC_SHA|	RSA-PSK-AES128-CBC-SHA	|{rsa_psk, aes_128_cbc, sha, default_prf}
|TLS_RSA_PSK_WITH_AES_256_CBC_SHA	|RSA-PSK-AES256-CBC-SHA	|{rsa_psk, aes_256_cbc, sha, default_prf}
|TLS_RSA_WITH_AES_128_GCM_SHA256	|AES128-GCM-SHA256	|{rsa, aes_128_gcm, null, sha256}
|TLS_RSA_WITH_AES_256_GCM_SHA384	|AES256-GCM-SHA384	|{rsa, aes_256_gcm, null, sha384}
|TLS_DHE_RSA_WITH_AES_128_GCM_SHA256	|DHE-RSA-AES128-GCM-SHA256	|{dhe_rsa, aes_128_gcm, null, sha256}
|TLS_DHE_RSA_WITH_AES_256_GCM_SHA384	|DHE-RSA-AES256-GCM-SHA384	|{dhe_rsa, aes_256_gcm, null, sha384}
|TLS_DH_RSA_WITH_AES_128_GCM_SHA256	|DH-RSA-AES128-GCM-SHA256	|{dh_rsa, aes_128_gcm, null, sha256}
|TLS_DH_RSA_WITH_AES_256_GCM_SHA384	|DH-RSA-AES256-GCM-SHA384	|{dh_rsa, aes_256_gcm, null, sha384}
|TLS_DHE_DSS_WITH_AES_128_GCM_SHA256	|DHE-DSS-AES128-GCM-SHA256	|{dhe_dss, aes_128_gcm, null, sha256}
|TLS_DHE_DSS_WITH_AES_256_GCM_SHA384	|DHE-DSS-AES256-GCM-SHA384	|{dhe_dss, aes_256_gcm, null, sha384}
|TLS_DH_DSS_WITH_AES_128_GCM_SHA256	|DH-DSS-AES128-GCM-SHA256	|{dh_dss, aes_128_gcm, null, sha256}
|TLS_DH_DSS_WITH_AES_256_GCM_SHA384	|DH-DSS-AES256-GCM-SHA384	|{dh_dss, aes_256_gcm, null, sha384}
|TLS_DH_anon_WITH_AES_128_GCM_SHA256	|ADH-AES128-GCM-SHA256	|{dh_anon, aes_128_gcm, null, sha256}
|TLS_DH_anon_WITH_AES_256_GCM_SHA384	|ADH-AES256-GCM-SHA384	|{dh_anon, aes_256_gcm, null, sha384}
|TLS_PSK_WITH_AES_128_GCM_SHA256	|PSK-AES128-GCM-SHA256	|{psk, aes_128_gcm, null, sha256}
|TLS_PSK_WITH_AES_256_GCM_SHA384	|PSK-AES256-GCM-SHA384	|{psk, aes_256_gcm, null, sha384}
|TLS_DHE_PSK_WITH_AES_128_GCM_SHA256	|DHE-PSK-AES128-GCM-SHA256	|{dhe_psk, aes_128_gcm, null, sha256}
|TLS_DHE_PSK_WITH_AES_256_GCM_SHA384|	DHE-PSK-AES256-GCM-SHA384	|{dhe_psk, aes_256_gcm, null, sha384}
|TLS_RSA_PSK_WITH_AES_128_GCM_SHA256	|RSA-PSK-AES128-GCM-SHA256	|{rsa_psk, aes_128_gcm, null, sha256}
|TLS_RSA_PSK_WITH_AES_256_GCM_SHA384|	RSA-PSK-AES256-GCM-SHA384	|{rsa_psk, aes_256_gcm, null, sha384}
|TLS_PSK_WITH_AES_128_CBC_SHA256	|PSK-AES128-CBC-SHA256	|{psk, aes_128_cbc, sha256, default_prf}
|TLS_PSK_WITH_AES_256_CBC_SHA384	|PSK-AES256-CBC-SHA384	|{psk, aes_256_cbc, sha384, default_prf}
|TLS_PSK_WITH_NULL_SHA256	|PSK-NULL-SHA256	|{psk, null, sha256, default_prf}
|TLS_PSK_WITH_NULL_SHA384	|PSK-NULL-SHA384	|{psk, null, sha384, default_prf}
|TLS_DHE_PSK_WITH_AES_128_CBC_SHA256	|DHE-PSK-AES128-CBC-SHA256	|{dhe_psk, aes_128_cbc, sha256, default_prf}
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
|TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA	|ECDHE-RSA-DES-CBC3-SHA	|{ecdhe_rsa, '3des_ede_cbc', sha, default_prf}
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA	|ECDHE-RSA-AES128-SHA	|{ecdhe_rsa, aes_128_cbc, sha, default_prf}
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA	|ECDHE-RSA-AES256-SHA	|{ecdhe_rsa, aes_256_cbc, sha, default_prf}
|TLS_ECDH_anon_WITH_NULL_SHA	|AECDH-NULL-SHA	|{ecdh_anon, null, sha, default_prf}
|TLS_ECDH_anon_WITH_RC4_128_SHA	|AECDH-RC4-SHA	|{ecdh_anon, rc4_128, sha, default_prf}
|TLS_ECDH_anon_WITH_3DES_EDE_CBC_SHA|	AECDH-DES-CBC3-SHA	|{ecdh_anon, '3des_ede_cbc', sha, default_prf}
|TLS_ECDH_anon_WITH_AES_128_CBC_SHA	|AECDH-AES128-SHA	|{ecdh_anon, aes_128_cbc, sha, default_prf}
|TLS_ECDH_anon_WITH_AES_256_CBC_SHA	|AECDH-AES256-SHA	|{ecdh_anon, aes_256_cbc, sha, default_prf}
|TLS_SRP_SHA_WITH_3DES_EDE_CBC_SHA	|SRP-3DES-EDE-CBC-SHA	|{srp_anon, '3des_ede_cbc', sha, default_prf}
|TLS_SRP_SHA_RSA_WITH_3DES_EDE_CBC_SHA	|SRP-RSA-3DES-EDE-CBC-SHA	|{srp_rsa, '3des_ede_cbc', sha, default_prf}
|TLS_SRP_SHA_DSS_WITH_3DES_EDE_CBC_SHA	|SRP-DSS-3DES-EDE-CBC-SHA	|{srp_dss, '3des_ede_cbc', sha, default_prf}
|TLS_SRP_SHA_WITH_AES_128_CBC_SHA	|SRP-AES-128-CBC-SHA	|{srp_anon, aes_128_cbc, sha, default_prf}
|TLS_SRP_SHA_RSA_WITH_AES_128_CBC_SHA	|SRP-RSA-AES-128-CBC-SHA	|{srp_rsa, aes_128_cbc, sha, default_prf}
|TLS_SRP_SHA_DSS_WITH_AES_128_CBC_SHA	|SRP-DSS-AES-128-CBC-SHA	|{srp_dss, aes_128_cbc, sha, default_prf}
|TLS_SRP_SHA_WITH_AES_256_CBC_SHA	|SRP-AES-256-CBC-SHA	|{srp_anon, aes_256_cbc, sha, default_prf}
|TLS_SRP_SHA_RSA_WITH_AES_256_CBC_SHA	|SRP-RSA-AES-256-CBC-SHA	|{srp_rsa, aes_256_cbc, sha, default_prf}
|TLS_SRP_SHA_DSS_WITH_AES_256_CBC_SHA	|SRP-DSS-AES-256-CBC-SHA	|{srp_dss, aes_256_cbc, sha, default_prf}
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256	|ECDHE-ECDSA-AES128-SHA256	|{ecdhe_ecdsa, aes_128_cbc, sha256, sha256}
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384	|ECDHE-ECDSA-AES256-SHA384	|{ecdhe_ecdsa, aes_256_cbc, sha384, sha384}
|TLS_ECDH_ECDSA_WITH_AES_128_CBC_SHA256	|ECDH-ECDSA-AES128-SHA256	|{ecdh_ecdsa, aes_128_cbc, sha256, sha256}
|TLS_ECDH_ECDSA_WITH_AES_256_CBC_SHA384|	ECDH-ECDSA-AES256-SHA384	|{ecdh_ecdsa, aes_256_cbc, sha384, sha384}
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256	|ECDHE-RSA-AES128-SHA256	|{ecdhe_rsa, aes_128_cbc, sha256, sha256}
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384	|ECDHE-RSA-AES256-SHA384	|{ecdhe_rsa, aes_256_cbc, sha384, sha384}
|TLS_ECDH_RSA_WITH_AES_128_CBC_SHA256	|ECDH-RSA-AES128-SHA256	|{ecdh_rsa, aes_128_cbc, sha256, sha256}
|TLS_ECDH_RSA_WITH_AES_256_CBC_SHA384	|ECDH-RSA-AES256-SHA384	|{ecdh_rsa, aes_256_cbc, sha384, sha384}
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256	|ECDHE-ECDSA-AES128-GCM-SHA256	|{ecdhe_ecdsa, aes_128_gcm, null, sha256}
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384|	ECDHE-ECDSA-AES256-GCM-SHA384	|{ecdhe_ecdsa, aes_256_gcm, null, sha384}
|TLS_ECDH_ECDSA_WITH_AES_128_GCM_SHA256	|ECDH-ECDSA-AES128-GCM-SHA256	|{ecdh_ecdsa, aes_128_gcm, null, sha256}
|TLS_ECDH_ECDSA_WITH_AES_256_GCM_SHA384	|ECDH-ECDSA-AES256-GCM-SHA384	|{ecdh_ecdsa, aes_256_gcm, null, sha384}
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256	|ECDHE-RSA-AES128-GCM-SHA256	|{ecdhe_rsa, aes_128_gcm, null, sha256}
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384	|ECDHE-RSA-AES256-GCM-SHA384	|{ecdhe_rsa, aes_256_gcm, null, sha384}
|TLS_ECDH_RSA_WITH_AES_128_GCM_SHA256	|ECDH-RSA-AES128-GCM-SHA256	|{ecdh_rsa, aes_128_gcm, null, sha256}
|TLS_ECDH_RSA_WITH_AES_256_GCM_SHA384	|ECDH-RSA-AES256-GCM-SHA384	|{ecdh_rsa, aes_256_gcm, null, sha384}
|TLS_ECDHE_PSK_WITH_RC4_128_SHA	|ECDHE-PSK-RC4-SHA	|{ecdhe_psk, rc4_128, sha, default_prf}
|TLS_ECDHE_PSK_WITH_3DES_EDE_CBC_SHA	|ECDHE-PSK-3DES-EDE-CBC-SHA	|{ecdhe_psk, '3des_ede_cbc', sha, default_prf}
|TLS_ECDHE_PSK_WITH_AES_128_CBC_SHA	|ECDHE-PSK-AES128-CBC-SHA	|{ecdhe_psk, aes_128_cbc, sha, default_prf}
|TLS_ECDHE_PSK_WITH_AES_256_CBC_SHA	|ECDHE-PSK-AES256-CBC-SHA	|{ecdhe_psk, aes_256_cbc, sha, default_prf}
|TLS_ECDHE_PSK_WITH_AES_128_CBC_SHA256	|ECDHE-PSK-AES128-CBC-SHA256	|{ecdhe_psk, aes_128_cbc, sha256, default_prf}
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