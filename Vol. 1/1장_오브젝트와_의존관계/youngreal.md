## 1장의 주요내용

- 요구사항과 환경이 변함에따라서 변화를 최소화할수있는 방향으로 항상 생각해보자

- 관심사, 변화의성격이 다른 부분을 기준으로 코드를 분리한다 

- 디자인패턴, 객체지향 원칙, 리팩토링 등  위의 2가지 목적을 달성하기위한 예시가 소개되었다.



### ApplicationContext장점

- 싱글톤 패턴의 단점을 일부 상쇄하여 빈오브젝트를 싱글톤으로 만든다.   
=> private생성자만 가질필요가 없어져 상속이 가능해지며, Mock으로 테스트하기도 수월해진다

- **구체적인 팩토리 클래스를 알필요없음** (이해가 안되는부분, 아래에 적었습니다) 

- IOC의 부가적인 기능을 제공받는다.(후처리, 전략,시점 등)

- 빈을 검색하는 다양한 방법을 제공한다
(타입검색, 애노테이션설정 검색 등) 




## 이해가 안되었던점

```java
        // 스프링 적용
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(DaoFactory.class);
        UserDao dao = context.getBean("userDao", UserDao.class);

        // 스프링 적용 x
        UserDao dao2 = new DaoFactory().userDao();
```


### p100 ApplicationContext 적용했을때 첫번째이점

> 클라이언트가 구체적인 팩토리 클래스를 알필요가없다      
클라이언트가 필요한 오브젝트를 가져오려면 **어떤 팩토리 클래스를 사용해야할지 알아야하고**      
필요할때마다 팩토리 오브젝트를 생성해야 하는 번거로움이있다. 


**굵게 표시한부분**이 어렵습니다 ㅠ

1. ApplicationContext를 적용하더라도, DaoFactory뿐 아니라 IoC를 적용한 다른오브젝트도 계속 추가된다면   
어떤오브젝트를 ApplicationContext에 넣어야할지 알아야하는것 아닌가요?

2. 알아야한다는것이 의존관계와관련된 설명이라면 적용전에는 UserDaoTest 와 DaoFactory의 관계,  적용후에는 UserDaoTest와 ApplicationContext의 관계가 생겨 유연해졌다는 이야기일까요?
