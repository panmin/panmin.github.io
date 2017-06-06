---
layout:     post                            # 使用的布局（不需要改）
title:      spring-boot中使用https                   # 标题
subtitle:     #副标题
date:       2017-06-06                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Java
---


# spring-boot中使用https

## 获取ssl证书
两张获取方式：
* 自己通过keytool生成
> keytool在jdk的bin目录下

```
keytool -genkey -alias tomcat  -storetype PKCS12 -keyalg RSA -keysize 2048  -keystore keystore.p12 -validity 3650

Enter keystore password:
Re-enter new password:
What is your first and last name?
[Unknown]:
What is the name of your organizational unit?
[Unknown]:
What is the name of your organization?
[Unknown]:
What is the name of your City or Locality?
[Unknown]:
What is the name of your State or Province?
[Unknown]:
What is the two-letter country code for this unit?
[Unknown]:
Is CN=Unknown, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown correct?
[no]: yes
```
这样就会生成一个PKCS12格式的叫做keystore.p12的证书。
* 通过证书授权机构证书
> 可以使用阿里云盾的免费证书：https://www.aliyun.com/product/cas

购买后, 会有邮件发到注册邮箱来认证的. 当然, 这里要注意这个免费的证书只能用在一个域名下。

## 证书在spring-boot配置
* 自己通过keytool生成的keystore.p12文件放在项目根目录下
在application.properties中配置HTTPS：
```
server.port: 8443
server.ssl.key-store: classpath:keystore.p12
server.ssl.key-store-password: 123456 #生成ssl时的密码
server.ssl.keyStoreType: PKCS12
server.ssl.keyAlias: tomcat
```
* 在阿里云盾申请的免费证书下载下来是pfx格式的，放在resources目录下和application.properties并列。
在application.properties中配置HTTPS：
```
server.ssl.key-store=classpath:1111111.pfx
server.ssl.key-store-password=1111111
server.ssl.keyStoreType=PKCS12
```
`因为是生产环境, 需要做 http 自动转到 https. 所以这里不配置端口号.`

## http重定向到https
在spring-boot的Application启动类中添加以下代码：
```
 @Bean
  public EmbeddedServletContainerFactory servletContainer() {

    TomcatEmbeddedServletContainerFactory tomcat = new TomcatEmbeddedServletContainerFactory() {

        @Override
        protected void postProcessContext(Context context) {

          SecurityConstraint securityConstraint = new SecurityConstraint();
          securityConstraint.setUserConstraint("CONFIDENTIAL");
          SecurityCollection collection = new SecurityCollection();
          collection.addPattern("/*");
          securityConstraint.addCollection(collection);
          context.addConstraint(securityConstraint);
        }
    };
    tomcat.addAdditionalTomcatConnectors(initiateHttpConnector());
    return tomcat;
  }

  private Connector initiateHttpConnector() {

    Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
    connector.setScheme("http");
    connector.setPort(8080);
    connector.setSecure(false);
    connector.setRedirectPort(8443);
    return connector;
  }
```