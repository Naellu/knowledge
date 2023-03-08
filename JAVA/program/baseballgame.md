# 숫자야구 프로그램

[숫자야구 링크](https://github.com/Naellu/baseballgame-ref)<br>

#### 목차
- [2023-02-17 숫자야구 첫 게시물](#2023-02-17)
- [2023-02-20 숫자야구 완성](#2023-02-20)
- [2023-02-21 숫자야구 수정](#2023-02-21)
- [2023-03-08 간단한 설계 추가하여 새로운 숫자야구 작성](#2023-03-08)

<br>

### 2023-02-17

- 깃헙 오픈소스에 우테코 프리코스 과제인 숫자야구를 모방하여 만들어봤다<br>
16일에는 하나의 클래스에 넣어 동작만 하는 숫자야구 프로그램을 했었는데  
이번엔 설계를 먼저 구상해보고 작업을 해보는 위주로 가보고 싶었다  
현재는 대략적으로 로직 분리를 해봤고 몇몇 기능만 테스트로 넣어놨다  


- 처음으로 설계를 해봤는데 쉽지 않았다  
`객체지향 생활체조` 원칙의 중요성을 지키고자 비슷하게 해보려고 했지만  
생각보다 객체지향이라는 틀 안에서 벗어나지 않기 위해 고려해야하는 점이 많았다고 생각했다  
원시 숫자 및 문자열의 상수 사용, else문 제거, 변수 및 메서드 작명법, 의존성을 약하게.. 등등  
심지어 테스트 위주의 개발을 이번에 처음 해봤다 ~~(현재 테스트 코드는 뭔가 이상한거 같다..)~~  
현재는 아무래도 자바에 대한 개념이 내가 생각한 것 보다 더 많이 부족한 것 같다

<hr>

<br>

### 2023-02-20

![image](https://user-images.githubusercontent.com/119831581/220084887-43c4ebf2-aebf-4e90-9dbe-aa27cd14be24.png)<br> 
이번엔 저번 숫자야구를 대강이나마 완성시켜봤다  
본래목적은 객체지향을 지키면서 완성시키기였지만 일단 이번건에서는 구현을 가장 최우선으로 두었다  
확실히 하나의 클래스 파일에 모든 기능을 작성하는 것보다는 나았지만  
오히려 부족한 점이 훨씬 더 많이 나왔기에 그것을 작성해보도록 한다  
다만 다른 사람의 코드리뷰를 받은 것은 아니라서 반드시 나중에 다시 공부하고 리팩토링도 해보도록 하자  
<br>

기능들을 모델 - 도메인(메인기능) - 검증 - 뷰 로 나누어 보았는데  
MVC패턴의 지식을 어중간하게 아는상태로 따라하다 세부 설계와 구현이 더 힘들었다  

![image](https://user-images.githubusercontent.com/119831581/220086660-731e86d1-20d8-4354-a33c-c79b47c2539a.png)<br> 

<br>

- 모델 로직의 경우 게임에서 Computer와 User의 수를 리스트에 담기로 했다  

![image](https://user-images.githubusercontent.com/119831581/220087487-d331db41-ed73-4e0a-8abd-94069c9cc805.png)
![image](https://user-images.githubusercontent.com/119831581/220087525-3f447c67-43ac-46f7-aa6b-b0fba1b46a6e.png)<br>

<br>

- 세가지 검증 로직을 묶은 코드

![image](https://user-images.githubusercontent.com/119831581/220088843-3e0ffa6e-1b8b-4f91-b64c-691e15b6407c.png)<br>
검증 로직은 이상한 값이 들어올 세가지 경우에만 이렇게 묶어서 오류를 발생시키기로 하였으나  
userInput과 각 오류를 하나로 묶어서 더 깔끔하게 하는 방법이 좋았을 것 같다  

<br>

- 검증 로직 중 같은 수를 받은경우를 걸러내는 코드

![image](https://user-images.githubusercontent.com/119831581/220090019-5e463a1e-4219-400d-b5a8-409e10bb8c01.png)<br>
검증 로직에서 같은 수를 입력받은 경우를 작성한 것인데 수가 단 3가지라서 이렇게 작성이 가능했던 것이지  
수의 제한을 수십가지로 늘리면 해당 코드는 엄청나게 길어지기에 바꿔야할 코드이다  

<br>

- Computer와 User의 리스트를 받아 볼과 스트라이크를 판별하여 그 수를 반환하는 메인로직이다  

![image](https://user-images.githubusercontent.com/119831581/220090565-e5ba8e93-5e9e-4597-ac0c-280867fe5cc7.png)<br>
각 리스트 인스턴스의 메서드를 바로 사용했기에 가독성이 떨어진다고 생각한다  
따로 변수로 빼서 사용하거나 혹은 다른 방법이 있었을거라 이것 또한 바꿔야하는 코드이다  

<br>

- 각 기능들을 모아 실행 및 재실행을 담은 메서드이다

![image](https://user-images.githubusercontent.com/119831581/220091265-7d6354b5-0279-43f4-a567-120941529aa8.png)
![image](https://user-images.githubusercontent.com/119831581/220091349-9c68c1bb-626e-49b6-8492-d17276da6706.png)<br>
제일 문제점이 많다고 생각하는 곳이다  
객체지향 설계를 무시하고 구현을 최우선적으로 한 결과  
`run()` 메서드를 계속해서 불러오면 메서드를 불러올 때마다 객체 생성과 동시에  
반복문 내에서도 객체가 계속해서 생성되는 구조를 깨뜨릴 수가 없었다  
구현보다 설계가 먼저이고 왜 중요한지 뼈저리게 느낀 부분 중 하나이다  

게다가 해당 파트가 가독성이 제일 좋지 않다고 생각한다  
변수 및 메서드만 보고 어떤 역할을 하는지 명확하게 하고 싶었으나 부족했다고 생각한다  

<br>

- 과도한 throws 예외 처리

![image](https://user-images.githubusercontent.com/119831581/220094268-4840121f-9130-40d5-bddc-d67456752173.png)<br>
![image](https://user-images.githubusercontent.com/119831581/220094397-14a74e8c-492b-4eba-8d5e-77152932cd33.png)<br>
![image](https://user-images.githubusercontent.com/119831581/220094430-bed8cec4-4b86-4a91-b9d5-abd3cc238aa9.png)<br>
![image](https://user-images.githubusercontent.com/119831581/220094610-bd7a7514-d1f1-4b4a-a6eb-06f3487e9b5c.png)<br>
마지막으로 무분별하게 사용한 throws IOException 이다  
프로그램 작성 시 userInput이 여러군데 들어가다보니 각 클래스끼리 의존성이 높아지고  
한 곳에 따로 예외처리를 할 수 있었을 것 같았는데 아쉬웠던 부분이다  
말그대로 예외 처리를 떠넘기는 방식인데  
이렇게 많이 사용하면 예외 발생 시 어디를 찾아가야 하는지 모를 것이라고 생각해 상당히 안좋다고 생각하는 코드였다  

<hr>

<br>

### 2023-02-21

객체지향을 공부하면서 조금만 수정했다  
![image](https://user-images.githubusercontent.com/119831581/220294451-d5bd9eeb-7a9b-4ea8-83e9-eccac864f24d.png)
![image](https://user-images.githubusercontent.com/119831581/220294497-21945718-7c5c-4312-b5be-06c3a212eb0e.png)<br>
Distinction클래스에 하나로 뭉쳐있던 메서드를 나눠보았다  


![image](https://user-images.githubusercontent.com/119831581/220294898-abd5288c-11ab-4238-b585-a32df0886470.png)<br>
ComputerNumber의 수는 한 게임마다 불변인 상태를 요구하기 때문에 [정적 팩토리 메서드](https://github.com/Naellu/TIL/blob/master/JAVA/OOP/Static-Factory-Method.md)를 적용해보았다  


![image](https://user-images.githubusercontent.com/119831581/220296042-79eb6242-cd86-4962-ae6e-4581fd8a5ce7.png)<br>
정적 팩토리 메서드 적용으로 메서드 호출마다 ComputerNumber의 객체 생성을 인스턴스 호출로 바뀌게 되었다


<hr>

<br>

### 2023-03-08

숫자야구를 리팩토링 하려고 했다가 건드리자니 다시 만드는게 빠를 것 같아서  
이번엔 객체의 역할과 책임, 그리고 메시지를 생각하며 설계해보았다  
하지만 처음부터 완벽하게 하지 않고 항상 변동이 있을 수 있다는 생각으로 설계했다  

![image](https://user-images.githubusercontent.com/119831581/223635637-65a43ef2-d037-4388-a778-df8c47a59ade.png)  
![image](https://user-images.githubusercontent.com/119831581/223639255-1693f6b9-7a5b-412f-9dd1-d48fd1eacb65.png)  
![image](https://user-images.githubusercontent.com/119831581/223639322-431c44d9-e215-401c-a6c6-c840245b19f3.png)  

[숫자야구 README](https://github.com/Naellu/baseballgame-ref/blob/master/README.md)  

<br>

- ConstValue

![image](https://user-images.githubusercontent.com/119831581/223662718-1d5d4d22-bf28-4d56-b2af-00a76c85ba1d.png)  
우선 모든 원시 타입의 값을 포장해 ConstValues라는 클래스에 모아두었다  
해당 필드를 사용할 땐 가독성을 위해 `import static`으로 코드 내에 상수 클래스의 이름이 들어가지 않게 하였다  
확실히 원시 타입 보다 명확한 이름을 가지게 되어 내가 사용할 때에도 헷갈리지 않게 되었다  

다만 같은 값이지만 쓰임새가 다를 경우 이름을 다르게 지어줘야 하기에 몇몇 상수의 값이 중복되는 것이 있다  

<br>

- Computer

![image](https://user-images.githubusercontent.com/119831581/223644996-82c94786-02cd-40bb-bb54-a79dd3a8bd2e.png)  
Computer의 숫자도 이전과 똑같이 뽑는 방식으로 했다  
다만 getter를 완전히 안쓸 수는 없을 것 같아  
getter보다는 `initNumbers()`라는 이름으로 컴퓨터의 숫자를 만들어내는 행동을 명확하게 표현하려고 했다  

이전엔 정적 팩토리 메서드를 사용했지만  
이번 숫자야구에선 컴퓨터 객체를 미리 static에 올려두지 않아도 된다고 생각하여 사용하지 않았다  

<br>

- Number

![image](https://user-images.githubusercontent.com/119831581/223640613-815f2d5e-7dfc-4ea5-9041-907ad3bb896e.png)  
입력받는 숫자객체를 만들었다  

<br>

- User

![image](https://user-images.githubusercontent.com/119831581/223640435-e3fc43f1-91ba-4d1d-b0a6-44f70521f3bb.png)  
완벽한 일급 컬렉션은 아니지만 비슷하게나마 구현시켰다  
입력값을 요청하고 검증을 통한 뒤 예외가 발생하지 않는다면 정상적으로 한 자리씩 나누어  
User에 있는 List<String> 필드에 들어가게 하였다  

처음엔 Number가 숫자를 받고, 한 자리씩 나누어 반환까지 하는 책임이 있었는데  
User가 숫자를 한 자리씩 나누는 **행동**을 가지고 있어야  
상태와 행동이 같이 있게된다는 생각이 들어 User에게 넘겼다  

  
<br>

  
- Referee

![image](https://user-images.githubusercontent.com/119831581/223641515-43b072bb-ede0-488f-9d9a-c3c53352c26e.png)  
이전에 `Distinction`클래스를 `Referee`로 바꾸어 심판이라는 이름을 명확하게 전달해보고자 하였다  
볼과 스트라이크를 비교하는 로직은 foreach, stream 없이 구현했다  
저번 기록에서 볼과 스트라이크를 판단하는 메서드를 더욱 세분화 시켰는데 가독성이 너무 안좋았었다  
의미없는 매개변수의 이름과 쓸데없이 길어진 흐름이 방해가 되었다고 생각했다  

판별한 볼과 스트라이크는 2자리의 int 배열에 담아 첫번째는 볼, 두번째는 스트라이크의 개수를 집어넣었다  

  
<br>

- GameDisplay 
 
![image](https://user-images.githubusercontent.com/119831581/223649138-bdc21b51-1bbd-4154-b18c-d0154dea9208.png)  
![image](https://user-images.githubusercontent.com/119831581/223649179-cd288460-a302-41c9-8853-a9f3a2b3c6d5.png)  
`GameDisplay`는 모든 출력을 담당하고 있다  
심판이 판독한 볼과 스트라이크인 `ballAndStrike`를 요청해 받아오면 개수에 따라서 답변을 출력하는 역할이다  

<br>

- BaseballController

![image](https://user-images.githubusercontent.com/119831581/223650194-492d7345-ab18-4fab-81c7-34f4c5274b23.png)  
컨트롤러에선 모든 객체를 불러와 협업을 하도록 하고싶었다  
컨트롤러를 제외한 다른 객체들은 자기자신이 맡은 역할만 수행하도록 했다  
  
또한 변수 및 메서드 네이밍도 조금 신경써서 이름만 보고도 무엇을 하는지 알 수 있게 하도록 했다  

  
<br>
  
  
- 테스트 작성
  
![image](https://user-images.githubusercontent.com/119831581/223651667-1c200a32-e562-4a1d-bd4a-c067b5e35f04.png)  
테스트 코드도 작성해보았다  
`JUnit`이 아니라 `assertJ`를 사용해서 `assertThat()...` 형식으로 사용했다  
많이 미숙하긴 하지만 작은 단위로나마 테스트를 해봤다는 것에 의미를 두었다  

  
  
  
  
