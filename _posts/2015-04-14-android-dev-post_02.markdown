---
layout: post
title: "[Android] In app WebView 에서 intent:// url scheme 실행하는 방법" 
date:   2014-04-14 20:24:00 +0900
categories: android
comments: true
---

디자인적인 요소와 앱 이탈을 방지하기 등 앱 안에 WebView layout 을 넣어 지정된 영역 안에서 브라우저를 보여주는 식의 개발이 많이 쓰이곤 한다.<br>
여기서 단순 브라우저 노출에서 그칠 것이 아니라 마케팅의 일환로써도 URL Scheme 값을 사용. JavaScript 로 만든 앱 설치 유무를 판별하고 서로 다른 Landing 효과를 누릴 수 있는 페이지도 대부분의 기업에서 채용하고 있다. (통상 브릿지 페이지라고 한다.)


하지만 위와 같이 '설치유무를 판단하여 앱에 내려주는 Intent://scheme 값을 앱에서는 소화하지 못 한다. (아마 브라우저 창에 이상한 주소가 띄어지겠지..)<br>
브라우저 어플(익스플로러 or Chrome 등)이 아닌 별도의 내장 `WebViewClient`를 사용하게 됨으로써 발생하게 되는 문제이다.


보통 WebView 사용시 아래와 같은 코드를 사용한다.

{% highlight java %}
WebView webView; // 'new WebView' or 'findViewById'
webView.setWebViewClient(myWebViewClient);
 
private class MyWebViewClient extends WebViewClient {
    @Override
    public boolean shuldOverrideUrlLoading(WebView view, String url) {
        view.loadUrl(url);
        return false;
    }
}
{% endhighlight %}


하지만 이 코드로는 위에서 말했던 것과 같이 Intent:// scheme 를 받아주지 못 한다.


그래서 이 문제를 해결하려면 아래와 같은 코드로 해결할 수 있다.<br>
(적어도 테스트엔 이상이 없었다)

{% highlight java %}
private class MyWebViewClient extends WebViewClient {
 
    @Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        if (url != null && url.startsWith("intent://")) {
            try {
                Intent intent = Intent.parseUri(url, Intent.URI_INTENT_SCHEME);
                Intent existPackage = getPackageManager().getLaunchIntentForPackage(intent.getPackage());
                if (existPackage != null) {
                    startActivity(intent);
                } else {
                    Intent marketIntent = new Intent(Intent.ACTION_VIEW);
                    marketIntent.setData(Uri.parse("market://details?id="+intent.getPackage()));
                    startActivity(marketIntent);
                }
                return true;
            }catch (Exception e) {
                e.printStackTrace();
            }
        } else if (url != null && url.startsWith("market://")) {
            try {
                Intent intent = Intent.parseUri(url, Intent.URI_INTENT_SCHEME);
                if (intent != null) {
                    startActivity(intent);
                }
                return true;
            } catch (URISyntaxException e) {
                e.printStackTrace();
            }
        }
        view.loadUrl(url);
        return false;
    }
}
{% endhighlight %}

---

**참고 : 보통의 Intent:// Scheme 작성 방법**
1. [네이버 개발자 센터][url-scheme1]
2. [Google Chrome 공식문서][url-scheme2]


[url-scheme1]: http://developer.naver.com/wiki/pages/UrlScheme 
[url-scheme2]: https://developer.chrome.com/multidevice/android/intents