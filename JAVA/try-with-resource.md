# try-with-resource
자원을 사용할 땐 메모리를 위해 자원을 사용 해제(반납)하는 작업 또한 중요하다

try-with-resource는 try-catch-finally보다 더 확실하게 자원을 반납할 수 있도록 하는 문법이다

먼저 예시 코드를 보자

```java
static String readFirstLineFromFileWithFinallyBlock(String path) throws IOEXception {	
	FileReader fr = new FileReader(path);
	BufferedReader br = new BufferedReader(fr);
			
	try {
            return br.readLine();
	} finally {
            br.close(); 
            fr.close();
	}
}
```

- 위의 코드는 자원 해제를 위해 기존에 try-catch-finally문을 사용하던 방법이다

  사용된 자원은 총 2가지, `FileReader` 와 `BufferedReader` 이다


finally블럭안에 자원을 `close()` 해서 실행여부와 관계없이 리소스가 닫히도록 작성된 모습이다

하지만 이 예제의 자원은 제대로 반납되지 않는데,  
`readLine()` 에서 예외를 던지고 finally 블록의 `br.close()` 에서 예외를 던지면  
예외 로그를 발생시키고`fr.close()`는 실행되지 않아 에러 로그가 뜨지 않게 된다

**결국은 `FileReader`의 `close`메서드가 실행되지 않고 자원 해제 과정이 일어나지 않아 `FileReader`의 자원 누수가 일어나게 된다**

프로그램은 자원 사용이 끝나면 자원이 사용했던 메모리를 회수하기 위해 ***garbage collector(GC)*** 에 의존하는 것 이상의 작업을 수행해야 한다  
게다가 일반적으로 자원의 close 메서드를 호출하여 자원을 다시 운영 체제로 릴리스 해야한다
하지만 GC가 회수하기전에 이 작업을 수행하지 못하면 자원을 해제하는데 필요한 정보가 손실되기 때문에 **운영체제가 여전히 사용중인 것으로 여겨 자원이 누출되어버린다**  

위와 같은 상황에서 try-with-resource문을 이용하여 자원을 반납하는 것을 더욱 확실하게 해줄 수 있다

다음은 try-with-resource문을 적용시킨 예제이다

```java
static Stirng readFirstLineFromFile(String path) throws IOException {

	try(FileReader fr = new FileReader(path);
            BufferedReader br = new BufferedReader(fr)) {
            return br.readLine();
	}

}
```

- `try()` 안에 리소스를 선언하였다 → `FileReader` 와 `BufferedReader`  
  해당 리소스들은 프로그램 종료후 닫혀야 하는 객체들이다  
  이렇게 리소스를 선언할 때는 try문 바로 뒤의 괄호 안에 적어주면 된다


다만 반납하고 싶은 자원들은 `java.lang.AutoClosable` 인터페이스를 구현해야한다

> `close()`를 구현하도록 되어있다
>

이렇게 `FileReader`자원과 `BufferedReader`자원은 `AutoClosable`을 구현하고 try-with-resource문 안에서 사용한다면  
실행로직이 정상적으로 완료되거나 try문이 예외를 던지는 것에 상관없이  
자동으로 자원 반납이 이루어진다

---

- 더 쉬운 예제

  AutoClosable을 구현한 리소스 클래스

    ```java
    public class MangKyuResource implements AutoCloseable {
    
        @Override
        public void close() throws RuntimeException{
            System.out.println("close");
            throw new IllegalStateException("close exception");
        }
    
        public void hello() {
            System.out.println("hello");
            throw new IllegalStateException("hello exception");
        }
    }
    ```

  try-catch-finally 사용

    ```java
    public void temp1() {
        MangKyuResource mangKyuResource = null;
    
        try {
            mangKyuResource = new MangKyuResource();
            mangKyuResource.hello();
        } finally {
            if (mangKyuResource != null) {
                mangKyuResource.close();
            }
        }
    }
    ```

  여기서 hello메서드에 있는 에러 로그가 찍히지 않는다 → 누락됨!

  try-with-resource 사용

    ```java
    public void temp2() {
        try (MangKyuResource mangKyuResource = new MangKyuResource()) {
            mangKyuResource.hello();
        }
    }
    ```

  소스참고 : [https://mangkyu.tistory.com/217](https://mangkyu.tistory.com/217)


참고 :   
[The try-with-resources Statement (The Java™ Tutorials > Essential Java Classes > Exceptions)](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)  
[[Java] try-with-resources란? try-with-resources 사용법 예시와 try-with-resources를 사용해야 하는 이유](https://mangkyu.tistory.com/217)  
[자원을 자동으로 해제, try-with-resource](https://tecoble.techcourse.co.kr/post/2021-04-26-try-with-resource/)