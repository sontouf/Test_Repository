## 전문가를 위한 C

# Chapter 1 필수요소

## 1.1 전처리기 지시자

전처리`preprocessing` 는 C의 강력한 구성요소이다.

일단은 컴파일러로 보내기 전에 소스코드를 만들고 수정할 수 있도록 하는 과정을 `전처리`라고 하자.

다른 프로그래밍 언어는 컴파일러로 소스코드가 바로 전달되지만 c와 C++ 같은 언어는 먼저 전처리를 해야 한다.

```c
#define

int main(int argc, char** argv)
{
    int x = 2;
    int y = ABC; 
    int z = x + y;
    return 0;
}
```

