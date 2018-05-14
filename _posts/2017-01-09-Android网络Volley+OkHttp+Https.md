--
layout:     post                            # 使用的布局（不需要改）
title:      Android网络Volley+OkHttp+Https             # 标题
subtitle:      #副标题
date:       2017-01-09                      # 时间
author:     PanMin                              # 作者
header-img: img/post-bg-2015.jpg            #这篇文章标题背景图片
catalog: true                               # 是否归档
tags:                                       #标签
    - Android 
---


## 使用 OkHttp 作为传输层的实现.

Volley 默认根据 Android 系统版本使用不同的 Http 传输协议实现.
在 Android 2.3以下使用 ApacheHttpStack 作为传输协议, 在 3.0 及以下使用 HttpURLConnection 作为传输层协议


OkHttp 相较于其它的实现有以下的优点.

* 支持SPDY，允许连接同一主机的所有请求分享一个socket。
* 如果SPDY不可用，会使用连接池减少请求延迟。
* 使用GZIP压缩下载内容，且压缩操作对用户是透明的。
* 利用响应缓存来避免重复的网络请求。
* 当网络出现问题的时候，OKHttp会依然有效，它将从常见的连接问题当中恢复。
* 如果你的服务端有多个IP地址，当第一个地址连接失败时，OKHttp会尝试连接其他的地址，这对IPV4和IPV6以及寄宿在多个数据中心的服务而言，是非常有必要的。

	因此使用 OkHttp 作为替代是好的选择.

1. 首先用 OkHttp 实现一个新的 HurlStack 用于构建 Volley 的 requestQueue.

	```
	public class OkHttpStack extends HurlStack {

	 private OkHttpClient okHttpClient;
	
	 /**
	  * Create a OkHttpStack with default OkHttpClient.
	  */
	 public OkHttpStack() {
	     this(new OkHttpClient());
	 }
	
	 /**
	  * Create a OkHttpStack with a custom OkHttpClient
	  * @param okHttpClient Custom OkHttpClient, NonNull
	  */
	 public OkHttpStack(OkHttpClient okHttpClient) {
	     this.okHttpClient = okHttpClient;
	 }
	
	 @Override
	 protected HttpURLConnection createConnection(URL url) throws IOException {
	     OkUrlFactory okUrlFactory = new OkUrlFactory(okHttpClient);
	     return okUrlFactory.open(url);
	 }
	}
	```
2. 然后使用 OkHttpStack 创建新的 Volley requestQueue.

	```
	requestQueue = Volley.newRequestQueue(getContext(), new OkHttpStack());
	requestQueue.start();
	```
	这样就行了.


## 使用 Https
OkHttp 自身是支持 Https 的. 参考文档 [OkHttp Https](https://github.com/square/okhttp/wiki/HTTPS), 直接使用上面的 OkHttpStack 就可以了, 但是如果遇到服务器开发哥哥使用了自签名的证书(不要问我为什么要用自签名的), 就无法正常访问了.

网上有很多文章给出的方案是提供一个什么事情都不做的TrustManager 跳过 SSL 的验证, 这样做很容受到攻击, Https 也就形同虚设了.

我采用的方案是将自签名的证书打包入 APK 加入信任.

**好处:**

* 应用难以逆向, 应用不再依赖系统的 trust store, 使得 Charles 抓包等工具失效. 要分析应用 API 必须反编译 APK.
* 不用额外购买证书, 省钱....

**缺点:**

* 证书部署灵活性降低, 一旦变更证书必须升级程序.


## 实现步骤
以最著名的自签名网站12306为例说明
1. 导出证书

	```
	echo | openssl s_client -connect kyfw.12306.cn:443 2>&1 |  sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > kyfw.12306.cn.pem
	```

2. 将证书转为 bks 格式
	下载最新的bcprov-jdk, 执行下面的命令. storepass 是导出密钥文件的密码.
	```
	keytool -importcert -v \
	 -trustcacerts \
	 -alias 0 \
	 -file <(openssl x509 -in kyfw.12306.cn.pem) \
	 -keystore $CERTSTORE -storetype BKS \
	 -providerclass org.bouncycastle.jce.provider.BouncyCastleProvider \
	 -providerpath ./bcprov-jdk16-1.46.jar \
	 -storepass asdfqaz
	```

3. 将导出的 kyfw.bks 文件放入 res/raw 文件夹下.
4. 创建 **SelfSignSslOkHttpStack**
	```
	/**
	* A HttpStack implement witch can verify specified self-signed certification.
	*/
	public class SelfSignSslOkHttpStack extends HurlStack {
	
	 private OkHttpClient okHttpClient;
	
	 private Map<String, SSLSocketFactory> socketFactoryMap;
	
	 /**
	  * Create a OkHttpStack with default OkHttpClient.
	  */
	 public SelfSignSslOkHttpStack(Map<String, SSLSocketFactory> factoryMap) {
	     this(new OkHttpClient(), factoryMap);
	 }
	
	 /**
	  * Create a OkHttpStack with a custom OkHttpClient
	  * @param okHttpClient Custom OkHttpClient, NonNull
	  */
	 public SelfSignSslOkHttpStack(OkHttpClient okHttpClient, Map<String, SSLSocketFactory> factoryMap) {
	     this.okHttpClient = okHttpClient;
	     this.socketFactoryMap = factoryMap;
	 }
	
	 @Override
	 protected HttpURLConnection createConnection(URL url) throws IOException {
	     if ("https".equals(url.getProtocol()) && socketFactoryMap.containsKey(url.getHost())) {
	         HttpsURLConnection connection = (HttpsURLConnection) new OkUrlFactory(okHttpClient).open(url);
	         connection.setSSLSocketFactory(socketFactoryMap.get(url.getHost()));
	         return connection;
	     } else {
	         return  new OkUrlFactory(okHttpClient).open(url);
	     }
	 }
	}
	```

5. 然后用 SelfSignSslOkHttpStack 创建 Volley 的 RequestQueue.
	
	```
	String[] hosts = {"kyfw.12306.cn"};
	int[] certRes = {R.raw.kyfw};
	String[] certPass = {"asdfqaz"};
	socketFactoryMap = new Hashtable<>(hosts.length);
	
	for (int i = 0; i < certRes.length; i++) {
	    int res = certRes[i];
	    String password = certPass[i];
	    SSLSocketFactory sslSocketFactory = createSSLSocketFactory(context, res, password);
	    socketFactoryMap.put(hosts[i], sslSocketFactory);
	}
	
	HurlStack stack = new SelfSignSslOkHttpStack(socketFactoryMap);
	
	requestQueue = Volley.newRequestQueue(context, stack);
	requestQueue.start();
	```

6. 我们来试一试, 用上一步穿件的 RequestQueue 替换掉原来的, 然后发请求试试.
	```
	StringRequest request = new StringRequest(
             Request.Method.GET,
             "https://kyfw.12306.cn/otn/",
             new Response.Listener<String>() {
                 @Override
                 public void onResponse(String response) {
                     responseContentTextView.setText(response);
                 }
             },
             new Response.ErrorListener() {
                 @Override
                 public void onErrorResponse(VolleyError error) {
                     responseContentTextView.setText(error.toString());
                 }
             });
     RequestManager.getInstance(this).addRequest(request, this);
	```

7. done