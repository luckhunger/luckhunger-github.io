---
layout: default
title: 연산자
parent: 자바
nav_order: 3
---
## 목차
{: .no_toc .text-delta }

1. TOC
{:toc}
---

# **연산자**
> 데이터를 처리하기 위한 각종 기호를 말한다.

---

## **1. 대입 연산자**
> 변수에 데이터를 입력하는 연산자로 `=` 를 말한다.
>
|종류|의미|사용 예| |
|--|-----|-----|-----|
|=|변수에 데이터 입력|num = 1;| |
|+=| |num += 3;|num = num + 3;|
|-=| |num -= 3;|num = num - 3;|
|/=| |num /= 3;|num = num / 3;|
|*=| |num *= 3;|num = num * 3;|

---

## **2. 연결 연산자**
> 문자와 문자를 붙여주는 연산자로 `+` 를 말한다.

---

## **3. 사칙 연산자**
> 숫자끼리의 더하기, 빼기, 곱하기, 나누기, 나머지를 구하는 연산자이다. 
> 
|종류|의미|사용 예|결과|
|--|-----|-----|-----|
|+|더하기|3 + 5|8|
|-|빼기|6 - 2|4|
|*|곱하기|4 * 8|32|
|/|나누기|8/2|4|
|%|나머지|7%3|1|

---

## **4. 증감 연산자**
> 변수 앞 또는 뒤에 붙어 변수에 저장된 값을 1씩 증가 또는 감소시키는 연산자이다.
> 증감 연산자가 붙은 변수가 다른 연산에 참여하거나 매개변수로 전달되는 경우 증감 연산자가 붙은 위치에 따라 다른 연산이 된다.
> + 앞에 붙을 경우 : 먼저 증감하고 연산에 참여
> + 뒤에 붙을 경우 : 연산에 참여한 후 증감
> 
|증감연산자|의미|사용 예|결과|
|--|-----|-----|-----|
|++|1씩 증가|int num1 = 5;<br>num1++;|변수 num1 선언 후 5 저장<br>num1에 1 증가(연산에 참여안함)|
| | |int num1 = 5;<br>++num1;|변수 num1 선언 후 저장<br>num1에 1 증가 (연산에 참여안함)|
| | |int num1 = 5;<br>int num2 = ++num1;|변수 num1 선언 후 5 저장<br>num1에 1 증가 후 num2에 6 저장<br>(연산에 참여함)|
| |int num1 = 5;<br>int num2 = num1++;|변수 num1 선언 후 5 저장<br>num1에 5 저장 후 num1에 1 증가<br>num1에 1 증가 (연산에 참여함)|
| | |int num1 = 5;<br>System.out.print(num1++);|변수 num1 선언 후 5 저장<br>5 출력 후 num1에 1 증가<br>(매개변수로 전달)|
| | |int num1 = 5;<br>System.out.print(++num1);|변수 num1 선언 후 5 저장<br>num1에 1 증가 후 6 출력<br>(매개변수로 전달)|
|--|1씩 감소|int num1 = 5;<br>num1--;|변수 num1 선언 후 5 저장<br>num1에 1 감소 (연산에 참여안함)|
| | |int num1 = 5;<br>++num1;|변수 num1 선언 후 저장<br>num1에 1 증가 (연산에 참여안함)|
| | |int num1 = 5;<br>--num1;|변수 num1 선언 후 5 저장<br>num1에 1 감소 (연산에 참여안함)|
| | |int num1 = 5;<br>int num2 = num1++;|변수 num1 선언 후 5 저장<br>num1에 5 저장 후 num1에 1 증가<br>num1에 1 증가 (연산에 참여함)|
| | |int num1 = 5;<br>int num2 = --num1;|변수 num1 선언 후 5 저장<br>num1에 1 감소 후 num2에 4 저장<br>(연산에 참여함)|
| | |int num1 = 5;<br>int num2 = num1--;|변수 num1 선언 후 5 저장<br>num1에 5 저장 후 num1에 1 증가<br>num1에 1 감소 (연산에 참여함)|
| | |int num1 = 5;<br>System.out.print(num1--);|변수 num1 선언 후 5 저장<br>5 출력 후 num1에 1 감소<br>(매개변수로 전달)|
| | |int num1 = 5;<br>System.out.print(--num1);|변수 num1 선언 후 5 저장<br>num1에 1 감소 후 4 출력<br>(매개변수로 전달)|

---

## **5. 비교 연산자**
> 2개 데이터의 같음, 다름, 대소관계를 판단하여 true 또는 false를 리턴하는 연산자이다.
> 주로 조건문이나 반복문에서 사용된다.
>
|비교연산자|의미|사용 예|결과|
|--|-----|-----|-----|
|==|좌우가 같으면 true 리턴, 아니면 false 리턴|int avg = 80;<br>boolean result = avg == 80;|result 에 true 저장|
|!=|좌우가 같지 않으면 true 리턴, 아니면 false 리턴|int avg = 80;<br>boolean result = avg != 83;|result 에 true 저장|
|>|왼쪽이 크면 true 리턴, 아니면 false 리턴|int avg = 80;<br>boolean result = avg > 100;|result 에 false 저장|
|<|오른쪽이 크면 true 리턴, 아니면 false 리턴|int avg = 80;<br>boolean result = avg < 80;|result 에 false 저장|
|>=|왼쪽이 크거나 같으면 같으면 true 리턴, 아니면 false 리턴|int avg = 80;<br>boolean result = avg >= 80|result 에 true 저장|
|<=|오른쪽이 크거나 같으면 true 리턴, 아니면 false 리턴|int avg = 80;<br>boolean result = avg <= 80;|result 에 true 저장|

---

## **6. 논리 연산자**
> true 또는 false 값을 가지고 논리적 연산을 하여 true 또는 false를 리턴하는 연산자이다.
> 주로 조건문이나 반복문에서 사용된다.
>
|논리연산자|의미|사용 예|결과|
|--|-----|-----|-----|
|&&|좌우 모두 true 일 경우 true 리턴<br>그 외의 경우 false 리턴<br>false 리턴 시 이후 남아있는 연산은 안함|int kor = 85, eng = 70;<br>boolean result = kor >= 80 && eng >= 80 |result 에 false 저장|
| | |int kor = 85, eng = 70;<br>boolean result = eng >= 80 && kor >= 80;|result 에 false 저장<br>kor >= 80 연산 안함|
|&|좌우 모두 true 일 경우 true 리턴<br>그 외의 경우 false 리턴<br>무조건 모두 연산|int kor = 85, eng = 70;<br>boolean result = kor >= 80 & eng >= 80;|result 에 false 저장|
| | |int kor = 85, eng = 70;<br>boolean result = eng >= 80 & kor >= 80;|result 에 false 저장|
|\|\||좌우 중 하나 이상 true 일 경우 true 리턴<br>true 리턴 시 이후 남아있는 연산은 안함|int kor = 85, eng = 70;<br>boolean result = kor >= 80 || eng >= 80;|result 에 true 저장<br>eng>=80 연산 안함|
| | |int avg = 80;<br>boolean result = avg <= 80;|result 에 true 저장|
|\||좌우 중 하나 이상 true 일 경우 true 리턴<br>무조건 모두 연산|int kor = 85, eng = 70;<br>boolean result = kor >= 80 | eng >= 80;|result 에 true 저장|
| | |int kor = 85, eng = 70;<br>boolean result = eng >= 80 | kor >= 80;|result 에 true 저장|
|!|우측이 true 면 false 리턴, false 면 true 리턴|int kor = 85, eng = 70;<br>boolean result = !(kor < 80 || eng < 80);|result 에 true 저장|

---

## **7. 삼항 연산자**
> 조건식 true 이면 설정 데이터를 리턴하는 연산자이다.
>
|삼항연산자 형식|의미|사용 예|
|--|-----|--------|
|변수명 = 조건식?데이터1:데이터2;|조건식이 true 일 경우 데이터 1 리턴<br>조건식이 false 일 경우 데이터 2 리턴|int avg = 80;<br>char result = (avg >= 80)?'합':'불';<br>result 에 합 저장|

---