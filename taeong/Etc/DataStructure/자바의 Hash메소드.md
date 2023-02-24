# Hash
Hash는 ArrayList의 추가/삭제 시 속도가 느려지는 단점과 LinkedList의 검색시 처음부터 끝까지 다 찾아봐야하는 비효율성을 해결하고자 나온 방법이다.

<br>

## HashSet
### 1. HashSet 선언
```
HashSet<Integer> set = new HashSet<>();
```

### 2. 값 추가 - add()
```
set.add(val);
set.addAll(new HashSet<>(Arrays.asList("Black", "Yellow", "Purple")));
```
💡` HashSet은 입력된 순서가 보장되지 않기 때문에 특정 위치에 값을 추가하거나 할 수 없음`

### 3. 값 삭제 - remove()
```
set.remove(val);
set.removeIf(v -> v.startsWith("B"));
set.removeAll(Arrays.asList("White", "Green"));
```
💡` 조건을 적용해서 여러 값들을 삭제할 때는 removeIf() 메소드를 사용`

### 4. 값 포함 여부 확인 - contains()
```
set.contains(val); // 찾는 키가 존재하면 true, 존재하지 않으면 false
```

### 5. iterator (For-Each Loop)
```
for (String val : set) {
    System.out.print(val + "  ");
}
```


---

<br>

## HashMap
### 1. HashMap 선언
```
HashMap<Integer, String> map = new HashMap<>();
```

### 2. 값 추가 및 삭제 - put(), remove()
```
map.put(key,"value");
map.remove(key);
```

### 3. 값 가져오기 - get()
```
map.get(key);
```

### 4. 값 포함 여부 확인 - containsKey(), getOrDefault()
```
map.containsKey(key); // 찾는 키가 존재하면 true, 존재하지 않으면 false

map.getOrDefault(key, defaultValue) // 찾는 키가 존재하면 value 반환, 존재하지 않으면 defaultValue 반환
map.put(key, map.getOrDefault(key, 0) + 1);   //이렇게 사용할 수 있음
```
`💡 Set은 contains(), Map은 containsKey()이므로 헷갈리지 말기!`

### 5. iterator (For-Each Loop)
```
// keySet()
for (Integer key : map.keySet()) {
    System.out.println("Key = " + key);
}

// values()
for (Integer value : map.values()) {
    System.out.println("Value = " + value);
}

// entrySet()
for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
    System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
}
```
`💡 이전에 오류났던 이유가 Entry<>..로 써서 그런듯! Map.Entry<> 암기하기`


---

<br>

### 외워두면 편한 구조
1. String배열을 리스트로 변환 - Arrays.asList(String[] array) 사용
```
String[] phone_book = {"123","456","789"};
Set<String> set = new HashSet<>(Arrays.asList(phone_book));
```
- 💡 알아두기
    - 이 방법은 자바 primitive타입 배열에는 사용 불가능
    - Wrapper클래스 배열로 만들어서 사용하거나 일일이 반복문으로 값을 넣어주어야 함
