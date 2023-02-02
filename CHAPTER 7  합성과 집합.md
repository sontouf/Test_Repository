# CHAPTER 7  합성과 집합

앞 장에서 캡슐화와 정보 은닉을 다뤘다. 이번에는 계속해서 C의 객체지향을 살펴보고, 두 클래스 간에 존재하는 다양한 관계를 설명한다. 궁극적으로는 이를 통해 객체 모델을 확장하고 이어질 여러 장에서 객체 간의 관계를 표현할 수 있다.

- 두 객체 사이에 존재하는 관계의 유형 type 과 그에 해당하는 클래스 : to-have, to-be 두 가지 관계를 다룬다.
- to-have 관계 중 첫 번째인 합성: 두 클래스 사이의 실제 합성 관계를 나타내는 예제가 제공된다. 이 예제로 합성 시 일반적으로 갖게 되는 메모리 구조를 살펴본다.
- to-have 관계 중 두 번째인 집합: 집합은 합성과 비슷하다. 둘 다 to-have 관계를 다루기 때문이다. 하지만 집합과 합성은 서로 다르다. 집합 관계를 다루는 예제도 제공한다. 집합과 합성의 차이는 이들의 관계에 연관된 메모리 레이아웃에서 두드러진다.



## 7.1 클래스 간의 관계

객체 모델은 관련된 객체에 대한 집합이다. 관계의 수는 많더라도 두객체 간 관계의 유형은 몇 가지 정도가 존재한다. 일반적으로 객체 사이에는 to-have 관계와 to-be 관계라는 두 종류의 관계가 있다.



## 7.2 객체 대 클래스

객체를 생성하는 두 가지 접근법이 있다. 하나는 __프로토타입 기반__ 이고 다른 하나는 __클래스 기반__ 이다.

프로토타입 기반의 접근법에서는 빈 객체를 생성하거나 또는 기존 객체를 복제해 객체를 생성한다. 여기서는 인스턴스와 객체는 같은 의미이다. 그러므로 프로토타입 접근법은 객체 기반의 접근법으로도 볼 수 있다. 객체 기반의 접근법은 클래스 대신 빈 객체에서 시작한다.

클래스 기반의 접근법에서는 클래스라는 청사진 없이는 객체를 생성할 수 없다. 그러므로 클래스부터 시작해야 한다. 그래야 클래스로 객체를 인스턴스화 할 수 있다. 앞서 __암묵적 캡슐화 기법__ 을 설명했다. 이는 클래스를 헤더 파일에 있는 선언에 대한 집합이라고 정의한다.



Person 이라는 클래스를 정의한다고 가정하자. 이 클래스는 name, surname, age 라는 속성을 갖는다. 행위에 관해서는 설명하지 않겠다. 클래스와 객체의 차이는 일반적으로 속성에 기인하며, 행위에 따른 것은 아니기 때문이다.



```c
// c 에서의 person 속성 구조체
typedef struct {
    char name[32];
    char surname[32];
    unsigned int age;
} person_t;
```

```c++
class Person {
    public:
    	std::string name;
    	std::string surname;
    	unit32_t age;
};
```

앞의 두 코드 박스는 같다. 사실 현재 설명하는 내용은 C와 C++ 뿐만 아니라 자바와 같은 다른 OOP 언어에도 적용할 수 있다. 클래스는 모든 객체에 존재해야 하는 속성을 정의하는 청사진일 뿐이지, 특정 객체에서 속성이 가지는 값이 아니다. 각 객체는 같은 클래스에서 인스턴스화되어 서로 다른 객체에 존재하는 동일한 속성에 대해 고윳값의 집합을 갖는다.

클래스를 기반으로 객체를 만들 때는 먼저 메모리가 할당된다. 이 할당된 메모리는 속성값에 대한 자리 표시자가 된다. 그런 다음 어떠한 값으로 속성값을 초기화해야 한다. 이는 중요한 단계이다. 이렇게 하지 않으면 객체는 만들어진 뒤 유효하지 않은 값을 갖는다. 이미 살펴본 대로 이 단계는 __생성__ 이라고 한다.

일반적으로 생성 단계를 수행하도록 지정된 함수가 있으며 이는 __생성자__ 라고 한다. 이전 장의 예제에서 함수 list_init 과 car_construct 가 생성자 함수였다. 객체를 생성하는 과정에서는 해당 객체에 필요한 다른 객체나 버퍼, 배열, 스트림 등과 같은 리소스에 대해 훨씬 더 많은 메모리를 할당해야 할 수도 있다. 객체가 소유한 리소스는 소유자 객체가 비워지기 전에 반드시 해제되어야 한다.

생성자와 비슷한 다른 함수는 할당된 리소스를 해제하는 역할을 한다. 이는 __소멸자__ 라고 한다. 앞에서와 마찬가지로 이전 장의 예제에서 함수 list_destroy 와 car_destruct 는 소멸자에 해당한다. 객체가 소멸한 뒤 할당된 메모리가 해제된다. 하지만 그 전에 객체가 소유한 모든 리소스와 그에 해당하는 메모리가 먼저 비워져야 한다.

지금까지 설명한 내용은 요약해보자.

- 클래스는 객체를 만들기 위한 지도로 사용되는 청사진이다.
- 같은 클래스에서 여러 객체를 만들 수 있다.
- 클래스는 해당 클래스를 기반으로 만들 모든 객체에 존재해야 할 속성이 어느 것인지 결정한다.클래스는 속성이 가질 값에 관해서는 아무것도 설명하지 않는다.
- 클래스 자체로는 메모리를 사용하지 않는다.(C 나 C++ 가 아닌 몇몇 언어는 제외) 그리고 클래스는 소스 파일 단계 및 컴파일 시에만 존재한다. 하지만 객체는 런타임 시에 존재하고 메모리를 사용한다.
- 객체를 만들 떄는 먼저 메모리를 할당해야 한다. 덧붙여 메모리 해제는 객체에 대한 마지막 작업이다.
- 객체를 만들 때, 객체는 메모리 할당 직후에 생성해야 한다. 또한 메모리 해제 직전에 객체가 소멸해야 한다.
- 객체는 스트림, 버퍼, 배열 등과 같은 리소스를 소유한다. 이들은 객체가 소멸하기 전에 반드시 해제되어야 한다.



## 7.3 합성

합성이라는 용어는 한 객체가 다른 객체를 포함하거나 소유할 때, 즉 한 객체가 다른 객체로 구성될 때, 이들 객체 사이에 합성 관계가 있다고 할 때 사용한다.

예를 들면 자동차는 엔진을 갖는다. 이떄 자동차는 엔진 객체를 포함하는 객체이다. 그러므로 자동차와 엔진이라는 객체는 합성 관계이다. 합성 관계가 가져야 하는 중요한 조건이 있다. __컨데이너에 포함된 객체의 수명은 컨테이너 객체의 수명에 바인딩된다__ 

컨테이너 객체가 존재하는 한, 컨테이너에 포함된 객체도 반드시 존재해야 한다. 하지만 컨테이너 객체가 소멸할 때는 포함된 객체가 먼저 소멸해야 한다. 이 조건은 포함된 객체가 컨테이너에 비해 더 내부적이고 비공개적임을 나타낸다.

포함된 객체의 일부는 컨테이너 클래스의 공용 인터페이스를 통해 여전히 접근할 수도 있다. 하지만 포함된 객체의 수명은 컨테이너 객체에 의해 내부적으로 반드시 관리되어야 한다. 코드가 컨테이너에 포함된 객체를 먼저 소멸시키지 않고도 컨테이너 객체를 소멸시킬 수 있다면, 이는 합성 관계를 위반하는 것이며 더 이상 이 관계는 합성이 아니다.

다음 예제는 파일 다섯 개로 구성된다. 헤더 파일 2개는 클래스 Car 와 Engine 의 공용 인터페이스를 선언한다. 소스 파일 2개는 클래스 Car 와 Engine 의 구현을 포함한다. 그리고 마지막 소스 파일 하나는 main 함수를 담고 있으며 car 와 engine 객체를 사용하는 간단한 시나리오를 실행한다.

어떤 도메인에서는 자동차 객체 외부에 엔진 객체가 있을 수 있다. 기계 공학 CAD 소프트웨어가 그 예이다. 다양한 객체 간의 관계 유형은 문제영역에 의해 결정된다. 

```c
//car.h
# ifndef CAR_H
# define CAR_H

struct car_t;

// 메모리 할당자
struct car_t* car_new();

//생성자
void car_ctor(struct car_t*);

//소멸자
void car_dtor(struct car_t*);

//행위 함수
void car_start(struct car_t*);
void car_stop(struct car_t*);
double car_get_engine_temperature(struct car_t*);

#endif
```

앞의 선언은 List 클래스에 대해 했던 것과 비슷한 방식으로 선언된다. 한 가지 차이점은 생성자 함수에 car_construct 대신 새로운 접미사인 car_new를 선택했다는 점이다. 또 다른 차이점은 속성 구조체 car_t 만을 선언했다는 것이다. 이 구조체의 필드는 정의하지 않았으며, 이는 __전방 선언 forward declaration__ 이라고 한다. 구조체 car_t 의 정의는 이후에 나올 소스 파일에 있다. 앞의 헤더 파일에서 car_t 자료형은 아직 정의되지 않은 불완전한 형식으로 간주한다는 점을 알아두자.

```c
// engine.h
# ifndef ENGINE_H
# define ENGINE_H

struct engine_t;

// 메모리 할당자
struct engine_t* car_new();

//생성자
void engine_ctor(struct engine_t*);

//소멸자
void engine_dtor(struct engine_t*);

//행위 함수
void engine_turn_on(struct engine_t*);
void engine_turn_off(struct engine_t*);
double engine_get_temperature(struct engine_t*);

#endif
```

```c
//car.c
#include <stdlib.h>

// Car는 Engine에 대한 공용 인터페이스로만 작동할 수 있다.
#include "engine.h"

typedef struct {
    // 이 속성으로 인해 합성이 발생
    struct engine_t* engine;
} car_t;

car_t* car_new() {
    return (car_t*)malloc(sizeof(car_t));
}

void car_ctor(car_t* car) {
    // 엔진 객체에 대해 메모리 할당하기
    car->engine = engine_new();
    
    // 엔진 객체 생성하기
    engine_ctor(car->engine);
}

void car_dtor(car_t* car) {
    // 엔진 객체 소멸시키기
    engine_dtor(car->engine);
    
    // 엔진 객체에 할당된 메모리 비우기
    free(car->engine);
}

void car_start(car_t* car) {
    engine_turn_on(car->engine);
}

void car_stop(car_t* car) {
    engine_turn_off(car->engine);
}

double car_get_engine temperature(car_t* car) {
    return engine_get_temperature(car->engine);
}

```

앞의 코드 박스는 자동차가 엔진을 포함하는 방식을 나타낸다. car_t 속성 구조체에 새로운 속성이 있는데 이는 struct engine_t* 형이다. 이 속성 때문에 합성이 발생한다.

struct engine_t* 는 소스 파일 내에서 여전히 불완전한 형식이지만, 런타임 시에 완전한 형식인 engine_t 의 객체를 가리킬 수 있다. 이 속성은 Car 클래스의 생성자에서 생성할 객체를 가리키며, Car 클래스의 소멸자 내부에서 해제된다. 생성 및 해제 부분에는 둘 다 Car 객체가 존재하며, 이는 자동차의 수명에 엔진의 수명이 포함된다는 뜻이다.

engine 포인터는 비공개이고, 구현에서는 어느 포인터도 유출되지 않는다. 이는 반드시 눈여겨봐야 할 사항이다. 합성 관계를 구현할 때 포인터가 유출되면 안된다. 만약 유출된다면, 포함된 객체의 상태를 외부 코드가 변경할 수 있게 된다. 캡슐화와 마찬가지로 포인터가 어떤 객체의 비공개 부분을 직접 접근하도록 할 경우에는 유출되면 안된다. 비공개 부분은 언제나 행위 함수로 간접적으로 접근해야 한다.

코드 박스에서 car_get_engine_temperature 함수는 엔진의  temperature 속성에 대한 접근을 허용한다. 하지만 이 함수에 대해 중요한 참고 사항이 있다. 이 함수는 엔진의 공용 인터페이스를 사용한다. 잘 살펴보면, __자동차에 대한 비공개 구현__ 은 __엔진의 공용 인터페이스__ 를 사용한다.

자동차 그 자체로는 엔진에 대한 구현의 세부 사항에 대해서는 모른다는 의미이다. 그래야만 하는 방식이기도 하다.

__대부분의 경우, 같은 자료형이 아닌 두 객체는 상대의 구현에 대한 세부 사항은 몰라야 한다__ 아는 정보 은닉에서 따라야 하는 규칙이다. car 의 행위는 engine 에 외부적인 것으로 간주된다는 점을 명심하자.

이 방식으로  engine 에 대한 구현을 다른 것으로 대체할 수 있으며, engine 의 헤더 파일에서 선언된 것과 같은 공유 함수에 대한 정의를 새로운 구현이 제공하는 한 작동해야 한다.



```c
//engine.c
#include <stdlib.h>

typedef eunm {
    ON,
    OFF
} state_t;

typedef struct {
    state_t state;
    double temperature;
} engine_t;

// 메모리 할당자
engine_t * engine_new() {
    return (engine_t*)malloc(sizeof(engine_t));
}

// 생성자
void engine_ctor(engine_t* engine) {
    engine->state = OFF;
    engine->temperature = 15;
}

void engine_dtor(engine_t* engine) {
    // 할 일 없음
}

// 행위 함수
void engine_turn_on(engine_t* engine) {
    if (engine->state == ON) {
        return ;
    }
    engine->state = ON;
    engine->temperature = 75;
}

void engine_turn_off(engine_t* engine) {
    if (engine->state == OFF) {
        return ;
    }
    engine->state = OFF;
    engine->temperature = 15;
}

double engine_get_temparature(engine_t* engine) {
    return engine->temperature;
}
```

이 코드는 앞선 예제와 매우 유사하게, 단순히 비공개 구현에 대한 암묵적 캡슐화 접근법을 사용한다. 하지만 한 가지 참고 사항이 있다. engine 객체는 합성 관계에서 engine 객체를 포함할 외부 객체에 대해서는 모른다. 이는 현실 세계와 비슷한다. 회사가 엔진을 만들 때, 어느 엔진이 차에 잘 맞을지는 확실하지 않다. 당연히 컨테이너인 car 객체에 대해 포인터를 둘 수 있지만, 이번 예제에서는 필요하지 않았다.



```c
//main.c
#include <stdio.h>
#include <stdlib.h>

#include "car.h"

int main(int argc, char** argv)
{
    struct car_t * car = car_new();
    
    car_ctor(car);
    
    printf("Engine temperature before starting the car: %f\n", car_get_engine_temperature(car));
    car_start(car);
    printf("Engine temperature after starting the car: %f\n", car_get_engine_temperature(car));
    car_stop(car);
    printf("Engine temperature after stopping the car: %f\n", car_get_engine_temperature(car));
    
    // 자동차 객체 소멸
    car_dtor(car);
    
    // 자동차 객체에 대해 할당된 메모리 해제
    free(car);
    return 0;
}
```

앞의 예제를 빌드하려몀ㄴ 우선 이전의 소스파일 3개를 컴파일해야 한다. 그다음 함께 링크해 최종 실행 가능한 목적 파일을 만들어야 한다. 메인 소스 파일은 car 의 공용 인터페이스에만 의존하니 주의하자. 그러므로 링크 시 메인 소스 파일은 car 객체의 비공개 구현만 필요하다. 하지만 car 객체에 대한 비공개 구현은 engine 인터페이스의 공용 인터페이스에 의존한다. 그러므로 링크 단계에서 engine 객체에 대한 비공개 구현을 제공해야 한다. 따라서 최종 실행 파일을 만들려면 객체 파일 3 개를 모두 링크해야 한다.



## 7.4 집합

집합은 다른 객체를 포함하는 컨테이너 객체를 동반한다. 집합 관계에서는 컨테이너에 포함된 객체의 수명이 컨테이너 객체의 수명에 독립적이라는 점이 합성과의 주된 차이점이다.

집합에서 컨테이너에 포함된 객체는 컨테이너 객체가 생성되기 이전에도 생성될 수 있다. 이는 합성과 정반대이다. 합성의 경우 컨테이너에 포함된 객체가 컨테이너 객체의 수명보다 더 짧거나 같아야 한다.

다음 예제는 집합관계를 나타낸다. 이 예제는 플레이어가 총을 집어 들고 여러 번 발사한 뒤 총을 내려놓는 매우 간단한 게임 시나리오를 나타낸다.

player 객체는 잠시 컨테이너 객체가 된다. 그리고 gun 객체는 플레이어 객체가 총을 들고 있는 동안에는 컨테이너에 포함된 객체가 된다. gun 객체의 수명은 player 객체의 수명에 독립적이다.



```c
// gun.h
# ifndef GUN_H
# define GUN_H

typedef int bool_t;

// 전방 선언
struct gun_t;

// 메모리 할당자
struct gun_t* gun_new();

// 생성자
void gun_ctor(struct gun_t*, int);

// 소멸자
void gun_dtor(struct gun_t*);

// 행위함수
bool_t gun_has_bullets(struct gun_t*);
void gun_trigger(struct gun_t*);
void gun_refill(struct gun_t*);

#endif

```

gun_t 속성 구조체만 정의했을 뿐 이에 대한 필드는 정의하지  않았다. 앞서 설명했듯 이는 전방 선언이라고 하며, 그 결과 인스턴스화할 수 없는 불완전한 형식이 된다.



```c
// player.h
# ifndef PLAYER_H
# define PLAYER_H

// 전방 선언
struct player_t;
struct gun_t;

// 메모리 할당자
struct player_t* player_new();

// 생성자
void player_ctor(struct player_t*, const char*);

// 소멸자
void player_dtor(struct player_t*);

// 행위함수
void player_pickup_gun(struct player_t*, struct gun_t*);
void player_shoot(struct player_t*);
void player_drop_gun(struct player_t*);

#endif
```

이 코드박스는 모든 플레이어 객체의 공용 인터페이스를 정의한다. 즉, player 클래스의 공용 인터페이스를 정의한다.

다시 강조하지만 gun_t 및 player_t 구조체에 대해 전방 선언해야 한다. gun_t 형을 선언해야 하는 이유는 Player 클래스에서 행위 함수가 gun_t 형에 대한 인수를 갖기 때문이다.



