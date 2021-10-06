# Java



## Java의 Collection

자바의 대표 Collection에는 List, Map, Set, Stack, Queue와 같은 것들이 있다. 이 추상화된 Collection 인터페이스 아래, 특정한 기법으로 구현된 자료구조가 들어간다. 예를 들어, List라는 인터페이스에는 구현방법에 따라 ArrayList가 들어갈 수도, LinkedList가 들어갈 수도 있다.

### List

- ArrayList
  - 자바의 Vector를 개선한, 배열로 구현된 List이다. 그 말인 즉슨, 데이터가 저장된 순서가 같다는 말이다. 사실상 배열과 같은 자료구조이기 때문에, 리스트의 연산 자체의 수행시간 속도는 배열과 같다.
- LinkedList
  - 다음 노드의 주소를 기억하고 있는 List로, 배열에 비해 삽입과 삭제가 간단하다. 그러나 탐색의 경우 첫 번째 노드부터 탐색해 나가야 하기 때문에 느리다.

### Map 

- HashMap
  - 가장 일반적으로 사용하는 Map. HashTable을 사용, Key값에 해시함수를 적용하여 나온 index에 Value를 저장하는 식. 중복성을 허용하지 않으며, 순서가 없다는 것이 특징
- TreeMap
  - Red-Black Tree 자료구조를 이용한 Map이다. Tree 구조이기 때문에 어느 정도 순서를 보장한다.
- LinkedHashMap
  - LinkedList로 구현된 HashMap이다. List로 구현되어있기 때문에 순서가 보장된다. 하지만 LinkedList 특성상 랜덤 접근에서 느릴 수 있다.

### Set

- HashSet
  - HashMap에서 Key값이 없는 자료형. 집합이라고 생각해도 무방하다. 값이 포함되어 있는지 아닌지만 관심이 있다. 순서를 보장하지 않으며, 중복값을 허용하지 않는다. Set중에는 가장 많이 사용된다.
- TreeSet
  - Red-Black Tree 자료구조를 사용한 Set.
- LinkedHashSet
  - LinkedList로 구현된 HashSet. 순서를 보장한다.

### Stack & Queue

- Stack
  - 직접 new 연산자로 객체를 생성하여 사용 가능.
- Queue
  - LinkedList 에 new 연산자로 객체를 생성함으로서 사용 가능.

### Array vs ArrayList

- 둘 다 배열이라는 점은 동일하나, Array는 인덱스로 접근하는 반면, ArrayList는 메서드를 통해 접근한다(어짜피 Index로 호출한다는 점은 동일 하겠지만..). 또한 Array는 Object 뿐만 아니라 원시 형태(Primitive, 예를 들어 int, double 등)도 담을 수 있지만, Array는 Object형(Reference, 객체)만 담을 수 있다. 따라서 정수를 ArrayList에 넣을 경우 Integer형은 가능하지만 int형은 안 된다. 덧붙여서, Integer처럼 int와 같은 원시타입을 담을 수 있는 객체를 Wrapper Class라고 한다.