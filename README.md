# 디자인패턴- part2
## 프록시 패턴 
### 프록시 패턴이란?
![image](https://user-images.githubusercontent.com/58967292/158029911-6882207d-7586-4f31-8755-ec0e6c35086d.png)

* 프록시는 대리인이라는 뜻으로, 무엇인가를 대신 처리하는 의미, 일종의 비서
* 사장님한테 사소한 질문을 하기보다는 비서한테 먼저 물어보는 개념
* 어떤 객체를 사용하고자 할때, 객체를 직접적으로 참조 하는것이 아니라, 해당 객체를 대행(대리, proxy)하는 객체를 통해 대상객체에 접근하는 방식을 사용
* 해당 객체가 메모리에 존재하지 않아도 기본적인 정보를 참조하거나 설정할 수 있고 또한 실제 객체의 기능이 반드시 필요한 시점까지 객체의 생성을 미룰 수 있습니다.

### 가상프록시
* 꼭 필요로 하는 시점까지 객체의 생성을 연기하고, 해당 객체가 생성된 것처럼 동작하도록 만들고 싶을때 사용하는 패턴입니다.
* 프록시 클래스에서 자잘한 작업들을 처리하고 리소스가 많이 요구되는 작업들이 필요할 때에만 주체 클래스를 사용하도록 구현하며 
* 해상도가 아주 높은 이미지를 처리해야 하는 경우 작업을 분산하는것을 예로 들 수 있겠습니다.

### 원격프록시
* 원격 객체에 대한 접근을 제어 로컬 환경에 존재하며, 원격객체에 대한 대변자 역할을 하는 객체 서로다른 주소 공간에 있는 객체에 대해 마치 같은 주소 공간에 있는 것처럼 동작하게 만드는 패턴입니다. * * 예시로 Google Docs를 들 수 있겠습니다. 브라우저는 브라우저대로 필요한 자원을 로컬에 가지고 있고 또다른 자원은 Google 서버에 있는 형태입니다.

### 보호프록시
* 주체 클래스에 대한 접근을 제어하기 위한 경우에 객체에 대한 접근 권한을 제어하거나 객체마다 접근 권한을 달리하고 싶을때 사용하는 패턴으로 
* 프록시 클래스에서 클라이언트가 주체 클래스에 대한 접근을 허용할지 말지 결정하도록 할수가 있습니다.

### 여러 종류 프록시
* 방화벽 프록시 : 일련의 네트워크 자원에 대한 접근을 제어함으로써 주 객체를 '나쁜' 클라이언트들로부터 보호하는 역할을 합니다.
* 스마트 레퍼런스 프록시 (Smart Reference Proxy) : 주 객체가 참조될 때마다 추가 행동을 제공합니다. ex) 객체 참조에 대한 선 작업, 후 작업 등
* 캐싱 프록시 (Caching Proxy) : 비용이 많이 드는 작업의 결과를 임시로 저장 하고, 추후 여러 클라이언트에 저장된 결과를 실제 작업처리 대신 보여주고 자원을 절약하는 역할을 합니다.
* 동기화 프록시 (Synchronization Proxy) : 여러 스레드에서 주 객체에 접근하는 경우에 안전하게 작업을 처리할 수 있게 해줍니다. 주로 분산 환경에서 일련의 객체에 대한 동기화 된 접근을 제어해주는 자바 스페이스에서 쓰입니다.
* 복잡도 숨김 프록시 (Complexity Hiding Proxy) : 복잡한 클래스들의 집합에 대한 접근을 제어하고, 복잡도를 숨깁니다. 


### 프록시 패턴 장단점
프록시 패턴 장점
1. 사이즈가 큰 객체(ex : 이미지)가 로딩되기 전에도 프록시를 통해 참조를 할 수 있다.
2. 실제 객체의 public, protected 메소드들을 숨기고 인터페이스를 통해 노출시킬 수 있다. 
3. 로컬에 있지 않고 떨어져 있는 객체를 사용할 수 있다. 
4. 원래 객체의 접근에 대해서 사전처리를 할 수 있다.


프록시 패턴 단점
1. 객체를 생성할때 한단계를 거치게 되므로, 빈번한 객체 생성이 필요한 경우 성능이 저하될 수 있다.
2. 프록시 내부에서 객체 생성을 위해 스레드가 생성, 동기화가 구현되야 하는 경우 성능이 저하될 수 있다.
3. 로직이 난해해져 가독성이 떨어질 수 있다.


### 예시
```
public interface IService {
	String runSomething();
}
```
```
public class Service implements IService {
	@Override
	public String runSomething() {
		return "서비스 짱!!!";
	}
}
```
```
public class Proxy implements IService {
	IService service1;

	@Override
	public String runSomething() {
		System.out.println("호출에 대한 흐름 제어가 주목적, 반환 결과를 그대로 전달");
		service1 = new Service();
		return service1.runSomething();
	}
}
```
```
public class Main {  	
public static void main(String[] args) { 		
	//직접 호출하지 않고 프록시를 호출한다. 		
	IService proxy = new Proxy(); 		
	System.out.println(proxy.runSomething()); 	
	}
}
}
```

## 어댑터 패턴
### 어댑터 패턴이란 
![image](https://user-images.githubusercontent.com/58967292/158030507-aa4a8da4-fd86-4042-b0af-ac2a2202947b.png)
* 어댑터 패턴은 이름대로 어댑터처럼 사용되는 패턴이다. 220V 를 사용하는 한국에서 쓰던 기기들을, 어댑터를 사용하면 110V 를 쓰는곳에 가서도 그대로 쓸 수 있다.
*  이처럼, 호환성이 없는 인터페이스 때문에 함께 동작할 수 없는 클래스들이 함께 작동하도록 해주는 패턴이 어댑터 패턴이라고 할 수 있겠다. 
*  이를 위해 어댑터 역할을 하는 클래스를 새로 만들어야 한다.
* 기존에 있는 시스템에 새로운 써드파티 라이브러리가 추가된다던지
* 레거시 인터페이스를 새로운 인터페이스로 교체하는 경우에 코드의 재사용성을 높일 수 있는 방법이 어댑터 패턴을 사용하는 것이다.
* Client
  * 써드파티 라이브러리나 외부시스템을 사용하려는 쪽이다.
* Adaptee
  * 써드파티 라이브러리나 외부시스템을 의미한다.
* Target Interface
  * Adapter 가 구현(implements) 하는 인터페이스이다. 클라이언트는 Target Interface 를 통해 Adaptee 인 써드파티 라이브러리를 사용하게 된다.
* Adapter
  * Client 와 Adaptee 중간에서 호환성이 없는 둘을 연결시켜주는 역할을 담당한다. Target Interface 를 구현하며, 클라이언트는 Target Interface 를 통해 어댑터에 요청을 보낸다. 어댑터는 클라이언트의 요청을 Adaptee 가 이해할 수 있는 방법으로 전달하고, 처리는 Adaptee 에서 이루어진다.


### 클래스 어댑터 , 객체 어댑터
![image](https://user-images.githubusercontent.com/58967292/158030731-3bff0f0e-470e-4745-9b3b-d4056cfec335.png)
*  차이는 클래스 어댑터는 상속을 사용하고 객체 어댑터는 합성을 사용한다는 점
*  다이어 그램을 확인해보면 결국 Adapter 가 operation 을 사용할 때 speicificOperation 함수를 호출하는데 이것이 내부의 객체로 오는지 상속을 통해서 오는지의 차이가 있을 뿐
### 어댑터 패턴 장단점
장점
1. Adapter 패턴의 가장 큰 장점을 기존 코드를 변경하지 않아도 된다는 점입니다.
2. 기존 코드를 변경하지 않기 때문에 클래스 재활용성을 증가시킬 수 있습니다.

단점
1. 구성요소를 위해 클래스를 증가시켜야 하기 때문에 복잡도가 증가할 수 있습니다.
2. 클래스 Adapter 의 경우 상속을 사용하기 때문에 유연하지 못합니다.

### 어댑터 패턴 예시
```
public class Volt {
 
    private int volts;
	
    public Volt(int v){
        this.volts=v;
    }
 
    public int getVolts() {
        return volts;
    }
 
    public void setVolts(int volts) {
        this.volts = volts;
    }
	
}
```

```
public class Socket {
 
    public Volt getVolt(){
        return new Volt(110);
    }
}
```

```
public interface SocketAdapter {
 
    public Volt get110Volt();
		
    public Volt get220Volt();
	
    public Volt get330Volt();
}
```

```
public class SocketObjectAdapterImpl implements SocketAdapter{
 
    //Using Composition for adapter pattern
    private Socket sock = new Socket();
	
    @Override
    public Volt get110Volt() {
        return sock.getVolt();
    }
 
    @Override
    public Volt get220Volt() {
        Volt v= sock.getVolt();
        return convertVolt(v,2);
    }
 
    @Override
    public Volt get330Volt() {
        Volt v= sock.getVolt();
        return convertVolt(v,3);
    }
	
    private Volt convertVolt(Volt v, int i) {
        return new Volt(v.getVolts()*i);
    }
}
```
