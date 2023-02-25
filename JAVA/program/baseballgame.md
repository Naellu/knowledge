[숫자야구 링크](https://github.com/Naellu/baseballgame-ref)<br>


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






