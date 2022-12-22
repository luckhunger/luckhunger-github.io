---
layout: default
title: 의존관계 주입
parent: 스프링
nav_order: 5
---

## 목차
{: .no_toc .text-beta }

1. TOC
{:toc .text-gamma}
---

## **다양한 의존관계 주입 방법**
> 1. 생성자 주입
> 2. 수정자 주입(setter 주입)
> 3. 필드 주입
> 4. 일반 메서드 주입

---

## **생성자 주입**
> + 생성자를 통해서 의존 관계를 주입받는 방법이다.
> + 생성자 호출 시점에 딱 1번만 호출되는 것이 보장된다.
> + 즉, 한번 코드를 짠 후에 변경되지 않는다.
> + 이러한 특징으로 인해 불변, 필수 의존관계에 주로 사용한다.
> + **<font color='#ff0000'>생성자가 1개만 있으면</font>** `@Autowired` **<font color='#ff0000'>를 생략해도 자동으로 주입된다.</font>** (스프링 빈에만 적용된다.)
> + ❗ 생성자 주입은 스프링이 객체를 생성하기 위해서 생성자를 호출해야만 한다. 이 때, 의존관계 주입이 동시에 이루어진다.

```java
@Component
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    public OrderServiceImpl(MemberRepository memberRepository, @MainDiscountPolicy DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

### **생성자 주입 사용 이유**
> + 과거에는 수정자 주입과 필드 주입을 많이 사용했지만, 최근에는 스프링을 포함한 DI 프레임워크 대부분이 생성자 주입을 권장한다.
> + 일반적으로 생성자 주입 방식을 사용하고, 옵션이 필요한 경우에만 수정자 주입을 선택하는 방식으로 사용하는게 좋다.
> + 생성자 주입 방식은 프레임워크에 의존하지 않고 순수한 자바 언어의 특징을 잘 살리는 방법이다.
> + 그 이유는 아래와 같다.
>
>> ### **1. 불변**
>> + 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료까지 의존관계를 변경할 일이 없다.
>> + 대부분의 의존관계는 보통 애플리케이션 종료 전까지 변하면 안된다.
>> + 수정자 주입을 사용하면 setXxx 메소드를 public 으로 열어두어야 한다.
>> + 그런데 변경하면 안되는 메소드를 열어두는 것은 좋은 설계가 아니다.
>> + 생성자 주입은 객체를 생성할 때 딱 1번만 호출되며 이후로 호출되는 일이 없다. 즉, **<font color='#0101DF'>불변</font>**하다.
>> + 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안된다.
>>
>> ### **2. 누락**
>> + 생성자 주입을 사용하면 주입 데이터를 누락했을 때 컴파일 오류가 발생한다.
>> + IDE에서 바로 어떤 값을 필수로 주입해야 하는지 알 수 있다.
>>
>> ### **3. final 키워드**
>> + 생성자 주입 방식만 `final 키워드`를 사용할 수 있다.
>> + 그래서 생성자에서 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에 막아준다.

**<font color='#ff0000'>❗ 컴파일 오류는 세상에서 가장 빠르고, 좋은 오류이다</font>**


---

## **수정자 주입(setter 주입)**
> + setter 라 불리는 필드의 값을 변경하는 수정자 메서드를 통해서 의존관계를 주입하는 방법이다.
> + 선택과 변경 가능성이 있는 의존관계에 주로 사용한다.
> + 자바빈 프로퍼티 규약의 수정자 메서드 방식을 사용하는 방법이다.
> + `@Autowired의` 기본 동작은 주입할 대상이 없으면 오류가 발생한다.
> + 주입할 대상이 없어도 동작하게 하려면 '@Autowired(required = false)' 로 지정하면 된다.
> + 자바에서는 과거부터 필드의 값을 직접 변경하지 않고 setXxx, getXxx 라는 메서드를 통해서 값을 읽거나 수정하는 규칙을 만들었는데, 이것이 자바빈 프로퍼티 규약이다.

```java
@Component
public class OrderServiceImpl implements OrderService {
    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;

    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    @Autowired
    public void setDiscountPolicy(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }
}
```

---

## **필드 주입**
> + 필드에 바로 주입하는 방식이다.
> + 코드가 간결하지만 외부에서 변경이 불가능하기에 테스트가 힘들다는 치명적인 단점이 있다.
> + DI 프레임워크가 없으면 아무것도 할 수 없다.
> + 사용을 지양한다.
>   + 애플리케이션의 실제 코드와 관계 없는 테스트 코드에서 사용해도 된다.
>   + 스프링 설정을 목적으로 하는 @Configuration 같은 곳에서만 특별한 용도로 사용한다.

```java
@Component
public class OrderServiceImpl implements OrderService {
    @Autowired private MemberRepository memberRepository;
    @Autowired private DiscountPolicy discountPolicy;
}
```

---

## **일반 메서드 주입**
> + 일반 메서드를 통해서 주입받을 수 있다.
> + 한번에 여러 필드를 주입받을 수 있다.
> + 일반적으로 잘 사용하지 않는다.

---

## **옵션 처리**
> + 주입할 스프링 빈이 없어도 동작해야 할 때가 있다.
> + `@Autowired`만 사용하면 required 옵션의 기본값이 true 로 되어있어 자동 주입 대상이 없으면 오류가 발생한다.

> ### **자동 주입 대상의 옵션 처리**
> + `@Autowired(required = false)` : 자동으로 주입할 대상이 없으면 수정자 메소드 자체가 호출이 되지 않는다.
> + `org.springframework.lang.@Nullable` : 자동으로 주입할 대상이 없으면 null이 입력된다.
> + `Optional<>` : 자동 주입할 대상이 없으면 Optional.empty 가 입력된다.

```java
public class AutowiredTest {
    @Test
    void AutowiredOption() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestBean.class);
    }

    static class TestBean {
        // required = false > 의존관계가 없으면 호출 자체가 안됨
        @Autowired(required = false)
        public void setNoBean1(Member noBean1) {
            System.out.println("noBean1 = " + noBean1);
        }

        @Autowired
        // 호출하되, 값을 null로
        public void setNoBean2(@Nullable Member noBean2) {
            System.out.println("noBean2 = " + noBean2);
        }

        @Autowired
        // 값이 없으면 empty
        public void setNoBean3(Optional<Member> noBean3) {
            System.out.println("noBean3 = " + noBean3);
        }
    }
}
```
---

## **롬복**
> + getter, setter, 생성자 등 Java 라이브러리로 반복되는 메소드를 작성하는 코드를 줄여주는 코드 다이어트 라이브러리다.
> + @RequiredArgsConstructor : final이 붙은 생성자를 만들어준다.
> + @getter, @setter : getter, setter 코드 없이 사용할 수 있다.
> + 롬복을 적용하면 아래의 OrderServiceImpl 처럼 코드가 아주 간결해진다.

### **HelloLombok**
```java
package hello.core;

import lombok.Getter;
import lombok.Setter;
import org.w3c.dom.ls.LSOutput;

@Getter
@Setter
public class HelloLombok {
    private String name;
    private int age;

    public static void main(String[] args) {
        HelloLombok helloLombok = new HelloLombok();
        helloLombok.setName("lalala");

        String name = helloLombok.getName();
        System.out.println("name = " + name);
    }
}

```

### **OrderServiceImpl**
```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;
}
```

### **롬복 설치 및 적용법**
> + 롬복은 build.gradle에 라이브러리와 환경을 추가한 후에 적용할 수 있다.
> + 인텔리제이에서는 File > Settings > Plugins에서 lombok 입력 후, lombok를 설치한다.
> + 단, 2020.3 버전부터는 기본으로 제공하고 있다.
> + 자세한 설치 방법은 [이 곳](https://leeys.tistory.com/27)를 참조한다.

### **build.gradle**
```java
plugins {
	id 'org.springframework.boot' version '2.7.4'
	id 'io.spring.dependency-management' version '1.0.14.RELEASE'
	id 'java'
}

group = 'hello'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

//lombok 설정 추가 시작
configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}
//lombok 설정 추가 끝

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter'
	//web 라이브러리 추가
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'javax.inject:javax.inject:1'

	//lombok 라이브러리 추가 시작
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testCompileOnly 'org.projectlombok:lombok'
	testAnnotationProcessor 'org.projectlombok:lombok'
	//lombok 라이브러리 추가 끝

    implementation 'org.testng:testng:7.1.0'
	implementation 'org.testng:testng:7.1.0'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
	useJUnitPlatform()
}

```

**<font color='#ff0000'>📌 최근에는 생성자를 딱 1개 두고, @Autowired 를 생략하는 방법을 주로 사용한다.<br>
여기에 Lombok 라이브러리의</font>** `@RequiredArgsConstructor` **<font color='#ff0000'>함께 사용하면 기능은 다 제공하면서, 코드는 깔끔하게 사용할 수 있다.</font>**

---

## **자동 의존관계로 조회하는 빈이 2개 이상일 때**
> + `@Autowired`는 타입으로 조회한다.
> + 타입으로 조회하기 때문에, 선택된 빈이 2개 이상일 때에 문제가 발생한다.
> + 2개 이상에 `@Component`가 설정된 상태라면 `NoUniqueBeanDefinitionException` 오류가 발생한다.
> + 해당 오류메시지는 하나의 빈을 기대했는데 여러개가 발견되었다는 의미이다.
> + 하위 타입으로 지정해서 해결할 수도 있지만, 이는 DIP를 위배하고 유연성이 떨어진다.
> + 또한 이름만 다르고 동일한 타입의 스프링 빈이 2개 있을 때에 해결이 되지 않는다.
>
>> ### **해결방법**
>> ### **1. @Autowired 필드명**
>> + @Autowired는 타입 매칭을 시도하고, 이 때 여러 빈이 있으면 필드 혹은 파라미터 이름으로 빈 이름을 추가 매칭한다.
>> + 즉, 타입이 동일하면 필드명 또는 파라미터명으로 빈 이름을 매칭한다.
>>
>> ```java
>> @Autowired
>> private DiscountPolicy rateDiscountPolicy 
>> ```
>> 
>> ### **2. @Qualifier**
>> + @Qualifier는 추가 구분자를 붙여주는 방식이다.
>>   + ex) @Qualfier("plusName")
>> + 주입 시 추가적인 방법을 제공하는 것이지 빈 이름을 변경하는 것이 아니다.
>> + @Qualifier 끼리 매칭한다.
>>
>> ```java
>> @Component
>> @Qualifier("mainDiscountPolicy")
>> public class RateDiscountPolicy implements DiscountPolicy {}
>> ```
>>
>>
>> ### **3. @Primary**
>> + 우선순위를 지정하는 방법이다.
>> + `@Autowired` 시에 여러 빈이 매칭되면 `@Primary`가 우선권을 가진다.
>>
>> ```java
>> @Component
>> @Primary
>> public class RateDiscountPolicy implements DiscountPolicy {}
>> ```
**❗ 스프링은 자동보다는 수동, 넓은 범위보다는 좁은 범위가 우선권을 가진다. 즉, 더 세세하게 정해주는 `@Qualfier`가 `@Primary`보다 우선 순위가 높다.**

---

## **조회한 빈이 모두 필요할 때**
> + 여러 개의 빈을 조회하고 싶을 때에는 List와 Map을 사용한다.

### **AllBeanTest**
> + DiscountService는 Map으로 모든 DiscountPolicy 를 주입받는다. 
> + 이때 fixDiscountPolicy , rateDiscountPolicy 가 주입된다.
> + discount () 메서드는 discountCode로 "fixDiscountPolicy"가 넘어오면 map에서 fixDiscountPolicy 스프링 빈을 찾아서 실행한다. 
> + 물론 “rateDiscountPolicy”가 넘어오면 rateDiscountPolicy 스프링 빈을 찾아서 실행한다.

```java
public class AllBeanTest {

    @Test
    void findAllBean() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);

        DiscountService discountService = ac.getBean(DiscountService.class);
        Member member = new Member(1L, "userA", Grade.VIP);
        int discountPrice = discountService.discount(member, 10000, "fixDiscountPolicy");

        assertThat(discountService).isInstanceOf(DiscountService.class);
        assertThat(discountPrice).isEqualTo(1000);

        int rateDiscountPrice = discountService.discount(member, 20000, "rateDiscountPolicy");
        assertThat(rateDiscountPrice).isEqualTo(2000);
    }

    static class DiscountService {
        private final Map<String, DiscountPolicy> policyMap;
        private final List<DiscountPolicy> policies;

        @Autowired
        public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
            this.policyMap = policyMap;
            this.policies = policies;
            System.out.println("policyMap = " + policyMap);
            System.out.println("policies = " + policies);
        }

        public int discount(Member member, int price, String discountCode) {
            DiscountPolicy discountPolicy = policyMap.get(discountCode);
            return discountPolicy.discount(member, price);
        }
    }
}

```

---

## **자동, 수동의 올바른 실무 운영 기준**
> + 기본적으로는 자동을 사용하자.
> + 스프링이 나오고 점점 자동을 선호하는 추세이다.
> + 스프링은 계층에 맞추어 일반적인 애플리케이션 로직을 자동으로 스캔할 수 있도록 지원한다.
> + 또한 스프링 부트로 넘어오면서 컴포넌트 스캔을 기본으로 사용하고, 스프링 부트의 다양한 스프링 빈들도 조건이 맞으면 자동으로 등록하도록 설계되었다.

### **애플리케이션 로직**
> + 애플리케이션은 크게 업무 로직과 기술 지원 로직으로 나눌 수 있다.
>> + ### **업무 로직 빈**
>> + 웹을 지원하는 컨트롤러, 핵심 비즈니스 로직이 있는 서비스, 데이터 계층의 로직을 처리하는 리포지토리 등이 모두 업무 로직이다.
>> + 보통 비즈니스 요구사항을 개발할 때 추가되거나 변경된다.
>> + 업무 로직은 숫자가 매우 많고 한번 개발하려면 컨트롤러, 서비스, 레파지토리 처럼 어느정도 유사한 패턴이 있다.
>> + 이런 경우 자동 기능을 적극적으로 사용하는 것이 좋다.
>> + 보통 문제가 발생해도 어떤 곳에서 문제가 발생했는지 명확하게 파악하기 쉽다.
>> + ### **기술 지원 빈**
>> + 기술적인 문제나 공통 관심사(AOP)를 처리할 때 주로 사용된다.
>> + 데이터베이스 연결이나 공통 로그 처리 처럼 업무 로직을 지원하기 위한 하부 기술이나 공통 기술들이다.
>> + 기술 지원 로직은 업무 로직과 비교해서 그 수가 매우 적고 보통 애플리케이션 전반에 걸쳐서 광범위한 영향을 미친다. 
>> + 문제가 발생했을 때 위치 파악은 커녕 적용이 잘 되고 있는지 조차 파악하기 어려운 경우가 많다.
>> + 이런 경우에는 가급적 수동 빈 등록을 사용해서 명확하게 들어내는 것이 좋다.