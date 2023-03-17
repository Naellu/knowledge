## 스트림(Stream)

데이터 소스가 무엇이든 간에 동일한 방식으로 다룰 수 있게 해주는 java8부터 도입된 신기술  
배열, 컬렉션 그리고 파일에 저장된 데이터도 모두 같은 방식으로 다룰 수 있다

<br>

**특징**

- 스트림은 데이터 소스로 부터 데이터를 **읽기(read)** 만 하지, 소스를 변경하진 않는다
- Iterator처럼 일회용이다, 필요하다면 스트림을 다시 생성해 사용해야한다
- 핵심은 **내부 반복** 이다, 반복문을 메서드 내부에 숨겼다는 것을 의미한다

    ```java
    for(String str : strList)
    	System.out.println(str);
    
    ↓
    
    stream.forEach(System.out::println);
    ```

- 최종 연산이 수행되기 전까지는 중간 연산이 수행되지 않는다
    - 최종 연산이 수행되어야 스트림의 요소들이 중간 요소를 거쳐 최종연산에서 소모된다


- 병렬 처리가 쉽다
    - 기본적으론 병렬 스트림이 아니지만 parallel()을 호출하면 병렬 연산을 가능케 한다
  

- 다만 속도차이에서 스트림이 월등하거나 그렇진 않으며 오히려 더 느릴 수도 있다
    - 오직 코드를 간결하게 만드는 의미로 보는 게 낫다

<br>

### **스트림 만들기**

스트림을 작업하기전에 작업할 스트림이 필요하다  
스트림의 소스는 배열, 컬렉션, 임의의 수 등 다양한 종류가 있다

<br>

- 컬렉션

Collection에 stream()이 정의되어 있어 컬렉션의 자손인 List와 Set을 구현한 클래스 모두 스트림을 생성할 수 있다

```java
List<Integer> list = Arrays.asList(1,2,3,4,5); // 가변인자
Stream<Integer> intStream = list.stream();    // list를 소스로 하는 스트림 생성

intStream.forEach(System.out::println); // 스트림의 모든 요소 출력
~~intStream.forEach(System.out::println);~~
```

> 밑의 forEach로 스트림의 요소를 소모하며 작업했기에 같은 스트림에 forEach를 두 번은 호출할 수 없다
>

<br>


- 배열

배열을 소스로 하는 스트림 생성 메서드는 Stream과 Arrays에 static 메서드로 정의되어 있다

생성은 다음과 같다

```java
1. 문자열 스트림
Stream<String> strStream = Stream.of("a","b","c"); // 가변인자
Stream<String> strStream = Stream.of(new String[]{"a","b","c"});
Stream<String> strStream = Arrays.Stream(new String[]{"a","b","c"});
Stream<String> strStream = Arrays.Stream(new String[]{"a","b","c"}, 0, 3);

2. int, long, double 등 기본형 스트림
IntStream IntStream.of(int... values)
IntStream IntStream.of(int[])
IntStream Arrays.stream(int[])
IntStream Arrays.stream(int[] array, int startInclusive, int endExclusive)
```

<br>


- 임의의 수

난수를 생성하는데 사용되는 Random클래스에 스트림 메서드가 있다

```java
IntStream ints() / LongStream longs() / DoubleStream doubles()
```

위 메서드들은 크기가 정해지지 않은 무한 스트림이다

limit()메서드를 사용해 크기를 제한할 수 있다

```java
IntStream intStream = new Random().ints(); // 무한 스트림
intStream.limit(5).forEach(System.out::println); // 5개 요소만 출력

// 매개변수에 스트림의 크기 지정을 할 수 있다
IntStream intStream = new Random().ints(5); // 크기가 5인 난수 스트림 반환
```

<br>


- 특정범위 정수

IntStream과 longStream은 지정된 범위의 연속된 정수를 스트림으로 생성해 반환할 수 있다

```java
IntStream intStream = IntStream.range(1, 5) // 1,2,3,4
IntStream intStream = IntStream.rangeClosed(1, 5) // 1,2,3,4,5
```

<br>


- 람다식

Stream클래스의 iterate()와 generate()는 람다식을 매개변수로 받아 람다식에 의해 계산되는 값을 요소로 하는 무한 스트림을 생성한다

```java
static <T> Stream<T> iterate(T seed, UnaryOperator<T> f)
static <T> Stream<T> generate(Supplier<T> s)
```

<br>


iterate는 seed값부터 시작해서 람다식 f에 의해 계산된 결과를 다시 seed값으로 해서 계산을 반복한다

```java
Stream<Integer> evenStream = Stream.iterate(0, n->n+2); // 0,2,4,6
```

> 0부터 시작해 값이 2씩 증가하는 예제
>

<br>


generate도 람다식에 의해 계산된 무한스트림을 생성해 반환하지만 이전결과를 이용해 다음 요소를 계산하진 않는다

```java
Stream<Double> randomStream = Stream.generate(Math::random);
Stream<Integer> oneStream = Stream.generate(()->1);
```

<br>


주의할 점은 iterate와 generate로 생성된 스트림은 기본형 스트림 타입의 참조변수로 다룰 수 없다

```java
IntStream evenStream = Stream.iterate(0, n->n+2); // error
DoubleStream randomStream = Stream.generate(Math::random); // error

↓

IntStream evenStream = Stream.iterate(0, n->n+2).mapToInt(Integer::valueOf);
Stream<Integer> stream = evenStream.boxed(); // IntStream -> Stream<Integer>
```

변환하고 싶다면 mapToInt()같은 메서드로 변환해야한다

<br>


- 파일과 빈 스트림

지정된 디렉토리(dir)에 있는 파일의 목록을 소스로 하는 스트림을 생성해서 반환한다

```java
Stream<Path>  Files.list(Path dir)

..

Stream<String> Files.lines(Path path)
Stream<String> Files.lines(Path path, Charset cs)
Stream<String> lines() // BufferedReader클래스의 메서드
```

스트림에 연산을 수행한 결과가 하나도 없을 때, null보단 빈 스트림을 반환하는 것이 좋다

```java
Stream emptyStream = Stream.empty(); // empty()는 빈 스트림을 생성해서 반환한다
long count = emptyStream.count(); // count의 값은 0
```

<br>


### 스트림 연산

스트림이 제공하는 다양한 연산을 이용해 복잡한 작업을 간단히 처리할 수 있다  
스트림이 정의된 메서드 중에서 데이터 소스를 다루는 작업을 수행하는 것을 연산이라고 한다  
중간 연산은 연산 결과를 스트림으로 반환해 중간 연산을 연속해서 사용할 수 있지만  
최종 연산은 스트림의 요소를 소모하면서 작업해 연산 결과를 뽑아내기에 **단 한번만 연산이 가능**하다

```java
stream.distinct().limit(5).sorted().forEach(System.out::println)
          중간      중간      중간          최종 연산
```

이렇게 중간 연산과 최종연산이 합쳐서 만든 것을 스트림 파이프라인`stream pipelines`이라고 한다

<br>


### 중간 연산(Intermediate operation)

스트림을 반환하지 않고 가공하는 작업이다  
최종 연산과의 차이는 단지 스트림을 반환하냐 반환하지 않냐의 차이이다

> 모르겠다면 소스 코드로 들어가서 리턴 타입을 보자
>

중간 연산의 경우 Lazyness-seeking이다  
필터링, 매핑, 혹은 중복제거와 같은 많은 스트림 작업을 느리게 수행하여 최적화를 할 수 있게 한다  

예를 들어 중간 연산중에서 요소를 필터링하는 filter()를 적용했을 때  
실제로 필터링을 수행하지는 않고 조건과 일치하는 요소를 포함한 새로운 스트림을 생성한다  
그리고 스트림 파이프라인에서 최종 연산이 실행될 때까지는 파이프라인에 들어있는 소스를 탐색하지 않는다  
새로운 스트림을 가지고만 있지 최종 연산이 올 때까지 아무것도 할 수 없는 것이다

<br>


아래는 중간 연산 메서드들이다

<br>


- skip(), limit()

둘 다 스트림의 일부를 잘라낼때 사용한다  
skip(3)은 처음 3개의 요소를 건너뛰고  
limit(5)는 스트림의 요소를 5개로 제한한다

<br>


- filter(), distinct()

distinct는 스트림에서 중복된 요소를 제거한다  
filter(**Predicate<? super T> predicate**)는 지정된 조건(predicate)에 맞지 않는 요소를 걸러낸다

```java
intStream.distinct().forEach(System.out::print);

intStream.filter(i -> i%2==0).forEach(System.out::print);

intStream.filter(i -> i%2!=0 && i%3!=0).forEach(System.out::print);
intStream.filter(i -> i%2!=0)
				 .filter(i -> i%3!=0).forEach(System.out::print);
```

<br>


- sorted()

스트림을 정렬할때 sorted를 사용한다  
sorted(Comparator<? super T> comparator)에 람다식을 넣을 수도 있다  
JDK1.8부터 Comparator인터페이스에 static메서드, 디폴트 메서드가 추가되어 이를 이용해 정렬을 쉽게 할 수 있다

```java
studentStream.sorted(Comparator.comparing(Student::getBan)
                .thenComparing(Student::getTotalScore)
                .thenComparing(Student::getName)
                .forEach(System.out::println);
```

> 학생 스트림을 반별, 성적순, 이름순으로 정렬하여 출력한 예시
>

<br>


- map()

스트림 요소에 저장된 값 중 원하는 필드만 뽑아내거나 특정 형태로 변환해야 할 때 사용한다

```java
Stream<R> map(Function<? super T,? extends R> mapper)
```

매개변수로 T타입을 R타입으로 변환해서 반환하는 함수를 지정해야한다

- map() 사용 예제

    ```java
    import java.io.*;
    import java.util.stream.*;
    
    public class Practice {
        public static void main(String[] args) {
    
            File[] fileArr = {new File("Ex1.java"), new File("Ex1.bak"),
                    new File("Ex2.java"), new File("Practice"), new File("Ex1.txt")
            };
    
            Stream<File> fileStream = Stream.of(fileArr);
    
            // map()으로 Stream<File>을 Stream<String>으로 변환
            Stream<String> filenameStream = fileStream.map(File::getName);
            filenameStream.forEach(System.out::println); // 모든 파일의 이름을 출력
    
            fileStream = Stream.of(fileArr);  // 스트림을 다시 생성
    
            fileStream.map(File::getName)               // Stream<File> -> Stream<String>
                    .filter(s -> s.indexOf('.')!=-1)        // 확장자가 없는 것은 제외
                    .map(s->s.substring(s.indexOf('.')+1))  // 확장자만 추출
                    .map(String::toUpperCase)           // 모두 대문자로 변환
                    .distinct()                         // 중복 제거
                    .forEach(System.out::print);        // JAVABAKTXT
    
            System.out.println();
        }
    }
    ```


<br>


- peek()

연산과 연산사이 올바르게 처리되었는지 확인할 때 사용한다

filter()나 map()의 결과를 확인할 때 유용하게 사용된다

<br>


- flatMap()

스트림 타입이 Stream<T[ ]>인 경우 Stream<T>로 변환해야 작업이 더 편리한데 그럴 때 사용한다

- flatMap() 사용 예제

    ```java
    import java.util.*;
    import java.util.stream.*;
    
    public class Practice {
    
        public static void main(String[] args) {
    
            Stream<String[]> strArrStrm = Stream.of(
                    new String[]{"abc", "def", "jkl"},
                    new String[]{"ABC", "GHI", "JKL"}
            );
    //      Stream<Stream<String>> strStrmStrm = strArrStrm.map(Arrays::stream);
            Stream<String> strStrm = strArrStrm.flatMap(Arrays::stream);
    
            strStrm.map(String::toLowerCase)
                    .distinct().sorted()
                    .forEach(System.out::println);
            System.out.println();
    
            String[] lintArr = {
                    "Believe or not It is true",
                    "Do or do not There is no try"
            };
    
            Stream<String> lintStream = Arrays.stream(lintArr);
            lintStream.flatMap(line -> Stream.of(line.split(" +")))
                    .map(String::toLowerCase)
                    .distinct().sorted()
                    .forEach(System.out::println);
            System.out.println();
        }
    }
    
    ```

  map()을 사용하면 스트림 안에 스트림이 생겨나 Stream<Stream<String>>이 되어버린다

  그래서 flatmap()을 사용해 Stream<String>의 형태로 만들어준 것이다


<br>


### 최종 연산(Terminal operation)

스트림의 요소를 소모하여 결과를 만들어내는 파트이다  
중간 연산을 거치거나 말거나 상관없이 스트림 안의 요소들을 모두 사용한다  
**최종 연산 후 스트림은 닫히게 되고 사용할 수 없다**  
결과는 스트림 요소의 합과 같은 단일 값이거나, 스트림 요소가 담긴 배열,컬렉션일 수 있다


<br>


아래는 최종 연산의 종류들이다

- forEach()

스트림의 요소를 출력하는 용도로 사용한다

<br>

- 조건검사

요소에 대해 지정된 조건에 따라 판별하는 메서드들이 있다  
allMatch(), anyMatch(), noneMatch() 가 있고 이 메서드들은 매개변수로 Predicate를 넣어야하기에 반환타입이 boolean이다

```java
boolean noFailed = stuStream.anyMatch(s->s.getTotalScore()<=100)
```

<br>


성적정보스트림stuStream에서 총점이 100이하인 학생이 있는지 판별하는 간단한 예제

findFirst()는 요소중 조건에 일치하는 첫 번째 것을 반환한다  
findAny()는 병령 스트림인 경우에 사용한다

```java
Optional<Student> stu = stuStream.filter(s->s.getTotalScore()<=100).findFirst();
Optional<Student> stu = parallelStream.filter(s->s.getTotalScore()<=100).findAny();
```

두 메서드 모두 반환타입이 Optional<T>이고 스트림 요소가 없을 때는 비어있는 Optional객체를 반환한다


<br>



- reduce()

스트림의 요소를 줄여나가면서 연산을 수행하고 최종결과를 반환한다  
매개변수 타입이 BinaryOperator<T>인 이유이다  
처음 두 요소를 가지고 연산한 결과로 그 다음 요소와 연산한다

```java
Optional<T> reduce(BinaryOperator<T> accmulator)
```

<br>


연산결과의 초기값(identity)을 갖는 reduce()도 있는데, 이 메서드들은 초기값과 스트림의 첫 번째 요소로 연산을 시작한다  
스트림 요소가 없는 경우 초기값이 반환되므로 반환 타입은 Optional<T>가 아니라 T다

```java
T reduce(T identity, BinaryOperator<T> accumulator)
U reduce(U identity, BiFunction<U,T,U> accumulator, BinaryOperator<U> combiner)
```

reduce()의 내부동작은 대략 이렇다

```java
// 일반적인 for문이다
int a = identity; // 초기값 a에 저장

for(int b : stream)
	a = a + b; // 모든 요소의 값을 a에 저장

↓

// 위의 for문이 내장되어있다고 생각하자
// 어떤 연산(accumulator)으로 요소를 줄일건지 생각하자
T reduce(T identity, BinaryOperator<T> accumulator) {
	T a = identity;

	for(T b : stream)
		a = accumulator.apply(a, b);

	return a;
}
```

- reduce 사용 예제

    ```java
    import java.util.Optional;
    import java.util.OptionalInt;
    import java.util.stream.IntStream;
    import java.util.stream.Stream;
    
    public class Practice {
    
        public static void main(String[] args) {
    
            String[] strArr = {
              "Inheritance", "Java", "Lambda", "stream", "OptionalDouble", "IntStream", "count", "sum"
            };
    
            Stream.of(strArr).forEach(System.out::println);
    
            boolean noEmptyStr = Stream.of(strArr).noneMatch(s -> s.length() == 0);
            Optional<String> sWord = Stream.of(strArr)
                    .filter(s -> s.charAt(0) == 's').findFirst();
    
            System.out.println("noEmptyStr=" + noEmptyStr);
            System.out.println("sWord=" + sWord.get());
    
            IntStream intStream1 = Stream.of(strArr).mapToInt(String::length);
            IntStream intStream2 = Stream.of(strArr).mapToInt(String::length);
            IntStream intStream3 = Stream.of(strArr).mapToInt(String::length);
            IntStream intStream4 = Stream.of(strArr).mapToInt(String::length);
    
            int count = intStream1.reduce(0, (a, b) -> a + 1);
            int sum = intStream2.reduce(0, (a, b) -> a + b);
    
            OptionalInt max = intStream3.reduce(Integer::max);
            OptionalInt min = intStream4.reduce(Integer::min);
            System.out.println("count=" + count);
            System.out.println("sum=" + sum);
            System.out.println("max=" + max.getAsInt());
            System.out.println("min=" + min.getAsInt());
        }
    }
    ```


<br>


- collect()

collect()가 스트림의 요소를 수집하려면 어떻게 수집할건지에 대한 방법이 정의되어 있어야하는데 이걸 정의한 것이 컬렉터`Collector`이다  
직접 구현하거나 `Collectors`클래스를 이용할 수 있다  
collect()의 매개변수는 Collector를 구현한 클래스의 객체인데 이 객체에 구현된 방법대로 스트림의 요소를 수집한다

<br>


**스트림을 컬렉션, 배열로 변환**

```java
List<String> names = stuStream.map(Student::getName)
                                .collect(Collectors.toList());
ArrayList<String> list = names.stream()
                                .collect(Collectors.toCollection(ArrayList::new));
---

Map<String, Person> map = personStream.collect(Collectors.toMap(p->p.getRegId(), p->p));
```

Collector 클래스의 toList같은 메서드를 사용하면 스트림의 요소를 컬렉션에 수집한다  
List, Set이 아닌 특정 컬렉션이라면 toCollection에 원하는 컬렉션의 생성자 참조를 넣어주면 된다  
Map은 어떤 키와 값을 사용할지 지정해줘야 한다  
스트림에 저장된 요소들을 ‘T[ ]’ 타입 배열로 변환하려면 toArray()를 사용하면 된다

<br>


**스트림의 통계 counting(), summingInt()**

최종연산들이 제공하는 통계 정보를 collect()로 똑같이 얻을 수 있다

- 통계 예시

  굳이 collect를 안쓰고도 구현할 수 있지만 사용예시를 든 것이다

    ```java
    long count = stuStream.count();
    long count = stuStream.collect(counting());
    
    long totalScore = stuStream.mapToInt(Student::getTotalScore).sum();
    long totalScore = stuStream.collect(summingInt(Student::getTotalScore));
    
    Optional<Student> topStudent = stuStream.max(Comparator.comparingInt(Student::getTotalScore));
    Optional<Student> topStudent = stuStream.collect(maxBy(Comparator.comparingInt(Student::getTotalScore)));
    
    IntSummaryStatistics stat = stuStream.mapToInt(Student::getTotalScore).summaryStatistics();
    IntSummaryStatistics stat = stuStream.collect(summarizingInt(Student::getTotalScore));
    ```

<br>



**스트림 리듀싱(reducing)**

리듀싱 또한 collect로 사용할 수 있다  
IntStream에는 매개변수 3개짜리 collect만 정의되어 있기 때문에  
boxed를 통해 IntStream을 Stream<Integer>로 변환하여 매개변수 1개짜리 collect로 사용할 수 있다

- 리듀싱 예시

    ```java
    IntStream intStream = new Random().ints(1,46).distinct().limit(6);
    
    OptionalInt max = intStream.reduce(Integer::max);
    Optional<Integer> max = intStream.boxed().collect(reducing(Integer::max));
    
    long sum = intStream.reduce(0, (a,b) -> a + b);
    long sum = intStream.boxed().collect(reducing(0,(a,b) -> a + b));
    
    int grandTotal = stuStream.map(Student::getTotalScore).reduce(0, Integer::sum);
    int grandTotal = stuStream.collect(reducing(0, Student::getTotalScore, Integer::sum));
    ```

<br>



**스트림을 문자열로 결합 joining**

joining은 문자열 스트림의 모든 요소를 하나의 문자열로 연결해 반환한다  
스트림 요소가 String이나 StringBuffer처럼 CharSequence의 자손인 경우에만 가능하다  
아닌 경우 map을 이용해 스트림 요소를 문자열로 변환해야 한다

- joining 예시

    ```java
    String studentNames = stuStream.map(Student::getName).collect(joining());
    String studentNames = stuStream.map(Student::getName).collect(joining(","));
    String studentNames = stuStream.map(Student::getName).collect(joining("," , "[" , "]"));
    ```


<br>


**스트림 그룹화와 분할**

그룹화는 스트림의 요소를 특정 기준으로 그룹화하는 것이고  
분할은 스트림의 요소를 두 가지, 지정된 조건에 일치하는 그룹과 그렇지 않은 그룹으로의 분할을 뜻한다  
- 분할 - partitioningBy() 예시

    ```java
    import java.util.List;
    import java.util.Map;
    import java.util.Optional;
    import java.util.stream.Stream;
    
    import static java.util.Comparator.comparingInt;
    import static java.util.stream.Collectors.collectingAndThen;
    import static java.util.stream.Collectors.counting;
    import static java.util.stream.Collectors.maxBy;
    import static java.util.stream.Collectors.partitioningBy;
    
    class Student {
        String name;
        boolean isMale;
        int hak;
        int ban;
        int score;
    
        public Student(String name, boolean isMale, int hak, int ban, int score) {
            this.name = name;
            this.isMale = isMale;
            this.hak = hak;
            this.ban = ban;
            this.score = score;
        }
    
        public String getName() {return name;}
        public boolean isMale() {return isMale;}
        public int getHak() {return hak;}
        public int getBan() {return ban;}
        public int getScore() {return score;}
    
        public String toString() {
            return String.format("[%s, %s, %d학년 %d반, %3d점]",
                    name, isMale ? "남" : "여", hak, ban, score);
        }
    
        enum Level {HIGH, MID, LOW,}
    
    }
    
    public class Practice {
    
        public static void main(String[] args) {
    
            Student[] stuArr = {
                    new Student("나자바", true, 1, 1, 300),
                    new Student("김지미", false, 1, 1, 250),
                    new Student("김자바", true, 1, 1, 200),
                    new Student("이지미", false, 1, 2, 150),
                    new Student("남자바", true, 1, 2, 100),
                    new Student("안지미", false, 1, 2, 50),
                    new Student("황지미", false, 1, 3, 100),
                    new Student("강지미", false, 1, 3, 150),
                    new Student("이자바", true, 1, 3, 200),
    
                    new Student("나자바", true, 2, 1, 300),
                    new Student("김지미", false, 2, 1, 250),
                    new Student("김자바", true, 2, 1, 200),
                    new Student("이지미", false, 2, 2, 150),
                    new Student("남자바", true, 2, 2, 100),
                    new Student("안지미", false, 2, 2, 50),
                    new Student("황지미", false, 2, 3, 100),
                    new Student("강지미", false, 2, 3, 150),
                    new Student("이자바", true, 2, 3, 200),
            };
    
            System.out.printf("1. 단순분할(성별로 분할)%n");
            Map<Boolean, List<Student>> stuBySex = Stream.of(stuArr)
                    .collect(partitioningBy(Student::isMale));
    
            List<Student> maleStudent = stuBySex.get(true);
            List<Student> femaleStudent = stuBySex.get(false);
    
            for (Student s : maleStudent) System.out.println(s);
            for (Student s : femaleStudent) System.out.println(s);
    
            System.out.printf("%n2. 단순분할 + 통계(성별 학생수)%n");
            Map<Boolean, Long> stuNumBySex = Stream.of(stuArr)
                    .collect(partitioningBy(Student::isMale, counting()));
    
            System.out.println("남학생 수 :"+ stuNumBySex.get(true)); 
            System.out.println("여학생 수 :"+ stuNumBySex.get(false));
    
            System.out.printf("%n3. 단순분할 + 통계(성별1등)%n");
            Map<Boolean, Optional<Student>> topScoreBySex = Stream.of(stuArr)
                    .collect(partitioningBy(Student::isMale,
                            maxBy(comparingInt(Student::getScore))
                    )); // 반환타입이 Optional<Student>
            System.out.println("남학생 1등 :"+ topScoreBySex.get(true));
            System.out.println("여학생 1등 :"+ topScoreBySex.get(false));
    
            Map<Boolean, Student> topScoreBySex2 = Stream.of(stuArr)
                    .collect(partitioningBy(Student::isMale,
                            collectingAndThen(
                                    maxBy(comparingInt(Student::getScore)), Optional::get
                            ) // collectionAndThen과 Optional::get을 사용해 Student를 반환
                    ));
            System.out.println("남학생 1등 :"+ topScoreBySex2.get(true));
            System.out.println("여학생 1등 :"+ topScoreBySex2.get(false));
    
            System.out.printf("%n4. 다중분할(성별 불합격자, 100점 이하)%n");
            Map<Boolean, Map<Boolean, List<Student>>> failedByStuSex =
                    Stream.of(stuArr).collect(partitioningBy(Student::isMale,
                            partitioningBy(s -> s.getScore() <= 100))
                    ); // partitioningBy를 한번 더 사용해 이중 분할
            List<Student> failedMaleStu = failedByStuSex.get(true).get(true);
            List<Student> failedFemaleStu = failedByStuSex.get(false).get(true);
    
            for (Student s : failedMaleStu) System.out.println(s);
            for (Student s : failedFemaleStu) System.out.println(s);
        }
    }
    ```


<br>


- 그룹화 - groupingBy() 예시

    ```java
    import java.util.List;
    import java.util.Map;
    import java.util.Optional;
    import java.util.Set;
    import java.util.TreeSet;
    import java.util.stream.Stream;
    
    import static java.util.Comparator.comparingInt;
    import static java.util.stream.Collectors.collectingAndThen;
    import static java.util.stream.Collectors.counting;
    import static java.util.stream.Collectors.groupingBy;
    import static java.util.stream.Collectors.mapping;
    import static java.util.stream.Collectors.maxBy;
    import static java.util.stream.Collectors.toSet;
    
    class Student {
        String name;
        boolean isMale;
        int hak;
        int ban;
        int score;
    
        public Student(String name, boolean isMale, int hak, int ban, int score) {
            this.name = name;
            this.isMale = isMale;
            this.hak = hak;
            this.ban = ban;
            this.score = score;
        }
    
        public String getName() {return name;}
        public boolean isMale() {return isMale;}
        public int getHak() {return hak;}
        public int getBan() {return ban;}
        public int getScore() {return score;}
    
        public String toString() {
            return String.format("[%s, %s, %d학년 %d반, %3d점]",
                    name, isMale ? "남" : "여", hak, ban, score);
        }
    
        enum Level {HIGH, MID, LOW,}
    
    }
    
    public class Practice {
    
        public static void main(String[] args) {
    
            Student[] stuArr = {
                    new Student("나자바", true, 1, 1, 300),
                    new Student("김지미", false, 1, 1, 250),
                    new Student("김자바", true, 1, 1, 200),
                    new Student("이지미", false, 1, 2, 150),
                    new Student("남자바", true, 1, 2, 100),
                    new Student("안지미", false, 1, 2, 50),
                    new Student("황지미", false, 1, 3, 100),
                    new Student("강지미", false, 1, 3, 150),
                    new Student("이자바", true, 1, 3, 200),
    
                    new Student("나자바", true, 2, 1, 300),
                    new Student("김지미", false, 2, 1, 250),
                    new Student("김자바", true, 2, 1, 200),
                    new Student("이지미", false, 2, 2, 150),
                    new Student("남자바", true, 2, 2, 100),
                    new Student("안지미", false, 2, 2, 50),
                    new Student("황지미", false, 2, 3, 100),
                    new Student("강지미", false, 2, 3, 150),
                    new Student("이자바", true, 2, 3, 200),
            };
    
            System.out.printf("1. 단순그룹화(반별로 그룹화)%n");
            Map<Integer, List<Student>> stuByBan = Stream.of(stuArr)
                    .collect(groupingBy(Student::getBan));
    
            for (List<Student> ban : stuByBan.values()) {
                for (Student s : ban) {
                    System.out.println(s);
                }
            }
    
            System.out.printf("%n2. 단순그룹화(성적별로 그룹화)%n");
            Map<Student.Level, List<Student>> stuByLevel = Stream.of(stuArr)
                    .collect(groupingBy(s -> {
                        if (s.getScore() >= 200) return Student.Level.HIGH;
                        else if (s.getScore() >= 100) return Student.Level.MID;
                        else return Student.Level.LOW;
                    }));
    
            TreeSet<Student.Level> keySet = new TreeSet<>(stuByLevel.keySet());
    
            for (Student.Level key : keySet) {
                System.out.println("[" + key + "]");
    
                for (Student s : stuByLevel.get(key)) {
                    System.out.println(s);
                }
                System.out.println();
            }
    
            System.out.printf("%n3. 단순그룹화 + 통계(성적별 학생 수)%n");
            Map<Student.Level, Long> stuCntByLevel = Stream.of(stuArr)
                    .collect(groupingBy(s -> {
                        if (s.getScore() >= 200) return Student.Level.HIGH;
                        else if (s.getScore() >= 100) return Student.Level.MID;
                        else return Student.Level.LOW;
                    }, counting()));
    
            for (Student.Level key : stuCntByLevel.keySet())
                System.out.printf("[%s] - %d명, ", key, stuCntByLevel.get(key));
            System.out.println();
    
            System.out.printf("%n4. 다중그룹화(학년별, 반별)%n");
            Map<Integer, Map<Integer, List<Student>>> stuByHakAndBan =
                    Stream.of(stuArr)
                            .collect(groupingBy(Student::getHak,
                                    groupingBy(Student::getBan)
                            ));
    
            for (Map<Integer, List<Student>> hak : stuByHakAndBan.values()) {
                for (List<Student> ban : hak.values()) {
                    System.out.println();
                    for (Student s : ban)
                        System.out.println(s);
                }
            }
    
            System.out.printf("%n5. 다중그룹화 + 통계(학년별, 반별1등)%n");
            Map<Integer, Map<Integer, Student>> topStuByHakAndBan =
                    Stream.of(stuArr)
                            .collect(groupingBy(Student::getHak,
                                    groupingBy(Student::getBan,
                                            collectingAndThen(
                                                    maxBy(comparingInt(Student::getScore))
                                                    , Optional::get
                                            )
                                    )
                            ));
    
            for (Map<Integer, Student> ban : topStuByHakAndBan.values())
                for (Student s : ban.values())
                    System.out.println(s);
    
            System.out.printf("%n6. 다중그룹화 + 통계(학년별, 반별 성적그룹)%n");
            Map<String, Set<Student.Level>> stuByScoreGroup = Stream.of(stuArr)
                    .collect(groupingBy(s -> s.getHak() + "-" + s.getBan(),
                            mapping(s -> {
                                if (s.getScore() >= 200) return Student.Level.HIGH;
                                else if (s.getScore() >= 100) return Student.Level.MID;
                                else return Student.Level.LOW;
    
                            }, toSet())
                    ));
    
            Set<String> keySet2 = stuByScoreGroup.keySet();
    
            for (String key : keySet2) {
                System.out.println("[" + key + "]" + stuByScoreGroup.get(key));
            }
        }
    }
    ```

<br>

참고 :  

- 자바의 정석  

- 자바 공식문서 : [java.util.stream (Java Platform SE 8 )](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Statelessness)