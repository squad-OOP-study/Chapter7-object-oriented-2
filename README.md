# 객체 지향과 디자인 패턴

## 프록시 패턴

- 프록시 패턴은 실제 객체를 대신하는 프록시 객체를 사용해서 실제 객체의 생성이나 접근 등을 제어할 수 있도록 하는 디자인 패턴이다.

### 언제 사용할 수 있을까?

- 로컬에 있는 많은 이미지를 화면에 표현해주어야 할 때 하나의 화면에 표현할 수 있는 개수가 정해져 있음에도 불구하고 전부 다 이미지를 갖는 객체를 생성하게 된다면 이미지를 불러와 저장하는데 시간이 오래 걸리게 된다.
- 이럴 때 화면에 한 번에 표현할 수 있는 객체의 수만큼은 이미지를 불러오고 나머지는 이미지의 위치만 저장함으로써 필요할 때 이미지를 불러오는 방식으로 사용할 수 있다.

### 구현 방법

- 이미지를 표현하는 객체에서 인터페이스를 통해 이미지 객체와 통신한다.

    ![프록시 패턴](https://user-images.githubusercontent.com/29175138/158019406-ecfd7602-1d56-456b-a521-f6106662255d.png)

#### 인터페이스 구현
```kotlin
package proxy_pattern

interface Image {
    fun draw()
}
```

#### 실제 이미지 객체 구현
```kotlin
package proxy_pattern

class RealImage(private val path: String) : Image {
    override fun draw() {
        loadFromLocal()
    }

    private fun loadFromLocal() {
        println("Loading From Local ($path)")
    }
}
```

#### 프록시 객체 구현
```kotlin
package proxy_pattern

class ProxyImage(private val path: String) : Image {
    private var realImage: RealImage? = null

    override fun draw() {
        if (realImage == null) {
            println("proxy")
            realImage = RealImage(path)
        }

        realImage?.draw()
    }
}
```

#### 이미지 리스트 구현
```kotlin
package proxy_pattern

class ImageList {
    private val images = mutableListOf<Image>()

    fun add(path: String) {
        if (images.size > 4) {
            images.add(ProxyImage(path))
        } else {
            images.add(RealImage(path))
        }
    }

    fun getAllImages(): List<Image> {
        return images.toList()
    }
}
```

#### 메인 구현
```kotlin
package proxy_pattern

fun main() {
    val imageList = ImageList()
    imageList.add("test1.png")
    imageList.add("test2.png")
    imageList.add("test3.png")
    imageList.add("test4.png")
    imageList.add("test5.png")
    imageList.add("test6.png")
    imageList.add("test7.png")
    imageList.add("test8.png")
    imageList.add("test9.png")
    imageList.add("test10.png")

    for (image in imageList.getAllImages()) {
        image.draw()
    }
}
```

#### 결과
```text
Loading From Local (test1.png)
Loading From Local (test2.png)
Loading From Local (test3.png)
Loading From Local (test4.png)
Loading From Local (test5.png)
proxy
Loading From Local (test6.png)
proxy
Loading From Local (test7.png)
proxy
Loading From Local (test8.png)
proxy
Loading From Local (test9.png)
proxy
Loading From Local (test10.png)
```

---

## 어댑터 패턴

- 서로 관계없는 인터페이스를 함께 사용할 수 있도록 하는 것으로, 호환성이 없는 인터페이스 때문에 동작할 수 없는 클래스를 동작할 수 있도록 해주는 디자인 패턴이다.

- 즉, 인터페이스에 적용할 클래스들을 선택할 수 있도록 도와줌으로써 다른 클래스를 선택하더라도 동일하게 동작할 수 있게 된다.

- 어댑터 패턴은 개방/폐쇄 원칙(OCP)를 따른다.

![image](https://user-images.githubusercontent.com/29175138/158022242-5a198a49-0498-4123-bdfc-a3fc2bc53a5e.png)

### 언제 사용할 수 있을까?

- 어떤 라이브러리를 적용하고 싶은데 반환값이 원하는 값이 아니거나 매개변수가 다를때 등 여러 상황에서 어댑터 패턴을 사용할 수 있다.

- 어댑터 패턴이 적용된 예로 slf4j 로깅 API가 있는데 slf4j는 어댑터 패턴을 사용함으로써 자바의 Logging, log4j, LogBack 등을 선택해서 사용할 수 있도록 한다.

### 구현 방법

- 구현 방법에는 두 가지가 존재한다. 
    1. 객체를 위임한 어댑터 구현
    2. 클래스 상속 방식의 어댑터 구현

#### 객체를 위임한 어댑터 구현

<details>
<summary>로깅 인터페이스 구현</summary>

<div markdown="1">

```kotlin
interface Logger {
    fun log(message: String): String
}
```

</div>
</details>

<details>
<summary>Logging 클래스 구현</summary>

<div markdown="1">

```kotlin
class Logging {
    fun print(message: String): String {
        return "[Logging] $message"
    }
}
```

</div>
</details>

<details>
<summary>Looooogging 클래스 구현</summary>

<div markdown="1">

```kotlin
import java.time.LocalDateTime

data class Message(val time: String, val message: String)

class Looooogging {
    fun printLog(message: String): Message {
        return Message(LocalDateTime.now().toString(), message)
    }
}
```

</div>
</details>

<details>
<summary>LoggingAdapter 구현</summary>

<div markdown="1">

```kotlin
class LoggingAdapter : Logger {
    private val logging = Logging()
    override fun log(message: String): String {
        return logging.print(message)
    }
}
```

</div>
</details>

<details>
<summary>LoooooggingAdapter 구현</summary>

<div markdown="1">

```kotlin
class LoooooggingAdapter : Logger {
    private val logging = Looooogging()
    override fun log(message: String): String {
        val logData = logging.printLog(message)
        return "[${logData.time}] : ${logData.message}"
    }
}
```

</div>
</details>

<details>
<summary>메인 클래스 구현</summary>

<div markdown="1">

```kotlin
fun main() {
    var logging: Logger = LoggingAdapter()
    println(logging.log("this is logging"))

    logging = LoooooggingAdapter()
    println(logging.log("this is Looooogging"))
}
```

</div>
</details>

<details>
<summary>결과</summary>

<div markdown="1">

```text
[Logging] this is logging
[2022-03-12T23:58:05.810366] : this is Looooogging
```

</div>
</details>

- Logging 클래스는 print 메소드명을 사용하고 리턴값이 String이며 Looooogging 클래스는 printLog 메소드명을 사용하고 리턴값이 Message 데이터 클래스이다.

- 이런 경우에 Logger 인터페이스를 이용해 서로 다른 클래스를 동일한 구조로 사용할 수 있게끔 어댑터 패턴을 이용해 구현할 수 있다.

#### 클래스 상속 방식의 어댑터 구현

- 객체를 위임한 어댑터 구현과 비슷한 구조로 LoggingAdapter만 클래스 상속 방식의 어댑터로 구현하면 아래와 같다.

```kotlin
class LoggingAdapter : Logger, Logging() {
    override fun log(message: String): String {
        return super.print(message)
    }
}
```

- Logging 클래스를 상속받고 super 키워드를 이용해 접근하면 된다.

---

## 옵저버 패턴

- 한 객체의 상태 변화를 정해지지 않은 다른 객체들에 통지할 때 옵저버 패턴이 사용된다.

- 관찰대상을 주제(Subject) 라고 부르며 관찰자를 옵저버(Observer) 라고 부른다.

- 인터페이스나 추상메소드를 통해 관찰대상과 관찰자가 통신한다.

### 관찰대상과 관찰자가 하는 일은 무엇일까?

- **관찰대상 (Subject)**

    1. 옵저버의 목록을 관리하고, 옵저버를 등록 및 제거할 수 있다.
        - 여러개의 옵저버가 등록될 수 있다.
    2. 상태의 변경이나 이벤트가 발생하면 등록된 옵저버들에게 변경 내용이나 이벤트 발생을 알린다.

- **옵저버 (Observer)**

    1. 관찰대상에서 받은 정보를 토대로 특정 행위를 수행한다.

### 언제 사용할 수 있을까?

- 게임의 공지사항처럼 어떤 상태변화나 이벤트가 발생했을 때 등록된 옵저버들에게 모두 메시지를 전달할 때 사용될 수 있다.

## 장점

- 런타임 시에 객체를 구독하거나 취소할 수 있기 때문에 유연하다.
- 개방/폐쇄 원칙(OCP)을 지키므로 관찰대상의 코드 변경없이 새로운 옵저버를 등록할 수 있다.
- 관찰대상과 관찰자 객체들 사이의 결합도(의존성)를 낮춰준다.
    - 인터페이스로 통신하기 때문

## 단점

- 제때 옵저버를 해제해주지 않으면 메모리 누수가 발생할 수 있다.
    - 구독이 된 상태에서 해제하지 않고 옵저버가 종료될 경우

- 비동기 상태에서 옵저버들이 원하는 순서대로 상태변화를 얻어올 수 없다.
    - 원하는 순서대로 옵저버를 등록할 수는 있지만 비동기(예를 들어 쓰레드 등)에서는 언제 등록이 되고 해제가 될지 모르기 때문

### 예시

<details>
<summary>옵저버 인터페이스</summary>

<div markdown="1">

```kotlin
package observer

interface Observer {
    fun updateNotice(message: String)
}
```

</div>
</details>

<details>
<summary>옵저버를 상속받는 유저 클래스 (User)</summary>

<div markdown="1">

```kotlin
package observer

class User(private val username: String) : Observer {
    override fun updateNotice(message: String) {
        println("$username : $message")
    }
}
```

</div>
</details>

<details>
<summary>관찰대상 클래스 (Notice)</summary>

<div markdown="1">

```kotlin
package observer

class Notice {
    private val observerList = mutableListOf<Observer>()

    fun registerObserver(observer: Observer) {
        observerList.add(observer)
    }

    fun unregisterObserver(observer: Observer) {
        observerList.remove(observer)
    }

    fun noticeAllUser(message: String) {
        for (observer in observerList) {
            observer.updateNotice(message)
        }
    }
}
```

</div>
</details>

<details>
<summary>메인 클래스</summary>

<div markdown="1">

```kotlin
package observer

fun main() {
    val notice = Notice()
    val user1 = User("스폰지밥")
    val user2 = User("뚱이")
    val user3 = User("징징이")
    val user4 = User("핑핑이")

    notice.registerObserver(user1)
    notice.registerObserver(user2)
    notice.registerObserver(user3)
    notice.registerObserver(user4)

    notice.noticeAllUser("게살버거 먹고싶다")
    notice.unregisterObserver(user3)
    println()
    notice.noticeAllUser("배부르다")
}
```

</div>
</details>

<details>
<summary>결과</summary>

<div markdown="1">

```text
스폰지밥 : 게살버거 먹고싶다
뚱이 : 게살버거 먹고싶다
징징이 : 게살버거 먹고싶다
핑핑이 : 게살버거 먹고싶다

스폰지밥 : 배부르다
뚱이 : 배부르다
핑핑이 : 배부르다
```

</div>
</details>

---
