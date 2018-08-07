---
title: Tomcat双证书
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---

# Tomcat 设置双向证书

## 双向认证

 1. 生成客户端密钥库client.p12

		keytool -genkeypair -v -keystore client.p12 -storetype pkcs12 -storepass 123456 -alias client -keyalg RSA -keysize 2048 -validity 36500

2. 导出客户端证书client.cer

		Keytool -exportcert -v -file client.cer -keystore client.p12 -storepass 123456 -alias client

3. 将客户端证书client.cer导入服务端密钥库信任证书列表

		Keytool -importcert -v -file client.cer -keystore server.p12 -storepass 123456 -alias client

3.1 网上有的将服务器证书添加进了客户端的证书信任列表，但是经测试，客户端不需要添加对服务端证书的信任也能正常访问

3.2 抱着验证是不是只要能找到信任证书路径，就可以了正常访问了的想法，我没将客户端的证书添加进服务端密钥库信任列表，而是直接保存在了一个新的密钥库里面

		Keytool -importcert -v -file client.cer -alias client -keystore trustore.p12 -storepass 123456 -storetype pkcs12

配置文件如下：

	<Connector       
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           sslImplementationName="org.apache.tomcat.util.net.jsse.JSSEImplementation"
           port="8443" maxThreads="200"
           scheme="https" secure="true" SSLEnabled="true"
          keystoreFile="C:\CodeProjects\IdeaWebProjects\TomcatHttpsConfigure\双向认证\server.p12" keystorePass="123456"
   		  truststoreFile="C:\CodeProjects\IdeaWebProjects\TomcatHttpsConfigure\双向认证\trustore.p12" truststorePass="123456"
           clientAuth="true" sslProtocol="TLS"/>

打开 https://www.sy.com:8443 ，访问成功！

4. 配置server.xml

		<Connector
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           sslImplementationName="org.apache.tomcat.util.net.jsse.JSSEImplementation"
           port="8443" maxThreads="200"
           scheme="https" secure="true" SSLEnabled="true"
           keystoreFile="C:\CodeProjects\IdeaWebProjects\TomcatHttpsConfigure\双向认证\server.p12" keystorePass="123456"
           truststoreFile="C:\CodeProjects\IdeaWebProjects\TomcatHttpsConfigure\双向认证\server.p12" truststorePass="123456"
           clientAuth="true" sslProtocol="TLS"/>



port="8443"：https访问端口为8443（Tomcat已使用8080作为http的访问端口）
keystoreFile：存储加密证书的密钥库
keystorePass：密钥库访问密码（JKS格式的话，需要在生成密钥库时storepass和keypass相同）
truststoreFile：保存信任证书的密钥库。这里只需要证书（公钥）即可，而我们服务端密钥库将客户端证书添加信任的同时也保存了他，所以这里可以直接设置服务端密钥库地址。假如有客户端的密钥库的话，或者新建了一个密钥库用以专门保存信任证书的话，也可以写它的地址，虽然我们只用它的公钥
truststorePass：保存信任证书密钥库的访问密码
clientAuth：是否验证客户端，false为单向认证，true为双向认证。双向认证时需要提供信任证书列表（需配置truststoreFile，truststorePass属性）

 
5. 安装CA根证书root.cer（CA密钥库公钥）到可信任的根证书颁发机构

6. 安装客户端密钥库到个人（注意，是密钥库而不是证书，因为在通信的时候需要用到私钥）

打开https://www.sy.com:8443 验证

其他说明

X.509证书（*.cer、*.crt）
个人信息交换-PKCS#12（*.pfx、*.p12）//一个文件中可存储多个证书
证书信任列表（*.stl）
证书吊销列表（*.crl）
Microsoft系列证书存储（*.cer、*.cer）//一个文件中可存储多个证书
加密消息语法标准-PKCS#7证书（*.spc、*.p7b）//一个文件中可存储多个证书

 
在只安装证书（公钥）的情况下，如.cer格式，在打开已安装证书列表中会找不到(安装到受信任的根证书颁发机构可以找到，其他的没测试)。可以通过以下方法找到：

1. Win+R->输入命令：mmc
2. 文件->添加/删除管理单元
3. 在左面列表中选择证书项，点击中间的添加按钮，在弹出界面中选择完成，回到原来界面选确定
4. 之后就可以在最开始界面的左面列表发现证书->当前用户了，进去后可以看到隐藏的已安装的公钥证书，可以右键删除也可以在这里导入新的证书