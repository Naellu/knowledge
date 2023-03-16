# 열거형(enum)



여러 상수를 선언해야 할 때, 편리하게 선언 할 수 있는 클래스이다  
상수가 많을 때는 코드가 너무 길어지기 때문에 열거형을 쓴다

주로 상수를 모아놨기 때문에 값들을 제한할 수 있다  
게다가 다른 언어의 Enum보다 Java에서의 Enum이 훨씬 효율이 좋다  
→ 필드와 메서드 등의 기능을 가진 **클래스**이기 때문!

<br>

열거형 예시
```java
public enum Day {
	SUNDAY,
	MONDAY,
	TUESDAY, 
	WEDNESDAY,
  THURSDAY, 
	FRIDAY, 
	SATURDAY
}

// 보통 enum클래스를 두고 사용한다
// Day.SUNDAY;

-----

// enum을 inner class로 사용
class Card {
	enum Kind { CLOVER, HEART, DIAMOND, SPADE } // 열거형 kind를 정의
	enum Value { TWO, THREE, FOUR }             // 열거형 value를 정의

	final Kind kind;  // 타입이 int가 아닌 Kind임에 주의
	final Value value;
}
```

> 따로 값을 지정해주지 않아도 Kind는 CLOVER부터 자동적으로 0부터 시작하는 정수값이 할당된다(Value도 마찬가지)
> 

- 열거형에 정의된 상수는 **열거형이름.상수명** 으로 사용가능하다
    - 클래스의 static변수를 참조하는 것과 동일
    - `Day.SUNDAY` , `Card.Kind.CLOVER`


- 열거형의 상수간엔 `==` 를 사용할 수 있지만 `>` , `<` 와 같은 비교연산자는 사용할 수 없다
    - 비교연산자 대신 `compareTo()`로 비교할 수 있다

<br>

열거형 상수의 값이 불규칙적인 경우 열거형 상수이름 옆에 원하는 값을 적어줄 수가 있다

```java
public enum Direction {
	EAST(1), SOUTH(5), WEST(-1), NORTH(10); // 끝에 ; 를 추가해야한다

	private final int value;
	Direction(int value) { this.value = value; } // private Direction(int value) ...

	public int getValue() { return value; }
}
```

- 각 enum 상수의 값은 필드에 있는 파라미터로 선언된다
    - 이 값들은 상수가 생성될 때 자동으로 생성자에 전달된다


- 지정된 값을 저장할 수 있는 인스턴스 변수, 생성자를 새로 추가해 주어야 한다
    - 먼저 열거형 상수를 모두 정의한 다음에 다른 멤버들을 추가해야 한다
    - value는 열거형 상수의 값을 저장하기 위해 final을 붙였다


- 하지만 생성자가 추가되었음에도 new연산자로 열거형 객체를 직접 생성할 수 없다
    - 열거형 생성자는 **묵시적으로 private가 붙어있기 때문이다**
    - enum에 정의된 상수를 자동으로 만든다

<br>

추가 참고 - enum 예제

```java
public enum PayGroupAdvanced {

    CASH("현금", Arrays.asList(PayType.ACCOUNT_TRANSFER, PayType.REMITTANCE, PayType.ON_SITE_PAYMENT, PayType.TOSS)),
    CARD("카드", Arrays.asList(PayType.PAYCO, PayType.KAKAO_PAY, PayType.CARD, PayType.BAEMIN_PAY)),
    ETC("기타", Arrays.asList(PayType.POINT, PayType.COUPON)),
    EMPTY("없음", Collections.EMPTY_LIST);

    private String title;
    private List<PayType> payList;

    PayGroupAdvanced(String title, List<PayType> payList) {
        this.title = title;
        this.payList = payList;
    }

    public static PayGroupAdvanced findByPayCode(PayType payType) {
        return Arrays.stream(PayGroupAdvanced.values())
                .filter(payGroup -> payGroup.hasPayCode(payType))
                .findAny()
                .orElse(EMPTY);
    }

    public boolean hasPayCode(PayType payType) {
        return payList.stream()
                .anyMatch(pay -> pay.equals(payType));
    }

    public String getTitle() {
        return title;
    }

}

----------------------------------

public enum PayType {

    ACCOUNT_TRANSFER("계좌이체"),
    REMITTANCE("무통장입금"),
    ON_SITE_PAYMENT("현장결제"),
    TOSS("토스"),
    PAYCO("페이코"),
    CARD("신용카드"),
    KAKAO_PAY("카카오페이"),
    BAEMIN_PAY("배민페이"),
    POINT("포인트"),
    COUPON("쿠폰"),
    EMPTY("없음");

    private String title;

    PayType(String title) {
        this.title = title;
    }

    public String getTitle() {
        return title;
    }
}

------------------------------------

public class Main {
  public static void main(String[] args) throws IOException {
	  String type = PayGroupAdvanced.findByPayCode(PayType.REMITTANCE).getTitle();
	  System.out.println("결제 타입은 " + type + " 입니다"); 
		// REMITTANCE의 title인 현금이 출력된다
  }
}
```

참고에 따르면 Enum을 사용할 경우 **변경이 어렵다**는 점이 있었다  
그렇기에 코드 변경이 적은 곳에 사용한다면 enum의 효율이 좋을 것이라고 한다

<br>

### EnumMap

HashMap과 다르게 키 값을 enum으로 받아 사용하는 [Map 인터페이스](https://docs.oracle.com/javase/8/docs/api/java/util/Map.html)의 특수한 구현체이다

EnumMap은 HashMap과 다른 차이점이 있다

- 모든 키(key)는 **동일한 enum 타입의 키만 허용**한다
- 키에 null값을 삽입하려고 하면 NPE를 발생시킨다
- 키값을 기준으로 정렬되어 저장한다(enum에 상수가 선언된 순서로)
- 내부적으로 배열을 만들어 사용하고 `ordinal()` 을 사용하여 정렬된 상태로 키를 저장하기에 충돌 가능성이 없다

<hr>

<br>

포스팅 참고 : 

- 자바의 정석

<br>

추가 항목 참고 : 

- java 11 docs

[Enum Types (The Java™ Tutorials >        
            Learning the Java Language > Classes and Objects)](https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html?ref=nextreesoft)

- enum의 활용

[Java Enum 활용기 | 우아한형제들 기술블로그](https://techblog.woowahan.com/2527/)

- 다양한 enum 예시

[https://www.baeldung.com/tag/enums](https://www.baeldung.com/tag/enums)

- EnumMap

[A Guide to EnumMap | Baeldung](https://www.baeldung.com/java-enum-map)