
이번장은 읽으면서 생각 해볼 거리가 많은 장이였다. 예외를 실제로 이용하기보다는 만났을때 해결해야하는 문제로만 인식했지 어떻게 하면 예외를 잘 이용해볼까 ? 에 대한 고민은 많이 안해봤던것같다. 이번 글에서 예외에 대한 생각들을 정리해보자

<hr>

# 예외

<hr>

## 예외의 종류
먼저 간단하게 예외의 종류를 알아보자
java의 예외는 간단하게 그림처럼 나눌수있다.


<img src="https://www.tutorialspoint.com/java/images/exceptions1.jpg" style="background-color:white">

>https://www.tutorialspoint.com/java/java_exceptions.htm

- Error
  에러란 시스템에 뭔가 비정상적인 상황이 발생했을 때 사용된다. 따라서 복구할 수 있는 방법이나 조치를 취할 수 없기 때문에 catch로 잡지 않는다.
  ex) OOM, StackOverflow

- Exceptions
  예외란 개발자의 실수로 인해 참조된 값, 입력값 등의 여러 가지 이유로 일반적인 프로그램의 흐름을 벗어나는 것을 말한다. 따라서 이 흐름을 정상적으로 바꾸거나 복구하도록 후속 조치를 취할 수 있다.
    - Runtime Exceptions(unchecked)
    - Other Exceptions(checked)

그림을 보면 Exceptions는 두 가지로 나뉘는데 그중 `RuntimeException`을 상속받은 클래스들은 unchecked Exception라고 분류할 수 있고 그 외에 클래스들은 checked Exception으로 분류할 수 있다. 그럼 왜 이렇게 분류할까?

1. checked Exception은 다음과 같이 발생할 수 있는 예외들을 이렇게 메서드 옆에 명시적으로 Exception을 적어야 한다. 따라서 이를 체크한다고 말할 수 있기 때문에 checked Exception이라 한다.

```java
public void exceptionTest() throws sqlException ...

```

2. 반대로 unchecked Exception인 경우에는 checked Exception 과는 다르게 별도로 명시적으로 작성할 필요는 없다.

```java
public void exceptionTest() 
```

<hr>

## JdbcTemplate의 Exception
`JdbcTemplate` update, query*.. method에 붙어있던 Sql Exception(checked)은 다 어디로 갔을까?

![](https://velog.velcdn.com/images/ddh963963/post/f3356445-3188-4be0-8b0b-d330497e8a95/image.png)
update 내부를 살펴보면 `SqlException`은 사라지고 웬 엉뚱한 `DataAccessException`이라는 것을 던지고 있다는 것을 볼 수 있다.

사용할 때 이 Exception을 명시적으로 적지 않는 것을 보아 Unchecked Exception이라고 유추할 수 있다.

![](https://velog.velcdn.com/images/ddh963963/post/653414fc-4933-45ee-98a6-ffa4163df5e9/image.png)

조금 더 코드를 따라가보면 `translateException`이라는 메서드를 볼 수 있는데 바로 이 메서드가 기존의 `SqlException`과 같은 Check Exception을 바로 `DataAccessException`으로 전환해서 보내준다는 사실을 할 수 있다. 이 예외 전환은 예외 처리 전략 중 하나이다. 간단하게 살펴보자면 `getExceptionTranslator()`로`SQLExceptionTranslator`구현체에 있는 `translate`라는 메서드를 통해서 전환된다.

어떻게 예외가 전환될까? Jdbctemplate 같은 경우에는 `SQLErrorCodeSQLExceptionTranslator`을 통해서 실제로 예외가 전환되는데 `sqlerrorcodes.xml`라는 xml 파일의 코드를 기준으로 적절한 Exception으로 전환되어 리턴된다. `sqlerrorcodes.xml` 파일은 그럼 뭘까? 이 xml 파일은 각 db마다 고유한 error code와 각 code 별로 반환할 적절한 Exception들이 명세되어 있다.

> sql-error-codes.xml
![](https://velog.velcdn.com/images/ddh963963/post/5152d926-4cc3-4c20-bc47-3b9d2ab1d3c9/image.png)


이런 식으로 `JdbcTemplate`은 기존의 checked Exception을 `DataAccessException`이라는 잘 추상화된 unchecked 예외로 전환해서 던져주는 방식으로 설계되어 있기 때문에 기존에 붙어있던 `SqlException`들이 사라진 것이다.

<hr>

## 예외처리 방법
예외 처리 방법으로는 예외 복구, 예외 회피, 예외 전환 이 3가지의 방법이 있다 전환은 앞서살펴봤듯이 적절한 예외를 감싸서 예외를 전환시키는 방법이다.

### 예외 복구와 회피는 뭘까?

예외 복구는 Exception이 발생했을 때 적절한 방법으로 복구를 시도해서 정상상태로 되돌려 놓는 방법이다.

예시로 `SocketException` 인 경우에 재접속 시도를 하는 복구방법을 예시로 들 수 있겠다.

예 외 회피는 현재 블록에서 처리할 수 있는 예외가 아닌 경우 자신을 호출한 쪽으로 예외를 던지는 것을 말한다.

<hr>

# 생각해볼거리

<hr>

## 예외의 또 다른기준
`IllegalStateException`과 `IOException`(Socket.. 등 네트워크 관련) 이 같은 예외로 취급해야 하나? 생각해 볼 필요가 있다.

- 1.프로그램이 정상적이라면 발생하면 안 되는 예외
    - `IOException` > 장애로 해석된다
    - `IllegalStateException`, `nullpointerException` ... > 개발자의 실수로 인해 버그가 있다고 해석

- 2.분기를 위한 용도 등..으로 클라이언트에게 안내,로 그저 장 등.. 넘어가도 되는 예외
    - application 예외 > 비즈니스 로직에 사용되는 필요한 Exception ex) 잔고 부족 예외

이처럼 예외가 무엇인지에 대해서도 생각해 보는 것이 좋다.

## 예외는 습관이다.
예외는 곧 습관이라고 한다. 토론 중에 예외 습관에 대한 얘기가 나왔었는데 기초 java 서적에서 Exception을 캐치한 후에 아무런 조치를 취하지 않거나 `e.printstacktrace`를 날리고 넘어가는 걸 습관적으로 보고 또 그렇게 사용하는 게 습관이 되어서 안 좋은 예외 처리 습관이 생기는 경우가 많다고 한다. 처음부터 예외의 중요성을 알고 그 습관을 고쳐나갔으면 좀 더 나은 예외 처리 습관을 길렀을 수 있었을지도 모른다 지금부터라도 고치도록 하자
  