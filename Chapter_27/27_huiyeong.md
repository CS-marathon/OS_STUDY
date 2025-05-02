# 막간: 스레드 API

## 1. 스레드 생성
```
#include <pthread.h>
int
pthread_create(
                        pthread_t *  thread,
                const   pthread_attr_t * attr ,
                        void * (*start_routine)(void*),
                        void *  arg);
```
- thread : pthread_t 타입 구조체를 가리키는 포인터이다.
  - 이 구조가 스레드와 상호작용하는 데 사용되기 때문에 스레드 초기화 시 pthread_create()에 이 구조체를 전달한다.
- attr : 스레드의 속성을 지정하는 데 사용한다.
  - 스택의 크기와 스레드의 스케줄링 우선순위 같은 정보를 지정하기 위해서 사용될 수 있다.
  - 개별 속성은 pthread_attr_init() 함수를 호출하여 초기화한다. 
  - 대부분의 경우에 디폴트 값을 지정하면 충분하다. 
  - 간단히 NULL을 전달하면 된다.
- (*start_routine)(void*) : 이 쓰레드가 실행할 함수를 나타낸다.
  - C 언어에서는 이를 함수 포인터라고 부른다.
  - 이 함수는 void * 타입의 인자 한 개를 전달받는다(함수 이름 start_routine 다음에 괄호 안에 표시된 것처럼). 그리고 void * (즉, void 포인터) 타입의 값을 반환한다.
  - void 포인터 타입 대신 integer를 인자로 사용하는 루틴이라면 선언은 다음과 같이 될 것이다. `void * (*start_routine)(int)`
- arg : 실행할 함수에게 전달할 인자를 나타낸다.
  - 왜 void 포인터 타입이 필요하지라고 질문할 수도 있다. <br>
  -> void 포인터를 start_routine 함수의 인자로 사용하면, 어떤 데이터 타입도 인자로 전달할 수 있고, 반환 값의 타입으로 사용하면 스레드는 어떤 타입의 결과도 반환할 수 있다.

```
#include <pthread.h>
typedef struct __myarg_t {  // 두 개의 인자를 전달받는 새로운 스레드를 생성하는 데 두 인자는 우리가 정의한 myarg_t 타입으로 묶여진다.
     int a;
     int b;
 } myarg_t;
void *mythread(void *arg) {
     myarg_t *m = (myarg_t *) arg;
     printf(“%d %d\n”, m−>a, m−>b); return NULL;
}
int
main(int argc , char *argv[]) {
     pthread_t p;
     int rc;
     myarg_t args;
     args.a = 10;
     args.b = 20;
     rc = pthread_create(&p , NULL , mythread , &args);
...
}
```

## 2. 스레드 종료
