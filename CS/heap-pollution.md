# Heap Pollution(힙 오염)



프로그램이 컴파일 타임에 확인되지 않은 경고를 발생시키는 일부 작업을 수행하려고 하는 경우에 힙 오염이 발생한다  
런타임 시 힙에 예기치 않은 유형의 개체가 존재하는 것으로 오류가 나타나는 상황이다  

특히 매개변수화된 타입의 변수에 **올바른 매개변수화된 타입이 아닌 값이 할당될 때**,  
**원시 타입과 매개변수화된 타입을 혼합**하거나 **확인되지 않은 캐스팅**을 진행하는 경우가 있다  

<br>

예시 코드를 보자

```java
public class HeapPollution {
    public static <T> void addToList (List<T> listArg, T... elements) {
        for (T x : elements) {
            listArg.add(x);
        }
    }

    public static void faultyMethod(List<String>... l) {
        Object[] objectArray = l;     // Valid
        objectArray[0] = Arrays.asList(42);
        String s = l[0].get(0);       // ClassCastException thrown here
    }

    public static void main(String[] args) {
        List<String> stringListA = new ArrayList<String>();
        List<String> stringListB = new ArrayList<String>();

        HeapPollution.addToList(stringListA, "Seven", "Eight", "Nine");
        HeapPollution.addToList(stringListB, "Ten", "Eleven", "Twelve");
        List<List<String>> listOfStringLists = new ArrayList<List<String>>();
        HeapPollution.addToList(listOfStringLists, stringListA, stringListB);

        HeapPollution.faultyMethod(Arrays.asList("Hello!"), Arrays.asList("World!"));
    }
}
```

addToList메서드의 경우  

- 컴파일러는 addToList의 varargs인 `T… elements`를 `T[] elements` 로 바꾼다
- T는 타입 매개변수이므로 타입 소거 과정을 거치기 때문에 다시 `Object[] element`로 바뀐다
- 개변 매개변수로 들어오는 element의 타입이 Object로 변했기 때문에 어떤 데이터 타입이든 들어올 수 있게 되었다  

이러한 경우 첫번째 인자인 `List<T> listArg`에 들어가는 데이터는 타입이 정해져도  
`String`이 들어갈 수도 있고 `Integer`가 들어갈수도 있어 힙 오염이 발생할 가능성이 생기게 된다  

![Untitled](https://user-images.githubusercontent.com/119831581/227905876-576d5de7-a1b1-41d9-bb3d-3d0c53215a89.png)  
![Untitled 1](https://user-images.githubusercontent.com/119831581/227905898-8a31f8cd-a055-4be0-b8cc-4d53056b1671.png)  

<br>

faultyMethod메서드의 경우

- `List<String>`으로 들어오는 가변 매개변수인  `l` 을 Object배열 `objectArray`에 할당했다  
    
    → 가변 매개변수의 경우 컴파일 시 배열로 바뀌게 때문에 가능하다
    
- `objectArray`의 첫 번째 자리에 정수 42를 가지고 있는 리스트를 추가했다  
    
    → 메서드 내부에서 Integer 타입의 리스트 추가
    
- 마지막으로 `l`의 첫 번째 자리에 있는 리스트의 첫 번째 값을 `s`에 할당하였다  

<br> 

이러한 경우 실제로 실행해보면 `ClassCastException`이 발생한다  
![Untitled 2](https://user-images.githubusercontent.com/119831581/227905959-2703a709-a0c0-4bb1-9333-575716675dc0.png)  

매개변수 `l`은 `List<String>`이었고 Object 배열의 첫 번째 자리에 들어있다  
하지만 그 자리에 `Arrays.asList(42)` 메서드를 사용하여 `List<Integer>`를 가진 Object가 포함되었다  

`List<String>`을 참조해야하는 objectArray가 `List<Integer>`를 참조하기 때문에 힙 오염이 발생한 것이다  

`List<String> → Object[] → List<Integer>`로 업 캐스팅, 다운 캐스팅이 되었지만 컴파일러 입장에선 문제가 없기 때문이다  

그 뒤로 `String s`에 첫 번째 매개변수의 첫 번째 요소를 할당하려 했지만  
실제론 `Integer`이기 때문에(타입 불일치) 런타임 시점에서 `ClassCastException` 오류가 발생하였다  

변수는 항상 매개변수화된 타입을 나타내는 클래스의 인스턴스 객체를 참조한다는걸 알아두자  

<br> 

#### Heap Pollution 대책

1. `@SafeVarargs()`를 사용하여 Heap Pollution경고를 무시하는 방법이 있다

`@SuppressWarnings()` 는 단순히 컴파일러 경고를 무시하는 애너테이션이지만  
`@SafeVarargs()` 는 제네릭 형식의 varargs가 있는 메서드 또는 생성자가 호출되거나 재정의될 때   
컴파일러에서 생성되는 확인되지 않은 경고를 억제하는 데 사용되는 특정 주석이다  

2. 제대로된 제네릭의 사용

타입 소거로 인해 Object가 되고 데이터가 아무렇게나 들어갈 수 있는 상황이 많아지는 것도 원인이 될 수 있다  
binding wildcard(제한된 와일드카드)와 같이 타입을 정확하게 알려주는 것이 좋다  

<br> 

참고 : 

[Non-Reifiable Types (The Java™ Tutorials >        
            Learning the Java Language > Generics (Updated))](https://docs.oracle.com/javase/tutorial/java/generics/nonReifiableVarargsType.html)  

[Chapter 4. Types, Values, and Variables](https://docs.oracle.com/javase/specs/jls/se8/html/jls-4.html#jls-4.12.2)  
