# Chapter7-object-oriented-2


# 프록시 패턴

주로 프록시 패턴은 RealSubject 가 원격 시스템에서 돌아가거나, 그 객체의 생성 비용이 많이 들어 실제 사용 시점에 객체를 생성하거나, 실제 객체에 접근을 제한 및 제어를 해야 할 때 등 의 경우에 사용 됩니다.
예를들어, 화면으 이미지 크기가 너무 클 경우에는 소수의 이미지만 로딩해야 하는 경우가 있기 때문에 프록시 패턴 을 이용해서 소수의 이미지만을 보여준다

![image](https://user-images.githubusercontent.com/83396157/158041097-8329baa0-545e-43b6-a2b3-8f2b53bcd928.png)


### 프록시패턴 장점

- 사이즈가 큰 객체가 로딩되기 전에도 프록시를 통해 참조를 할 수 있다.
- 실제 객체의 public, protected 메소드를 숨기고 인터페이스를 통해 노출시킬 수 있다.
- 로컬에 있지 않고 떨어져있는 객체를 사용할 수 있다.
- 원래 객체에 접근에 대해 사전처리를 할 수 있다.

### 프록시패턴 단점

- 객체를 생성할 때 한 단계를 거치게 되므로, 빈번한 객체 생성이 필요한 경우 성능이 저하될 수 있다.
- 프록시 내부에서 객체 생성을 위해 스레드가 생성, 동기화가 구현되어야 하는 경우 성능이 저하될 수 있다.
- 로직이 난해해져 가독성이 떨어질 수 있다.


# 어뎁터 패턴

한 클래스의 인터페이스를 클라이언트에서 사용하고자하는 다른 인터페이스로 변환한다.어댑터를 이용하면 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을 연결해서 쓸 수 있다.

### 객체 어뎁터
![image](https://user-images.githubusercontent.com/83396157/158041205-df26f9a1-269a-42f3-ac7d-0f73359ad622.png)

### 상속 어뎁터
![image](https://user-images.githubusercontent.com/83396157/158041225-09502de3-31c4-4536-af5f-7db5b314876f.png)


```Kotlin

fun main() {
    var hairdryer = HairDryer()
    var convertedSocket = Adapter220vTo110v(hairdryer)
    convertedSocket.powerOn()
}

fun connect(electronic110v: Electronic110v) {
    electronic110v.powerOn()
}


interface Electronic110v {
    fun powerOn()
}

interface Electronic220v {
    fun connect()
}

class HairDryer : Electronic220v {
    override fun connect() {
        println("220v connected")
    }
}

class Adapter220vTo110v(var electronic220v: Electronic220v) : Electronic110v {
    override fun powerOn() {
        electronic220v.connect()
    }
}

```

### 장점

- Adapter 패턴의 가장 큰 장점을 기존 코드를 변경하지 않아도 된다.
- 기존 코드를 변경하지 않기 때문에 클래스 재활용성을 증가시킬 수 있다.

### 단점

- 구성요소를 위해 클래스를 증가시켜야 하기 때문에 복잡도가 증가할 수 있다.
- 클래스 Adapter 의 경우 상속을 사용하기 때문에 유연하지 못하다.
- 반명 객체 Adapter 의 경우는 대부분의 코드를 다시 작성해야 하기 때문에 효율적이지 못하다.
