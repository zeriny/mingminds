## Web相关

#### webkit

广义上来说，WebKit是一个开源的项目，其前身是来源于KDE的KHTML和KJS。该项目专注于网页内容的展示，开发出**一流的网页渲染引擎**。它不是浏览器，而且也不想成为浏览器。

该项目包含两个部分，第一是WebCore，其中包含了对HTML，CSS等很多W3C规范的实现；第二部分就是狭义上的WebKit，它主要是各个平台的移植并提供相对应的Web接口，也就是WebView或者类似WebView，这些接口提供操作和显示网页的能力。目前使用WebKit的主流的浏览器或者WebView包括Chrome, Safari, QtWebKit, Android Browser以及众多的移动平台的浏览器。

#### WebView

https://developer.android.com/guide/webapps/webview?hl=zh-cn#java

- `WebView` 类是 Android 的 `View` 类的扩展，可让您将网页显示为 Activity 布局的一部分。它不会包含功能全面的网络浏览器的任何功能，例如导航控件或地址栏。`WebView` 默认只显示网页。

- WebView中可以启用JavaScript

  ```java
  WebView myWebView = (WebView) findViewById(R.id.webview);
  WebSettings webSettings = myWebView.getSettings();
  webSettings.setJavaScriptEnabled(true);
  ```

##### 处理网页导航

当用户在 `WebView` 中点击网页中的链接时，Android 的默认行为是启动处理网址的应用。默认网络浏览器通常会打开并加载目标网址。不过，您可以为 `WebView` 替换此行为，以便在 `WebView` 内打开链接。然后，您可以允许用户向后/向前浏览由您的 `WebView` 维护的网页历史记录。

```java
private class MyWebViewClient extends WebViewClient {
  @Override
  public boolean shouldOverrideUrlLoading(WebView view, String url) {
  if ("www.example.com".equals(Uri.parse(url).getHost())) {
  // This is my website, so do not override; let my WebView load the page
  return false;
  }
  // Otherwise, the link is not for a page on my site, so launch another Activity that handles URLs
  Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
  startActivity(intent);
  return true;
  }
}
```

然后，为 `WebView` 创建这一新 `WebViewClient` 的实例：

```JAVA
WebView myWebView = (WebView) findViewById(R.id.webview);
myWebView.setWebViewClient(new MyWebViewClient());
```

现在，当用户点击某个链接时，系统会调用 `shouldOverrideUrlLoading()`，后者会检查网址主机是否与特定网域匹配（如上所述）。如果匹配，则该方法会返回 false，以避免替换网址加载（它允许 `WebView` 像往常一样加载网址）。如果网址主机不匹配，则创建 `Intent` 以启动用于处理网址的默认 Activity（解析为用户的默认网络浏览器）。

##### 定义程序化操作 

当 [`WebView`](https://developer.android.com/reference/android/webkit/WebView?hl=zh-cn) 实例尝试加载被 Google 归类为已知威胁的页面时，`WebView` 默认会显示一个插页，以便警告用户存在已知威胁。通过该界面，用户可以选择仍然加载网址或返回到上一个安全的页面。

如果您以 Android 8.1（API 级别 27）或更高版本为目标平台，则可以程序化地定义您的应用响应已知威胁的方式：

- 您可以控制应用是否向安全浏览功能报告已知威胁。
- 您可以让应用每次遇到被归类为已知威胁的网址时都自动执行特定操作（例如返回安全的页面）。

以下代码段演示了如何指示应用的 `WebView` 实例在遇到已知威胁后始终返回安全页面：

MyWebActivity.java

```java
private WebView superSafeWebView;
  private boolean safeBrowsingIsInitialized;
  // ...
  @Override
  protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  superSafeWebView = new WebView(this);
  superSafeWebView.setWebViewClient(new MyWebViewClient());
  safeBrowsingIsInitialized = false;

  if (WebViewFeature.isFeatureSupported(WebViewFeature.START_SAFE_BROWSING)) {
  WebViewCompat.startSafeBrowsing(this, new ValueCallback<Boolean>() {
  @Override
  public void onReceiveValue(Boolean success) {
  safeBrowsingIsInitialized = true;
  if (!success) {
  Log.e("MY_APP_TAG", "Unable to initialize Safe Browsing!");
  }
  }
  });
  }
}
```

MyWebViewClient.java

```java
public class MyWebViewClient extends WebViewClientCompat {
  // Automatically go "back to safety" when attempting to load a website that
  // Google has identified as a known threat. An instance of WebView calls
  // this method only after Safe Browsing is initialized, so there's no
  // conditional logic needed here.
  @Override
  public void onSafeBrowsingHit(WebView view, WebResourceRequest request,
  int threatType, SafeBrowsingResponseCompat callback) {
  // The "true" argument indicates that your app reports incidents like
  // this one to Safe Browsing.
  if (WebViewFeature.isFeatureSupported(WebViewFeature.SAFE_BROWSING_RESPONSE_BACK_TO_SAFETY)) {
      callback.backToSafety(true);
      Toast.makeText(view.getContext(), "Unsafe web page blocked.",
      Toast.LENGTH_LONG).show();
    }
  }
}
```

> Google Safe Browsing: https://developers.google.com/safe-browsing/?hl=zh-cn



#### Android Security Policy

- Starting from Android 6.0, Google introduced a new security mechanism to help apps preventing cleartext communication, named **Network Security Policy** [5]. With this new policy, an app can specify the usesCleartextTraffic boolean attribute in its manifest file and, by setting it to **false**, the app can completely **opt-out from using cleartext protocols**, such as HTTP, FTP, IMAP, SMTP, WebSockets or XMPP