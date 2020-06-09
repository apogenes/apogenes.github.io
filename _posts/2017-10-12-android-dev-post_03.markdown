---
layout: post
title: "[Android] enum 값을 찾는 method 만들어보기" 
date:   2017-10-12 12:00:00 +0900
categories: android
comments: true
---

자바를 사용할 때 enum 을 자주 사용하였다.

그 중 enum value 값들 중 특정 값을 찾는 find 코드를 필요해서 java 에서 만들어 쓰다가 이번에 kotlin 에서도 필요하게 되어 비슷하게 만들어 쓰고 있었다.

그래서 자바랑 코틀린 두 개를 비교해보자.

#### Java
{% highlight java %}
public enum AnimalType {
    DOG, CAT, COW, PIG;

    public static AnimalType find(String type) {
        for (AnimalType animalType : AnimalType.values()) {
            if (animalType.toString().toLowerCase().equals(type.toLowerCase())) {
                return animalType;
            }
        }
        return null;
    }
}
{% endhighlight %}  

#### Kotlin
{% highlight java %}
enum class AnimalType {
    DOG, CAT, COW, PIG

    companion object {
    fun find(findValue: String): AnimalType? = AnimalType.values().find { 
            it.name.toLowerCase() == findValue.toLowerCase()
        }
    }
}
{% endhighlight %}
