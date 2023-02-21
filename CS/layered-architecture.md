# 레이어드 아키텍처(Layered Architecture)

### 계층 구조

- 계층화된 아키텍처(**layered architecture**) 또는 **n-tier** 아키텍처 패턴 이라고도 하며

  대부분의 Java EE 애플리케이션에 대한 표준으로 알려져 있다

  소규모 응용 프로그램인 경우엔 3개의 계층만 있을 수 있고, 반면 대규모인 경우 5개 이상의 계층이 포함될 수 있다


### 패턴

![Untitled](https://user-images.githubusercontent.com/119831581/220222929-4bafce11-7749-4e98-81f0-654d4f48c387.png)

![Untitled 1](https://user-images.githubusercontent.com/119831581/220222827-852a7333-92e5-44b3-84ee-8a8e2b441aae.png)

- 계층 내 구성 요소는 수평관계이며 각 계층에는 애플리케이션 내에서 역할과 책임을 담당하고 있다

1. **Presentation Layer**
    - 모든 사용자 인터페이스와 브라우저 통신 로직을 처리하는 계층
    - 사용자 데이터를 어떻게 얻어야 하는지 알 필요가 없음
    - 특정 형식으로 화면에 해당 정보를 표시하기만 하면 되는 계층
    - View, Controller
2. **Business Layer**
    - 요청(request)과 관련된 특정 비즈니스 규칙을 실행하는 계층
    - 데이터 형식, 데이터 출처를 알 필요가 없음
    - 받아온 데이터에 비즈니스 로직(계산, 집계 등등)을 수행하고 Presentation Layer에 전달하기만 하면 되는 계층
    - Service
3. **Persistence Layer**
    - 데이터 및 파일을 가져오고 다루는 것이 주 목적인 계층
    - ORM, DAO, Entity, Repository
4. **Database Layer**
    - MySQL, MariaDB, MongoDB, OracleDB 등 데이터베이스가 위치한 계층

<aside>
💡 Layered Architecture의 가장 중요한 점은 구성 요소간의 ‘**관심사 분리(seperation of concerns**’이다

</aside>

- 관심사 분리가 왜 중요한건지?

  특정 계층 내 구성요소는 해당 계층과 관련된 로직만 처리한다

  Presentation Layer가 Buisness Layer에 있는 로직을 처리하지 않는다는 것이다

  구성요소의 범위를 제한시켜 해당 계층의 로직만 처리하게 된다면 (그림에서의)수직적인 계층 간 의존성이나 종속성을 떨어뜨릴 수 있다

  스프링에서의 관심사 분리와 비슷하다


### 주요 개념

![Untitled 2](https://user-images.githubusercontent.com/119831581/220222794-5ccf60b2-eae2-4a49-b78e-6e0d6d694f32.png)

- 아키텍쳐의 각 계층은 기본적으로 **격리**되어있다
    - 요청이 다음으로 이동할 때 2계층, 3계층을 한번에 건너뛰는 것이 아니라 바로 아래 다음 계층을 먼저 통과해야한다
- 계층 격리 개념은 아키텍처의 한 계층에서 이루어진 변경사항이 다른 계층에 영향을 미치지 않는 것이다
    - 격리 개념이 없다면 구성 요소간 종속성이 매우 많아지기 때문에 이러한 유형의 아키텍처 변경 시 비용이 더 증가하게 된다

      앞서말한 관심사 분리의 중요성의 이유다


- 하지만 계층 격리 구조는 아키텍처 내 변경 사항의 영향을 최소화하는데 도움이 되지만 특정 계층은 열려(개방)있어야 하는 경우가 있다

![Untitled 3](https://user-images.githubusercontent.com/119831581/220222998-0cfe5fc1-94f4-429d-ad16-8619aa690917.png)

- 앞서 말한 것처럼
  만약 비즈니스 계층과 지속성 계층 안에 새로운 계층을 만든다고 해도 문제 될 것이없다
    - 서비스 계층을 개방시켜 놓는다면 서비스 계층이 필요하지 않은 로직일 경우 비즈니스 계층에서 바로 지속성 계층으로 넘어갈 수 있기 때문이다
    - 서비스 계층은 결국 비즈니스 계층 다음에 놓여있기 때문에 서로 간 종속성 문제를 걱정할 필요도 없게 되는 셈이다

- 개방 및 격리 계층의 개념을 적용한다면 여러 설계자 및 개발자가 알 수 있게 어떤 계층이 열려있고 닫혀있는지를 문서화하거나 주석을 달아 보기 쉽게 만들도록 하자

### 동작 구조

![Untitled 4](https://user-images.githubusercontent.com/119831581/220223028-91d1af6b-3346-4844-b2cd-06c478e725ac.png)

> 검은색 화살표는 요청에 의한 패턴, 빨간색 화살표는 요청에 따른 응답 패턴이다
>

- presentation 계층은 고객의 요청을 받아 정보를 표시한다
    - 특정 개인에 대한 정보 요청을 수신하면 해당 요청을 delegate(대리자) 모듈로 전달한다
- 이 모듈은 business 계층에서 해당 요청을 처리할 수 있는 모듈과 모듈에 접근하는 방법및 데이터를 파악하는 역할을 한다
- business 계층의 customer object는 고객 정보 가져오기 등의 요청에 필요한 모든 정보를 집계하는 역할을 한다
- persistence 계층의 customer DAO모듈은 고객 데이터를, order DAO는 주문 정보를 가져온다
    - 해당 모듈들이 차례대로 SQL문을 실행하여 데이터를 검색하고 business 계층의 customer object에 다시 전달한다
- customer object가 데이터를 수신하면 데이터를 집계하고 그 정보를 delegate에 전달 → 화면으로 전달하여 사용자에게 표시한다

### 싱크홀 안티 패턴

- 싱크홀 안티 패턴은 들어온 요청이 각 계층 내에서 수행되는 논리가
  거의 없거나 전혀 없는 상태로 여러 계층을 통과하는 흐름을 말한다

- 모든 계층화된 아키텍처에는 싱크홀 안티 패턴에 해당하는 상황이 적어도 조금은 있다
    - 다만 핵심은 이 패턴범위에 속하는 요청의 비율을 분석하는 것이다
    - 80-20 규칙을 적용하여 일반적으로 요청의 20%는 단순 통과처리,
      80%는 요청과 관련된 비즈니스 로직을 포함하게 한다
        - 이 비율이 역전된다면?
            - 대부분의 요청이 단순 통과 처리로 되는 것
            - 계층 격리가 부족하기 때문에 변경 사항을 제어하기가 더 어렵다는 점을 염두에 두고 아키텍처 계층의 일부를 개방하는 것을 고려할 수 있다


### 패턴 분석

- Overall agility(전반적인 민첩성?) : 낮다
    - 끊임없이 변화하는 환경에 신속하게 대응할 수 있는 능력
    - 대부분의 구현에서 모놀리식 특성과 아키텍처 패턴에서 발견되는 구성요소의 긴밀한 결합으로 인해 번거롭고 시간이 많이 걸린다

- Ease of deployment(배포 용이성) : 낮다
    - 패턴 구현방법에 따라 배포가 문제가 될 수 있다(특히 규모가 큰 애플리케이션)
    - 구성 요소에 대한 사소한 변경으로 인해 전체 애플리케이션을 재배포 해야만 하기 때문이다

- Testability(테스트 가능성) : 높다
    - 구성 요소가 아키텍처의 특정 계층에 속하기 때문에 다른 계층을 단위 테스트를 할 수 있으므로 비교적 쉽게 테스트가 가능하다
        - Stub과 Mock이란?

          Stub의 의미는 테스트 중 호출에 대한 미리 준비된 답변을 제공하고 테스트를 위해 프로그래밍 된 것 이외의 항목엔 응답하지 않는것

          Mock의 의미는 호출 시 받게되는 예상된 값으로 사전 프로그래밍된 개체를 말함

          [https://stackoverflow.com/questions/3459287/whats-the-difference-between-a-mock-stub](https://stackoverflow.com/questions/3459287/whats-the-difference-between-a-mock-stub)


- Performance(성능) : 낮다
    - 비즈니스 요청을 수행하기 위해 여러 계층을 거쳐야 하는 비효율성 때문에 고성능 애플리케이션엔 적합하지 않다

- Scalability(확장성) : 낮다
    - 계층을 별도의 물리적 배포로 분할하거나 전체 애플리케이션을 여러 노드로 복제하여 계층화된 아키텍처를 확장할 수 있지만 세분성이 너무 광범위하여 확장 시 비용이 많이든다

- Ease of development(개발 용이성) : 높다
    - 이 패턴이 잘 알려져 있고 구현하기에 지나치게 복잡하지 않다
    - 대부분이 기술 세트를 계층(화면, 비즈니스, db) 별로 분리하여 개발한다

참조 :

[Software Architecture Patterns](https://www.oreilly.com/library/view/software-architecture-patterns/9781491971437/ch01.html)