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
    
#### 2.7. Stream of Primitive
자바8은 3가지 종류의 원시타입에 대한 stream을 생성할 수 있다: int, long, double.
Stream<T>는 generic 인터페이스이고 원시를 타입parameter로써 generic으로 사용할 수 있는 방법이 없기 때문에 
**IntStream, LongStream, DoubleStream** 이 특별한 인터페이스가 생성되었다. 
이 새로운 interface들은 불필요한 auto-boxing을 안하게 하면서 생산성을 높였다.
    
```java
IntStream intStream = IntStream.range(1, 3);
LongStream longStream = LongStream.rangeClosed(1, 3);
```

자바8에서 부터, Random class가 원시 stream을 생성하기 위한 넓은 범위의 함수를 공급한다. 예를 들어 아래 코드는 DoubleStream을 만들고 이것은 3의 원소를 갖고 있다.
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
중간 연산자를 호출 하는 동안은 stream을 인스턴스화 할 수 있고 접근가능한 참조를 가질 수 있다. 최종 연산 실행할때는 stream에 접근이 불가능하다.

이 것을 증명하기 위해서, 잠시동안 베스트 프랙티스에서 명령어가 순서대로 이어지는 것을 잊어보자. 이것은 불필요하게 장황할 뿐만아니라, 일반적으로 아래 코드만이 유효하다.
```java
Stream<String> stream = Stream.of("a", "b", "c").filter(element -> element.contains("b"));
Optional<String> anyElement = stream.findAny();
```
하지만 최종 연산 호출 후에 같은 참조를 재사용하기 위한 시도는 _IllegalStateException_ 를 발생시킬 것이다:
```java
Optional<String> firstElement = stream.findFirst();
```
_IllegalStateException_ 이 RuntimeException이기 때문에 컴파일러는 이 문제를 문제 삼지 않을 것이다. 그래서 **Java8 Stream을 재사용할 수 없다는 것을 기억하는게 중요하다.**
이런 종류의 동작은 논리적으로 동작한다. 그래서 원소를 저장하지 않고 함수형 스타일에서의 원소에 관련하 소스가 유한한 순서로 동작될 수 있게 설계한다. 그래서 앞의 코드를 적절하게 동작하게 하기 위해서 아래와 같이 만들어야 한다.
```java
List<String> elements = Stream.of("a", "b", "c").filter(element -> element.contains("b")).collect(Collectors.toList());

Optional<String> anyElement = elements.stream().findAny();
Optional<String> firstElement = elements.stream().findFirst();
```
    
### 4. Stream Pipeline

### 5. Lazy Invocation

### 6. Order of Execution

### 7. Stream Reduction

### 8. Parallel Streams

### 9. Conclusion

