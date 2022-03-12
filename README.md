## 프록시 패턴

- 프록시 패턴은 실제 객체를 대신하는 프록시 객체를 사용해 실제 객체의 생성이나 접근 등을 제어할 수 있도록 하는 디자인 패턴이다.

-  프록시는 대리인이라는 의미로 대신 처리해줌을 의미한다.

- 리소스가 큰 객체를 모든 것을 생성하기 보다는 필요할 때 생성하는데 사용될 수 있다.

- 장점

  사이즈가 큰 객체(이미지)가 로딩되기 전에 프록시를 통해 참조할 수 있다.

  실제 객체의 메서드를 숨기고 인터페이스를 통해 노출시킬 수 있다.

- 단점

  객체가 많아지고 로직이 복잡해지므로 가독성이 떨어질 수가 있다.

```kotlin
class ProxyImage(val path: String) : Image {
    val image: RealImage? = null
    
    override fun draw() {
	    image?.let {
			image = RealImage(path) // 최초 접근 시 객체 생성
	    }
        image.draw() // RealImage 객체에 위임
    }
}

fun onScroll(start: Int, end: Int) {
    //스크롤 시, 화면에 표시되는 이미지를 표시
    for (i in start..end) {
        val image = images.get(i)
        image.draw()
    }
}
```

---


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

---

## 옵저버 패턴

- 한 객체의 상태 변화를 정해지지 않은 여러 다른 객체에게 통지하고 싶을 때 사용되는 디자인 패턴이다.

> 옵저버 패턴을 이루는 객체 구성

- subject(주제 객체)

  옵저버 목록을 관리하고, 옵저버를 등록하고 제거할 수 있는 메서드를 제공한다.

  상태의 변경이 발생하면 등록된 옵저버에 변경 내역을 알려준다.

- observer(옵저버 객체)

  주체 객체에서 상태가 변경되었따는 알림을 받으면 특정 기능을 수행한다.

> 옵저버 객체에게 상태 전달 방법

- subject에서 옵저버 객체에게 호출 시 값을 전달해준다. 옵저버는 파라미터로 받아 전달받은 상태 값을 사용한다.

- 옵저버 객체의 메서드를 호출할 때 전달한 객체만으로 옵저버의 기능을 구현할수 없을 수도 있다. 그 경우는 옵저버 객체에서 콘크리트 주제 객체에 직접 접근하는 방법을 이용한다.

```kotlin
class SpecialStatusObserver(private val statusChecker: StatusChecker): StatusObserver {
	fun onAbnormalStatus(status: Status) {
		if(status.isFault() && statusChecker.isContinuousFault()) ...
	}
}
```

> 옵저버에서 주제 객체 구분

- 하나의 옵저버 객체를 여러 주제 객체로 등록할 수 있다.

- 안드로이드

  Activity가 View.OnClickListener를 상속해 옵저버 객체가 된다.

  버튼 객체(주제 객체)가 setOnClickerLister(add) 를 통해 옵저버 객체로 등록한다.

  Activity 내 onClick(v: View) 메서드를 오버라이드하여 주제 객체를 통지할 때, 특정 기능을 설정할 수 있다.

- 하나의 주제 객체에 다양한 옵저버 객체가 존재할 수 있다.

  다양한 옵저버 객체를 관리 및 통지하는 기능을 가진 추상 클래스를 사용한다.

> 옵저버 패턴 구현의 고려 사항

- 주제 객체의 통지 기능 실행 주체
- 옵저버 인터페이스의 분리
- 통지 싯점에서의 주제 객체 상태
- 옵저버 객체의 실행 제약 조건

---

## 미디에이터 패턴

- 각 객체들이 직접 메시지를 주고받는 대신, 중간에 중계 역할을 수행하는 미디에이터 객체를 두고 미디에이터를 통해서 각 객체들이 간접적으로 메시지를 주고받도록 하는 디자인 패턴이다.

- 다른 협업 객체들도 요청들을 미디어에이터에 보내고, 미디에이터는 그 요청에 맞는 객체를 실행하면서 진행한다.

- 장점

  개별 협업 클래스들을 수정이나 확장 및 재사용하기가 편해진다.

- 협업 클래스의 개수가 많아지면 미디에이터의 코드에 다 몰리게 되면서 유지보수가 어렵다.
