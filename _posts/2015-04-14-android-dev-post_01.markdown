---
layout: post
title: "Android Dialog 영역 외에 터치 시 이벤트 동작" 
date:   2014-04-14 20:23:00 +0900
categories: android
comments: true
---

보통 실무를 경험하다보면 간단한 로직을 단순하게 생각하고 개발팀에 전달되는 경향이 많다.<br>
예를 들면, 키보드 올라오는 영역 이외에 영역을 터치하면 키보드가 닫히는 기능을 구현할 시에 간단한 방법으로,<br>
키보드 외의 window 영역에 click listener 를 잡을 수 있지만 이는 굉장한 노가다가 필요한 작업이다.<br>
(내가 아는 한에서는 키보드가 열렸을 때 상황을 체크하여 모든 뷰에다가 click 시 키보드 윈도우가 닫히는 이벤트를 걸어야 한다.)

보통 outside touch 를 풀어주고 touch event 를 받게끔 많이 나와있었는데,<br>
본인의 경우 다이아로그 영역을 wrap_contents 로 하고 내부의 아이템만으로 영역을 제한을 두었었다.<br>
그 경우에는 아래와 같은 코드로 외부 터치 영역을 잡을 수 있었다.

{% highlight java %}
public class MyDialog extends Dialog {
    
    public MyDialog(Context context) {
        super(context, android.R.style.Theme_Translucent_NoTitleBar);
    }
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
 
        WindowManager.LayoutParams wlp = new WindowManager.LayoutParams();
        wlp.flags = WindowManager.LayoutParams.FLAG_DIM_BEHIND;
        wlp.dimAmount = 0.8f;
        if (Build.VERSION.SDK_INT > Build.VERSION_CODES.GINGERBREAD_MR1) {
            wlp.width = WindowManager.LayoutParams.WRAP_CONTENT;
            wlp.height = WindowManager.LayoutParams.WRAP_CONTENT;
        } else {
            wlp.width = WindowManager.LayoutParams.MATCH_PARENT;
            wlp.height = WindowManager.LayoutParams.MATCH_PARENT;
        }
        wlp.gravity = Gravity.TOP | Gravity.RIGHT;
        getWindow().setAttributes(wlp);
    }
 
    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        Rect dialogBounds = new Rect();
        getWindow().getDecorView().getHitRect(dialogBounds);
 
        if (!dialogBounds.contains((int) ev.getX(), (int) ev.getY())) {
            this.dismiss();
        }
        return super.dispatchTouchEvent(ev);
    }
}
{% endhighlight %}
