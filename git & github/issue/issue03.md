### 커밋 메시지 변경

이번 오류도 rebase를 사용해 바꿨기 때문에 
예전에 작성했던 커밋 합치기 오류와 상당히 비슷하다

![Untitled](https://user-images.githubusercontent.com/119831581/221127383-3723cf36-bb4b-4cb0-9eb8-2f91d94078eb.png)

인텔리제이 내부에서 git bash로 커밋메시지를 작성했다  
중간에 한글로 쓰고 지웠다가 위의 메시지로 작성했는데 문자가 깨져버렸다

<br>

fixed untracked files가 가장 최근에 작성한 커밋이다  
HEAD 커밋에서 해당 커밋까지 접근해야하니 HEAD~2를 넣고 실행했다  
`git rebase HEAD~커밋숫자 -i`  

![Untitled 1](https://user-images.githubusercontent.com/119831581/221127426-d318ab65-b8d9-417b-a50b-c4a4957609c8.png)<br>

vi편집기 Commands안에 보니 reword로 바꾸면 커밋메시지를 바꿀 수 있는듯 하다  
i를 누르고 입력모드로 들어가 pick을 reword로 바꿨다
![Untitled 2](https://user-images.githubusercontent.com/119831581/221127450-3153e1fc-ed2e-44bc-a946-b4658d7b397c.png)

![Untitled 3](https://user-images.githubusercontent.com/119831581/221127467-173ed8b1-a37c-4699-bf95-6a8b43df1fc3.png)<br>

그 후 `esc` **→** `:wq` 로 빠져나오려 했지만 esc가 먹히지 않아서 
`Ctrl + c` 로 빠져나와 :wq를 입력해 빠져나왔다

> :wq!는 강제 저장 후 종료 / :wq 는 저장 후 종료라고 한다

![Untitled 4](https://user-images.githubusercontent.com/119831581/221127512-a4b99c79-a75c-4607-a7fb-c62454948ec1.png)

![Untitled 5](https://user-images.githubusercontent.com/119831581/221127524-8152c505-03d8-4520-b468-fc6df567ed2e.png)

이상하게 작성된 문자를 삭제하고 다시 깨끗하게 메시지를 작성했다

> 뒤에 나오지만 주석에 있는 메시지를 바꾸는게 아니었다.. 주석은 무시된다는걸 깜빡했다

<br>

다시 나가려고 Ctrl + c를 눌렀는데 아예 vi편집기가 종료됐다

<br>

git bash를 열어 git status로 확인해보니

![Untitled 6](https://user-images.githubusercontent.com/119831581/221127553-73abe1a5-5f11-41ae-a0f0-dcb3149a82e0.png)

편집하다가 만 상황이 나왔다  
rebase 하던게 남아있다고 알려준 것  

git rebase —abort로 직전에 실행했던 rebase를 취소시켰다

![Untitled 7](https://user-images.githubusercontent.com/119831581/221127593-1341f4e9-b10f-411e-831c-b02d617752c9.png)

> master 옆의 rebase가 사라졌다


다시 처음부터 git rebase HEAD~2 -i로 해당 커밋을 reword로 바꾸어 들어가니  
이미 로컬에 rebase 파일이 남아있는 현상 발생 

→ 01-27 커밋 합치기 오류에서 발생한 E325 : ATTENTION 단계부터 따라했다 

<br>


![Untitled 8](https://user-images.githubusercontent.com/119831581/221127632-5dbfad97-842a-43fe-aa37-bb3f00ac46a4.png)

이거를

![Untitled 9](https://user-images.githubusercontent.com/119831581/221127652-215a4344-3e5a-4888-bec2-3962cdc4b632.png)

이렇게 바꿨다

![Untitled 10](https://user-images.githubusercontent.com/119831581/221127664-84f6833f-24e9-436d-9e94-586355042281.png)

성공했다는 메시지가 나오고

![Untitled 11](https://user-images.githubusercontent.com/119831581/221127684-285da586-d9f6-4e76-a00b-451c93e840c6.png)

> git log는 q를 누르면 나갈 수 있다
> 

![Untitled 12](https://user-images.githubusercontent.com/119831581/221127696-da200d29-6f4d-4fce-99b9-c22535b37ca7.png)

확인을 위해 git log와 인텔리제이 push commit 창에서 동시에 확인해본 모습이다

정상적으로 커밋 메시지가 바뀌었다

<br>

![Untitled 13](https://user-images.githubusercontent.com/119831581/221127720-42f673b1-5621-48bb-a405-0d8fe209ba9a.png)

강제 푸쉬로 넣어준 모습

다만 협업에서 강제푸쉬는 쓰지 말거나 사용을 최소화 해야한다

커밋을 바꾸는 작업이었기에 커밋안에 들어있는 코드의 위치를 찾기 힘들어지므로

사전에 어느 커밋을 바꿀지 모두 협의한 뒤에 바꾸길 추천한다
