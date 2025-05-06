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
`int pthread_join(pthread_t thread , void **value_ptr);` : 다른 쓰레드가 종료될 때까지 기다리는 함수
-  pthread_t thread : 어떤 스레드를 기다리려고 하는지를 명시한다. 스레드 생성 루틴에 의해 초기화된다.
-  void **value_ptr : 반환 값에 대한 포인터

-> 스레드가 실행을 마치면 메인 스레드가 리턴하고, 종료한 스레드가 반환한 값에 접근할 수 있게 된다.

### 유의
(1) 여러 인자를 한 번에 전달하기 위해 묶고 해체하는 불편한 과정을 항상 해야 하는 것은 아니다.
-> 인자가 없는 스레드를 생성할 때에는 NULL 을 전달하여 스레드를 생성할 수도 있다. 
-> 반환 값이 필요 없다면 pthread_join()에 NULL 을 전달할 수도 있다.
(2) 값 하나만 전달해야 한다면 인자를 전달하기 위해 묶을 필요가 없다.
(3) 스레드에서 값이 어떻게 반환되는지에 대해 각별한 신경을 써야 한다.
-> 스레드의 콜 스택에 할당된 값을 가리키는 포인터를 반환하지마라
-> 스레드가 종료되면, 해당 스레드의 콜 스택은 소멸한다. 따라서 스택에 선언된 변수의 주소를 반환하면, 이미 해제된 메모리 영역을 참조하는 포인터가 되어 정의되지 않은 동작(undefined behavior) 이 발생할 수 있다.
(4) pthread_create() 생성 직후 pthread_join()을 호출하는 것은 스레드를 생성하는 이상한 방법이다.

-> 프로시저 호출 (procedure call)
여러 개의 스레드를 생성해 놓고 스레드가 끝나기를 기다리는 게 보통이다.

## 3. 락
락을 통한 임계 영역에 대한 상호 배제 기법이다.
(1) 초기화를 하지 않는다.
-> 모든 락은 올바르게 초기화되어야 한다.

## 4. 컨디션 변수
스레드가 계속 진행하기 전에 다른 스레드가 무언가를 해야 스레드 간에 일종의 시그널 교환 메커니즘이 필요하다.
-> 컨디션 변수 사용을 위해서는 이 컨디션 변수와 연결된 락이 반드시 존재해야 한다.



