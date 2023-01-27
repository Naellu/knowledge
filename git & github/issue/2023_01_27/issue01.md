# issue

생성 일시: 2023년 1월 27일 오후 5:24
최종 편집 일시: 2023년 1월 27일 오후 5:50

2023-01-27

- 커밋 합치기 오류(해결)
    
    ![ScreenShot](issue_img/Untitled.png)
    
    - rebase -i HEAD 로 커밋을 합치려고 vim 모드 입장
        - vim모드를 몰라서 아무거나 입력하다 commit은 합쳐지지도 않고 로컬 디렉토리의 [27일.md](http://27일.md) 항목의 내용이 없어져버림
        - 커밋 합치기는 중단하고 오류 부터 검색(fatal : You are not currently on a branch)
        - 현재 브랜치를 이탈한 상태라기에 [ git checkout master ] 로 되돌아옴
        
        ![ScreenShot](issue_img/Untitled_1.png)
        
        - 된줄 알고 다시 git rebase -i HEAD~2로 합치기 시도했지만 이미 rebase중이라고 오류 발생
        - git rebase —quit으로 rebase 탈출
        
        [https://stackoverflow.com/questions/19134845/forgotten-rebase-interactive-git](https://stackoverflow.com/questions/19134845/forgotten-rebase-interactive-git)
        
        ![ScreenShot](issue_img/Untitled_2.png)
        
        - .COMMIT_EDITMSG.swp가 이미 있다고 오류가 나옴
            - 맨위 경로에 나온 파일을 지우면 된다고 나와서 D를 누르고 진행
            
            [https://github.com/FE-Lex-Kim/-TIL/blob/master/Tool/E325: ATTENTION 에러 처리.md](https://github.com/FE-Lex-Kim/-TIL/blob/master/Tool/E325:%20ATTENTION%20%EC%97%90%EB%9F%AC%20%EC%B2%98%EB%A6%AC.md)
            
        
        ![ScreenShot](issue_img/Untitled_3.png)
        
        - esc + :wq 입력후 git push origin -f 입력하여 강제 푸쉬
        
        ![ScreenShot](issue_img/Untitled_4.png)
        
        ![ScreenShot](issue_img/Untitled_5.png)
        
        - 커밋 합치기가 정상적으로 됐다