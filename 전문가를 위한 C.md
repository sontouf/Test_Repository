## 전문가를 위한 C

# Chapter 1 필수요소

## 1.1 전처리기 지시자

전처리`preprocessing` 는 C의 강력한 구성요소이다.

일단은 컴파일러로 보내기 전에 소스코드를 만들고 수정할 수 있도록 하는 과정을 `전처리`라고 하자.

다른 프로그래밍 언어는 컴파일러로 소스코드가 바로 전달되지만 c와 C++ 같은 언어는 먼저 전처리를 해야 한다.

​	전처리라는 추가 단계는 컴파일러로 소스코드를 보내기 전 C 개발자가 효과적으로 소스코드를 바꿀 수 있다는 점에서 C를 독특한 프로그래밍 언어로 만들었다. 전처리요소는 대부분의 고급 프로그래밍 언어에서는 존재하지 않는다.

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

​	또 다른 예제를 보자

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

​	유사함수매크로는 입력 인수를 받을 수 있기 때문에 C 함수를 모방할 수 있다. 즉, 자주 사용되는 로직을 C 함수에 넣는 것  대신, 이 로직을 유사함수매크로로 명명해 사용할 수 있다.



`매크로는 컴파일 단계 이전에만 존재한다. 즉, 컴파일러가 이론적으로는 매크로에 관해 아무것도 모른다는 의미이다.` 컴파일러는 함수의 모든 것을 알고 있다. 함수는 C 문법의 일부이며 파스 트리`parse tree`내에서 함수가 분석되기 때문이다. 그러나 매크로는 전처리기 자체에 관해서만 알고 있는, C의 전처리기 지시자에 불과하다.

 	사실 현대의 컴파일러는 C 전처리기 지시자를 인지하고 있다.  참고로 대부분의 현대 컴파일러는 컴파일 직전에 전처리 결과를 볼 수 있다. 예를 들어 gcc 나 clang 을 사용할 경우 전처리 이후 코드를 덤프`dump` 하기 위해 `-E`옵션을 사용할 수 있다. 

​	`변환단위 translation unit (혹은 컴파일 단위 compilation unit)`는 컴파일러로 전달될 준비가 된, 전처리된 C 코드이다. 변환 단위에서는 모든 지시자가 포함되거나 매크로 확장으로 대체되며 단 한 줄의 긴 C코드가 만들어진다.

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



​	다음은 매크로 매개변수에 관한 새로운 두 가지 연산를 소개한다. # 과 ## 연산자이다.

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

