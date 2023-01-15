## 전문가를 위한 C

# Chapter 1 필수요소

## 1.1 전처리기 지시자

전처리`preprocessing` 는 C의 강력한 구성요소이다.

일단은 컴파일러로 보내기 전에 소스코드를 만들고 수정할 수 있도록 하는 과정을 `전처리`라고 하자.

다른 프로그래밍 언어는 컴파일러로 소스코드가 바로 전달되지만 c와 C++ 같은 언어는 먼저 전처리를 해야 한다.

	전처리라는 추가 단계는 컴파일러로 소스코드를 보내기 전 C 개발자가 효과적으로 소스코드를 바꿀 수 있다는 점에서 C를 독특한 프로그래밍 언어로 만들었다. 전처리요소는 대부분의 고급 프로그래밍 언어에서는 존재하지 않는다.

```
전처리의 목적은 전처리 지시자를 제거하고 이 지시자를 C 코드에 의해 생성된 동일한 내용으로 바꾸는 것이다.또한 컴파일러로 보낼 준비가 된 최종 소스 파일을 준비하려는 목적도 있다.
```

 전처리 수행은 일련의 `지시자`를 사용해서 관리되는데 헤더와 소스 파일 모두에서 # 문자로 시작하는 코드이다. 이코드는 C 전처리기에만 의미가 있을 뿐  C 컴파일러에는 의미가 없다.



### 1.1.1매크로

매크로는 다음과 같이 다양하게 활용할 수 있다.

```
- 상수 정의하기
- C 함수를 작성하지 않고 함수로 사용하기
- 루프 풀기 loop unrolling
- 헤더 가드 header guard
- 코드 생성
- 조건부 컴파일
```

#### 매크로 정의하기

매크로는 `#define` 지시자를 이용해 정의한다. 각 매크로는 이름과 사용 가능한 매개변수 리스트를 갖는다. 또한 값을 가지며, 이 값은 `매크로 확장`이라는 단계를 통해 전처리 단계에서 매크로의 이름으로 대체될 수 있다. 매크로는 `#undef` 지시자로 매크로의 정의를 제거할 수 있다. 

```c
// 매크로 정의하기
#define ABC 5

int main(int argc, char** argv)
{
    int x = 2;
    int y = ABC; 
    int z = x + y;
    return 0;
}
```

ABC는 정숫값이나 정수 상수를 담는 변수가 아니다. 이는 ABC라는 매크로이며 해당하는 값은 5이다. 매크로 확장 단계 이후 C 컴파일러로 보낸 코드의 결과물은 다음과 같다.

```C
int main(int argc, char **argv)
{
    int x = 2;
    int y = 5;
    int z = x + y;
    return 0;
}
```

앞의 예제에서 전처리기는 매크로 확장을 수행해서 매크로의 이름을 값으로 간단히 바꾸었다. 또한 시작하는 줄의 #define 부분도 삭제됐다.

	또 다른 예제를 보자

```c
#define ADD(a, b) a + b

int main(int argc, char **argv)
{
    int x = 2;
    int y = 3;
    int z = ADD(x, y);
    return 0;
}
```

이 코드에서 ADD 는 함수가 아니다. 이는 인수를 받는 `유사 함수 매크로 function-like macro`이다. 전처리 이후의 코드는 다음과 같다.

```c
int main(int argc, char **argv)
{
    int x = 2;
    int y = 3;
    int z = x + y;
    return 0;
}
```

위에서 볼 수 있듯이 매크로 확장이 이루어지는 방식은 다음과 같다. 매개변수 a로 사용된 인수 x 는 매크로 값에 있는 a 의 인스턴스로 모두 바뀌었다. 매개변수 b도 마찬가지이며 b에 해당하는 y 인수에서도 그렇다. 모두 변환된 이후 전처리된 코드에서는 ADD(a, b) 대신 x + y를 얻는다.

	유사함수매크로는 입력 인수를 받을 수 있기 때문에 C 함수를 모방할 수 있다. 즉, 자주 사용되는 로직을 C 함수에 넣는 것  대신, 이 로직을 유사함수매크로로 명명해 사용할 수 있다.



`매크로는 컴파일 단계 이전에만 존재한다. 즉, 컴파일러가 이론적으로는 매크로에 관해 아무것도 모른다는 의미이다.` 컴파일러는 함수의 모든 것을 알고 있다. 함수는 C 문법의 일부이며 파스 트리`parse tree`내에서 함수가 분석되기 때문이다. 그러나 매크로는 전처리기 자체에 관해서만 알고 있는, C의 전처리기 지시자에 불과하다.

 	사실 현대의 컴파일러는 C 전처리기 지시자를 인지하고 있다.  참고로 대부분의 현대 컴파일러는 컴파일 직전에 전처리 결과를 볼 수 있다. 예를 들어 gcc 나 clang 을 사용할 경우 전처리 이후 코드를 덤프`dump` 하기 위해 `-E`옵션을 사용할 수 있다. 
 	
 	`변환단위 translation unit (혹은 컴파일 단위 compilation unit)`는 컴파일러로 전달될 준비가 된, 전처리된 C 코드이다. 변환 단위에서는 모든 지시자가 포함되거나 매크로 확장으로 대체되며 단 한 줄의 긴 C코드가 만들어진다.

```c
#include <stdio.h>

#define PRINT(a) printf("%d\n", a);
#define LOOP(v, s, e) for(int v = s; v <= e; v++) {
#define ENDLOOP }

int main(int argc, char **argv) {
    LOOP(counter, 1, 10)
        PRINT(counter)
    ENDLOOP
    return 0;
}
```

```c
...
... content of stdio.h ...
...
int main(int argc, char **argv)
{
    for(int counter = 1; counter <= 10; counter++) {
        printf("%d\n", counter);
    }
	return 0;
}
```

 전처리 이후 위 코드는 잘 작동되는 올바른 프로그램이다. 이는 `도메인 특화 언어 domain specific language (DSL)`를 정의하고 DSL을 이용해 코드를 작성하는 매크로의 중요한 활용법이다.

최종 전처리된 코드에는 C 지시자가 전혀 없다는 사실을 눈여겨봐야 한다. 이는 #include 지시자가 자신이 가리키는 파일의 내용으로 대체되엇다는 뜻이다.



	다음은 매크로 매개변수에 관한 새로운 두 가지 연산를 소개한다. # 과 ## 연산자이다.

```c
#include <stdio.h>
#include <string.h>

#define CMD(NAME) \
	char NAME ## _cmd[256] = ""; \
	strcpy(NAME ## _cmd, #NAME);

int main(int argc, char **argv) {
    
    CMD(copy)
    CMD(paste)
    CMD(cut)
        
    char cmd[256];
    scanf("%s", cmd);
    
    if (strcmp(cmd, copy_cmd) == 0) {
        // ...
    }
    if (strcmp(cmd, paste_cmd) == 0) {
        // ...
    }
    if (strcmp(cmd, cut_cmd) == 0) {
        // ...
    }
    return 0;
}
```

매크로를 확장할 때 `#` 연산자는 매개변수를 한 쌍의 따옴표로 둘러싼 문자 형태로 변환한다. 예를 들어 앞의 코드에서 NAME 매개변수 앞에 쓰인 # 연산자는 전처리된 코드에서 NAME 을 "copy"로 변환한다.

`##` 연산자는 다른 의미가 있다. 이 연산자는 매크로 정의에서 매개변수와 다른 요소를 문자열로 결합해 변수이름을 만든다.

```c
#define CMD(NAME) \
	char NAME ## _cmd[256] = ""; \
	strcpy(NAME ## _cmd, #NAME);

int main(int argc, char **argv) {
    
    char copy_cmd[256] = ""; strcpy(copy_cmd, "copy");
    char paste_cmd[256] = ""; strcpy(paste_cmd, "paste");
    char cut_cmd[256] = ""; strcpy(cut_cmd, "cut");        
    char cmd[256];
    scanf("%s", cmd);
    if (strcmp(cmd, copy_cmd) == 0) {
        // ...
    }
    if (strcmp(cmd, paste_cmd) == 0) {
        // ...
    }
    if (strcmp(cmd, cut_cmd) == 0) {
        // ...
    }
    return 0;
}
```

전처리된 최종 코드에서 같은 매크로 정의로부터 확장된 모든 행은 같은 행에 위치한다.

다음 절은 `가변 인자 매크로 variable macro`에 관한 것으로 가변 인자 매크로는 가변 인수를 받을 수 있다.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define VERSION "2.3.4"

#define LOG_ERROR(format, ...) \
	fprintf(stderr, format, __VA_ARGS__)

int main(int argc, char **argv)
{
    if (argc < 3)
    	LOG_ERROR("Invalid number of arguments for version %s\n.", VERSION);
    	exit(1);
	}
    if (strcmp(argv[1], "-n") != 0) {
        LOG_ERROR("%s is a wrong param at index %d for version %s.", \
                  argv[1], 1, VERSION);
        exit(1);
    }
    // ...
    return 0;
}
```

 이 코드 박스에 새로운 식별자인 `__VA_ARGS__`가 보인다. 이 식별자는 아직 매개변수에 할당되지 않은 나머지 입력 인수로 모두 교체하도록 전처리기로 지시한다.

추가로 `fprinf` 함수는 __파일서술자__를 작성한다. 위 상황에서 파일 서술자는 `stderr`이며, 이는 프로세스의 오류 스트림이다.

```c
...
... content of stdio.h ...
...
...
... content of stdlib.h ...
...
...
... content of string.h ...
...
int main(int argc, char **argv)
{
    if (argc < 3)
        fprintf(stderr, "Invalid number of arguments for version %s\n.", "2.3.4")
    	exit(1);
	}
    if (strcmp(argv[1], "-n") != 0) {
        fprintf(stderr, "%s is a wrong param at index %d for version %s.",\
                argv[1], 1, "2.3.4");
        exit(1);
    }
    // ...
    return 0;
}
```

 루프를 모방한 가변 인자 매크로 예제를 보자

```c
#include <stdio.h>

#define LOOP_3(X, ...) printf("%s\n", #X);
#define LOOP_2(X, ...) printf("%s\n", #X); LOOP_3(__VA_ARGS__)
#define LOOP_1(X, ...) printf("%s\n", #X); LOOP_2(__VA_ARGS__)
#define LOOP(X, ...) printf("%s\n", #X); LOOP_1(__VA_ARGS__)

int main(int argc, char** argv)
{
    LOOP(copy paste cut)
    LOOP(copy, paste, cut)
    LOOP(copy, paste, cut, select)
        
    return 0;
}
```

```c
...
... content of stdio.h ...
...
int main(int argc, char** argv)
{
    printf("%s\n", copy paste cut); printf("%s\n", ""); printf("%s\n", "");
    printf("%s\n", "copy"); printf("%s\n", "paste"); printf("%s\n", "cut");
    printf("%s\n", "copy"); printf("%s\n", "paste"); printf("%s\n", "cut");
        
    return 0;
}
```

 전처리된 코드를 주의 깊게 살펴보면, LOOP 매크로가 for나 while 같은 루프 명령어 대신 여러 개의 printf 지시어로 확장되엇다는 것을 알 수 있다. 이는 확실히 전처리기가 c 코드를 똑똑하게 작성하지 않았기 때문이다. 최종 코드에는 어떠한 c 루프도 존재하지 않게 된다. 이러한 방식은 이진 파일 크기가 커지므로 단점이라고 볼 수 있지만, 명령어를 루프에 넣지 않고 하나하나 두는 것은 `루프풀기`라고도 알려져 있다. 루프 풀기는 제한된 환경에서 고성능을 요하는 경우에 나름의 용도가 있다.

#### 매크로의 장점과 단점

매크로를 남발하더라도 우리에게는 큰 문제가 되지 않지만 동료에게는 문제가 될 수 있다. 매크로에는 중요한 특성이 있다. 매크로에 무언가를 작성할 경우, 매크로에 쓰인 것은 컴파일 단계 이전에 다른 코드로 교체 된다. 그리고 컴파일을 거치고 나면 모듈성을 전혀 갖지 않는, 단 한 줄로 긴 코드를 얻게 된다. 모듈성을 고려해서 매크로에 썼겠지만, 최종 이진 파일에는 존재하지 않게 된다. 이 부분이 바로 매크로 사용 시 설계 문제가 생기는 지점이다.

 하지만 매크로는 모든 것을 한 줄로 만들어버린다. 그러니 소프트웨어 설계 시 로직의 구성 요소로 매크로를 사용하게 되면, 최종 변환 단위의 일환인 전처리 단계 이후에 매크로와 관련된 정보를 잃어버릴 수 있다. 따라서 소프트웨어 아키텍트와 디자이너가 매크로에 관해서는 경험에 기반한 결정을 해야한다.

```
만약 매크로가 c 함수로 사용될 수 있다면, 매크로 대신에 c 함수로 반드시 작성해야 한다.
```

디버깅 관점에서도 매크로는 사악하다고 평가된다. 문법 오류가 발생한 곳을 찾기 위해 일상적으로 컴파일 오류를 이용하지만 이전의 c 컴파일러의 경우에서는 매크로를 알지 못해 개발자는 컴파일러가 보고하는 내용을 쉽게 이해할 수 없었다.

 다행히 오늘날의 c 컴파일러 덕분에 이 문제는 더 이상 심각한 이슈가 아니다.

### 1.1.2 조건부 컴파일

`조건부 컴파일 conditional copilation`은 c의 또 다른 고유한 특성이다. 조건부 컴파일을 거치면 서로 다른 조건에 기반해 서로 다른 전처리된 소스 코드를 갖게 된다. 비록 조건부라는 이름이 붙기는 하지만, 컴ㅂ파일러는 조건적으로 무언가를 하지 않는다.

`#ifdef #ifndef #else #elif #endif`

```C
// 조건부 컴파일 예
#define CONDITION

int main(int argc, char** argv)
{
#ifdef CONDITION
    int i = 0;
    i++;
#endif
    int j = 0;
    return 0;
}
```

CONDITION 매크로가 이미 정의되었으므로 #ifdef와 #endif 사이의 모든 줄은 최종 소스 코드에 복제된다.

```c
// 위 소스코드의 전처리 단계 이후
int main(int argc, char** argv)
{
    int i = 0;
    i++;
    
    int j = 0;
    return 0;
}
```

만약 매크로가 정의되지 않았다면 #ifdef와 #endif  지시자는 교체되지 않는다는 것을 알 수 있다. 

```c
// 위 소스코드의 전처리 단계 이후 (매크로가 정의되지 않았다면)
int main(int argc, char** argv)
{

    
    int j = 0;
    return 0;
}
```

 빈 줄이 남아있음을 유의하자

``` 
매크로는 컴파일 명령어에 전달되는 -D 옵션을 사용해 정의될 수 있다. 이는 소스 코드 바깥에서 매크로를 정의할 수 있기 때문에 훌륭한 기능이다.
```

`gcc -DCONDITION -E main.c`

`#ifndef (정의가 안 되었다면)`는 헤더가드 구문으로도 흔히 사용한다. 이 구문은 전처리 단계에서 헤더파일이 두 번 포함되는 것을 방지한다. 거의 모든 프로젝트에서 C와 C++헤더파일은 첫 명령어로 #ifndef 문을 갖는다.

```c
#ifndef MAIN_H
#define MAIN_H

void say();
int read_age();

#endif
```

첫번째로 헤더 파일이 포함될 때, MAIN_H 매크로는 아직 정의되지 않았으므로 전처리기는 계속해 #ifndef ~ #endif 블록으로 진입한다. 그 후에 헤더를 두번째로 포함하게 되면, MAIN_H 매크로가 이미 정의되었기 때문에 전처리기는 해당 부분을 건너뛰게 된다.

## 1.2 포인터 변수

### 1.2.1 문법

어떤 종류의 포인터라도 개념은 단순하다. `메모리주소`를 저장하는 단순한 변수일 뿐이다. 

```c
int main(int argc, char **argv)
{
    int var = 100;
    int * ptr;
    ptr = &var;
    *ptr = 200;
    
    return 0;
}
```

위 예제는 포인터 문법에 관해 알아야 할 모든 것을 담고 있다. main 함수의 첫 번째 줄에서는 `stack segment`의 맨 위에 놓은 var 변수를 선언한다. 두번째 줄은 초깃값이 0인 포인터 ptr을 선언한다. 0의 값을 갖는 포인터는 `NULL pointer`라고 한다. ptr 포인터의 값이 0인 한, 널 포인터로 간주한다. 선언 시 유효한 주소를 저장하지 않는 경우 포인터를 널로 만드는 것이 중요하다.

세번째 줄의 &은 참조연산자, 네번째 줄의 *은 역참조 연산자이다.

### 1.2.2 포인터 변수의 산술연산

메모리를 가장 단순한 그림으로 나타낸다면 아주 긴 1차원 바이트 배열이다. 그래서 포인터에 대한 연산은 바이트 배열에서의 움직임과 유사하다. 포인터를 1만큼 증가시키면, 메모리에서 1바이트 이상 앞으로 나아가게 되기 때문에 새로운 개념이 필요하다. 이러한 산술연산 간격은 포인터의 c `자료형 data type`에 의해 결정된다.

```c
#include <stdio.h>

#define SIZE 5

int main(int argc, char **argv)
{
    int arr[SIZE];
    arr[0] = 9;
    arr[1] = 22;
    arr[2] = 30;
    arr[3] = 23;
    arr[4] = 18;
    
    for (int i = 0; i< SIZE; i++) {
        printf("%d\n", arr[i]);
    }
    return 0;
}
```

 위 예제는 `인덱서 문법([와 ] 사이에 있는 정수)`를 이용해 원소에 접근하는 대신 포인터를 사용하려면 다른 방식으로 접근해야 한다.

```c
#include <stdio.h>

int main(int argc, char** argv)
{
    int arr[SIZE];
    arr[0] = 9;
    arr[1] = 22;
    arr[2] = 30;
    arr[3] = 23;
    arr[4] = 18;
    
    int * ptr = &arr[0];
    for(;;){
        printf("%d\n", ptr);
        if (ptr == &arr[SIZE - 1]) {
            break;
        }
        ptr++;
    }
    return 0;
}
```

 앞에서 명확히 알 수 있듯이 ptr 포인터는 `int*`자료형을 갖는다. 배열의 각 원소가 int 자료형이므로 정수를 가리킬 수 있어야 한다. 따라서 ptr 포인터를 증가시키면 포인터는 배열 내부의 다음 원소를 가리키게 된다. 그리고 c 배열이란 실제로는 자신의 첫 번째 원소를 가리키는 포인터이다. 따라선 예제에서 arr의 실제 자료형은 `int*`이다. 그러므로 코드는 `int* ptr = &arr[0]; `대신 `int* ptr = arr;`로 작성해야 한다.



### 1.2.3 제네릭 포인터

 void* 자료형의 포인터는 `제네릭 포인터`라고 한다. 다른 모든 포인터와 마찬가지로 제네릭 포인터도 주소를 가리킬 수 있지만, 실제 자료형은 알 수 없다. 그러므로 산술연산 간격도 알 수 없다. 제네릭 포인터는 다른 포인터의 내용을 담기 위해 주로 사용된다. 하지만 다른 포인터의 실제 자료형은 담지 않는다. 따라서 제네릭 포인터는 역참조될 수 없으며, 산술연산도 할 수 없다.

```c
// 해당 예제를 컴파일하게 되면 오류 메세지가 발생한다.
#include <stdio.h>

int main(int argc, char** argv)
{
    int var = 0;
    int* ptr = &var;
    void * gptr = ptr;
    printf("%d\n", *gptr);
    return 0;
}
```

역참조도 할 수 없는 제네릭 포인터는 어디에 좋다는 걸까? 제네릭 포인터를 이용하면 여러가지 포인터를 입력 인수로 받을 수 있는 `제네릭 함수`를 매우 편리하게 정의할 수 있다.
