

### 인텔리제이 .gitignore 적용되지 않고 git changes에 파일 남아있는 현상
    
![Untitled](https://user-images.githubusercontent.com/119831581/221132838-69a3fead-9b9a-40b1-b9a5-2152faf5d493.png)

![Untitled 1](https://user-images.githubusercontent.com/119831581/221132846-4879b808-dc32-4f5e-88af-d352c950cd61.png)

쓸모없는 파일이 계속 나타나 gitignore에 해당 경로 째로 넣어버렸다  
하지만 적용이 안되는 모습

<br>

`git rm -r —cached <파일명>` 으로 추적하고 있는 캐시를 지웠다

![Untitled 2](https://user-images.githubusercontent.com/119831581/221132856-eb9211a8-dea0-4b68-a39c-ecde175cbc96.png)

![Untitled 3](https://user-images.githubusercontent.com/119831581/221132877-91da1157-985b-42e6-a1f4-86d9b2c7ac97.png)

![Untitled 4](https://user-images.githubusercontent.com/119831581/221132904-b91c3f4c-6c53-4865-8957-d5229ed32363.png)

> 삭제된 모습(실제 파일이 삭제된 건 아니다)
> 

다시 커밋후 푸시를 하면 정상적으로 보인다
