## 정렬 알고리즘


### Bubble Sort

- 매 회전 마다 서로 붙어있는 걸 비교해서 큰 거를 뒤로, O(n^2)

![bubble sort](https://user-images.githubusercontent.com/53200166/131070633-6e6e26f5-57f4-40db-bba2-cb8e692a08c6.gif)



### Selection Sort

- 매 회전 마다 작은 걸 찾아서 맨 앞으로, O(n^2)

![selection sort](https://user-images.githubusercontent.com/53200166/131070650-cf36374f-43ef-4b10-ae8d-2429e021a1ba.gif)



### Insertion Sort

- 매 회전 마다 이미 정렬된 앞의 원소들 사이에 자기 자리를 찾아 삽입
- 최선은 O(n), 최악은 O(n^2)

![insertion sort](https://user-images.githubusercontent.com/53200166/131070658-3dc7c261-0472-4ad5-9a1e-9dc8c5739396.gif)



### Quick Sort

- 어떤 피벗의 왼쪽엔 그보다 작은 값, 오른쪽엔 큰 값만 존재하도록 재귀적으로 반복
  - 오른쪽(j)에서 왼쪽으로 가면서 피벗보다 작은 수를 찾음
  - 왼쪽(i)에서 오른쪽으로 가면서 피벗보다 큰 수를 찾음
  - 각 인덱스 i, j에 대한 요소를 교환
  - 위 과정을 반복하다가 더 이상 진행이 불가능하면 현재 피벗과 교환
  - 이제 교환된 피벗 기준으로 왼쪽엔 피벗보다 작은 값, 오른쪽엔 큰 값들만 존재함
- 평균은 O(nlogn), 최악은 O(n^2)

![quick sort](https://user-images.githubusercontent.com/53200166/131070664-78899c7c-90a0-4a6b-8fb6-3956aab3a65c.gif)



### Merge Sort

- 영역을 쪼갤 수 있을 만큼 쪼갠 다음 점점 합병시키는 방식
- 순차적이므로, Head부터 탐색해야 하는 Linked List에 적합함
- 모든 경우에서 O(nlogn)

![merge sort](https://user-images.githubusercontent.com/53200166/131070667-bb455601-90e1-444a-8de7-342e2ba2b189.gif)



### Heap Sort

- 매 회전마다 Max Heap을 구성하고 Root값을 하나씩 뽑아내어 정렬 라인에 넣은 뒤 다시 Max Heap을 구성하는 과정을 반복하여 정렬
- 최댓값/최솟값을 구할 때, 최대 k만큼 떨어진 요소들을 정렬할 때 유용함
- 모든 경우에서 O(nlogn)

![heap sort](https://user-images.githubusercontent.com/53200166/131070670-9a277858-c70a-472a-99e5-ea984dacd91b.gif)



------



## 자료구조



### Stack vs Queue

- Stack : LIFO(후입선출), 뒤로가기
- Queue : FIFO(선입선출), 대기줄



### Array vs Linked List

|                 | 배열(Array)                                                  | 연결리스트(Linked List)                                      |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 크기            | 고정적 (선언할 때 크기가 고정)                               | 동적 (데이터를 삽입할 때마다 증가)                           |
| 주소            | 순차적                                                       | 랜덤                                                         |
| 접근속도        | 인덱스만 안다면 접근 가능<br />(속도 빠름)                   | 하나하나 위치를 따라가서 접근<br />(배열에 비해 속도 느림)   |
| 삽입/<br />삭제 | 삽입 시, 기존의 데이터들의 위치를 뒤로 이동시킴.<br />삭제 시, 기존의 데이터들의 위치를 앞으로 이동시킴<br />전반적으로 비효율적 | 삽입 시, 리스트에 연결되어 있는 위치에 접근한 후 리스트 추가.<br />삭제 시, 리스트에 연결되어 있는 위치에 접근하여 삭제 후, 기존의 리스트들을 연결.<br />삽입/삭제 시 메모리를 할당/해제할 필요가 있음<br />전반적으로 효율적 |



### 우선순위 큐(Priority Queue)

- 들어간 순서에 상관없이 우선순위가 높은 데이터가 먼저 나오는 것
- 배열로 구현할 경우
  - 데이터 삽입 및 삭제 과정에서 데이터를 한 칸씩 당기거나 밀어야 하는 연산을 계속 해야 한다.
  - 삽입의 위치를 찾기 위해 배열에 저장된 모든 데이터와 우선순위를 비교해야 한다.
- Linked List로 구현할 경우
  - 삽입의 위치를 찾기 위해 리스트에 저장된 모든 데이터와 우선순위를 비교해야 한다.
- 따라서 Heap을 이용하여 구현한다.



------



### Hash Table

![image](https://user-images.githubusercontent.com/53200166/131070691-168d6160-c623-4da0-b3c6-118e2c55032f.png)

- 데이터를 해시함수에 통과시켜 얻은 값을 index로 삼아 (key, value) 형태로 저장하는 자료구조
- 예를 들어 우리가 (Key, Value)가 ("John Smith", "521-1234")인 데이터를 크기가 16인 해시 테이블에 저장한다고 하자. 그러면 먼저 index = hash_function("John Smith") % 16 연산을 통해 index 값을 계산한다. 그리고 array[index] = "521-1234" 로 전화번호를 저장하게 된다.
- 개념
  - 키(key) : 고유한 값, 해시함수의 input
  - 해시함수(hash function) : key를 hash로 바꿔주는 역할
  - 해시(hash) : 해시함수의 결과물이며, 저장소(bucket)에서 값(value)과 매칭되어 저장됨
  - 값(value) : bucket에 최종적으로 저장되는 값. 키와 매칭되어 저장, 삭제, 검색이 가능
- 장점
  - 해싱으로 인덱스를 정하기 때문에 삽입, 삭제, 검색이 매우 빠르다.
  - 적은 리소스로 많은 데이터를 효율적으로 관리할 수 있다.
- 단점
  - 순서가 있는 배열에는 어울리지 않는다.
  - Hash collision



#### Hash Collision

"John Smith"를 해시함수에 통과시켜 나온 index와 "Mang Kyu"를 해시함수에 통과시켜 나온 index가 동일하여 충돌이 일어날 때

- 분리 연결법(Separate Chaining)

  - 동일한 bucket에 저장하되 Linked List 형태로 저장하는 방법

    ![image](https://user-images.githubusercontent.com/53200166/131070709-ffbdd351-a729-41e2-8655-3b7adba87fad.png)

- 개방 주소법(Open Addressing)

  - 근처의 다른 주소도 이용하도록 하는 방법

  ![image](https://user-images.githubusercontent.com/53200166/131070716-80513391-1ad4-46e2-a230-92a2d22ae19c.png)

- Open Adderssing의 3가지 방식

  - Linear Probing
    - 현재의 버킷 index로부터 고정폭 만큼씩 이동하여 차례대로 검색해 비어 있는 버킷에 데이터를 저장한다.
  - Quadratic Probing
    - 해시의 저장순서 폭을 제곱으로 저장하는 방식이다. 예를 들어 처음 충돌이 발생한 경우에는 1만큼 이동하고 그 다음 계속 충돌이 발생하면 2^2, 3^2 칸씩 옮기는 방식이다.
  - Double Hashing Probing
    - 해시된 값을 한번 더 해싱하여 해시의 규칙성을 없애버리는 방식이다. 해시된 값을 한번 더 해싱하여 새로운 주소를 할당하기 때문에 다른 방법들보다 많은 연산을 하게 된다.



------



### Heap

![image](https://user-images.githubusercontent.com/53200166/131070732-64aa1f62-ebc5-466f-8e81-3a34b5fcecfc.png)

- 완전 이진 트리이다.
- 최댓값 또는 최솟값을 빠르게 찾을 때 유용하다.
- 모든 노드들은 값이 자식 노드의 값보다 크면 Max Heap, 작으면 Min Heap이다.
- 시뮬레이션, 트래픽 제어, OS 작업 스케줄링, 수치해석 계산
- 삽입 및 삭제 모두 O(logn)
- 부모 노드와 자식 노드 사이의 관계
  - 왼쪽 자식 index = (부모 index) * 2
  - 오른쪽 자식 index = (부모 index) * 2 + 1
  - 부모 index = (자식 index) / 2



#### Heap의 삽입

![image](https://user-images.githubusercontent.com/53200166/131070743-7f9709bf-0ee3-41be-a0b2-df89616c3d20.png)



#### Heap의 삭제

![image](https://user-images.githubusercontent.com/53200166/131070751-f8c89351-a5be-4230-9b94-8a006874bda8.png)



------



### 이진탐색트리 (Binary Search Tree)

- 만들어진 목적
  - Binary Search : 탐색의 시간복잡도는 O(logn), 단 삽입 및 삭제가 불가능
  - Linked List : 삽입 및 삭제의 시간복잡도는 O(1), 단 탐색의 시간복잡도가 O(N)
  - 이 두 가지를 합하여, 효율적인 탐색 능력을 가지고, 삽입 삭제도 가능하게 만들자
- 특징
  - 모든 노드의 키는 유일하다. 즉, 중복이 존재하지 않는다.
  - 각 노드의 자식은 최대 2개이다.
  - 각 노드의 왼쪽 자식은 부모보다 작고, 오른쪽 자식은 부모보다 크다.
- 삭제 시, 삭제하려는 노드가 두 개의 서브 트리를 가지고 있는 경우

![image](https://user-images.githubusercontent.com/53200166/131070762-74b5b21d-fe9f-44f1-8100-f15c302e8f39.png)



#### B Tree

- DB 및 파일 시스템에서 널리 사용되는 자료구조
- 이진트리를 확장시킨 것으로, 더 많은 수의 자식을 가질 수 있음
- B Tree는 각 노드에 데이터가 저장되고, B+ Tree는 index 노드와 leaf 노드로 분리됨




------



## 탐색 알고리즘



### Binary Search

- 탐색 범위를 두 부분으로 분할하면서 찾는 방식
  - 정렬된 상태에서 left와 right로 mid 값 설정
  - Mid와 내가 구하고자 하는 값과 비교
  - 구할 값이 mid보다 높으면 left = mid + 1, mid보다 낮으면 right = mid – 1
  - left > right가 될 때까지 계속 반복
- O(logN)



### DFS/BFS

![image](https://user-images.githubusercontent.com/53200166/131070726-85615822-0974-44e8-ae2b-e8896de80205.png)

#### 깊이우선탐색(DFS, Depth-First Search)

- 최대한 깊이 내려간 뒤, 더 이상 깊이 갈 곳이 없을 경우 옆으로 이동
- 루트 노드에서 시작해서 다음 분기(branch)로 넘어가기 전에 해당 분기를 완벽하게 탐색하는 방식
- 모든 경로를 방문해야 할 경우나 경로의 특징을 저장해야 할 경우에 적합함
- Stack 또는 Recursive Function으로 구현
- 시간복잡도 : 인접 행렬은 O(V^2), 인접 리스트는 O(V+E) (V는 접점, E는 간선)

#### 너비우선탐색(BFS, Breadth-First Search)

- 최대한 넓게 이동한 다음, 더 이상 갈 수 없을 때 아래로 이동
- 루트 노드에서 시작해서 인접한 노드를 먼저 탐색하는 방식. 가까운 정점을 먼저 방문하고 멀리 떨어진 정점을 나중에 방문하는 방식
- 최소 비용, 즉 최단거리 구하기 등에 적합함
- Queue로 구현
- 시간복잡도 : 인접 행렬은 O(V^2), 인접 리스트는 O(V+E) (V는 접점, E는 간선)