---
layout: default
title: 빈 생명주기 콜백
parent: 스프링
nav_order: 6
---

## 목차
{: .no_toc .text-beta }

1. TOC
{:toc .text-gamma}
---

# **빈 생명주기 콜백**
> + 데이터베이스 커넥션 풀이나 네트워크 소켓처럼 애플리케이션 시작 시점에 필요한 연결을 미리 해두고, 애플리케이션 종료 시점에 연결을 모두 종료하는 작업을 진행하려면 객체의 초기화와 종료 작업이 필요하다.
> + 즉, 미리 연결을 해두고 고객의 요청이 들어오면 이미 열려있는 소켓을 통해 빠르게 응답을 줄 수 있다.
> + 또한 안전하게 종료 처리를 해야한다.
> + 아래 코드를 확인해보자.
>
> ## **NetworkClient**
> ```java
> public class NetworkClient {
>     private String url;
>
>     public NetworkClient() {
>         System.out.println("생성자 호출, url = " + url);
>         connect();
>         call("초기화 연결 메시지");
>     }
>
>     public void setUrl(String url) {
>         this.url = url;
>     }
>
>     // 서비스 시작 시 호출
>     public void connect() {
>         System.out.println("connect : " + url);
>     }
>
>     public void call(String message) {
>         System.out.println("call : " + url + " message = " + message);
>     }
>
>     // 서비스 종료 시 호출
>     public void disconnect() {
>         System.out.println("close " + url);
>     }
> }
>
> ```
>
> ## **BeanLifeCycleTest**
> ```java
> public class BeanLifeCycleTest {
>
>     @Test
>     public void lifeCycleTest() {
>         ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
>         NetworkClient client = ac.getBean(NetworkClient.class);
>         ac.close();
>     }
>
>     @Configuration
>     static class LifeCycleConfig {
>
>         @Bean
>         public NetworkClient networkClient() {
>             NetworkClient networkClient = new NetworkClient();
>             networkClient.setUrl("http://hello-spring.dev");
>             return networkClient;
>         }
>     }
>
> }
>
> ```
>
> + 위 코드를 실행해보면 `url`에 `null`이 출력되는 것을 볼 수 있다.
> + 생성자 부분을 보면 url 정보 없이 connect가 호출되는 것을 확인할 수 있다.
> + 객체를 생성하는 단계에는 url이 없고, 객체를 생성한 다음에 외부에서 수정자 주입을 통해서 setUrl() 이 호출되어야 url이 존재하게 되기 때문이다.
> + 스프링 빈은 간단하게 **<font color='#0101DF'>객체 생성 -> 의존관계 주입</font>** 이라는 라이프 사이클을 가진다.
> + 스프링 빈은 객체를 생성하고 의존관계 주입이 다 끝난 다음에야  필요한 데이터를 사용할 수 있는 준비가 완료된다.
> + 따라서 **<font color='#0101DF'>초기화 작업</font>**은 의존관계 주입이 모두 완료되고 난 다음에 호출해야 한다.
> + ❗ 여기에서 **<font color='#0101DF'>초기화 작업</font>**이란 객체 안에 필요한 값이 연결이 되어있는 상태에서 외부와 연결하여 작업하기 위한 첫 단계라고 볼 수 있다.
> + 그렇다면 의존관계 주입이 완료된 시점은 어떻게 알 수 있을까?
> + 스프링은 의존관계 주입이 완료되면 스프링 빈에게 **<font color='#0101DF'>콜백 메소드</font>**를 통해서 초기화 시점을 알려주는 다양한 기능을 제공한다.
> + 또한 스프링은 스프링 컨테이너가 종료되기 직전에 **<font color='#0101DF'>소멸 콜백</font>**을 준다.
> + 따라서 안전하게 종료 작업을 진행할 수 있다.

---

# **스프링 빈의 이벤트 라이프사이클**
> + **<font color='#0101DF'>스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존관계 주입 -> 초기화 콜백 -> 사용 -> 소멸 전 콜백 -> 스프링 종료</font>**
> + `초기화 콜백` : 빈이 생성되고 빈의 의존관계 주입이 완료된 후 호출
> + `소멸 전 콜백` : 빈이 소멸되기 직전에 호출
>
>> + ## **객체의 생성과 초기화 분리**
>> + 생성자는 필수 정보(파라미터)를 받고 메모리를 할당해서 객체를 생성하는 책임을 가진다.
>> + 반면 초기화는 이렇게 생성된 값들을 활용해서 외부 커넥션을 연결하는 등의 **무거운 동작**을 수행한다.
>> + 따라서 생성자 안에는 무거운 초기화 작업을 함께하지 말고 **객체 생성과 초기화를 나누는 것이 유지보수 관점에서 좋다.**

---

# **스프링의 빈 생명주기 콜백**
> + 스프링은 크게 3가지 방식으로 빈 생명주기 콜백을 지원한다.
>   1. 인터페이스(InitializingBean, DisposableBean)
>   2. 설정 정보에 초기화 메소드, 종료 메소드 지정
>   3. @PostConstruct, @PreDestroy 어노테이션 지원
>
>> ## **1. 인터페이스(InitializingBean, DisposableBean)**
>>
>> ### **NetworkClient**
>> ```java
>> public class NetworkClient implements InitializingBean, DisposableBean {
>>     private String url;
>>
>>     public NetworkClient() {
>>         System.out.println("생성자 호출, url = " + url);
>>     }
>>
>>     public void setUrl(String url) {
>>         this.url = url;
>>     }
>>
>>     // 서비스 시작 시 호출
>>     public void connect() {
>>         System.out.println("connect : " + url);
>>     }
>>
>>     public void call(String message) {
>>         System.out.println("call : " + url + " message = " + message);
>>     }
>>
>>     // 서비스 종료 시 호출
>>     public void disconnect() {
>>         System.out.println("close " + url);
>>     }
>>
>>     // 의존관계 주입이 끝난 후에 호출
>>     @Override
>>     public void afterPropertiesSet() throws Exception {
>>         connect();
>>         call("초기화 연결 메시지");
>>     }
>>
>>     // 빈 종료 시 호출
>>     @Override
>>     public void destroy() throws Exception {
>>         disconnect();
>>     }
>> }
>>
>> ```
>>
>> ### **BeanLifeCycleTest**
>> ```java
>> public class BeanLifeCycleTest {
>> 
>>     @Test
>>     public void lifeCycleTest() {
>>         ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
>>         NetworkClient client = ac.getBean(NetworkClient.class);
>>         ac.close();
>>     }
>>
>>     @Configuration
>>     static class LifeCycleConfig {
>>
>>         @Bean
>>         public NetworkClient networkClient() {
>>             NetworkClient networkClient = new NetworkClient();
>>             networkClient.setUrl("http://hello-spring.dev");
>>             return networkClient;
>>         }
>>     }
>> }
>>
>> ```
>>
>> + 실행해보면, 처음 생성자 호출에는 url이 존재하지 않지만 그 후에 `afterPropertiesSet()` 가 호출되면서 url에 값이 들어간다. 
>> + `nitializingBean` 은 `afterPropertiesSet()` 메서드로 **<font color='#0101DF'>초기화</font>**를 지원한다.
>> + `DisposableBean` 은 `destroy()` 메서드로 **<font color='#0101DF'>소멸</font>**을 지원한다.
>> ### **❗ 초기화(InitializingBean), 소멸(DisposableBean) 인터페이스 단점**
>> + 이 인터페이스는 스프링 전용 인터페이스기에 내 코드가 스프링 전용 인터페이스에 의존해야 한다.
>> + 초기화, 소멸 메소드의 이름을 변경할 수 없다.
>> + 내가 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다.
>> + **인터페이스를 사용하는 초기화, 종료 방법은 스프링 초창기에 나온 방법들이고, 지금은 다음의 더 나은 방법들이 있어서 거의 사용하지 않는다. (2003년 12월 8일에 나왔다.)**
>> 
>> ## **2. 초기화, 종료 메소드 지정**
>>
>> ### **NetworkClient**
>> ```java
>> public class NetworkClient implements InitializingBean, DisposableBean {
>>     private String url;
>>
>>     public NetworkClient() {
>>         System.out.println("생성자 호출, url = " + url);
>>     }
>>
>>     public void setUrl(String url) {
>>         this.url = url;
>>     }
>>
>>     // 서비스 시작 시 호출
>>     public void connect() {
>>         System.out.println("connect : " + url);
>>     }
>>
>>     public void call(String message) {
>>         System.out.println("call : " + url + " message = " + message);
>>     }
>>
>>     // 서비스 종료 시 호출
>>     public void disconnect() {
>>         System.out.println("close " + url);
>>     }
>> 
>>     public void init() {
>>         System.out.println("NetworkClient.init");
>>         connect();
>>         call("초기화 연결 메시지");
>>     }
>>
>>     public void close() {
>>         System.out.println("NetworkClient.close");
>>         disconnect();
>>     }
>> }
>>
>> ``` 
>>
>> ### **BeanLifeCycleTest**
>> ```java
>> public class BeanLifeCycleTest {
>> 
>>     @Test
>>     public void lifeCycleTest() {
>>         ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
>>         NetworkClient client = ac.getBean(NetworkClient.class);
>>         ac.close();
>>     }
>>
>>     @Configuration
>>     static class LifeCycleConfig {
>>
>>         @Bean(initMethod = "init", destroyMethod = "close")
>>         public NetworkClient networkClient() {
>>             NetworkClient networkClient = new NetworkClient();
>>             networkClient.setUrl("http://hello-spring.dev");
>>             return networkClient;
>>         }
>>     }
>> }
>>
>> ```
>>
>> + 메소드 이름을 자유롭게 줄 수 있다.
>>   + ex) @Bean(initMedthod = "init", destoryMethod = "close")
>> + 스프링 빈이 스프링 코드에 의존하지 않는다.
>> + **❗ 코드가 아니라 설정 정보를 사용하기 때문에 코드를 고칠 수 없는 외부 라이브러리에도 적용할 수 있다.**
>> + 초기화,종료 메소드 지정 할 때에 한해서 `@Bean의 destoryMethod 속성`에는 아주 **<font color='#0101DF'>특별한 기능</font>**이 있다.
>> + 외부 라이브러리의 종료 메소드는 대부분 'close', 'shutdown' 이라는 이름을 사용한다.
>> + 그런데 @Bean의 destoryMethod는 기본값이 (inferred) 이다.
>> + 이는 추론이라는 의미인데, 이 기능은 'close', 'shutdown' 라는 이름의 메소드를 자동으로 호출해준다.
>> + 즉, 종료 메소드를 추론해서 호출해준다.
>> + 따라서 직접 스프링 빈으로 등록하면 종료 메소드는 따로 적어주지 않아도 잘 동작한다.
>> + 추론 기능을 사용하기 싫으면 destoryMethod = "" 처럼 빈 공백을 지정하면 된다.
>>
>> ## **@PostConstruct, @PreDestroy 어노테이션**
>>
>> ### **NetworkClient**
>> ```java
>> public class NetworkClient implements InitializingBean, DisposableBean {
>>     private String url;
>>
>>     public NetworkClient() {
>>         System.out.println("생성자 호출, url = " + url);
>>     }
>>
>>     public void setUrl(String url) {
>>         this.url = url;
>>     }
>>
>>     // 서비스 시작 시 호출
>>     public void connect() {
>>         System.out.println("connect : " + url);
>>     }
>>
>>     public void call(String message) {
>>         System.out.println("call : " + url + " message = " + message);
>>     }
>>
>>     // 서비스 종료 시 호출
>>     public void disconnect() {
>>         System.out.println("close " + url);
>>     }
>> 
>>     @PostConstruct
>>     public void init() {
>>         System.out.println("NetworkClient.init");
>>         connect();
>>         call("초기화 연결 메시지");
>>     }
>>
>>     @PreDestroy
>>     public void close() {
>>         System.out.println("NetworkClient.close");
>>         disconnect();
>>     }
>> }
>>
>> ``` 
>>
>> ### **BeanLifeCycleTest**
>> ```java
>> public class BeanLifeCycleTest {
>> 
>>     @Test
>>     public void lifeCycleTest() {
>>         ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
>>         NetworkClient client = ac.getBean(NetworkClient.class);
>>         ac.close();
>>     }
>>
>>     @Configuration
>>     static class LifeCycleConfig {
>>
>>         @Bean
>>         public NetworkClient networkClient() {
>>             NetworkClient networkClient = new NetworkClient();
>>             networkClient.setUrl("http://hello-spring.dev");
>>             return networkClient;
>>         }
>>     }
>> }
>>
>> ```
>> 
>> + @PostConstruct , @PreDestroy 이 두 애노테이션을 사용하면 가장 편리하게 초기화와 종료를 실행할 수 있다.
>> + **<font color='#ff0000'>최신 스프링에서 가장 권장하는 방법이다.</font>**
>> + 어노테이션 하나만 붙으면 되므로 매우 편리하다.
>> + 패키지를 보면 **<font color='#0101DF'>javax.annotation.PostConstruct</font>** 이다.
>> + 스프링에 종속적인 기술이 아니라 JSR-250 라는 자바 표준이다.
>> + 따라서 스프링이 아닌 다른 컨테이너에서도 동작한다.
>> + javax 란 자바에서 공식적으러 지원한다는 것을 의미한다.
>> + 컴포넌트 스캔과 잘 어울린다.
>> + ❗ 유일한 단점은 외부 라이브러리에는 적용하지 못한다는 점이다.
>> + 외부 라이브러리의 종료 ,초기화는 @Bean을 이용하자.
