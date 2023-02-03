# CHAPTER 8 상속과 다형성

6장과 7장에서는 C 에서 OOP 를 하는 방법을 설명하며 합성과 집합이라는 개념까지 도달했다. 계속해서 8장에서는 객체와 객체에 상응하는 개념인 클래스 간의 관계를 주로 설명하면서 상속과 다형성을 다룬다. 

이번 장은 6장과 7장에서 클래스 간 가능한 관계를 설며한 이론에 많이 의존한다. 6장과 7장에서는 __합성__ 과 __집합__ 관계를 설명했으며, 이번에는 __확정__ 및 __상속__ 관계에 대해 몇 가지 다른 주제와 함께 이야기해보자.

- 상속 관계는 처음 다루는 주제이다. C 에서 상속 관계를 구현하는 방법을 다루며 이러한 방법을 비교해보자
- 다음 큰 주제는 __다형성__ 이다. 클래스가 서로 상속 관계일 떄, 다형성을 통해 자식 클래스에서 같은 행위의 다른 버전을 만들 수 있다. C에서 다형적 함수를 만드는 방법을 서명한다. 이는 C++가 다형성을 제공하는 방식을 처음으로 이해하는 단계이다.



## 8.1 상속

앞서 to-have 관계를 설명하면서 7장을 마무리했다.  to-have 관계는 결국 합성과 집합관계로 이어졌다. 이번 절에서는 to-be 또는 is-a 관계를 설명한다. 상속관계는 to-be 관계이다.

상속 관계는 __확장 관계__ 이라고도 한다. 기존의 객체나 클래스에 속성과 행위를 추가할 뿐이기 때문이다. 상속이 무엇을 의미하는지 그리고 C에서 상속을 어떻게 구현하는 지는 다음 절에서 설명하겠다.

어떤 객체가 다른 객체에 존재하는 같은 속성을 가져야 하는 상황이 있다. 다시 말하면, 이러한 새 객체는 다른 객체를 확자한 것이다 . 예를 들면, 학생은 사람의 모든 속성을 갖고 있으나 다른 추가 속성을 가질 수도 있다.

```c
typedef struct {
    char first_name[32];
    char last_name[32];
    unsigned int birth_year;
} person_t;

typedef struct {
    char first_name[32];
    char last_name[32];
    unsigned int birth_year;
    char struct_number[16]; 	// 추가 속성
    unsigned int passed_credits // 추가 속성
} student_t;
```

이 예제는 student_t 가 person_t 속성을 student 에 한정된 속성인 student_number 와 passed_credits 라는 새로운 속성으로 확장하는 법을 명확하게 보여준다.

앞서 설명한 대로 상속은 to-be 관계이다. to-have 관계인 합성과 집합과는 다르다. 그러므로  이 예제에서 학생은 사람이다 라고 말할 수 있다. 이 말은 교육 소프트웨어의 도메인에서 옳은 것처럼 보인다. to-be 관계가 어떤 도메인에 존재할 때는 언제나 상속 관계일 것이다. 예제의 person_t 일반적으로 __슈퍼타입__ 또는 __베이스 타입__  , 또는 간단히 __부모 타입__ 이라고 한다. 그리고 __student_t__ 는 __자식 타입__ 또는 __상속받은 서브타입__ 이라고 한다.



### 8.1.1 상속의 본질

상속 관계가 실제로 무엇인지 깊이 파고들어가면 본질적으로는 합성관계이다. 예를 들어 학생은 학생 내부에 인간의 속성을 갖는다고 할 수 있다. 다시 말해 student 클래스의 속성 구조체 내부에 비공개 person 객체가 존재한다고 볼 수 있다. 즉, 상속 관계는 일대일 합성 관계에 해당한다.

```c
// Person 와 student 클래스 속성 구조체
typedef struct {
    char first_name[32];
    char last_name[32];
    unsigned int birth_year;
} person_t;

typedef struct {
    person_t person;
    char struct_number[16]; 	// 추가 속성
    unsigned int passed_credits // 추가 속성
} student_t;
```

이 구문은 C에서 전적으로 유효하며, 실제로 포인터가 아닌 구조체 변수를 사용해 구조체를 중첩하는 것은 강력한 문법이다. 이렇게 해서 이전 구조체를 상속한 새 구조체 안에 구조체 변수를 가질 수 있다.

첫 번째 필드에 person_t 형의 필드가 있어야 하는 이 설정을 이용하면 student_t 포인터를 person_t 포인터로 쉽게 변환할 수 있으며, 두 포인터 모두 메모리에서 같은 주소를 가리킬 수 있다.

이를 __업 캐스팅__ 이라고 한다. 다시 말해 자식의 속성 구조체의 자료형에 대한 포인터를 부모 클래스의 속성 구조체의 자료형을 변환하는 것을 업캐스팅이라고 한다. 참고로 구조체 변수에는 이런 기능이 없다.

```c
// student 와 person 의 객체 포인터 사이의 업캐스팅
# include <stdio.h>

typedef struct {
    char first_name[32];
    char last_name[32];
    unsigned int birth_year;
} person_t;

typedef struct {
    person_t person;
    char student_number[16];
    unsigned int passed_credits;
} student_t;

int main(int argc, char** argv) {
    student_t s;
    student_t* s_ptr = &s;
    person_t* p_ptr = (person_t*)&s;
    printf("Student pointer points to %p\n", (void*)s_ptr);
    printf("Person pointer points to %p\n", (void*)p_ptr);
    return 0;
}
```

s_ptr 과 p_ptr 포인터는 메모리에서 같은 주소를 가리킬 것으로 예상된다. 다음은 위 예제를 빌드하고 출력해본 결과이다.

```shell
$ gcc main.c -o exe.out
$ ./exe.out
Student pointer points to 0x7ffeecd41810
Person pointer points to 0x7ffeecd41810
$
```

예상대로 s_ptr 과 p_ptr은 같은 주소를 가리킨다. 참고로 실행할 때마다 주소는 다르게 나타날 수 있다. 하지만 포인터들은 동일 한 주소를 참조하고 있다는 것이 요점이다. 이는 student_t 형의 구조체 변수가 해당 메모리 레이아웃에서 실제로는 person_t 구조체를 상속한다는 의미이다. 그러므로 student 객체를 가리키는 포인터로 Person 클래스의 함수 행위를 사용할 수 있다. 즉, student 객체에서 Person 클래스의 행위 함수를 재사용할 수 있다. 이는 대단한 성과이다.



```c
// 컴파일되지 않는 상속 관계 만들기
struct person_t;

typedef struct {
    struct person_t person; // 오류 발생.
    char student_number[16];
    unsigned int passed_credits;
} student_t;
```

 person 필드를 선언한 행은 오류가 발생한다. __불완성한 형식__ 으로 변수를 생성할 수 없기 때문이다. 첫째 행과 비슷하게 구조체를 전방 선언하면, 불완전한 형식을 선언하게 된다는 점에 유의해야 한다. 이때 포인터는 불완전한 형식에 관한 것이지 변수에 관한 것이 아니다. 앞서 살펴봤듯 불완전한 형식에서 힙 메모리를 할당할 수도 없다.

그렇다면 이건 무슨 의미일까? 상속을 구현하기 위해 중첩된 구조체 변수를 사용하려면, 캡슐화에 대해 학습한 내용대로 student_t 구조체는 person_t 의 실제 정의를 알아야 하고, person_t 구조체는 비공개여야 하며 다른 클래스에는 보이지 않아야 한다.

- 자식 클래스가 베이스 클래스에 대한 비공개 구현에 접근할 수 있도록 하기
- 자식 클래스가 베이스 클래스의 공용 인터페이스에만 접근할 수 있도록 하기