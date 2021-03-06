---
layout  : wiki
title   : 그레이 코드(Gray code)
summary : reflected binary Gray code
date    : 2018-10-15 21:40:31 +0900
updated : 2018-10-15 23:41:01 +0900
tags    : binary algorithm
toc     : true
public  : true
parent  : what
latex   : true
---
* TOC
{:toc}

# 그레이 코드

* **그레이 부호**라고도 부른다.
* 그레이 코드의 종류는 다양하지만 이 글에서는 반사된 이진 그레이 코드(reflected binary Gray code)를 다룬다.
* 그레이 코드는 연속된 수를 한 비트만 다르게 인코딩한다.
    * 쉽게 말해, **다음 수로 넘어갈 때 언제나 1개 비트만 바뀐다**.

일반적인 2진법과 비교해보자.

| 숫자 | 2진법 | 그레이 코드 |
|------|-------|-------------|
| 0    | 000   | 000         |
| 1    | 001   | 001         |
| 2    | 010   | 011         |
| 3    | 011   | 010         |
| 4    | 100   | 110         |
| 5    | 101   | 111         |
| 6    | 110   | 101         |
| 7    | 111   | 100         |

* 3 에서 4 가 될 때,
    * 2진법은 자리올림이 일어나며 `011`이 `100` 이 된다.
    * 그레이 코드는 언제나 1개 비트만 바뀌므로 `010`이 `110`이 된다.

# 생성 알고리즘

```
(1)  (2)  (3)   (4)
0    0    00    00
1    1    01    01
     1     1    11
     0     0    10
```

1. 0과 1을 수직으로 쓴다.
2. 아래쪽에 거울을 놓은 것처럼 반사하여(reflect), 1과 0을 반대 순서로 쓴다.
3. 이제 원래 있었던 처음 두 숫자의 왼쪽에 0을 쓴다.
4. 반대 순서로 쓴 1, 0의 왼쪽에 1을 쓴다.
5. 다음 자릿수도 위의 과정을 반복하면 만들 수 있다.

```
(4)  (5)  (6)   (7)
00   00   000   000
01   01   001   001
11   11   011   011
10   10   010   010
     10    10   110
     11    11   111
     01    01   101
     00    00   100
```

한편, 다음의 방법으로 1부터 19까지의 그레이 코드를 출력할 수 있다.

```go
for i := 0; i < 20; i++ {
    g := i ^ (i >> 1)
    fmt.Printf("%d : %b\n", i, g)
}
```

# 변환 공식

4비트 이진수 $$abcd$$ 와 그레이 코드 $$efgh$$ 가 있다고 하자.

변환 공식은 다음과 같다.

## 이진수를 그레이 코드로 변환

* 첫 번째 비트 : $$ e = a $$
* 두 번째 비트 : $$ f = a \oplus b $$
* 세 번째 비트 : $$ g = b \oplus c $$
* 네 번째 비트 : $$ h = c \oplus d $$

만약 이진수 `1010`을 그레이 코드로 변환한다면 다음과 같을 것이다.

| a | b | c | d |
| 1 | 0 | 1 | 0 |

* 첫 번째 비트 : $$ 1 $$
* 두 번째 비트 : $$ 1 \oplus 0 = 1$$
* 세 번째 비트 : $$ 0 \oplus 1 = 1$$
* 네 번째 비트 : $$ 1 \oplus 0 = 1$$

따라서 이진수 `1010`은 그레이 코드로는 `1111`이 된다.

* 이진수 `1010` : 십진수로 10.
* 그레이 코드 `1111` : 십진수로 10.

한편, 위의 공식을 잘 살펴보면 오른쪽으로 비트 쉬프트 한 다음, 본래 값과 논리합을 하면 끝난다는 것을 알 수 있다.

go 로 짜면 이런 함수가 나올 것이다.

```go
func toGrayCode(bin int) int {
    return bin ^ (bin >> 1)
}
```

정리하자면 다음과 같다.

$$
G \leftarrow B \oplus ( B \overset{u}{\gg} 1 )
$$

* 참고
    * $$\oplus$$ : 배타적 논리합(XOR).
    * $$x \overset{u}{\gg} y $$ : 오른쪽 자리이동. 반대편에 0 채움.
    * $$x \ll y $$ : 왼쪽 자리이동. 반대편에 0을 채운다.
    * $$x \overset{s}{\gg} y $$ : 오른쪽 자리이동. 부호 채움.
    * $$x \overset{rot}{\gg} y $$ : 순환식 오른쪽 자리이동.
    * $$x \overset{rot}{\ll} y $$ : 순환식 왼쪽 자리이동.

## 그레이 코드를 이진수로 변환

* 첫 번째 비트 : $$ a = e $$
* 두 번째 비트 : $$ b = e \oplus f $$
* 세 번째 비트 : $$ c = e \oplus f \oplus g $$
* 네 번째 비트 : $$ d = e \oplus f \oplus g \oplus h $$

만약 그레이 코드 `1010`을 이진수로 변환한다면 다음과 같을 것이다.

| e | f | g | h |
| 1 | 0 | 1 | 0 |

* 첫 번째 비트 : $$ 1 $$
* 두 번째 비트 : $$ 1 \oplus 0 = 1$$
* 세 번째 비트 : $$ 1 \oplus 0 \oplus 1 = 0$$
* 네 번째 비트 : $$ 1 \oplus 0 \oplus 1 \oplus 0 = 0$$

따라서 그레이 코드 `1010`은 이진수로는 `1100`이 된다.

* 이진수 `1100` : 십진수로 12.
* 그레이 코드 `1010` : 십진수로 12.

한편, 위의 공식을 잘 살펴보면 왼쪽부터의 논리합 누적 값을 계산하면 된다는 것을 알 수 있다.

go 로 짜면 다음과 같은 함수가 나올 것이다.

```go
import "math"

func toBin(gray int) int {
    bitLength := 1 + int(math.Log2(float64(gray)))

    for i := 0; i < bitLength; i++ {
        gray ^= (gray >> uint(i))
    }
    return gray
}
```

정리하자면 다음과 같다.

$$
B \leftarrow \overset{n - 1}{\underset{i = 0}{\oplus}} G \overset{u}\gg i
$$

그런데 이 공식은 워드의 패리티 계산 방법과 똑같다.

따라서 위의 `toBin`함수는 다음과 같이 최적화할 수 있다.

```go
func toBin(gray int) int {
    bin := gray ^ (gray >> 1)
    bin = bin ^ (bin >> 2)
    bin = bin ^ (bin >> 4)
    bin = bin ^ (bin >> 8)
    bin = bin ^ (bin >> 16)
    return bin
}
```

# 참고문헌

* 해커의 기쁨. 비트와 바이트 그리고 알고리즘 (2판) / 헨리 워렌 지음 / 류광 옮김 / 제이펍 / 2013년 07월 22일 출간

# Links

* [The Aggregate Magic Algorithms](http://aggregate.org/MAGIC/#Gray%20Code%20Conversion )
* [Gray code (wikipedia)](https://en.wikipedia.org/wiki/Gray_code)

