## 어댑터 패턴

- 어댑터는 실생활에서 사용되는 언어이다. 충전기, 이어폰 잭 등이 존재한다.

  디자인 패턴의 Adapter도 비슷하게 사용된다. 변환시켜줌으로써 연결이 이루어지기 때문이다.

- 어댑터 패턴은 **클래스의 인터페이스**를 사용자가 **원하는 다른 인터페이스로 변환**시켜주는 패턴이다.

  > 호환성이 없이 인터페이스를 사용하는 클래스들이 상호 호환이 가능케해준다.

- target, adaptee, adapter, client을 주요 키워드로 바라보면된다.

  target: 클라이언트가 사용하길 원하는 인터페이스

  adaptee: 클라이언트가 갖고 있는 인터페이스

  adapter: target 인터페이스를 구현하는 클래스로, adaptee의 함수를 사용한다.

  client: target 인터페이스를 사용하는 주체

```kotlin
interface Target {
	fun operation()
}

interface Adaptee {
	fun specificOperation()
}

class SampleAdaptee: Adaptee {
	override fun specificOperation() {
		println("This is Adaptee")
	}
}

class SimpleAdapter(val adaptee: Adaptee): Target {
	override fun operation() {
		adaptee.specificOperation()
	}
}

class Client {
	val adapter: Target = SimpleAdapter(SampleAdaptee())
	
	fun run() {
		adapter.operation()
	}
}

fun main() {
	Client().run()
}
```

>  Adapter 패턴의 2가지 방식

- 조립을 이용하는 방식과 상속을 통한 방식이 존재한다.

- 상속을 통한 방식은 Adaptee를 상속하여 호환되게 사용할 수 있다.

  장점으로 어댑터 전체를 다시 구현할 필요가 없다.

  단점으로 상속을 활용하기에 조립보다 유연하지 못한다.

- 조립을 이용하는 방식은 Adaptee 구현체를 넘겨 받아 호환되게 사용할 수 있다.

  장점으로 구성하기 때문에 더욱 유연한다.

  단점으로 대부분 코드를 구현해야하기 때문에 비효율적일 수 가 있다.


> 어댑터 패턴을 이용하는 이유

- 변경할 수 없는 내부 구현, 라이브러리 등에 추가적인 기능을 만들고 싶을 때 유연하게 활용이 가능하다.

- 기존에 사용하던 라이브러리의 동작을 바꿔야 할 때, 라이브러리의 구현을 바꾸는 것보다 새롭게 추가하면 재활용성이 뛰어나게 좋다고 판단한다.

- 어댑터 패턴을 구현하기 위해 필요한 구성요소들로 인해 클래스 자체가 많아지기에 복잡도가 증가할 순 있다.

> 참고 사항
- [라이브러리에 추가하는 기능](https://fsd-jinss.tistory.com/11)

- [참고자료](https://kscory.com/dev/design-pattern/adapter)
