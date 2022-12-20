---
layout: default
title: 싱글톤
parent: 스프링
nav_order: 3
---

## 목차
{: .no_toc .text-beta }

1. TOC
{:toc .text-gamma}
---

## **싱글톤 패턴**
> + static 영역에 객체 instance를 미리 하나 생성해서 올려둔다.
> + 이 객체 인스턴스가 필요하면 오직 `getInstance()` 메서드를 통해서만 조회할 수 있다.
> + `getInstance()` 메소드를 호출하면 항상 같은 인스턴스를 반환한다.
> + <font color='#ff0000'>※ 1개의 객체 인스턴스만 존재해야하기에 생성자를 `private` 으로 막아서 외부에서 new 키워드로 객체 인스턴스가 생성되는 것을 막는다.</font>
> + 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.
> + 객체 인스턴스를 2개 이상 생성하지 못하도록 해야한다.

```java
package hello.core.singleton;

public class SingletonService {

    // 자기 자신을 내부에 가지고 있되, 접근제어자는 private, 속성변수는 static final을 선언한다.
    // 이렇게하면 클래스 레벨로 올라가서 딱 하나만 존재하게 된다.
    // 자바가 뜨면서 static 영역에서 singletonService 초기화 > new를 통해 생성해서 가지고 있음
    // 이제 new를 통해 이를 생성할 수 있는 곳은 어디에도 없음
    private static final SingletonService instance = new SingletonService();

    // 사용하기 위해 조회
    // JVM이 SingletonService를 실행해서, 객체를 만듬(위 코드) > 그 후에 조회할 떄에는 아래 코드 사용
    // instance 꺼내는 유일한 방법
    public static SingletonService getInstance() {
        return instance;
    }
    
    // private를 사용해서 추가 생성을 막음
    // 다른 파일, 예를 들어 SingletonTest 에서 new를 통해 새로 생성할 수 없음
    private SingletonService() {
        
    }

    public void logic() {
        System.out.println("싱글톤 객체 로직 호출");
    }
}

```

---

## **싱글톤 패턴을 사용한 테스트 코드**
> + 싱글톤 패턴은 여러가지가 있는데, 그 중 아래는 객체를 미리 생성해두는 가장 단순하고 안전한 방법이다.

```java
package hello.core.singleton;

import hello.core.AppConfig;
import hello.core.member.MemberService;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import static org.assertj.core.api.Assertions.*;

public class SingletonTest {
    
    // 스프링 컨테이너를 사용하면 기본적으로 다 싱글톤 패턴으로 적용한다.
    @Test
    @DisplayName("싱글톤 패턴을 적용한 객체 사용")
    void singletonServiceTesT() {
        // new로 생성 불가능 > 컴파일 에러 발생
        // new SingletonService();

        SingletonService singletonService1 = SingletonService.getInstance();
        SingletonService singletonService2 = SingletonService.getInstance();

        // 참조값이 같은 것을 확인
        System.out.println("singletonService1 = " + singletonService1);
        System.out.println("singletonService2 = " + singletonService2);

        // isSameAs > 자바에서 == 과 동일(참조어 비교)
        // isEqual > 자바에서 equals() 와 동일
        assertThat(singletonService1).isSameAs(singletonService2);

        singletonService1.logic();
    }
}

```

## **싱글톤 패턴의 문제점**
> + 싱글톤 패턴을 구현하기 위해 사용하는 코드가 많다.
> + 의존관계상 클라이언트가 구체 클래스에 의존한다. 이 때문에 `DIP를 위반`하고, `OCP 원칙 위반`의 가능성 또한 높다.
> + 테스트가 어렵다.
> + 내부 속성의 변경 혹은 초기화가 어렵다.
> + private 생성자로 자식 클래스를 만들기 어렵다.
> + 유연성이 떨어진다.
> + 안티패턴으로 불리기도 한다.

---

## **싱글톤 컨테이너**
> + 스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서 객체 인스턴스를 싱글톤으로 관리한다.
> + 스프링 컨테이너는 싱글톤 패턴을 적용하지 않아도 객체 인스턴스를 싱글톤으로 관리한다.
> + 스프링 컨테이너는 싱글톤 컨테이너 역할을 한다. 아처럼 싱글톤 객체를 생성하고 관리하는 기능을 싱글톤 레지스트리라 한다.
> + 스프링 컨테이너의 이러한 기능 덕분에 **<font color='#0101DF'>싱글톤 패턴의 모든 단점들을 해결하면서 객체를 싱글톤으로 유지할 수 있다.</font>**
    >   1. **<font color='#0101DF'>싱글톤 패턴을 위한 지저분한 코드가 들어가지 않아도 된다.</font>**
>   2. DIP, OCP, 테스트, private 생성자로부터 자유롭게 싱글톤을 사용할 수 있다.
> + 스프링 컨테이너를 사용하면 고객의 요청이 올 때마다 객체를 생성하지 않고 이미 만들어진 객체를 사용해서 효율적으로 재사용할 수 있다.

```java
public class SingletonTest {

    @Test
    @DisplayName("스프링 컨테이너와 싱글톤")
    void springContainer() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        // 1. 조회 : 호출할 때 마다 객체를 생성
        MemberService memberService1 = ac.getBean("memberService", MemberService.class);
        // 2. 조회 : 호출할 때 마다 객체를 생성
        MemberService memberService2 = ac.getBean("memberService", MemberService.class);

        System.out.println("memberService1 = " + memberService1);
        System.out.println("memberService2 = " + memberService2);

        assertThat(memberService1).isSameAs(memberService2);
    }
}
```

---

## **<font color='#ff0000'>❗ 싱글톤 방식의 주의점</font>**
> + 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안된다.
> + 무상태(stateless)로 설계해야 한다.
> + 특정 클라이언트에 의존적인 필드가 있으면 안된다.
> + 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
> + 되도록이면 읽기만 가능해야 한다.
> + 필드 대신에 자바에서 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.
> + 스프링 빈의 필드에 공유 값을 설정하면 큰 장애가 발생할 수 있으니 주의가 필요하다.

---

## **상태를 유지(stateful)하게 설계하는 예시**
+ A가 10000원을 주문하고 금액을 조회하는 사이에 B가 들어와서 주문을 하는 상황이다.
+ 기대하는 price의 값은 10000원이지만, 결과는 20000원이 나왔다.

---

### StatefulService
```java
package hello.core.singleton;

public class StatefulService {
    // 상태를 유지하는 필드
    private int price;

    public void order(String name, int price) {
        System.out.println("name = " + name + "price = " + price);
        this.price = price;
    }

    public int getPrice() {
        return price;
    }
}

```

### StatefulServiceTest
```java
package hello.core.singleton;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.testng.asserts.Assertion;

import static org.junit.jupiter.api.Assertions.*;

class StatefulServiceTest {
    @Test
    void StatefulServiceSingleton() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService1 = ac.getBean(StatefulService.class);
        StatefulService statefulService2 = ac.getBean(StatefulService.class);

        // ThreadA : A 사용자가 10000원 주문
        statefulService1.order("userA", 10000);
        // ThreadB : B 사용자가 20000원 주문
        statefulService2.order("userB", 20000);

        // ThreadA : 사용자 A 주문 금액 조회
        int price = statefulService1.getPrice();
        System.out.println("price = " + price);

        Assertions.assertThat(statefulService1.getPrice()).isEqualTo(20000);
    }

    static class TestConfig {
        @Bean
        public StatefulService statefulService() {
            return new StatefulService();
        }
    }
}

```

---

## **무상태로 설계하는 예시**
+ 아래처럼 변경하면 무상태가 되어서 price가 원하던 10000원으로 나온다.

---

### StatefulService
```java
package hello.core.singleton;

public class StatefulService {

    public int order(String name, int price) {
        System.out.println("name = " + name + "price = " + price);
        return price;
    }
}

```

### StatefulServiceTest
```java
package hello.core.singleton;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.testng.asserts.Assertion;

import static org.junit.jupiter.api.Assertions.*;

class StatefulServiceTest {
    @Test
    void StatefulServiceSingleton() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService1 = ac.getBean(StatefulService.class);
        StatefulService statefulService2 = ac.getBean(StatefulService.class);

        int userAPrice = statefulService1.order("userA", 10000);
        int userBPrice = statefulService2.order("userB", 20000);

        System.out.println("price = " + userAPrice);
    }

    static class TestConfig {
        @Bean
        public StatefulService statefulService() {
            return new StatefulService();
        }
    }
}

```

---

## **@Configuration 과 싱글톤, 바이트코드 조작**
> + 예제 코드에 System.out.println() 을 보면, @Bean > memberRepository 에 대한 출력문이 3번이 호출되어야 한다. 그러나 결과를 보면 1번만 호출되었다.
> + 그 이유는 바로 @Configuration 에 있다.
> + 스프링이 CGLIB라는 바이트코드 조작 라이브러리를 사용해서 AppConfig 클래스를 상속받은 임의의 다른 클래스를 만들고, 그 클래스를 스프링 빈으로 등록한다.
> + CGLIB의 예상 코드를 추측해보자면, @Bean이 붙은 메소드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고, 스프링 빈이 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드일 것이다.
> + 이 덕분에 싱글톤이 보장된다.
> + AppConfig@CGLIB 는 AppConfig의 자식 타입으로, AppConfig 타입으로 조회할 수 있다.
> + @Configuration 을 제거하면 싱글톤이 깨진다.

### AppConfig

```java
package hello.core;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

// 실제 앱의 동작에 필요한 구현 객체를 생성한다.
// AppConfig는 생성한 객체 인스턴스의 참조(레퍼런스)를 생성자를 통해서 주입(연결)한다.
// 객체 생성, 연결은 AppConfig가 하고 실행은 각 인터페이스가 담당하도록 분리한다.
// 인터페이스 입장에서는 의존관계를 주입해주는것 같다고 하여 이를 DI(Dependency Injection), 의존관계 주입 또는 의존성 주입이라 한다.
@Configuration
public class AppConfig {

    // @Bean > memberService > new MemoryMemberRepository() 호출
    // @Bean > orderService > new MemoryRepository() > 2번째 호출 > 싱글톤 깨진다??
    @Bean
    public MemberService memberService() {
        System.out.println("call AppConfig.memberService");
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        System.out.println("call AppConfig.memberRepository");
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        System.out.println("call AppConfig.orderService");
        return new OrderServiceImpl(
                memberRepository(), discountPolicy());
    }

    @Bean
    public DiscountPolicy discountPolicy() {
        return new RateDiscountPolicy();
    }

}

```

### configurationTest

```java
package hello.core.singleton;

import hello.core.AppConfig;
import hello.core.member.MemberRepository;
import hello.core.member.MemberServiceImpl;
import hello.core.order.OrderServiceImpl;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import static org.assertj.core.api.Assertions.*;

public class ConfigurationSingletonTest {

    @Test
    void configurationTest() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
        OrderServiceImpl orderService = ac.getBean("orderService", OrderServiceImpl.class);
        MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);

        MemberRepository memberRepository1 = memberService.getMemberRepository();
        MemberRepository memberRepository2 = memberService.getMemberRepository();

        System.out.println("memberService -> memberRepository = " + memberRepository1);
        System.out.println("orderService -> memberRepository = " + memberRepository2);
        System.out.println("memberRepository = " + memberRepository);

        assertThat(memberService.getMemberRepository()).isSameAs(memberRepository);
        assertThat(memberService.getMemberRepository()).isSameAs(memberRepository);
    }

    @Test
    void configurationDeep() {
        // AnnotationConfigApplicationContext(AppConfig.class) > 이 코드를 통해 Bean으로 등록됨
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        // 꺼냄
        AppConfig bean = ac.getBean(AppConfig.class);
        // 출력 > 클래스 타입
        // 기존 클래스와는 다르게 무언가가 붙어서 출력됨
        // 그 이유는 스프링이 CGLIB라는 조작 라이브러리 사용 > AppConfig 클래스를 상속받은 임의의 다른 클래스 생성 > 그 클래스를 스프링 빈으로 등록
        System.out.println("bean = " + bean.getClass());
    }

}

```







