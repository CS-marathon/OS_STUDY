# 락
## 1. 락 : 기본 개념
```c
lock(&mutex);
balance = balance + 1;
unlock(&mutex);
```
락(lock_t mutex)을 이용해 동시에 여러 스레드가 임계 영역에 들어오지 못하게 보호한다.
### (1) 락의 상태
- 사용 가능(Available): 어떤 스레드도 락을 획득하지 않은 상태 (unlocked) <br>
- 사용 중(Acquired): 한 스레드가 락을 획득하고 임계 영역을 실행 중인 상태 (locked) <br>

### (2) lock()과 unlock()
- lock(): 락이 사용 가능하면 즉시 획득하고 임계 영역에 진입한다.
  - 진입한 스레드를 락 소유자라고 한다.
  - 락이 사용 중이면, 다른 스레드는 대기(blocked).
- unlock(): 락을 해제하고, 대기 중인 스레드가 있다면 그 중 하나가 락을 획득한다.


## 2. pthread 락
```c
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_lock(&lock);
balance = balance + 1;
pthread_mutex_unlock(&lock);
```
pthread_mutex_lock()과 pthread_mutex_unlock()을 사용해 임계 영역 보호. <br>
변수에 따라 다른 락을 사용 가능 
-> 거친 락(coarse-grained) : 하나의 락이 임의의 임계 영역에 진입할 때마다 사용
-> 세밀한 락(fine-grained lock) : 서로 다른 데이터와 자료 구조를 보호하기 위해 여러 락을 사용하여 한 번에 여러 스레드가 서로 다른 락으로 보호된 코드내에 각자가 진입이 가능하도록 하는 방법

## 3. 락 구현
락 구현에는 하드웨어와 운영체제의 지원이 필요 <br>
CPU는 락 구현을 위한 원자적 명령어(atomic instruction) 제공

운영체제는 락을 효과적으로 사용하기 위한 기본 기능 제공

