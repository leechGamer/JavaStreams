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
builder를 사용할 때, 오른쪽 statement에 추가적으로 원하는 타입을 지정해야한다. 그렇지 않으면 build 함수는 Stream<Object> 를 만들 것이다.
```java
Stream<String> streamBuilder = Stream.<String>builder().add("a").add("b").build();
// [a, b]
```
#### 2.5. Stream.generate()
generate 함수는 원소 생성을 위해 `Supplier<T>`를 허용합니다. stram의 결과가 무한할 때, 개발자는 원하는 사이즈를 더 구체화해야하거나
generate함수는 메모리 limit에 도달할때까지 동작할 것이다.
```java
Stream<String> streamGenerated = Stream.generate(() -> "element").limit(10);
// [element, element, element, element, element, element, element, element, element, element]
```
위 코드는 "element"라는 연속된 10개의 문자열을 만든다.
#### 2.6. Stream.iterate()
무한 stream을 생성하는 또다른 방법은 iterate()함수이다.
```java
Stream<Integer> streamIterated = Stream.iterate(40, n -> n+2).limit(20);
```
stream결과에 대한 첫번째 원소는 iterate함수의 첫번째 parameter이다. 모든 뒤따르는 원소를 생성할 때, 명시된 함수는 앞에 있는 원소로 적용된다. 
예를 들어 위 코드의 두번째 원소는 42일 것이다.
    
#### 2.7. Stream of Primitives
```java
IntStream intStream = IntStream.range(1, 3);
LongStream longStream = LongStream.rangeClosed(1, 3);
```
    
```java
Random random = new Random();
DoubleStream doubleStream = random.doubles(3);
```

#### 2.8. Stream of String
String을 String클래스의 chars()메소드를 사용해서 stream을 생성하는 소스로 사용할 수 있다. JDK에서 CharStream에 대한 interface가 없기 때문에 IntStream을 chars에 대한 stream으로 대신 쓸 수 있다.
```java
IntStream streamOfChars = "abc".chars();
```

#### 2.9. Stream of File
자바 NIO 클래스 파일은 텍스트파일에 대한 Stream<String>을 line()함수를 통해 생성하게 한다. text의 모든 라인은 stream의 원소가 된다.
```java
Path path = Paths.get("C:\\file.txt");
Stream<String> streamOfStrings = Files.lines(path);
Files.lines(path, Charset.forName("UTF-8"));
```

### 3. Referencing a Stream

### 4. Stream Pipeline

### 5. Lazy Invocation

### 6. Order of Execution

### 7. Stream Reduction

### 8. Parallel Streams

### 9. Conclusion

