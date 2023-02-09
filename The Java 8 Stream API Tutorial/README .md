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
데이터 소스의 원소에 대해서 명령을 수행하고 결과를 합치려면 3부분이 필요한데: source, 중간 연산자, 최종 연산자.
    
중간 연산자는 새로운 수정된 stream을 리턴한다. 예를 들어 원소 없이 기존 스트림에 대한 새로운 stream을 생성하기 위해서는 _skip()_ 함수가 쓰여져야 한다.
```java
Stream<String> onceModifiedStream = Stream.of("abcd", "bbcd", "cbcd").skip(1);
```
만약 한번 이상의 수정이 필요하면, 중간 연산자를 연결할 수 있다. 현재 Stream<String>의 모든 원소를 대상으로 첫번째 몇 글자를 바꿔야 하는게 필요하다고 가정해보자. 아래와 같이 _skip()_ , _map()_ 함수로 연결할 수 있다: 
```java
Stream<String> twiceModifiedStream = stream.skip(1).map(element -> element.substring(0, 3));
```
map() 함수는 파라미터로써 람다표현을 가져간다. 만약 람다에 대해서 더 공부하길 원하면 이 튜토리얼을 확인하라 [Lambda Expressions and Functional Interfaces: Tips and Best Practices.](https://www.baeldung.com/java-8-streams#5-streamgenerate)

stream 그 자체로써는 의미가 없다; 유저는 최종 연산에 흥미를 가진다. 그리고 이것은 어떤 타입의 값이 되거나 스트림의 모든 원소에 적용되는 작업이다.
stream을 사용하는 정확하고 가장 편리한 방법은 stream source, 중간연산자 그리고 최종연산자의 체인인 stream pipeline을 사용하는 것이다.
    
### 5. Lazy Invocation
 **중간연산자는 게으르다.** 이 의미는 최종 연산자의 필요할 때만 발생될 것이라는 것이다. 예를 들어 _wasCalled()_라는 함수를 호출한다고 하자. 여기서 함수 안에 있는 카운터는 매번 호출할때 마다 값이 증가한다.
```java
private long counter;

private void wasCalled() {
    counter++;
}
```
이제 filter()에서 _wasCalled()_ 함수를 호출해보자.
```java
List<String> list = Arrays.asList("abc", "abc2", "abc3");
counter = 0;
Stream<String> stream = list.stream().filter(element -> {
    wasCalled();
    return element.contains("2");
});
```
소스에 3개의 원소가 들어있기 때문에 filter함수는 3번 호출될 것이고 counter 변수는 3이 될 것이라고 추정할 것이다. 그러나 이 코드를 돌려보면 counter변수는 전혀바뀌지 않았다. 여전히 0이다. 그래서 filter() 함수는 한번도 불리지 않았다. 그 이유는 최종 연산자를 빼먹었기 때문이다.
    
다시 코드를 map()연산자 그리고 최종연산자인 findFirst()를 더해서 작성해보자. 또 메서드 호출 순서를 추적할 수 있는 기능을 로깅을 통해 추가할 것이다.
```java
 Optional<String> stream = list.stream().filter(element -> {
            log.info("filter() was called" );
            return element.contains("2");
        }).map(element -> {
            log.info("map() was called");
            return element.toUpperCase();
        }).findFirst();
```
결과 로그가 filter()가 2번 호출되고 map()함수가 1번 호출되는 것을 볼 수 있다.
왜냐면 파이프라인이 수직적으로 수행되기 때문이다. 위 코드를 보면 스트임의 첫번째 원소는 filter의 predicate를 만족하지 못했다. filter 함수가 두번째 원소에서 발생했고 filter를 통과했다. 3번째 원소를 호출하지 않고 파이프라인을 통해 map()함수로 갔다.

findFirst() 연산자는 한가지 원소만 만족시킨다. 그래서 이 예제에서 지연 호출은 2개의 함수를 호출하는 것을 피할 수 있게 해준다. (filter에 관련된 호출 한번, map()에 관련된 호출 한번)
### 6. Order of Execution
퍼포먼스적인 관점에서 올바른 순서는 스트림 파이프라인의 체이닝 오퍼레이션 측면에서 가장 중요한 것 중 하나이다.
```java
long size = list.stream().map(element -> {
    wasCalled();
    return element.substring(0, 3);
}).skip(2).count();
```
이 코드 실행은 counter 값을 3만큼 증가시킬 것이다. 이 의미는 stream의 map()함수를 3번 호출한다는 것이다. 하지만 size 값은 1이다. 그래서 결과 stream에는 원소 하나 밖에 남지 않고 비싼 map()함수를 이유도 없이 3번 중 2번을 호출했다.

만약 skip()과 map()함수에 대한 순서를 바꾼다면 counter는 1이 될 것이다. 그래서 map()함수도 한번만 호출될 것이다.
```java
long size = list.stream().skip(2).map(element -> {
    wasCalled();
    return element.substring(0, 3);
}).count();
```
이 것을 통해 가져올 수 있는 룰: 스트림의 사이즈를 줄일 수 있는 중간연산자는 각 원소들에 적용되는 연산자 전에 위치되어야 한다. 그래서 skip,  filter, distinct와 같은 함수를 스트림 파이프라인의 위쪽에 둬야한다.
### 7. Stream Reduction
Stream API는 stram을 타입이나 원시로 모으는 최종연산자를 많이 갖고 있다: count(), max(), min() 그리고 sum(). 그러나 그 연산자들은 미리정의된 구현에 의해서 동작한다. 그렇다면 만약 개발자들이 스트림의 축소 매커니즘을 커스터마이즈해야한다면 어떻게 해야할까? 2가지 방법이 있는데, reduce() 와 collect() 메소드를 사용할 수 있다. 
    
### 7.1 reduce() 함수
이 메소드에 대해서 3가지 변형이 있다. 이것은 시그니처와 반환 타입에 따라 다르다. 
이것들은 아래와 같은 파라미터를 가질 수 있다:
- identity
    만약 stream이 비어있고 쌓일 것이 없다면 accumulator의 초기값 또는 기본값이다.
- accumulator
    이 함수는 원소 집합 로직에 대한 것이다. accumulator는 reducing의 모든 단계에서 새로운 값을 생성하기 때문에, 새로운 값의 양은 스트림의 사이즈와 같고 마지막 값만 유용하다. 이것은 퍼포먼스적으로 좋진 않다.
- combiner
    accumulator의 결과를 모은다. 서로 다른 스레드에서 accumulators의 결과를 줄이기 위한 병렬모드 일때만 combiner를 호출할 수 있다.
    
```java
    OptionalInt reduced = IntStream.range(1, 4).reduce((a, b) -> a + b);
```
reduced = 6(1 + 2 + 3)

```java
int reducedTwoParams = IntStream.range(1, 4).reduce(10, (a, b) -> a + b);
```
reducedTwoParams = 16(10 + 1 + 2 + 3)

```java
int reducedParams = Stream.of(1, 2, 3)
	.reduce(10, (a, b) -> a + b, (a, b) -> {
		  log.info("combiner was called");
		  return a + b;
	});
```
이 결과는 앞에 있는 코드와 같은 결과 16이 나온다 .그리고 로그도 안찍힌다. 이 말은 즉 combiner가 호출되지 않았다는 것이다. combiner를 동작하게 하기 위해 stream이 병렬이 되어야 한다.
    
```java
int reducedParams = Arrays.asList(1, 2, 3).parallelStream()
	.reduce(10, (a, b) -> a + b, (a, b) -> {
		  log.info("combiner was called");
		  return a + b;
	});
```
이 
### 7.2 collect() 함수
 stream의 reduction은 또한 또다른 최종 연산자인 collect를 이용해 실행될 수 있다. 이것은 type 컬랙터의 아규먼트를 받는다. 그리고 이 컬렉터는 reduction에 대한 매커니즘을 구체화 한다. 이것은 대부분의 공통 연산자를 위해 collector를 미리 생성하고 미리 정의 한다. collectors 타입의 도움으로 접근할 수 있다.

이 section에서는 stream 소스를 아래 리스트로 사용할 것이다.
```java
List<Product> productList = Arrays.asList(new Product(23, "potatoes"),
  new Product(14, "orange"), new Product(13, "lemon"),
  new Product(23, "bread"), new Product(13, "sugar"));
```
#### stream을 collection으로 바꾸기(collection, list 또는 set)
```java
List<String> collectorCollection = productList.stream().map(Product::getName).collect(Collectors.toList());
```
#### String으로 바꾸기
```java
String listToString = productList.stream().map(Product::getName).collect(Collectors.joining(",", "[", "]"));
```
### 8. Parallel Streams
자바8 이전에 병렬처리하기 복잡했었다. 
    
### 9. Conclusion

