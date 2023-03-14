# 제네릭(Generic)

- 다양한 타입의 객체들을 다루는 메서드나 컬렉션 클래스에 컴파일 시 타입 체크를 해주는 기능
    - 객체의 타입을 컴파일 시에 체크하기 때문에 객체의 **타입 안정성을 높이고 형변환의 번거로움을 줄일 수 있다**

```java
ArrayList<Tv> tvList = new ArrayList<Tv>();
tvList.add(new Tv()); // OK
tvList.add(new Audio()); // 컴파일 에러, Tv만 가능

---

ArrayList<> tvList = new ArrayList<>();
tvList.add(new Tv());
Tv t = (Tv)tvList.get(0);

↑
↓

ArrayList<Tv> tvList = new ArrayList<Tv>();
tvList.add(new Tv());
Tv t = tvList.get(0); // 형변환이 필요가 없다
```

- `class Box<T> {..}` 란 예시코드가 있을때
    - Box<T>는 제네릭클래스이고 T의Box, T Box라고 읽는다
    - T는 타입변수 또는 **타입 매개변수(type parameter)**이다
    - Box는 원시타입이다
- Box<String> b = new Box<String>
    - 여기서 T의 자리인 타입 매개변수에 타입을 지정한 것을 제네릭 타입 호출이라고 한다
    - 지정된 타입 String은 매개변수화된 타입이라고 한다

  <br>
  
<aside>
💡 type parameter와 type argument는 엄연히 다르다
`Foo<T>`에서 `T`가 type parameter, `Foo<String>`에서 `String`이 type argument이다

</aside>

<br>
<br>

  
- 컴파일 후 Box<String>은 원시타입인 Box<>로 바뀐다(**제네릭 타입 제거**)
    - Type Erasure 항목으로

- 제네릭 클래스 객체 생성 시, 참조변수에 지정해준 제네릭과 생성자에 지정해준 제네릭은 일치해야 한다
    - 상속관계간에도 일치해야한다

 <br>
  
  
### Subtypes

```java
Object someObject = new Object();
Integer someInteger = new Integer(10);
someObject = someInteger;   // OK

public void someMethod(Number n) { /* ... */ }
someMethod(new Integer(10));   // OK
someMethod(new Double(10.1));   // OK

public void boxTest(Box<Number> n) { /* ... */ }
box.add(new Integer(10)); // OK
box.add(new Double(10.1)); // OK

public void boxTest(Box<Number> n) { /* ... */ }
box.add(new Box<Integer>(10)); // ??
box.add(new Box<Double>(10.1)); // ???
```

- 일반적인 클래스 관계에서 보면 Integer나 Double의 경우 Number의 하위 클래스다, 그렇기에 이런 경우엔 상위 타입에 하위 타입을 대입 시킬 수 있다
    
    그러나 Box<Number>에는 Box<Integer>나 Box<Double>을 전달할 수 없다
    서브타입(하위 유형)이 아니기 때문이다
    

제네릭에서 매개변수화 타입은 불공변(무공변) 혹은 **불변(invariant)**이기 때문에 Box<Number>는 오직 Number 타입만 가능한 것이다

![Untitled](https://user-images.githubusercontent.com/119831581/224961821-841a4cc6-8844-4ba3-9977-746bea53fd8f.png)

- 제네릭 클래스간 type argument가 같다면 type argument를 바꾸지 않는 한 하위 유형 관계는 유지된다
    - `ArrayList<String>` → `List<String>` → `Collections<String>`
- 제네릭 타입의 형변환은 대입된 타입이 다른 제네릭 타입 간에는 불가능하다
    
  ![Untitled 1](https://user-images.githubusercontent.com/119831581/224961997-eb7733f2-343c-421b-8942-4141a2cf6906.png)
    
    - 일반 클래스 관계에선 Object 하위 클래스로 String이 있기 때문에 당연히 자동으로 변환이 될 것 같아보이지만
    제네릭의 경우 <Object>엔 오직 Object 타입만 들어가야 한다
    - String이 Object의 하위 유형이더라도 
    List<String>은 List<Object>의 하위 유형이 아니다
    - Box<String>이 Box<Object>로 형변환이 되지 않음
    - 하지만 Box<String>이 Box<? extends Object>로 형변환은 가능하다
        - 이것 덕에 매개변수의 다형성이 적용 가능해졌다

<br>

### Type Inference(유형 추론)

유형 추론은 type argument를 결정하기 위해 메서드 호출 및 선언에서 type argument를 지정하지 않아도 알아서 적용시켜주는 java 컴파일러의 기능이다

![Untitled 2](https://user-images.githubusercontent.com/119831581/224962327-95042e1b-5544-4528-a2f5-31aa60f6715a.png)

다만 다이아몬드라고 불리는 `<>` 를 사용하는 경우 인스턴스화 되는 일반 클래스의 실제 유형 매개변수를 유추할 수 있다

- 메서드 선언부에 제네릭 타입이 선언된 메서드를 제네릭 메서드라고 한다
    - ex_) static **<T>** void sort(List<T> list, Comparator<? super T> c) { … }
    접근지시자와 반환타입 사이에 위치하지 않으면 컴파일 에러가 발생한다
    - 메서드에 제네릭 타입을 선언하면 static을 사용할 수 있다
    - 메서드에 선언된 제네릭 타입은 지역 변수를 선언한 것과 같다고 생각하자
        - 이 타입 매개변수는 메서드 내에서만 지역적으로 사용되기 때문

<br>
<br>

### Bounded Type

- 한 종류의 타입만 저장을 해도 아직은 타입 매개변수에 모든 종류의 타입을 지정할 수 있다
    - 이럴때 제한된 제네릭 클래스로 위험성을 더 줄일 수 있다
    - 제네릭 타입에 extends를 사용해 특정 타입의 자손만 대입할 수 있게 만드는 것
    
    ```java
    public class Box<T extends Number>{
    	public void set(T value){};
    }
    
    public static void main(String[] args){
    	Box<Integer> box = new Box<>();
    	box.set("Hi"); // compile error
    }
    
    // Box의 타입을 Number의 서브타입만 들어오도록 허용했다
    // Integer는 Number의 서브타입이기 때문에 Box<Integer>와 같은 선언이 가능하다
    // 하지만 set함수로 문자열을 전달하려 했기 때문에 컴파일 오류가 발생했다
    ```
    
    - 클래스 말고도 인터페이스를 구현해야하는 제약에도 extends를 사용한다
- 타입 매개변수는 한 가지의 bound만 가질 수 있는게 아니라 여러가지 bound를 가질 수 있다
`<T extends B1>` → `<T extends B1 & B2 & B3>`
    - 단 bound 적용시 인터페이스보다 클래스가 먼저 선언되어야 한다 
    → 그렇지 않으면 컴파일 에러 발생
    
- static 멤버에는 타입변수 T를 사용할 수 없다
    - static멤버는 인스턴스변수를 참조할 수 없다
    - 제네릭 배열도 선언은 가능하지만 new T[ ]와 같이 생성은 안된다
        - **컴파일 시점에 T가 어떤 타입이 될지 전혀 알 수 없기 때문**

<br>

### WildCard

제네릭스에서 보이는 `?`  물음표는 와일드 카드다(타입의 상한, 하한을 걸어주는)

- `<? extends T>`는 T와 그 자손들만 가능
- `<? super T>`는 T와 그 조상들만 가능
- <?>는 제한이 없다, 모든 타입이 가능하고 <? extends Object>와 같은 의미이다

와일드카드 `?` 는 알 수 없는 타입이면서 아무거나 될 수 있는 타입을 의미한다

```java
public static double sumOfList(List<? extends Number> list) {
    double s = 0.0;
    for (Number n : list)
        s += n.doubleValue();
    return s;
}

List<Integer> li = Arrays.asList(1, 2, 3);
System.out.println("sum = " + sumOfList(li));

List<Double> ld = Arrays.asList(1.2, 2.3, 3.5);
System.out.println("sum = " + sumOfList(ld));
```

`List<? extends Number>` 가 의미하는 것은 List의 타입이 Number의 서브타입이 될 수 있다는 것이다

그렇기에 Number의 하위 타입인 Integer, Double타입으로 `List<Integer>`, `List<Double>`이 전달될 수 있게 되었다

<br>

### **PECS**

Productor-extends, Consumer-super

컬렉션을 생성하고 소비할때 제네릭을 사용하는 경우 규칙이 있다

```java
public void doSomething(List<? extends MyClass> list) {
  for (MyClass e : list) { // read - Ok
      System.out.println(e);
  }
}

public void doSomething(List<? extends MyClass> list) {
  list.add(new MyClass()); // read - Compile Error
}

public void doSomething(List<? super MyClass> list) {
  for (MyClass e : list) { // write - Compile Error
      System.out.println(e);
  }
}

public void doSomething(List<? super MyClass> list) {
  list.add(new MyClass()); // write - Ok
}
```

Productor-extends의 경우 **읽기**만 가능하고, Consumer-super의 경우 **쓰기**만 가능하다

- 오라클 공식 문서에선 이와 같은 지침으로 사용하라고 나와있다
    - In변수는 코드에 데이터를 제공(생산)하는 것 - `extends` 를 사용
        - `copy(src, dest)` 라는 복사 메서드에서 src는 복사할 데이터를 ‘제공’ 하므로 in 매개변수이다
        - 이것이 Covariance(공변)이라고 한다
    - out변수는 다른 곳에서 사용할 데이터를 보유(소비)하는 것 - `super` 를 사용
        - 반대로 `copy(src, dest)` 에서 dest는 들어올 데이터를 허용(사용)하기 때문에 out 매개변수이다
        - 이것을 Contravariance(반공변)이라고 한다
    - 다만 두 가지 사용지침은 메서드의 반환 타입엔 적용되지 않는다

- 결론적으로 구조에서 값을 가져오려는 경우에는 공변(extends)을 사용하고, 값을 구조에 넣으려는 경우에는 반공변(super)를 사용하면 된다

### Recursive type bound(재귀적 타입 제한)

- 자기 자신이 들어간 표현식을 사용하여 타입 매개변수의 허용범위를 한정할 수 있다

```java
public interface Comparable<T> {
	int compareTo(T o);
}
```

여기서 타입 매개변수 T는 자신과 비교될 수 있는 요소를 정의한 것이다

Comparable을 구현하는 요소들의 목록을 정렬하거나 최대값, 최소값 등을 구하는 작업을 하기 위해서는 목록의 모든 요소들이 서로 비교 가능해야한다

```java
public static <T extends Comparable<T>> T max(Collection<T> c);
```

`<T extends Comparable<T>>` 은 **자신과 비교될 수 있는 모든 타입 T** 라고 읽을 수 있다

<br>
<br>

**재귀적 타입 제한 예시 1**

```java
package org.example.generic;

import java.util.ArrayList;
import java.util.List;

public class GenericPractice {
    public static void main(String[] args) {

        List<String> list = new ArrayList<>();
        list.add("1");
        list.add("2");
        list.add("3");

        System.out.println(RecursiveType.getMax(list));

    }
}

// 1.
// 비검사 경고 2개 발생
// Comparable 클래스를 raw type 으로 사용했다
// 검사받지 않은 원시타입Comparable의 멤버로 compareTo(T)를 호출했다
class RecursiveType {
    public static <E extends Comparable> E getMax(List<E> list){
        return list.stream().max((e1, e2) -> e1.compareTo(e2)).orElseThrow();
    }
}

// 2.
class RecursiveType {
    public static <E extends Comparable<E>> E getMax(List<E> list){
        return list.stream().max((e1, e2) -> e1.compareTo(e2)).orElseThrow();
    }
}

// 3.
class RecursiveType {
    public static <E extends Comparable<? super E>> E getMax(List<E> list){
        return list.stream().max((e1, e2) -> e1.compareTo(e2)).orElseThrow();
    }
}

// 3가지 메서드 모두 작동은 한다
```

첫번째 `getMax` 의 경우 경고가 두 개 발생한다

Comparable이 로우 타입이라 타입 안정성을 보장 할 수 없다는 것이다

e1의 타입은 파라미터로 받은 list의 실제 타입 매개변수로 실체화 되기에 컴파일러가 타입 추론이 가능하다

e2의 타입은 Comparable의 실제 타입 매개변수로 추론해야하는데
Comparable은 **로우 타입(raw type)이라 제네릭 정보가 소거되기 때문에 타입 추론이 불가능**하다

그래서 재귀적 한정 타입 방식으로 제한을 걸어주면 해결된다

<br>
<br>


**재귀적 타입 제한 예시 2**

[https://stackoverflow.com/questions/7385949/what-does-recursive-type-bound-in-generics-mean](https://stackoverflow.com/questions/7385949/what-does-recursive-type-bound-in-generics-mean)

과일을 크기별로 비교하고 같은 종류만 비교할 수 있다고 가정해보자

```java
1.
class Fruit implements Comparable<Fruit> {
    private final Integer size;

    public Fruit(Integer size) {
        this.size = size;
    }

    public Integer getSize() {
        return size;
    }

    @Override public int compareTo(Fruit other) {
        return size.compareTo(other.getSize());
    }
}

class Apple extends Fruit<Apple> {
    public Apple(Integer size) {
        super(size);
    }
}

class Orange extends Fruit<Orange> {
    public Orange(Integer size) {
        super(size);
    }
}

...
apple1.compareTo(orange1);    // No error
```

1번의 경우 사과끼리 오렌지끼리 비교해야하는데 사과와 오렌지를 비교해도 오류가 나지 않는다

<br>

같은 종류의 과일끼리 비교 하기 위해 타입 매개변수를 넣어 특정 과일 종류만 비교하도록 만들었다

```java
2.
class Fruit<T> implements Comparable<T> {
    private final Integer size;

    public Fruit(Integer size) {
        this.size = size;
    }

    public Integer getSize() {
        return size;
    }

    @Override
    public int compareTo(T other) {
        return size.compareTo(other.getSize()); // compile error
    }
}

class Apple extends Fruit<Apple> {
    public Apple(Integer size) {
        super(size);
    }
}

class Orange extends Fruit<Orange> {
    public Orange(Integer size) {
        super(size);
    }
}

```

다른 과일 타입의 비교를 제한하기 위해 타입 매개변수 T를 넣어 재구성했지만

Fruit클래스의 `getSize()` 가 컴파일 되지 않는다

컴파일 시 T의 타입에 제한이 걸려있지 않기 때문이다

T는 아무 클래스나 될 수 있지만 T에 들어올 수 있는 모든 클래스가 `getSize()` 메서드를 사용할 수 없기 때문이다

<br>

여기서 재귀적 한정(제한, bound) 타입을 사용한다

```java
3.
class Fruit<T extends Fruit<T>> implements Comparable<T> {
    private final Integer size;

    public Fruit(Integer size) {
        this.size = size;
    }

    public Integer getSize() {
        return size;
    }

    @Override
    public int compareTo(T other) {
        return size.compareTo(other.getSize());
    }
}

class Apple extends Fruit<Apple> {
    public Apple(Integer size) {
        super(size);
    }
}

class Orange extends Fruit<Orange> {
    public Orange(Integer size) {
        super(size);
    }
}
```

T에 제한을 걸어 컴파일러에게 T가 Fruit의 서브타입이라고 알려야 한다

→ 다른 말로 T가 Fruit<T>의 제한을 받는 타입이어야만 한다는 것
(T타입은 Fruit를 상속받아야 한다는 것이다)

→ 그렇기에 extends를 사용해 상한선을 걸었다 `T extends Fruit<T>` 

Fruit의 서브 타입만 타입 인자로 허용된다는 것이다

이제 컴파일러는 Fruit 클래스의 서브타입에서 `getSize()` 메서드를 사용할 수 있다는걸 알게 되었기에 `getSize()` 사용이 가능해진다

이렇게되면 같은 종류의 과일끼리 서로 비교가 가능해진다

<br>

### Type Erasure(타입 삭제)

- 컴파일러는 모든 타입 매개변수를 지우고 타입 매개변수가 제한되어 있으면 첫 번째 바인딩으로 타입 매개변수가 지정되지 않으면 Object로 바꾼다
    
    
- 브리지 메서드를 생성하여 다형성을 유지한다

<br>

### 브리지 메서드(bridge method)란?

타입 삭제 전 예시 코드를 보자

```java
# 타입 삭제 전
public class Node<T> {

    public T data;

    public Node(T data) { this.data = data; }

    public void setData(T data) {
        System.out.println("Node.setData");
        this.data = data;
    }
}

public class MyNode extends Node<Integer> {
    public MyNode(Integer data) { super(data); }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }
}
```

이후 코드를 작성하면 오류가 발생한다

```java
MyNode mn = new MyNode(5);
Node n = mn;            // A raw type - compiler throws an unchecked warning
n.setData("Hello");     // Causes a ClassCastException to be thrown.
Integer x = mn.data;

--------

MyNode mn = new MyNode(5);
Node n = mn;            // A raw type - compiler throws an unchecked warning
                        // Note: This statement could instead be the following:
                        //     Node n = (Node)mn;
                        // However, the compiler doesn't generate a cast because
                        // it isn't required.
n.setData("Hello");     // Causes a ClassCastException to be thrown.
Integer x = (Integer)mn.data;
```

여기서 브리지 메서드가 필요한 이유가 나온다

먼저 타입 삭제 후의 코드를 보자

```java
# 타입 삭제 후
public class Node {

    public Object data;

    public Node(Object data) { this.data = data; }

    public void setData(Object data) {
        System.out.println("Node.setData");
        this.data = data;
    }
}

public class MyNode extends Node {

    public MyNode(Integer data) { super(data); }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }
}
```

타입 삭제 이후 타입 파라미터 `T` 가 Object로 바뀌었다 

그렇기 때문에 MyNode에 있는 메서드와 타입이 일치하지 않는 상황이 발생했다

`Node.setData(T)`는 `Node.setData(Object)`가 되어버렸기에

`MyNode.setData(Integer)` 메서드는 `Node.setData(Object)`를 재정의(override)하지 않게된다

결과적으로 java의 컴파일러는 **다형성**을 유지하기 위해 

서브타입이 제대로 작동하는지 브리지 메서드를 만들어 확인하게 된다

즉, 자식 클래스에서 부모 클래스의 메서드를 **재정의(Override)** 하지 않고 사용하려고 했기에 오류가 난 것이다

```java
class MyNode extends Node {

    // Bridge method generated by the compiler
    //
    public void setData(Object data) {
        setData((Integer) data);
    }

    public void setData(Integer data) {
        System.out.println("MyNode.setData");
        super.setData(data);
    }

    // ...
}
```

> 컴파일러가 MyNode 클래스의 setData()에 대해 브리지 메서드를 생성한다
> 

브리지 메서드가 부모클래스의 메서드를 자동으로 재정의 해줬기에 다시 다형성이 적용되었다

---

참고 :

- 자바의 정석

- java generic docs

[Why Use Generics? (The Java™ Tutorials >        
            Learning the Java Language > Generics (Updated))](https://docs.oracle.com/javase/tutorial/java/generics/why.html)

[Time To Really Learn Generics: A Java 8 Perspective - No Fluff Just Stuff](https://nofluffjuststuff.com/magazine/2016/09/time_to_really_learn_generics_a_java_8_perspective)

[[ Java] Java의 Generics](https://medium.com/@joongwon/java-java의-generics-604b562530b3)

- PECS

[Java Generics PECS – Producer Extends Consumer Super | Baeldung](https://www.baeldung.com/java-generics-pecs)

- 공변(Covariance)과 반공변(Contravariance)

[Covariance and Contravariance In Java - DZone](https://dzone.com/articles/covariance-and-contravariance)

- 재귀적 타입 제한(Recursive type bound)

[What does "Recursive type bound" in Generics mean?](https://stackoverflow.com/questions/7385949/what-does-recursive-type-bound-in-generics-mean)
