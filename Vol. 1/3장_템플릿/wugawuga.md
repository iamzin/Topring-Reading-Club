## 3장 정리

### ✔️ 전략 패턴
- 인터페이스나 추상 클래스를 이용해 분리 시키는 패턴

### ✔️ 템플릿-콜백 패턴
- 전략패턴에 속할 수 있는 패턴??
- 메소드 하나만을 가지는 인터페이스를 이용하는 패턴

### ✔️ jdbcTemplate 추상화가 너무 잘되어 있는데 굳이 패턴을 알아야하나?
> 메소드만 알면 되는거 아닌가? 다양한 패턴이 너무 많구나 의구심을 가지고 있었다

>그런데 결국에는 반복되는 문제에 이러한 방식으로 접근했구나 생각이들고
>점차 콜백패턴으로 줄여 나가는 거에 되게 흥미로웠다

### ✔️ 람다 표현식과 익명 클래스의 차이점은??
- 익명 클래스와 동작이 같다고 생각을 한다

### ✏️ 느낀점
- 디자인 패턴이 계속해서 등장하는데 디자인 패턴 공부에 시간을 투자를 해야지 이 책을 조금 더 재밌게 읽을 것 같다
- 예제코드를 따라치다가 콜백이 여러번 등장하는 순간이 있는데 집중이 잘 되지 않았다
- 템플릿과 콜백을 찾아낼 때, 변하는 코드의 경계를 찾고 그 경계를 사이에 두고 주고받는 일정한 정보가 있는지 확인하라고 한다
- 토비님 말씀을 듣다가 어떤 외국형님이 이 말씀을 하셨다고 한다
    - 객체지향은 데이터를 없애는거다,,,,,,????
    - 알고보니 전역변수를 없애는 것이라고 한다
