---
layout: post
title: "[React Native] 커스텀 폰트 적용" 
date:   2018-09-21 15:18:00 +0900
categories: react-native
comments: true
---

먼저 React Native 로 하나의 Text 컴포넌트가 있는 화면 예시를 보자.

#### TextSample.tsx 
{% highlight JavaScript %}
import React from 'react'
import { StyleSheet, Text } from 'react-native'

export class TextSample extends React.Component {
    render() {
        return (
            <Text style={styles.title}>{ 'Sample Text' }</Text>
        )
    }
}

const styles = StyleSheet.create({
    title: {
        color: '#fff',
        fontSize: 16,
        fontFamily: 'SpoqaHanSans'
    }
})

{% endhighlight %}

여기에 fontFamily 에 적혀져 있는 커스텀 폰트를 적용하려고 하면 어떻게 해야하는지는 아래 블로그에 잘 나와있다.

https://medium.com/react-native-training/react-native-custom-fonts-ccc9aacf9e5e


하지만 나의 경우, react-native link 로만으로는 안드로이드에서 폰트가 제대로 적용이 되지 않았다.

적용하려는 폰트는 아래와 같았다.

```
SpoqaHanSansRegular.ttf
SpoqaHanSansBold.ttf
```

확인해보니 이유인 즉슨, iOS 는 폰트의 속성에 정의되어있는 이름을 인식하는데 반해, Android 의 경우 asset 폴더에 들어가있는 폰트 파일의 이름을 사용한다는 차이점이 있었다.<br>
그래서 해결한 방법은 react-native link 이후에 안드로이드에서 직접 파일명을 아래와 같이 변경해주었다.

```
SpoqaHanSansRegular.ttf -> SpoqaHanSans.ttf
SpoqaHanSansBold.ttf -> SpoqaHanSans_bold.ttf
```

참고로 spoqa 폰트와 디폴트 폰트를 비교하려면 제일 빠른 방법은 숫자 `1` 을 써서 숫자 하단에 가로줄이 있으면 spoqa 라고 한다.

