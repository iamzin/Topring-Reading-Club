# 템플릿이란?
> 템플릿은 일정한 틀이나 형식을 의미한다.

스프링에서도 이 뜻은 일맥상통한다 바뀌지 않는 틀은 하나로 만들어두고 달라지는 부분만 분리해서 사용할 수 있도록 하는 것들이 보통 ~ 템플릿 ~ 패턴이라고들 많이 부른다.


<hr>

## 템플릿에 관련된 용어들

- ~Template Class들 ex) JdbcTemplate
- 템플릿 메서드 패턴
- 템플릿 / 콜백 패턴 (전략 패턴)

스프링에서는 다음과 같은 패턴들을 이용해서 중복되는 코드를 획기적으로 줄였다.

<hr>

## 중복 그거 그냥 메서드나 클래스로 분리하면 안 되나?
메서드나 클래스로 뽑을 수 있는 코드면 나눠서 재사용해도 되지만 분리하기 힘든 코드들이 있다 대표적으로 try ~ catch ~ finally 문 같은 것들이다.

**그럼 [try ~ catch ~ finally](https://github.com/HYK97/Topring-Reading-Example/blob/fcdbcd07b1256a37a774905b66a6805e8923fd5e/src/main/java/tobystudy/group/template/v1/UserDao.java#L26)가 계속 반복되는 것들을 어떻게 분리할 수 있을까?**

다음 3가지 방법으로 중복을 제거해보자

1. 템플릿 메서드 패턴
2. [전략 패턴](https://github.com/HYK97/Topring-Reading-Example/blob/fcdbcd07b1256a37a774905b66a6805e8923fd5e/src/main/java/tobystudy/group/template/v2/UserDao.java#L24)
3. [템플릿 / 콜백 패턴](https://github.com/HYK97/Topring-Reading-Example/blob/fcdbcd07b1256a37a774905b66a6805e8923fd5e/src/main/java/tobystudy/group/template/v4/UserDao.java#L26)

 <hr>

## 변하는 것과 변하지 않는 것

위의 3가지 방법들의 핵심은 변하는 부분과 변하지 않는 부분을 적절히 분리해 내는 것이다.
현재는 try 문이 계속 반복되고 있고 대부분의 메서드들에서도 비슷한 양상을 띄고 있다. 여기서
변하는 부분과 변하지 않는 부분을 한번 나눠보자

```java
 public void deleteAll() {
 
        //******변하지않는부분
        Connection conn = null;
        PreparedStatement stmt = null;
        try {
        //******
        
        
        //******변하는부분
            conn = connectionMaker.makeConnection();
            stmt = conn.prepareStatement("delete from users");
            stmt.executeUpdate();
        //****** 
            
            
        //****** 변하지않는부분
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } finally {
            if (stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    throw new RuntimeException(e);
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
  		//******
```

이렇게 변하지 않는 부분은 따로 빼고 가운데 부분 변하는 부분만 우리가 원하는 대로 바꿔 쓸 수 없을까?

<hr>

## 템플릿 메서드 패턴
> 슈퍼클래스에 변하지 않는 부분을 두고(템플릿) 서브클래스로 변하는 부분을(hook) 메서드 오버라이드 해서 사용하는 패턴

장점 : 중복의 분리가 가능하다
단점 : 변하는 부분의(hook)이 생길 때마다 class를 만들어야 한다.

이런 단점이 있기 때문에 Dao처럼 여러 메서드들을 만들어야 하는 클래스에서 분리해서 사용하기에는 적합하지 않아 보인다.

<hr>

## 전략패턴

> Context(전략을 사용하는 주체) 와 Strategy Interface(전략/알고리즘)로 분리해서 확장에 용이하고 유연하며 재사용 가능한 구조로 만드는 패턴 스프링에서는 보통 Strategy를 결정하는 것은 DI를 통해서 외부에서 결정하도록 많이 사용한다.

템플릿 메서드 패턴보다 훨씬 유연하고 확장성이 뛰어난 패턴이다.
Strategy Interface를 만들어 추상화된 인터페이스를 Context에 위임하고 Context는 Interface를 통해서 사용하기 때문에 직접 의존하지 않아 더 유연하게 사용이 가능하다.


여기서 [Context는](https://github.com/HYK97/Topring-Reading-Example/blob/fcdbcd07b1256a37a774905b66a6805e8923fd5e/src/main/java/tobystudy/group/template/v2/UserDao.java#L121) try ~ catch ~finally 부분이라고 생각하면 되고 내부의 변경되는 로직은 [Strategy로](https://github.com/HYK97/Topring-Reading-Example/blob/fcdbcd07b1256a37a774905b66a6805e8923fd5e/src/main/java/tobystudy/group/template/v2/AddStatement.java#L9) 생각하고 필요할 때 전략을 생성해서 사용하면된다.

하지만 전략 패턴도 마찬가지로 템플릿 메서드 패턴처럼 각 전략마다 클래스를 생성해야 한다는 단점이 있다.

<hr>

## 템플릿 콜백 패턴

> 전략 패턴 + 익명 내부 클래스를 활용한 패턴이다(특별한 하나의 케이스). 보통은 인터페이스에 한 개의 메서드를 가지고 있고 java8부터는 새로 추기된 람다를 이용해서 자주 쓰이는 패턴

[템플릿 콜백 패턴](https://github.com/HYK97/Topring-Reading-Example/blob/fcdbcd07b1256a37a774905b66a6805e8923fd5e/src/main/java/tobystudy/group/template/v4/UserDao.java#L26)을 이용하게 되면 추가적으로 클래스를 생성하지 않고도 전략을 바꿔서 사용할 수 있다. 보통은 파라미터에 바로 전략을 주입해서 사용한다.


<hr>

# 정리
다음 3가지 패턴으로 중복을 제거해 보았다. Spring에는 ~Template라는 이름의 클래스들이 많이 있는데 이러한 클래스들이 바로 위에서 살펴본 이런 패턴들을 이용해서 만들어진 클래스들이다. 이러한 Template 클래스들을 사용하는 것도 중요하지만 내부에 Template이 어떻게 동작하는지 혹은 내가 필요하다면 중복제거를 위해서 Template을 이용할 줄 알아야 한다. 이렇게 보니 스프링이 객체지향 설계에 얼마나 진심인지도 알 것 같다.
