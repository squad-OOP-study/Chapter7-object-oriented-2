# chap7 디자인패턴2

2022.3.13

> 목차
- Proxy 패턴
- Adapter 패턴
- Observer 패턴
- ㅇㄹ

## 프록시 패턴 

> 종류 
- 가상 프록시(virtual proxy)
  - 필요한 순간에 실제 객체를 생성해 주는 프록시
- 보호 프록시(protection proxy)
  - 실제 객체에 대한 접근을 제어하는 프록시
- 원격 프록시(remote proxy)
  - 다른 프로세스에 존재하는 객체에 접근할 때 사용하는 프록시. 
  - 내부적으로 IPC(Inter process communication), TCP 통신을 이용해서 다른 프로세스의 객체를 실행. 
  - 예)자바의 RMI(Remote Method Invocation)


> 사용 예시
- 제품 목록 구성할 때 이미지가 실제로 화면에 보여질 때 이미지 데이터를 로딩
  - 불필요한 이미지 로딩에 따른 메모리 낭비와 이미지 로딩에 따른 화면 출력 대기 시간이 길어지는 문제 해결

> 구현 방법
1. 프록시 패턴 미적용
   - 필요시점에 Image클래스를 이용해서 이미지를 로딩하는 DynamicLoadingImage 클래스를 추가
   - 목록을 보여주는 클래스에서 Image클래스 대신 DynamicLoadingImage를 사용
   - 문제점 
     - 이미지 로딩 방식 변경시 ListUI 코드 변경.
     - 이미지 갯수에 따라 ListUI에서 Image관련 두 클래스 구분하는 조건문 갖게 된다.
   - <img src="../그림7-16.jpg">

2. 프록시 패턴 적용
   - ListUI변경 없이 이미지 로딩 방식 교체 
   - 프록시 객체를 사용해 실제 객체의 생성이나 접근을 제어할 수 있는 패턴
   - ListUI클래스는 Image 타입을 사용하기 때문에 실제타입이 Proxy타입인지 아닌지 모른다.
   - <img src="../그림7-17.jpg">
  
    ```java
    public class ProxyImage implements Image {

        private String path;
        private RealImage image;

        public ProxyImage(String path) {
            this.path = path;
        }

        public void draw() {
            if (image == null) {
                image = new RealImage(path); //최초 접근 시 객체 생성
            }
            
            image.draw(); //RealImage 객체에 위임
        }
    }
    ```
    - ListUI 클래스
    ```java        
    public class ListUI {

        private List<Image> images;

        public ListUI(List<Image> images) {
            this.images = images;
        }
        
        public void onScroll(int start, int end) {
            //스크롤 시, 화면에 표시되는 이미지를 표시
            for (int i = start; i < end; i++) {
                Image image = images.get(i);
                image.draw();		
            }
        }
        ...
    }
    ```

    - 동작과정
    - <img src="그림7-18.jpg">

> 프록시 패턴 적용할 때 고려할 점
- 실제 객체를 누가 생성할 것인지 고려해야 한다.
1. 가상 프록시 
    - 필요한 순간에 실제 객체를 생성. 
    - 프록시에서 실제 생성할 객체의 타입을 사용
2. 보호 프록시
    - 보호 프록시 객체 생성할 때 실제 객체를 전달하면 되므로 실제 객체의 타입을 알 필요 없이 추상 타입을 사용
3. 상속을 사용해서 프록시 구현
    - 예시) 특정 기능은 관리자만 실행할 수 있어야 하는 경우 보호프록시 사용
    - 보호 프록시를 상위 클래스의 메서드를 재정의 하는 방법으로 구현
    ```java
    public class ProtectedService extends Service {

        @Override
        public void someMethod() {
            if(!CUrrentContext.getAuth().isAdmin())
                throw new AccessDeniedException();
            
            super.someMethod();
        }
    }
    ```
> 데코레이터 패턴과 차이
- 위임 기반의 프록시 패턴은 데코레이터 패턴과 유사
- 의도에서 차이가 있다. 클래스의 이름 부여할 때 의도에 맞는 단어 선택.
- 프록시 패턴 : 실제 객체에 대한 접근을 제어하는 데 초점
- 데코레이터 패턴 : 기존 객체의 기능을 확장하는데 초점

<br><br>

## 어댑터 패턴

- 클라이언트가 요구하는 인터페이스와 재사용하려는 모듈의 인터페이스가 일치하지 않을 때 사용할 수 있는 패턴
  - 주로 외부 제공 모듈을 사용할 때 기존 인터페이스와 맞지 않은 경우 사용할 수 있다.
  - 기존
    - <img src="그림7-20.jpg">
  - 어댑터 패턴 적용
    - <img src="그림7-21.jpg">
  - SearchService 인터페이스를 구현하고 있으므로 WebSearchRequestHandler 클래스 코드 수정 없이 DB기반 통합 검색에서 TolrClient를 이용한 통합 검색으로 구현을 변경할 수 있다. 
    ```java
    public class SearchServiceTolrAdapter implements SearchService {
        private TolrClient tolrClent = new TolrClient();

        public SearchResult search(String keyword) {
            //keyword(String)를 tolrClient가 요구하는 형식으로 변환
            TolrQuery tolrQuery = new TolrQuery(keyword);
            //TolrCient 기능 실행
            QueryResponse response = tolrClent.query(tolrQuery);
            //TolrClient의 결과를 SearchResult로 변환 (이 부분이 까다로울 수 있을 듯?)
            searchResult result = convertToResult(response);
            return result;
        }

        private searchResult convertToResult(QueryResponse response) {
            List<TolrDocument> tolrDocs = response.getDocumentList().getDocuments();
            List<SearchDocument> docs = new ArrayList<>();
            for (TolrDocument tolrDoc : tolrDocs) {
                docs.add(new SearchDocument(tolrDoc.getId(), ...));
            }
            return new SearchResult(docs);
        }
    }
    ```

> 어댑터 패턴 적용 예
- SLF4J 로깅 API 
  - 단일 로깅 API를 사용하면서 자바 로깅, log4j, LogBack등의 로깅 프레임워크를 선택적으로 사용할 수 있도록 해준다. 
  - SLF4J가 제공하는 인터페이스와 각 로깅 프레임워크를 맞춰 주기 위해 어댑터를 사용하고 있다.




     


