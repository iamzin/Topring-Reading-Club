# Chpater 2 - 테스트

### ❓ 웹을 통한 테스트의 문제점

👉 매번 실행 또는 배포를 시켜야해서 느리다. 내 의도대로 코드가 동작하는지 빠른 시간 안에 확인하는 것이 불가능하다.
테스트를 하는 중에 에러가 나면 어디서 문제가 발생했는지 찾는 과정이 필요하다.

<br/>

### ❓ JUnit이란?

👉 자바로 단위 테스트를 만들 때 유용하게 사용할 수 있는 프레임워크

<br/>

### ❓ 왜 JUnit을 써야할까?

👉 TODO 가장 유명한 자바 테스트 프레임워크가 JUnit이라서 사용하는걸까? 왜 하필 JUnit일까?

<br/>

### ❓ 테스트를 해야하는 이유

👉
- 코드를 확신할 수 있게 하고 변화에 유연하게 대처 가능.
- 자주 반복할 수 있다.

> 스프링 프레임워크도 JUnit 프레임워크를 이용해 테스트를 하며 만들어졌다.

<br/>

### ❓ 테스트 코드 작성 시 유의할 점

👉 테스트는 항상 일관성 있는 결과가 보장되어야 한다. 외부 환경이나 테스트 실행 순서 등에 영향을 받으면 안된다. 

<br/>

### ❓ DI를 이용한 테스트 방법

👉 JUnit4의 `@Runwith(SpringJUnit4ClassRunner.class)`는 JUnit5에서 `@ExtendWith(SpringExtension.class)`로 대체되었다.
해당 어노테이션을 통해 테스트 컨텍스트를 제공받고 `@ContextConfiguration`을 통해 어떤 애플리케이션 컨텍스트를 만들어줄지 선택할 수 있다.
테스트 코드에서는 `@Autowired`를 통해 bean으로 등록된 객체를 자동으로 주입받을 수 있다.

```java
@ExtendWith(SpringExtension.class)
@ContextConfiguration(classes = DaoConfig.class)
class UserDaoTest {

    @Autowired
    private UserDao userDao;

    @BeforeEach
    void delete() {
        userDao.deleteAll();
    }

    @DisplayName(value = "사용자 조회하기")
    @Test
    void findById() {
        // given
        User user = new User("id", "name", "1234");
        userDao.add(user);

        // when
        User actual = userDao.findById(user.getId());

        // then
        assertThat(actual).isEqualTo(user);
    }
}
```

<br/>

### ❓ 테스트의 컨텍스트 공유

👉 여러 개의 테스트 클래스가 같은 설정파일을 사용하는 경우 같은 애플리케이션 컨텍스트를 공유한다.

<br/>
