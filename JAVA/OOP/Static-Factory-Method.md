# Static-Factory-Method

정적 팩토리 메서드는 새로운 객체를 만들지 않고 클래스의 인스턴스를 반환한다  

먼저 생성자와 비교하면 이런점이 있다


### 이름을 가질 수 있다

- 기본 생성자는 항상 클래스 이름과 같기에 생성자 매개변수에 어떤 것이 들어가도 어느 필드로 들어가는지 알 수 없지만  
정적 팩토리 메서드는 의미 있는 이름을 가질 수 있기 때문에 메서드명만 보고도 어떤 기능으로 들어가 쓰이는지 한눈에 알 수 있다  
```
public class LottoFactory() {
  private static final int LOTTO_SIZE = 6;

  private static List<LottoNumber> allLottoNumbers = ...; // 1~45까지의 로또 넘버

  public static Lotto createAutoLotto() {
    Collections.shuffle(allLottoNumbers);
    return new Lotto(allLottoNumbers.stream()
            .limit(LOTTO_SIZE)
            .collect(Collectors.toList()));
  }

  public static Lotto createManualLotto(List<LottoNumber> lottoNumbers) {
    return new Lotto(lottoNumbers);
  }
  ...
}
```  
로또번호를 생성하는 클래스이다
> 기본 생성자인 LottoFactory보다 createAutoLotto(), createManualLotto()가 무엇을 하는지 의미를 전달하는 면에서 좀 더 명확하다


### 하위 자료형 객체의 반환이 가능하다

- 같은 타입으로 구현한 메서드, 서브타입, 기본타입을 반환할 수 있다  
기본 생성자보다 더 유연한 범위의 타입을 반환하는 이점이 있다
    - 생성자 역할을 하는 정적 팩토리 메서드가 반환 값을 가지고 있기 때문에 가능한 것


### 호출할 때마다 새로운 객체를 생성할 필요가 없다

- 불변(immutable)의 경우나 매번 새로운 객체를 만들 필요가 없는 경우 미리 만들어둔 인스턴스를 반환하면 된다
- 사용되는 개수가 정해져있다면 해당 개수만큼 미리 생성해놓고 조회(캐싱)할 수 있는 구조로
만들 수 있다
    - 정적 팩토리 메서드와 캐싱구조를 함께 사용하면 매번 새로운 객체를 생성할 필요가 없어진다
- 생성자의 접근제한자를 `private` 로 설정함으로써 객체 생성을 정적 팩토리 메서드로만 가능하도록 제한할 수 있다


### 객체 생성을 캡슐화 할 수 있다

- 정적 팩토리 메서드를 사용하면 내부 구현을 모르더라도 쉽게 변환할 수 있다  
```
public class CarDto {
  private String name;
  private int position;

  pulbic static CarDto from(Car car) {
    return new CarDto(car.getName(), car.getPosition());
  }
}


// Car -> CatDto 로 변환
CarDto carDto = CarDto.from(car);
```
만약 정적 팩토리 메서드를 쓰지 않고 변환한다면 생성자의 내부 구현을 모두 드러낸 채 사용해야 할 것이다  
```
Car carDto = CarDto.from(car); // 정적 팩토리 메서드를 쓴 경우
CarDto carDto = new CarDto(car.getName(), car.getPosition); // 생성자를 쓴 경우
```

<hr>

다만 단점도 있는데

### 하위 클래스 생성 불가(상속 불가)

- 정적 팩토리 메서드만 가지고 있는 클래스의 경우 상속이 불가능하다  
Collections가 상속할 수 없는 이유와 같다  
하지만 불변 타입으로 만들기 위해 이 제약을 지키는 것이 오히려 도움이 될 수가 있다  


### 정적 팩토리 메서드를 찾기 어렵다

- 기본 생성자는 클래스랑 이름도 같고 상단에 모여있기 때문에 찾기가 수월하지만  
정적 팩토리 메서드를 사용하면 인스턴스화 할 방법을 알아서 찾아야한다  


<hr>

### 정적 팩토리 메서드 명명 방식
- `of` : 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드 ex) **EnumSet.of()**
- `from` : 매개변수를 하나 받아 해당 타입의 인스턴스를 반환하는 형변환 메서드 ex) **Data.from()**
- `valueOf` : of와 from의 구체적인 버전 ex) **BigInteger.valueOf()**
- `getInstance` | `instance` : 매개변수를 받아 인스턴스를 반환, 이전에 반환했던 인스턴스일 수도 있음 ex) **StackWalker.getInstance()**
- `newInstance` | `create` : 매번 새로운 인스턴스를 반환 ex) **Array.newInstance()**
- `getType` : getInstance와 같지만 명시한 Type으로 객체가 반환됨 ex) **Files.getFileStore()**
- `newType` : newInstance와 같지만 명시한 Type으로 객체가 반환됨 ex) **Files.newBufferedReader()**
- `type` : getType과 newType의 간결한 버전 ex) **Collections.list()**
<br>
<br>

추가 예시<br>
→ Collections를 사용하려면 new가 아닌 .메서드() 방식으로 사용한다는 걸 떠올려보자
![image](https://user-images.githubusercontent.com/119831581/220281392-6b82cd1e-c2c0-45db-826d-6fa809b750cf.png)<br>
![image](https://user-images.githubusercontent.com/119831581/220282037-8ebe6c96-ff1e-4179-8d13-3e3ef58e9eb7.png)<br>

-> String클래스의 valueOf()도 정적 팩토리 메서드다 <br>
![image](https://user-images.githubusercontent.com/119831581/220282365-90487848-f62a-48a3-9889-f13b23265460.png)





참고
- https://tecoble.techcourse.co.kr/post/2020-05-26-static-factory-method/ <br>
- https://parkgaebung.tistory.com/29 <br>
