---
layout: default
title: 단계별로 풀어보기 7단계 1.손익분기점
grand_parent: 알고리즘
parent: 백준
nav_order: 1
---
## 목차
{: .no_toc .text-delta }

1. TOC
{:toc}
---
## **문제**
> + 월드전자는 노트북을 제조하고 판매하는 회사이다. 
> + 노트북 판매 대수에 상관없이 매년 임대료, 재산세, 보험료, 급여 등 A만원의 고정 비용이 들며, 한 대의 노트북을 생산하는 데에는 재료비와 인건비 등 총 B만원의 가변 비용이 든다고 한다.
> + 예를 들어 A=1,000, B=70이라고 하자. 이 경우 노트북을 한 대 생산하는 데는 총 1,070만원이 들며, 열 대 생산하는 데는 총 1,700만원이 든다.
> + 노트북 가격이 C만원으로 책정되었다고 한다. 
> + 일반적으로 생산 대수를 늘려 가다 보면 어느 순간 총 수입(판매비용)이 총 비용(=고정비용+가변비용)보다 많아지게 된다. 
> + 최초로 총 수입이 총 비용보다 많아져 이익이 발생하는 지점을 손익분기점(BREAK-EVEN POINT)이라고 한다.
> + A, B, C가 주어졌을 때, 손익분기점을 구하는 프로그램을 작성하시오.

---

## **입력**
> + 첫째 줄에 A, B, C가 빈 칸을 사이에 두고 순서대로 주어진다. 
> + A, B, C는 21억 이하의 자연수이다.

---

## **출력**
> + 첫 번째 줄에 손익분기점 즉 최초로 이익이 발생하는 판매량을 출력한다. 
> + 손익분기점이 존재하지 않으면 -1을 출력한다.

___

## **풀이방식 설계**
> 1. 수입에 대한 식 `N + C = A + (N * B)` 을 구한다.
> 2. 해당 식을 토대로 하여 손익분기점이 되는 시점에 대한 수학식 `N = A / (C-B)` 을 구한다.
> 3. 구한 식을 이용하여 문제에 따른 결과를 출력한다.

---

## **풀이과정**
> 1. 문자열을 입력받은 후, StringTokenizer 를 이용해서 공백을 기준으로 하나씩 쪼갠다.
> + **<font color='#0101DF'>BufferedReader</font>** : 입력된 데이터를 바로 전달하지 않고 버퍼에 저장해두었다가 전달하는 방법이다.
>   + [BufferedReader 란?](https://silverji.tistory.com/35)
> + **<font color='#0101DF'>StringTokenizer</font>** : 특정 문자에 따라 문자열을 나눈다.
> 2. 위 과정을 통해 A, B, C를 구분하고, 문제에 대한 식을 작성한다. 여기서 N이란, 노트북의 개수를 말한다.
> + 노트북 N대에 대한 수익 > `N * C = A + (N * B)`  
> 3. 위 식에서 우리가 구해야 할 것은 `N`이다. N을 구하는 과정은 다음과 같다.
> + `(N * C) - (N * B) = A`
> + `N * (C-B) = A`
> + ∴ `N = A / (C-B)`
> + 여기서 주의할 점은 손익분기점이 발생하는 시점은 N에 1을 더해야한다. 즉, 아래가 최종 결과식이 된다.
> + `N = A / (C-B)+ 1`
> 4. 이제 손익분기점이 발생하지 않을 때의 식을 구해보자.
> + 바로 분모가 음수일 때인 `C - B < 0` 일 때이다.
> + 미적분을 통해서 이 식을 좀 더 단순화하면 `C <= B` 가 된다.
> + (필자도 미적분에 대해서는 잘 모른다. 다만 C와 B가 0에 가까워질수록 무한에 가까워지고, 이로 인해 N 또한 무한이 되어 손익분기점이 발생하지 않는다고 한다.)
> 5. 도출한 식을 통해 if-else 문으로 출력값을 정해준다.

---

## **답안소스**
```java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine());
		
		int A = Integer.parseInt(st.nextToken());
		int B = Integer.parseInt(st.nextToken());
		int C = Integer.parseInt(st.nextToken());
		
		if(C <= B) {
			System.out.println("-1");
		}else {
			System.out.println((A / (C-B)) + 1);
		}
	}
}

```

## **출처**
[Stranger's LAB](ttps://st-lab.tistory.com/71)