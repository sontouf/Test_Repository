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



#### C의 상속에 관한 첫 번째 접근법

두 가지 접근법에서 모두 행위 함수가 있는 같은 클래스 Student 와 Person 을 가지며, main 함수에서 간단한 시나리오를 보여주는 같는 객체를 갖는다.

첫 번째 접근법의 예제에서 Student 클래스는 Person 클래스의 속성 구조체에 대한 실제 비공개 정의에 접근할 수 있어야 한다. 다음 코드 박스들은 main 함수와 더불어 student 및 person 클래스에 대한 헤더와 소스파일을 차례로 나타낸다. Person 클래스를 선언하는 헤더파일부터 시작해보자.

```c
// person.h
#ifndef PERSON_H
#define PERSON_H

// 전방 선언
struct person_t;

// 메모리 할당자
struct person_t* person_new();

// 생성자
void person_ctor(struct person_t*, const char*, const char*, unsigned int);

// 소멸자
void person_dtor(struct person_t*);

// 행위 함수
void person_get_first_name(struct person_t*, char*);
void person_get_last_name(struct person_t*, char*);
unsigned int person_get_birth_year(struct person_t*);

#endif
```

위 코드 박스의 생성자 함수를 보자. 이 함수는 person 객체를 생성하는 데 필요한 모든 값을 받는다. first_name, second_name, birth_year 가 이에 해당한다. 속성 구조체 person_t 가 불완전하므로 student 클래스는 이 헤더 파일로 상속 관계를 만들 수 없다.

 한편, 이 헤더 파일은 속성 구조체 person_t 에 관한 실제 정의를 포함해서는 안된다. 이 헤더는 person 의 내부를 알지 못하는 코드에 쓰일 것이기 때문이다. 그러면 어떻게 해야 할까? 다른 코드가 알면 안되는 구조체 정의를 로직의 특정 부분이 알도록 만들고 싶다. 바로 여기가 비공개 헤더 파일이 등장하는 부분이다.

비공개 헤더 파일은 이 헤더가 실제로 필요한 코드의 특정 부분이나 특정 클래스에 포함되어 쓰이는 일반적인 헤더 파일이다. person_t 의 실제 정의가 반드시 비공개 헤더에 있어야 한다.

```c
//person_p.h

#ifndef PERSON_P_H
#define PERSON_P_H

// 비공개 정의
typedef struct {
    char first_name[32];
    char last_name[32];
    unsigned int birth_year;
} person_t;

#endif
```

이 헤더 파일은 오직 person_t 구조체에 대한 정의만 포함할 뿐이다. 이는 Person 클래스의 일부분으로, 비공개 상태여야 하지만 Student 클래스에는 공개되어야 한다. student_t 속성 구조체를 정의하려면 이 정의가 필요하다.



```c
// person.c
#include <stdlib.h>
#include <string.h>

// person_t 는 다음 헤더 파일에 정의되어 있다.
#include "person_p.h"

// 메모리 할당자
person_t* person_new() {
    return (person_t*)malloc(sizeof(person_t));
}

// 생성자
void person_ctor(person_t* person, const char* first_name, const char* last_name, unsigned int birth_year) {
    strcpy(person->first_name, first_name);
    strcpy(person->last_name, last_name);
    person->birth_year = birth_year;
}

// 소멸자
void person_dtor(person_t* person) {
    // 할 일 없음
}

// 행위 함수
void person_get_first_name(person_t* person, char* buffer) {
    strcpy(buffer, person->first_name);
}

void person_get_last_name(person_t* person, char* buffer) {
    strcpy(buffer, person->last_name);
}

unsigned int person_get_birth_year(person_t* person) {
    return person->birth_year;
}
```

Person 클래스의 정의는 그리 특별할 것이 없다. 이전의 모든 예제와 같다.

```c
// student.h

#ifndef STUDENT_H
#define STUDENT_H

// 전방 선언
struct student_t;

// 메모리 할당자
struct student_t* student_new();

// 생성자
void student_ctor(struct student_t*, const char*, const char*, unsigned int, const char*, unsigned int);

// 소멸자
void student_dtor(struct student_t*);

// 행위 함수
void student_get_student_number(struct student_t*, char*);
unsigned int student_get_passed_credits(struct student_t*);

#endif
```

 클래스의 생성자는 Person 클래스의 생성자와 비슷한 인자를 받는다. student 객체가 실제로 person 객체를 포함하기 떄문이며, 또한 합성된 person 객체를 추가하는 값이 필요하기 때문이다. 즉, student 생성자가 student 의 person 부분에 대한 속성을 설정해야 한다는 의미이다.

Student 클래스에는 단 2개의 행위 함수만 있다는 점에 주목하자.  student 객체에서도 Person 클래스의 행위 함수를 사용할 수 있기 때문이다.

```c
// student.c
#include <stdlid.h>
#include <string.h>

#include "person.h"

// person_t 는 다음 헤더 파일에 정의되어 있으며 여기에서 필요하다.
#include "person_p.h"

// 전방 선언
typedef struct {
    // 여기에서 person 클래스에서 모든 속성을 상속 받으며 또한 이 중첩으로 인해 person 클래스의 모든 행위 함수를 사용할 수 있다.
    person_t person;
    char* student_number;
    unsigned int passed_credits;
} student_t;

// 메모리 할당자
student_t* student_new() {
    return (student_t*)malloc(sizeof(student_t));
}

// 생성자
void student_ctor(student_t* student, const char* first_name, const char* last_name, unsigned int birth_year, const char* 							student_number, unsigned int passed_credits) {
    person_ctor((struct person_t)student, first_name, last_name, birth_year);
    student->student_name = (char*)malloc(16 * sizeof(char));
    strcpy(student->student_number, student_number);
	student->passed_credits = passed_credits;
}

// 소멸자
void student_dtor(student_t* student) {
    // 자식 객체를 먼저 소멸시켜야 한다.
    free(student->student_number);
    // 그런 다음, 부모 클래스의 소멸자 함수를 호출해야 한다.
    person_dtor((struct person_t*)student);
}

// 행위 함수
void student_get_student_number(student_t* student, char* buffer) {
    strcpy(buffer, student->student_number);
}

unsigned int student_get_passed_credits(student_t* student) {
    return student->passed_credits;
}
```

위 코드박스는 상속 관계에서 가장 중요한 코드를 담고 있다. 우선 person 클래스의 비공개 헤더를 포함해야 한다. student_t 를 정의하는 부분에서 person_t 자료형으로부터 첫 번째 필드를 갖고자 했기 때문이다. 그리고 해당 필드는 포인터가 아닌 실제 변수이므로 person_t 이미 정의되어 있어야 한다. 이 변수는 반드시 구조체의 첫 번째 필드여야 한다. 그렇지 않으면 Person 클래스의 행위 함수를 사용하지 못하게 된다.

다시 돌아와서, 코드박스에서는 부모의 객체에 대한 속성을 초기화하고자 student 클래스의 생성자에서 부모의 생성자를 호출한다. person_ctor 함수로 student_t 포인터를 전달할 때 student_t 포인터를 person_t 포인터로 어떻게 변환했는지 보자. person 필드가 student_t 의 첫 번째 멤버이기 가능했다.

생성자와 마찬가지로 student 클래스의 소멸자에서는 부모 소멸자를 호출했다. 소멸은 먼저 자식 수준에서, 그다음으로 부모 수준에서 일어나야 한다. 생성과는 정반대 순서이다. 다음은 메인 시나리오를 포함한다. 여기서는 student 클래스를 사용하며 student 형의 객체를 생성하다.



```c
// main.c
#include <stdio.h>
#include <stdlib.h>

#include "person.h"
#include "student.h"

int main(int argc, char** argv)
{
    // student 객체를 만들고 생성하기
    struct student_t* student = student_new();
    student_ctor(student, "John", "Doe", 1987, "TA5667", 134);
    
    // 이제 person 의 속성을 student 객체에서 읽기 위해 person 의 행위 함수를 사용한다.
    char buffer[32];
    
    // 부모의 자료형에 대한 포인터로 업캐스팅하기
    struct person_t* person_ptr = (struct person_t*)student;
    
    person_get_first_name(person_ptr, buffer);
    printf("First name: %s\n", buffer);
    
    person_get_last_name(person_ptr, buffer);
    printf("Last name: %s\n", buffer);
    
    printf("Birth year: %d\n", person_get_birth_year(person_ptr));
    
    // 이제 student 객체에 한정된 속성을 읽는다.
    student_get_student_number(student, buffer);
    printf("Student number: %s\n", buffer);
    
    printf("Passed credits: %d\n", student_get_passed_credits(student));
    
    // student 객체를 소멸 및 해제하기
    student_dtor(student);
    free(student);
    
    return 0;
}
```



#### C의 상속에 관한 두 번째 접근법

첫 번째 방법을 사용해 자식의 속성 구조체 내부에 첫 번째 필드로 구조체 변수를 두었다. 이제 두 번째 접근 방법을 사용해서 부모의 구조체 변수를 가리키는 포인터를 두어보자. 이 방식으로 자식 클래스는 부모 클래스의 구현과 독립적일 수 있다. 이는 정보 은닉을 고려했을 때 좋은 방법이다. 

두 번째 접근법을 선택하면 이점도 있지만, 잃는 것도 있다. 이 방식을 통해 각 기법을 사용하는 장단점을 알게 된다.

출력결과는 실제 결과의 측면에서 비슷하다. 하지만 student 클래스는 person 클래스의 공용 인터페이스에만 의존할 뿐 비공개 정의에는 의존하지 않는다는 점이 주된 차이이다. 이는 클래스를 분리하는 것이고, 그러면 자식 클래스의 구현을 변경하지 않고도 부모 클래스의 구현을 쉽게 바꿀 수 있으므로 아주 유용하다.

Student 클래스는 엄밀히 말해 정보 은닉 원리를 위반하지는 않았다. 하지만 person_t 의 실제 정의 및 person_t 의 필드에 접근했으므로 정보 은닉 원리를 위반할 수도 있다. 이에 접근한 결과로 Person 의 행위 함수를 사용하지 않고도 필드를 읽거나 수정할 수 있다.

하지만 몇 가지 근본적인 차이가 있다. person 클래스는 같은 공용 인터페이스를 갖는다. 하지만 Student 클래스의 경우에는 다르다. Student 클래스의 공용 인터페이스는 변경되어야 한다. 



```c
// student.h

#ifndef STUDENT_H
#define STUDENT_H

// 전방 선언
struct student_t;

// 메모리 할당자
struct student_ctor(struct student_t*, const char*, const char*, unsigned int, const char*, unsigned int);

// 소멸자
void student_dtor(struct student_t*);

// 행위 함수
void student_get_first_name(struct student_t*, char*);
void student_get_last_name(struct student_t*, char*);
unsigned int student_get_birth_year(struct student_t*, char*);
void student_get_student_number(struct student_t*. char*);
unsigned int student_get_passed_credits(struct student_t*);

#endif
```

Student 클래스는 Person 클래스에서 선언한 모든 행위 함수를 반복해야 한다. 그 이유는  student_t 포인터를 person_t 포인터로 더 이상 변환할 수  없기 때문이다. 즉, Student 및 Person 포인터에 대해 더 이상 업캐스팅을 할 수 없다.

```c
// person.c
#include <stdlib.h>
#include <string.h>

// person_t 는 다음 헤더 파일에 정의되어 있다.
typedef struct {
    char first_name[32];
    char last_name[32];
    unsigned int birth_year;
} person_t;

// 메모리 할당자
person_t* person_new() {
    return (person_t*)malloc(sizeof(person_t));
}

// 생성자
void person_ctor(person_t* person, const char* first_name, const char* last_name, unsigned int birth_year) {
    strcpy(person->first_name, first_name);
    strcpy(person->last_name, last_name);
    person->birth_year = birth_year;
}

// 소멸자
void person_dtor(person_t* person) {
    // 할 일 없음
}

// 행위 함수
void person_get_first_name(person_t* person, char* buffer) {
    strcpy(buffer, person->first_name);
}

void person_get_last_name(person_t* person, char* buffer) {
    strcpy(buffer, person->last_name);
}

unsigned int person_get_birth_year(person_t* person) {
    return person->birth_year;
}

```

person_t 의 비공개 정의는 소스 파일 내부에 있으며 더 이상 비공개 헤더 파일이 사용되지 않는다. Student 클래스와 같은 다른 클래스로 정의를 전혀 공유하지 않겠다는 의미이다. Person 클래스에 대한 완전한 캡슐화를 수행해 구현의 세부사항을 은닉한다.



```c
// student.c
#include <stdlid.h>
#include <string.h>

#include "person.h"

// 전방 선언
typedef struct {
    char* student_number;
    unsigned int passed_credits;
    struct person_t* person;
} student_t;

// 메모리 할당자
student_t* student_new() {
    return (student_t*)malloc(sizeof(student_t));
}

// 생성자
void student_ctor(student_t* student, const char* first_name, const char* last_name, unsigned int birth_year, const char* 							student_number, unsigned int passed_credits) {
    student->person = person_new();
    person_ctor(student->person, first_name, last_name, birth_year);
    student->student_name = (char*)malloc(16 * sizeof(char));
    strcpy(student->student_number, student_number);
	student->passed_credits = passed_credits;
}

// 소멸자
void student_dtor(student_t* student) {
    // 자식 객체를 먼저 소멸시켜야 한다.
    free(student->student_number);
    // 그런 다음, 부모 클래스의 소멸자 함수를 호출해야 한다.
    person_dtor(student->person);
    free(student->person);
}

// 행위 함수
void student_get_last_name(student_t* student, char* buffer) {
    // person 행위 함수를 사용해야 한다.
    person_get_first_name(student->person, buffer);
}

void student_get_last_name(student_t* student, char* buffer) {
    // person 행위 함수를 사용해야 한다.
    person_get_last_name(student->person, buffer);
}

unsigned int student_get_birth_year(student_t* student) {
    // person 행위 함수를 사용해야 한다.
   	return person_get_birth_year(student->person);
}

void student_get_student_number(student_t* student, char* buffer) {
    strcpy(buffer, student->student_number);
}

unsigned int student_get_passed_credits(student_t* student) {
    return student->passed_credits;
}
```

코드박스에서 나타나 있듯 Person 클래스의 공용 인터페이스를 헤더 파일에 포함해 사용했다. 이에 더해 student_t 의 정의 부분에 포인터 필드를 추가했다. 이 포인터 필드는 부모인 Person 객체를 가리킨다. 이를 보면 7장에서 했던 합성 관계의 구현이 생각날 것이다.

참고로 속성 구조체에서 이 포인터 필드가 첫 번쨰 항목이 될 필요는 없다. 첫 번째 접근법에서 봤던 것과는 대조적이다. student_t 와 person_t 형 포인터는 더 이상 상호변환할 수 없고, 메모리에서 다른 주소를 가리키며 인접한 주소도 아니다. 다시 한번 말하지만 앞의 접근법과는 대조적이다.

Student 클래스의 생성자에서 부모 객체를 인스턴스화했다는 점을 주목하자. 그런 다음 person 클래스의 생성자를 호출하고 여기에 필요한 매개 변수를 전달해 부모 객체를 생성했다. 소멸자에서도 마찬가지이며 Student 클래스의 소멸자에서 부모 객체를 마지막으로 소멸시킨다.

상속 받은 속성을 읽기 위해 Person 클래스의 행위를 사용할 수 없으므로 Student 클래스는 상속받은 속성 및 비공개 속성을 노출하기 위해 자신의 행위 함수들을 노출해야 한다.

즉, Student 클래스 내부에 있는 부모은 person 객체의 비공개 속성을 노출하기 위해 Student 클래스는 래퍼 함수를 노출해야 한다. Student 객체 그 자체로는 person 객체의 비공개 속성에 대해 아무것도 알지 못하며, 이는 첫 번째 접근법에서 봤던 것과는 다르므로 주의하자.



```c
// main.c
#include <stdio.h>
#include <stdlib.h>

#include "student.h"

int main(int argc, char** argv)
{
    // student 객체를 만들고 생성하기
    struct student_t* student = student_new();
    student_ctor(student, "John", "Doe", 1987, "TA5667", 134);
    
    //student 포인터는 person 포인터가 아니며 student 객체에 있는 비공개 부모 포인터에 접근할 수 없으므로, student 행위 함수를 사용해야 한다.
    char buffer[32];
    student_get_first_name(student, buffer);
    printf("First name: %s\n", buffer);
    
    student_get_last_name(student, buffer);
    printf("Last name: %s\n", buffer);
    
    printf("Birth year: %d\n", student_get_birth_year(person_ptr));
    
    student_get_student_number(student, buffer);
    printf("Student number: %s\n", buffer);
    
    printf("Passed credits: %d\n", student_get_passed_credits(student));
    
    // student 객체를 소멸 및 해제하기
    student_dtor(student);
    free(student);
    
    return 0;
}
```

이전 main 함수와 비교해보면 person 클래스의 공용 인터페이스를 포함하지 않았다. 또한 student 클래스의 행위 함수를 사용해야 했는데, student_t 포인터와 person_t 더 이상 상호 변환할 수 없기 때문이다.



#### 두 가지 접근법 비교하기

C 에서 상속을 구현할 때 취할 수 있는 두 접근법을 살펴봤으니 이제 이들을 비교할 수 있다.

- 두 접근법은 본질적으로 합성 관계를 나타낸다.
- 첫 번째 접근법에서는 자식의 속성 구조체 안에 구조체 변수를 둔다. 그리고 부모 클래스가 비공개 구현에 접근하는 데에 의존한다. 하지만 두 번째 접근법에서는 부모의 속성 구조체의 불완전 자료형으로부터 구조체 포인터를 두므로 부모 클래스의 비공개 구현에 의존하지 않는다.
- 첫 번째 접근법에서 부모와 자식의 자료형은 매우 의존적이다. 두 번째 접근법에서 클래스들은 서로에 대해 독립적이며, 부모의 구현 내부에 있는 모든 것은 자식에게 숨겨져 있다.
- 첫 번째 접근법에서는 부모가 오직 하나만 있어야 한다. 즉, C의 단일 상속을 구현하는 방식이다. 하지만 두 번째 접근법은 원하는 만큼 많은 부모를 가질 수 있으므로 다중 상속이라는 개념을 나타낸다.
- 첫 번째 접근법에서 부모의 구조체 변수는 자식 클래스의 속성 구조체의 첫 번째 필드여야 한다. 하지만 두 번째 접근법에서는 부모 객체에 대한 포인터를 구조체 내의 어디든 두어도 된다.
- 첫 번째 접근법에서 부모 및 자식 객체가 분리된 경우는 없었다. 부모 객체는 자식 객체에 포함되었으며, 자식 객체를 가리키는 포인터는 실제로 부모 객체를 가리키는 포인터였다.
- 첫 번째 접근법에서는 부모 클래스의 행위 함수를 사용할  수 있었다. 하지만 두 번쨰 접근법에서는 자식 클래스의 새로운 행위 함수를 통해 부모의 행위 함수를 전달해야 한다.

지금까지 상속 자체에 관해서만 설명했으며 상속의 사용법은 다루지 않았다. 상속을 사용할 때 가장 중요한 점은 객체 모델에서 다형성을 갖는 일이다. 다음 절에서는 다형성 및 C에서 다형성을 구현하는 방법을 설명하겠다.





