---
layout: post
title: "특정 단말에서 layout view measure 이슈가 있었을 때 처리방법" 
date:   2017-08-29 15:30:00 +0900
categories: android
comments: true
---

안드로이드 개발 시에 개발자들을 괴롭히는 `특정단말`에서의 이슈가 다수 존재한다.

만약에 RecyclerView 안에 들어가는 ViewHolder 레이아웃의 구조가 우측에 이미지가 있고 왼쪽에 텍스트가 한줄만 나온다고 가정해보자.
보통의 경우 `ellipsize="end"` 속성과 함께 `maxlines="1"` 를 추가해준다.

하지만 특정 단말에서 앱이 죽는 이슈가 있었고 오류메시지를 토대로 찾아본 결과,
view 가 ellipsize 속성을 적용하면서  measure 하는 과정 중에 크래시가 발생되는 것을 확인하였다. 
구글링과 삽질의 결과 지금은 Deprecated 된 `singleLine="true"` 속성을 넣어서 정상적으로 처리가 되었다.

떄문에 앞으로는 한줄의 텍스트만 표시하려면 아래의 속성은 셋트로 써줘야 할 것 같다.

{% highlight xml %}
<TextView
	android:maxLines="1"
	android:singleLine="true"
	android:ellipsize="end"
/>
{% endhighlight %}
