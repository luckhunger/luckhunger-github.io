---
layout: default
title: 스프링 빈
grand_parent: 스프링
parent: 스프링 컨테이너와 빈
nav_order: 2
---

## 목차
{: .no_toc .text-beta }

1. TOC
{:toc .text-gamma}
---

# **스프링 빈**
## **빈 출력해보기**
> + 스프링 컨테이너에 등록한 빈들을 확인해보자.
> + 각 메소드에 대한 설명은 주석으로 대체한다.

```java
package hello.core.beanfind;

import hello.core.AppConfig;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

class ApplicationContextInfoTest {

   AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

   // 스프링 내부 + 이미 등록된 빈 + 내가 등록한 빈이 전부 출력된다.
   // 실행하면 스프링에 등록된 모든 빈 정보를 출력할 수 있다.
   // ac.getBeanDefinitionNames() : 스프링에 등록된 모든 빈 이름을 조회한다.
   // ac.getBean() : 빈 이름으로 빈 객체(인스턴스)를 조회한다.
   @Test
   @DisplayName("모든 빈 출력하기")
   void findAllBean() {
      String[] beanDefinitionNames = ac.getBeanDefinitionNames();
      for (String beanDefinitionName : beanDefinitionNames) {
         Object bean = ac.getBean(beanDefinitionName);
         System.out.println("name = " + beanDefinitionName + "object = " + bean);
      }
   }

   // 내가 등록한 빈이 전부 출력된다.
   @Test
   @DisplayName("애플리케이션 빈 출력하기")
   void findApplicationBean() {
      String[] beanDefinitionNames = ac.getBeanDefinitionNames();
      for (String beanDefinitionName : beanDefinitionNames) {
         // getBeanDefinition() : bean 하나하나에 대한 메타데이터 정보
         // getBeanDefinition(beanDefinitionName) : beanDefinitionName 에 대한 정보를 꺼낼 수 있다.
         BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

         // 내가 애플리케이션 개발하기 위해 직접 등록한 빈
         // getRole() : 스프링이 내부에서 사용하는 빈을 구분할 수 있다.
         // ROLE_APPLICATION : 직접 등록한 애플리케이션 빈
         // ROLE_INFRASTRUCTURE : 스프링이 내부에서 사용하는 빈
         if(beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
            Object bean = ac.getBean(beanDefinitionName);
            System.out.println("name = " + beanDefinitionName + "object = " + bean);
         }
      }
   }
}
```

## **빈 조회해보기**
> + 가장 기본적인 조회 방법이다.
> + `ac.getBean(빈 이름, 타입);` 형태로 조회한다.
> + 타입만 조회하려면 `ac.getBean(타입);` 형태로 조회할 수 있다.
> + 조회하고자 하는 빈이 없으면 `NoSuchBeanDefinitionException` 에러가 발생한다.
> + 구체 타입으로 조회하는 것은 구현에 의존하기 때문에 좋은 코드는 아니다.
> + `->` 는 람다 문법이라고 해서 익명 함수의 일종이다. 람다에 대해 자세히 알고 싶으면 [이 곳](http://www.tcpschool.com/java/java_lambda_concept)을 참고하자.
> + `참고` : Assertions.assertThat(); 를 작성한 후에 alt + enter 를 누르면 static 형태인 assertThat() 로 전환된다.

```java
package hello.core.beanfind;

import hello.core.AppConfig;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;

public class ApplicationContextBasicFindTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("빈 이름으로 조회")
    void findBeanByName() {
        MemberService memberService = ac.getBean("memberService", MemberService.class);
        assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }

    @Test
    @DisplayName("이름 없이 타입으로만 조회")
    void findBeanByType() {
        MemberService memberService = ac.getBean(MemberService.class);
        assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }

    @Test
    @DisplayName("구체 타입으로 조회")
    void findBeanByName2() {
        MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
        assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }

    @Test
    @DisplayName("빈 이름으로 조회X")
    void findBeanByNameX() {
      MemberService xxxxx = ac.getBean("xxxxx", MemberService.class);
      assertThrows(NoSuchBeanDefinitionException.class, () -> ac.getBean("xxxxx", MemberService.class));
    }
}
```

## **중복되는 빈 조회**
> + 타입만으로 조회할 때에 같은 타입이 둘 이상 있으면 중복 오류(NoUniqueDefinitionException)가 발생한다.
> + 이 경우에는 아래와 같은 해결 방안들이 있다.
>   1. `ac.getBean(빈 이름, 타입);` 를 통해 이름을 지정하면 된다.
>   2. Map을 사용하여 특정 타입의 빈을 모두 조회한다.

```java
package hello.core.beanfind;

import hello.core.AppConfig;
import hello.core.discount.DiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.Map;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

public class ApplicationContextSameBeanFindTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SameBeanConfig.class);

    @Test
    @DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면, 중복 오류가 발생한다.")
    void findBeanByTypeDuplicate() {
        MemberRepository bean = ac.getBean(MemberRepository.class);
        assertThrows(NoSuchBeanDefinitionException.class,
                () -> ac.getBean(MemberRepository.class));
    }

    @Test
    @DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면, 빈 이름을 지정하면 된다.")
    void findBeanByName() {
        MemberRepository memberRepository = ac.getBean("memberRespository1", MemberRepository.class);
        assertThat(memberRepository).isInstanceOf(MemberRepository.class);
    }

    @Test
    @DisplayName("특정 타입을 모두 조회하기")
    void findAllBeanByType() {
        Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " value = " + beansOfType.get(key));
        }
        System.out.println("beansOfType = " + beansOfType);
        assertThat(beansOfType.size()).isEqualTo(2);
    }

    @Configuration
    static class SameBeanConfig {

        @Bean
        public MemberRepository memberRepository1() {
            return new MemoryMemberRepository();
        }

        @Bean
        public MemberRepository memberRepository2() {
            return new MemoryMemberRepository();
        }
    }
}
```

## **빈의 상속**
> + 부모 타입으로 조회하면 자식 타입도 함께 조회한다.
> + 모든 자바 객체의 최고 타입인 `Object` 타입으로 조회하면 모든 스프링 빈을 조회한다.
> + ❗ 예제 코드는 생략하겠습니다.