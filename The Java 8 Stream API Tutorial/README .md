### 1. Overview

### 2. Stream Creation
#### 2.1. Empty Stream
empty stream 만들기

```java
Stream<String> streamEmpty = Stream.empty();
```
원소가 없는 스트림의 경우 null이 리턴되는 것을 피하기 위해서 empty() 함수를 사용한다.

```java
public Stream<String> streamOf(List<String> list) {
    return list == null || list.isEmpty() ? Stream.empty() : list.stream();
}
```

#### 2.2. Stream Of Collection
어떤 Type의 Colleciton Stream으로 만들 수 있다. (Collection, List, Set)
```java
Collection<String> collection = Arrays.asList("a", "b", "c");
Stream<String> streamOfCollection = collection.stream();
```

#### 2.3. Stream of Array
배열은 stream의 소스가 될 수 있다.
```java
Stream<String> streamOfArray = Stream.of("a", "b", "c");
```
배열 전체 나 배열의 일부를 이용해서 stream을 만들 수 있다.
```java
String[] arr = new String[]{"a", "b", "c"};
Stream<String> streamOfArrayFull = Arrays.stream(arr);
Stream<String> streamOfArrayPart = Arrays.stream(arr, 1, 3);
```

#### 2.4. Stream.builder()

#### 2.5. Stream.generate()

#### 2.6. Stream.iterate()

#### 2.7. Stream of Primitives

#### 2.8. Stream of String

#### 2.9. Stream of File


### 3. Referencing a Stream

### 4. Stream Pipeline

### 5. Lazy Invocation

### 6. Order of Execution

### 7. Stream Reduction

### 8. Parallel Streams

### 9. Conclusion

