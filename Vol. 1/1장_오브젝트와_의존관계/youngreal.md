## 1장의 주요내용

- 요구사항과 환경이 변함에따라서 변화를 최소화할수있는 방향으로 항상 생각해보자

- 관심사, 변화의성격이 다른 부분을 기준으로 코드를 분리한다 

- 디자인패턴, 객체지향 원칙, 리팩토링 등  위의 2가지 목적을 달성하기위한 예시가 소개되었다.

- IoC 컨테이너는 싱글톤 패턴의 단점을 일부 상쇄하여 빈오브젝트를 싱글톤으로 만든다.   
=> private생성자만 가질필요가 없어졌고, Mock으로 테스트하기도 수월하다






## 이해가 안되었던점

```java
public class UserDaoTest {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {

        // 스프링 적용
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(DaoFactory.class);
        UserDao dao = context.getBean("userDao", UserDao.class);

        // 스프링 적용 x
        UserDao dao2 = new BeforeDaoFactory().userDao();
		...
    }
}
```


#### p100 DaoFactory를 ApplicationContext에 적용했을때 첫번째이점

- 클라이언트가 구체적인 팩토리 클래스를 알필요가없다   
클라이언트가 필요한 오브젝트를 가져오려면 **어떤 팩토리 클래스를 사용해야할지 알아야하고**   
필요할때마다 팩토리 오브젝트를 생성해야 하는 번거로움이있다. 



나머지 이점은 이해했는데 **굵게 표시한부분**을 이해를 못했습니다    
이건 ApplicationContext를 적용하더라도 ApplicationContext에 넣을때   
어떤 팩토리클래스를 사용해야하는지 클라이언트가 알아야 하는것 아닌가요??

> 제가 이해한내용   
(ApplicationContext를 적용하더라도, DaoFactory뿐 아니라 IoC를 적용한 다른오브젝트도 계속 추가된다면 어떤오브젝트를 ApplicationContext에 넣어야할지 알아야한다)
