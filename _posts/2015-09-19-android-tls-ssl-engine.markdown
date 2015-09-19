---
layout: post
title:  "How to make AndroidAsync use TLSv1.2 in older versions of Android"
date:   2015-09-19 13:47:00
categories: android
---

Recently, I was trying to enable [TLSv1.2][TLS] on an Android application to increase security of app/ backend server communication.
This app uses the [AndroidAsync][AndroidAsync] library and any Android device prior to API lvl 20 (Lollipop) was returning `SSLException` when trying to establish a websocket to the backend server.

According to [this Stack Overflow post][so_post], [SSLEngine][SSLEngine] (which AndroidAsync uses) only supports TLSv1.1 and TLSv1.2 on API lvl 20 or higher. However, the majority of Android users (at the time of me writing this post) aren't running API lvl 20 or higher and using older security protocols is a poor solution. Thus, I used the above post to determine how to make [AndroidAsync][AndroidAsync] allow TLSv1.2.  

First, install a newer security provider using [Google Play Services][gps]:

```
ProviderInstaller.installIfNeeded(context);
```

Next, create your SSLContext and pass it "TLSv1.2":

```
SSLContext sslContext = SSLContext.getInstance("TLSv1.2");
```

Initialize the SSLContext with whatever params you need:

```
sslContext.init(KeyManager[] km, TrustManager[] tm, SecureRandom rm);
```

Create a new instance of SSLEngine using your new SSLContext:

```
SSLEngine engine = sslContext.createSSLEngine();
```

Finally, insert this new SSLEngine as middleware to AndroidAsync:

```
AsyncHttpClient.getDefaultInstance().insertMiddleware((AsyncHttpClientMiddleware) engine);
```

Here are all the steps at once:

```
ProviderInstaller.installIfNeeded(context);

SSLContext sslContext = SSLContext.getInstance("TLSv1.2");

sslContext.init(KeyManager[] km, TrustManager[] tm, SecureRandom rm);

SSLEngine engine = sslContext.createSSLEngine();

AsyncHttpClient.getDefaultInstance().insertMiddleware((AsyncHttpClientMiddleware) engine);

```

I created a [branch][AndroidAsyncBranch] of [AndroidAsync][AndroidAsync] with this code, feel free to comment/ contribute to it.

[onfleet]: https://onfleet.com/
[TLS]: https://en.wikipedia.org/wiki/Transport_Layer_Security     
[SSLEngine]: http://developer.android.com/reference/javax/net/ssl/SSLEngine.html
[so_post]: http://stackoverflow.com/questions/24357863/making-sslengine-use-tlsv1-2-on-android-4-4-2
[AndroidAsync]: https://github.com/koush/AndroidAsync
[gps]: https://developers.google.com/android/reference/com/google/android/gms/security/ProviderInstaller

[AndroidAsyncBranch]: https://github.com/koush/AndroidAsync/pull/394
