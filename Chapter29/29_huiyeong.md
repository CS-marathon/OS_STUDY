# 락 기반의 병행 자료 구조
병행 자료 구조 : 여러 스레드(또는 프로세서)가 동시에 접근해도 올바르게 동작하도록 설계된 자료 구조
 
## 1. 병행 카운터
### (1) 단순한 카운터 (확장성 없음)
동기화되지 않은 카운터는 매우 간단한 구조. <br>
여러 스레드가 동시에 접근하면 경쟁 조건 발생 → 쓰레드 안전하지 않음.
<img width="586" alt="스크린샷 2025-05-03 오후 6 43 34" src="https://github.com/user-attachments/assets/e4f5fdfc-5616-4798-844a-503a6599be7a" />

### (2) 락 기반 동기화 카운터
lock을 사용하여 동기화 <br>
정확하지만 확장성이 떨어짐
<img width="612" alt="스크린샷 2025-05-03 오후 6 43 43" src="https://github.com/user-attachments/assets/ce3af0a9-a7d3-4c06-9825-b02120cf1052" />


### (3) 확장성 있는 카운팅
완벽한 확장성(perfect scaling): 작업량이 늘어도 처리 시간이 증가하지 않는 이상적 상태.
-> (예) 엉성한 카운터 (Sloppy Counter)

#### 엉성한 카운터
하나의 논리적 카운터로 표현되는데, <br>
CPU코어마다 지역 카운터 (Local Counter) + 전역 카운터 (Global Counter) 으로 구성된다. <br>
-> 4개의 CPU 를 갖고 있다면 4개의 지역 카운터 + 1개의 전역 카운터

- 기본 개념 : 지역 카운터를 위한 락과 전역 카운터를 위한 락 존재 <br>
- 작동 방식: <br>
각 쓰레드는 자신의 CPU에 있는 지역 카운터를 증가시킴. <br>
-> 지역 카운터가 임계치 S에 도달하면 전역 카운터에 반영하고 0으로 초기화. <br>
-> 전역 카운터 갱신 시 전역 락 필요.

장점: 병렬성 좋음, 성능 우수 <br>
단점: 정확도 저하 (지역 값이 전역에 반영되기 전까지 오차 존재)

S 값에 따라: <br>
작을수록 정확, 성능 저하 <br>
클수록 빠르지만 오차 큼

## 2. 병행 연결 리스트
- 기본 동기화 삽입 방식 : 전체 리스트에 락 하나 사용. <br>
- malloc() 실패 시 락 해제를 해줘야 한다.

### (1) 확장성 있는 연결 리스트
- hand-over-hand locking (lock coupling) : 개별 노드마다 락을 두고 다음 노드 락을 먼저 획득한 후 현재 노드 락 해제. <br>
-> 병행성은 높지만, 오버헤드가 크고 성능 개선 미미하다.

-> 하이브리드 방식(일정 노드 단위로 락 교체)이 더 실용적이다.

## 3. 병행 큐
- Michael & Scott 큐 (MS Queue) : 큐의 head와 tail에 각각 락을 분리 적용하는 방식 <br>
삽입 → tail 락<br>
삭제 → head 락

큐 초기화 시 더미 노드(dummy node)를 넣어 헤드와 테일 연산을 구분한다.

## 4. 병행 해시 테이블
전체 자료 구조에 하나의 락을 사용하는 것이 아니라 해시 버켓마다 락을 사용하기 때문에 성능이 우수하다. <br>
-> 병행성이 좋아진다.

