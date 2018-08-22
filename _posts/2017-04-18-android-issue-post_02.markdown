---
layout: post
title: "[Android] 누가 버전에서 file:// scheme 을 사용할수 없는 이슈" 
date:   2017-08-29 15:30:00 +0900
categories: android
comments: true
---

안드로이드 N(누가) 버전이 나왔다.<br>
여러가지 기능이 추가되었지만 마쉬멜로우 때도 그렇고 이번 누가버전에서도 권한에 대해서 꽤나 strict 모드로 전환되고 있는 추세인 것 같다.

보통 앱 내에 카메라 기능을 붙이고 이를 file 로 가져올 때 file:// scheme 값을 이용하여 가져왔지만,<br>
누가버전에서부터 권한 문제로 이 기능을 더 이상 사용하지 못하고 대신에 새로운 interface 를 지원한다.<br>
그래서 버전에 따라 분기를 둔 컨트롤러를 추가하여 해결하였다.

#### 먼저 콜백 인터페이스는 간단히 RX 를 사용하여 만들었다.

{% highlight java %}
import rx.functions.Action1;

public interface Callback<F> extends Action1<F> {
}
{% endhighlight %}


#### **아래는 컨트롤러코드**
{% highlight kotlin %}
import android.app.Activity
import android.content.Intent
import android.net.Uri
import android.os.Build
import android.os.Environment
import android.provider.MediaStore
import android.support.v4.app.ActivityCompat.startActivityForResult
import android.support.v4.content.FileProvider
import java.io.File
import java.lang.ref.WeakReference

class FileProviderDispatcher {

    private var activityWeakReference: WeakReference<Activity>
    private var uri: Uri? = null

    constructor(activity: Activity) {
        this.activityWeakReference = WeakReference(activity)
    }

    fun setUri(callback: Callback<String>) {
        val file: File? = makeFile() ?: return

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
            activityWeakReference.get()?.let { context ->
                uri = FileProvider.getUriForFile(context, "${context.packageName}.provider", file!!)
                callback.call(file!!.absolutePath)
            }
        } else {
            uri = Uri.fromFile(file)
            callback.call(uri.toString())
        }
    }

    fun startImageCapture() {
        Intent().run {
            activityWeakReference.get()?.let { context ->
                action = MediaStore.ACTION_IMAGE_CAPTURE
                addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION)
                addFlags(Intent.FLAG_GRANT_WRITE_URI_PERMISSION)
                putExtra(MediaStore.EXTRA_OUTPUT, uri)
                startActivityForResult(context, this, Constants.PICK_FROM_CAMERA, null)
            }
        }
    }

    private fun makeFile() =
            File.createTempFile("prefix", "suffix.jpg", Environment.getExternalStorageDirectory())

}
{% endhighlight %}


#### Activity 에서 이를 사용 시 아래와 같이 사용한다.

{% highlight kotlin %}
private fun takePicture() {
    val fileProviderDispatcher = FileProviderDispatcher(this)
    fileProviderDispatcher.setUri(Callback { path -> this.img_url = path })
    fileProviderDispatcher.startImageCapture()
}
{% endhighlight %}