---
layout: default
title: String(문자열) 1. 문자 찾기
grand_parent: 알고리즘
parent: 인프런
nav_order: 1
---
## 목차
{: .no_toc .text-alpha }

1. TOC
{:toc}
---

## **문제**
> + 한 개의 문자열을 입력받고, 특정 문자를 입력받아 해당 특정문자가 입력받은 문자열에 몇 개 존재하는지 알아내는 프로그램을 작성하세요.
> + 대소문자를 구분하지 않습니다.문자열의 길이는 100을 넘지 않습니다.

---

## **입력**
> + 첫 줄에 문자열이 주어지고, 두 번째 줄에 문자가 주어진다.
> + 문자열은 영어 알파벳으로만 구성되어 있습니다.

---

## **출력**
> + 첫 줄에 해당 문자의 개수를 출력한다.

___

## **입출력 예제**
> ![](../../assets/images/algorithmInflearn/findString.PNG)

---

## **풀이방식 설계**
> 1. 입력받은 문자열을 하나씩 쪼갠다.
> 2. 문자열과 쪼갠 문자들을 모두 영어 알파벳 대문자로 변형한다.
> 3. 쪼갠 문자들을 입력받은 문자열과 하나씩 비교해서 동일하면 결과값을 카운트한다.

---

## **풀이과정**
> 1. 문자열을 입력받은 후 그 중 0번째 문자를 따로 저장한다.
> + **<font color='#0101DF'>charAt()</font>** : String 타입의 데이터(문자열)에서 특정 문자를 char 타입으로 변환할 때 사용한다.
> 2. 입력받은 문자열 `str`과 0번째 문자인 `a` 를 모두 대문자로 변환한다.
> + **<font color='#0101DF'>toUpperCase()</font>** : 대상 문자열을 모두 대문자로 변환한다.
> 3. 향상된 for문을 통해 str의 문자 하나하나와 비교하여 `c`와 동일하면 result 변수에 1씩 더한다.
> + **<font color='#0101DF'>str.toCharArray()</font>** : str에 입력된 문자열을 문자 하나씩 분리해서 문자 배열을 생성함
> 4. 결과를 출력한다.

---

## **답안소스**
```java
import java.util.Scanner;

public class FindString {
	public int countString(String str, char c) {
		int result = 0;
		str = str.toUpperCase();
		c = Character.toUpperCase(c);
		
		for(char x : str.toCharArray()) {
			if(x == c) result++; 
		}
		return result;
	}
	
	public static void main(String[] args) {
		FindString cnt = new FindString();
		Scanner sc = new Scanner(System.in);
		String str = sc.next();
		char c = sc.next().charAt(0);
		
		System.out.println(cnt.countString(str, c));
	}
}

```