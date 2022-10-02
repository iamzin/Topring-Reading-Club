## [왜 초난감 DAO 인가?](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/main/java/tobystudy/group/object_di/base/UserDao.java#L7)
잘만 실행되는 DAO를 보고 왜 초난강 DAO라고 부르는 걸까?

<hr>
 

## 문제점
미래를 생각하지 않는 코드라는 점 현재의 DAO에 변화가 있거나 기능이 추가됐을 때 현재 코드에서 변경되는 부분을 생각해 보자 [Connetion](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/main/java/tobystudy/group/object_di/base/UserDao.java#L16) 만약 여러 DAO가 있고 메서드가 수백 개일 때 DB를 변경하게 된다면 수백 줄의 Connection 생성 코드를 일일이 바꿔야 하는 불상사가 생길 수 있다.

<hr>

## DAO의 분리 
- DAO를 어떻게 분리해야 할까?
감이 잘 오지않지만 책에서는 이러한 가이드를 준다. 서로 관련 있는 것들끼리 모으고 관련 없는 것들은 따로 떨어뜨린다.(결합도는 낮추고 응집성을 높여라)
- 서로 관련 있는 건 어떻게 알까?
리팩토링 관련 서적들을 참고하면 관련 있는 것들은 어떤 필드나 메서드의 시그니처를 변경했을 때 연속적으로 변경해야 할 것들이 생기거나 항상 같이 사용되는 메서드, 필드 등을 관련 있다고 생각해 보자.

그럼 이제 초난감 DAO를 나눠보자 크게 나눈다면 아래 3가지로 나눌 수 있다.

  1. [Connection을 어떻게 생성할 것인지?](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/main/java/tobystudy/group/object_di/base/UserDao.java#L16)
 
  2. [sql 생성 -> 실행](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/main/java/tobystudy/group/object_di/base/UserDao.java#L18)
  
  3. 리소스 정리

이렇게 3가지로 나누어 리팩토링해보자
먼저 메서드로 분리하는 방법도 있을 것이다. 하지만 확장을 좀 더 편리하게 하기 위해서 상속을 통해서 [클래스로 코드를 분리해](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/main/java/tobystudy/group/object_di/v1/DUserDao.java#L7) 보자
이렇게 변하는 부분을 따로 서브 클래스로 빼내는 것을 템플릿 메서드 패턴이라고 하고 마찬가지로 dao에서 사용하고 있는 getConnection은 팩토리 메서드 패턴이라고 할 수 있다.

> [디자인패턴에 대해서 읽어볼만한 글](https://zdnet.co.kr/view/?no=00000039131344)


<hr>

## DAO의 확장
- 모든 객체는 변한다.

- 어떻게 확장할것인가??
   - 아예 특정기능을 수행하는 클래스로 따로 분리시키자!
   - 인터페이스를 사용하자!
   
  
### 클래스 분리시키기
- Connection을 생성해 주는 클래스를 따로 분리하고 DAO가 해당 메서드를 의존하도록 만든다.

- 상속을 사용하면 되지 굳이 클래스를 분리한 이유는 뭘까?
예를 들어 superDAO에 어떤 메서드가 추가되거나 변경될 시에 하위 DAO에 변경이 일어날 수 있다. 이외에도 상속이 가지는 단점이 있다. 
때문에 아예 Connection을 담당하는 클래스로 분리시켜보자


### [인터페이스 사용하기](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/main/java/tobystudy/group/object_di/v2/ConnectionMaker.java#L6)
- 인터페이스를 사용했을 때의 장점은?
앞선 클래스 분리방법을 사용한다면 DAO가 ConnectionMaker의 구현체를 직접 알고 있어야 한다. 이렇게 되면 의존성 문제가 생기게 되고 코드가 유연해지지 못하게 된다. 따라서 DAO는 ConnectionMaker라는 인터페이스만 알고 있고 구현 클래스는 모르도록 설계하는 게 좋은 방법이라고 할 수 있다.


### [관계설정 책임의 분리](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/main/java/tobystudy/group/object_di/v2/UserDao.java#L13)
- 관계 설정?
객체들은 관계를 가지고 어떤 일을 수행한다 DAO 입장에서는 ConnectionMaker의 구현체들 중 한 명과 와 관계를 맺고 쿼리를 실행하는 일을 수행한다.
근데 이 관계를 설정하고 만드는 것은 DAO 자신이 직접 할 수도 있지만 다른 객체에서 DAO와 ConnectionMaker와 관계를 설정해 주는 것도 가능하다.


- 인터페이스를 사용하면서 DAO 내부에 구현체를 적는다면 앞선 클래스 분리시키기라는 방법과 다를 바가 없다 이를 해결하는 방법은 ConnectionMaker의 타입의 인스턴스를 DAO 내부가 아닌 바로 외부에서 주입시켜주는 것이다. 런타임 시에 DAO가 [ConnectionMaker의 구현체 클래스를 의존할 수 있도록 의존관계를 설정해 주자](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/test/java/tobystudy/group/object_di/UserDaoV2Test.java#L13)


### 원칙과 패턴
- 객체지향의 설계 원칙(SOLID)
  - SRP : 단일 책임 원칙
  - OCP : 개방 폐쇄 원칙
  - LSP : 리스 코프 치환 원칙
  - ISP : 인터페이스 분리 원칙
  - DIP : 의존관계 역전 원칙

- 아직 완성되진 않았지만 초난감 DAO에서 단일 책임 원칙을 지키도록 리팩토링했다고 볼 수 있다.
  - Connection을 생성하는 클래스를 분리한 것.(DAO가 바뀌거나 Connection 생성 내부의 로직이 바뀌어도 서로 영향이 없다)
  - 또한 DAO의 변경은 닫혀있지만 인터페이스를 통해서 언제든지 확장이 가능하도록 분리한 것(OCP)
  - DAO가 ConnectionMaker 인터페이스를 의존하고 외부에서 주입받는 것 (DIP)
  벌써 3가지의 설계 원칙을 지키도록 리팩토링했다.
  
 
- OCP를 잘 지키면 자연스럽게 응집도는 높아지고 결합 도는 낮아진다.

- DAO에서 ConnectionMaker를 의존하도록 설계하고 필요할 때마다 설정을 바꿔서 사용하는 이런 패턴을 전략 패턴이라고 한다. [ConnectionMaker의 구체 클래스](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/main/java/tobystudy/group/object_di/v3/NConnectionMaker.java#L7)들은 DAO가 사용할 수 있는 여러 전략들이라고 생각할 수 있다.


<hr>

## 제어의 역전

- 제어의 역전이 뭘까?
앞서 봤듯이 DAO 자신이 ConnectionMaker의 구현체를 생성해서 사용할 수 있다. 하지만 지금의 제어권은 DAO가 아닌 다른 제3의 객체가 이를 제어한다.
이렇게 제어의 흐름을 거꾸로 뒤집어놓은 것을 제어의 역전이라고 한다.

- TestCode에서 사용하던 DAO 설정을 분리해 DAO를 세팅하고 관계를 설정해 주는 [Factory 클래스](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/main/java/tobystudy/group/object_di/v3/DaoFactory.java#L4)를 만들어 보자

<hr>


## 스프링 IOC
- 스프링 IOC == Factory ??
앞서본 Factory 클래스가 IOC의 역할 중에 객체 간의 관계 설정과 생성을 하는 역할을 한다고 볼 수 있다. 이렇게 spring 기술과 설정을 통한 생성된 객체를 빈이라 하고 spring은 이 빈들을 잘 만들고 관리시켜준다 이처럼 IOS는 아주 중요한 역할을 수행하는 스프링의 핵심 기술이라고 할 수 있다.

<hr>


## 싱글톤 레지스트리와 오브젝트 스코프
- 싱글톤 패턴 : 인스턴스가 오직 1개만 생성되는 패턴

- spirng은 별다른 설정을 하지 않으면 대부분의 빈을 싱글톤 스코프로 사용한다

- 싱글톤 패턴의 단점
  - 상속이 불가능하다 private 키워드를 가진 생성자 때문에 상속이 불가능하다.
  - 테스트하기 힘들다.
  - 전역 상태를 만들 수 있다.
  - 서버 환경에서 싱글톤이 하나만 만들어지는 것을 보장할 수 없다.
이렇게 단점이 많기 때문에 안티 패턴이라고도 불린다.

- 그럼 스프링은 왜? 싱글톤 객체로 빈을 생성할까?
보통 spring은 웹같이 멀티 스레드 환경에서 자주 사용되는데 이때 각 request마다 객체를 생성한다면 아마 서버의 자원이 남아나지 않을 것이다.
 
- 어떻게 사용해야 할까?
가장 중요한 부분이다. spring에서 빈을 등록할 때 싱글톤으로 등록한다면 static 변수 혹은 인스턴스 변수를 값을 바뀌는 정보를 담는 변수로 사용했다가는 큰일 날 수 있다. 즉 인스턴스가 하나인데 그 인스턴스 변수에 여러 스레드들이 달려들어서 해당 변수의 값을 변경하거나 조회한다면 사용자는 정확한 데이터와 정보를 제공받을 수 없게 된다.

- 스프링의 싱글톤 레지스트리
- 앞서본 싱글톤은 private 키워드를 사용해야 하기 때문에 상속이 불가능한대 스프링은 이러한 문제 없이 private 키워드가 아닌 평범한 클래스도 싱글톤으로 사용할 수 있게 스프링이 관리해 준다.
- 이처럼 싱글톤 레지스트리는 싱글톤 패턴의 단점을 제거하고 객체를 만들고 관리해 주는 역할을 수행한다

## 의존관계 주입
- spring의 핵심 기술이다. (전략 패턴을 사용, 즉 코드 변경에는 닫혀있고 확장에는 열려있도록 설정과 구현을 분리해서 코드를 유연하고 객체지향적으로 설계할 수 있도록 도와주는 스프링의 핵심기술)

### 런타임 의존관계 설정
- 런타임 의존관계 설정이란 뭘까 말 그대로 실행될 때 의존관계가 생성된다는 것이다. DAO 입장에서 본다면 코드가 실행되기 전까지는 어떤 ConnectionMaker를 의존할지 전혀 모른다. 제3자에 의해서 의존관계가 설정되기 때문이다.

- 의존관계 주입의 3가지 조건
  - 코드에 의존관계가 드러나지 않는다 즉 인터페이스에만 의존해야 한다.
  - 컨테이너 또는 팩토리 같은 제3의 존재가 의존관계를 설정한다.
- 의존관계를 사용할 오브젝트는 외부에서 제공해 준다.
  

### 의존관계 검색과 주입
- DI(의존관계 주입)은 말 그대로 주입받는다는 걸 알겠는데 그럼 검색은 뭘까?
 의존관계 검색은 자신이 필요한 의존 오브젝트를 능동적으로 찾는 행위라 할 수 있다.

- DL이 필요한 경우는 [다음처럼 Test 할 때](https://github.com/HYK97/Topring-Reading-Example/blob/d641c7c5030e4218785d01b8d512f2ff1508c04c/src/test/java/tobystudy/group/object_di/UserDaoV4Test.java#L16)(getBean) 사용할 수 있다.
  - DI는 주입할 오브젝트가 반드시 스프링 빈으로 등록돼야 사용 가능하다.
  - DL는 스프링 빈으로 등록하지 않아도 사용이 가능하다.
