## 5장 정리

### ✔️ 테스트용 UserService 대역
- 테스트를 위해 복사를 하고 새로운 클래스를 만들어 구현한다면, 코드 중복 발생과 번거롭다
- 테스트에 필요한 기능만을 오버라이딩 하는 방법이 있다
- protected 로 수정해서 상속을 통해 오버라이딩 후 테스트를 한다

### ✔️ 숫자를 가지고 테스트를 할 땐??
- 경곗값을 가지고 테스트하는 것이 바람직하다

### ✔️ 트랜잭션 동기화
- TransactionSynchronizationManager...
- DataSourceUtils 제공하는 getConnection() 을 통해 커넥션 생성

### ✔️ 글로벌 트랜잭션을 위한 java api는?
- JTA!

### ✔️ JavaMail 관련 부분에서의 인상 깊었던 점
> mock 테스트 라이브러리를 사용하지 않고
> 직접 클래스를 만들어 테스트를 진행하는 것이 인상 깊었다
