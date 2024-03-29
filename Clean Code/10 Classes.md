## 클래스

코드의 표현력과 그 코드로 이루어진 함수에 아무리 신경 쓸지라도 좀 더 차원 높은 단계까지 신경 쓰지 않으면 깨끗한 코드를 얻기는 어렵다. 이 장에서는 깨끗한 클래스를 다룬다.

<BR>

### 클래스 체계

클래스를 정의하는 표준 자바 관례에 따르면 가장 먼저 변수 목록이 나온다. 공개 변수가 필요한 경우는 거의 없다.

정적 공개 상수 👉 정적 비공개 변수 👉 비공개 인스턴스 변수 

변수 목록 다음에는 공개 함수가 나온다. 비공개 함수는 자신을 호출하는 공개 함수 직후에 넣는다. 즉, 추상화 단계가 순차적으로 내려간다.

#### 캡슐화

변수와 유틸리티 함수는 가능한 공개하지 않는 편이 낫지만 **반드시 숨겨야 한다는 법칙도 없다**. 때로는 변수나 유틸리티 함수를 protected로 선언해 테스트 코드에 접근을 허용하기도 한다. 우리에게 테스트는 아주 중요하다. 같은 패키지 안에서 *테스트 코드가 함수를 호출하거나 변수를 사용해야 한다면* 그 함수나 변수를 protected로 선언한다. 하지만 그 전에 *비공개 상태를 유지할 온갖 방법을 강구*한다. **캡슐화를 풀어주는 건 언제나 최후의 수단**이다.

<br>

### 클래스는 작아야 한다

클래스를 만들 때 첫 번째 규칙은 크기다. 작아야 한다. 클래스를 설계할 때도 함수와 마찬가지로 '작게'가 기본 규칙이다. 그렇다면 얼마나 작아야 하는가?

함수는 행 수로 크기를 측정했다. 클래스는 **클래스가 맡은 책임**으로 크기를 측정한다.

**클래스 이름은 해당 클래스 책임을 기술해야 한다**. 실제로 작명은 클래스 크기를 줄이는 첫 번째 관문이다. *간결한 이름이 떠오르지 않는다면 클래스 크기가 너무 커서* 그렇다. 클래스 이름에 모호한 단어가 (Processor, Manager, Super) 있어 이름이 모호해진다면 클래스가 여러 책임을 떠안았다는 증거다.

또한 **클래스를 설명할 때** *만일 if*, *그리고 and*, *하며 or*, *하지만 but*을 사용하지 않고 25단어 내외로 가능해야 한다. 만약 그러지 못하다면 그 클래스에 책임이 너무 많은 것이다.

#### 🦾 단일 책임 원칙 *Single Responsibility Principle*

단일 책임 원칙은 **클래스나 모듈을 변경할 이유가 하나 뿐이어야 한다**는 원칙이다. SRP는 책임이라는 개념을 정의하며 적절한 클래스 크기를 제시한다. 

변경할 이유를 파악하려 애쓰다 보면 코드를 추상화하기도 쉬워진다. 더 좋은 추상화가 더 쉽게 떠오른다. (변경할 이유가 하나여야 함 = 책임 하나) 

SRP는 객체 지향 설계에서 더욱 중요한 개념이다. 또한 이해하고 지키기 수월한 개념이기도 하다. 하지만 SRP는 클래스 설계자가 가장 무시하는 규칙이다. **우리는 수많은 책임을 떠안은 클래스를 꾸준하게 접한다**. 왜일까? *소프트웨어를 돌아가게 만드는 활동*과 *소프트웨어를 깨끗하게 만드는 활동*은 완전히 별개다. 우리들 대다수는 '돌아가는 소프트웨어'에 초점을 맞춘다. 괜찮다. 관심사를 분리하는 작업은 프로그램만이 아니라 프로그래밍 활동에서 중요하다.

문제는 *프로그램이 돌아가면 일이 끝났다고 여기는 데 있다*. '깨끗하고 체계적인 소프트웨어'라는 다음 관심사로 전환하지 않는다. 프로그램으로 되돌아가 만능 클래스를 단일 책임 클래스 여럿으로 분리하는 일을 하는 대신 그냥 다음 문제로 넘어가 버린다.

> 만능 클래스란 모든 일을 하는 클래스, 말 그대로 여러 책임을 지고 있는 클래스를 말한다.

게다가 많은 개발자는 자잘한 단일 책임 클래스가 많아지면 큰 그림을 이해하기 어려워진다고 우려한다. 큰 그림을 이해하려면 이 클래스 저 클래스 수없이 넘나들어야 한다고 걱정한다. 하지만 어느 시스템이든 익힐 내용은 그 양이 비슷하다. 그러므로 고민할 질문은 다음과 같다. "도구 상자를 어떻게 관리하고 싶은가? 작은 서랍을 많이 두고 기능과 이름미 명확하게? 아니면 큰 서랍 몇 개에 모두를 던져넣고 싶은가?"

규모가 어느 수준에 이르는 시스템은 논리가 많고 복잡하다. **복잡성을 다루려면 체계적인 정리가 필수다**. 그래야 개발자가 무엇이 어디에 있는지 쉽게 찾고, 변경을 가할 때 직접 영향이 미치는 컴포넌트만 이해해도 충분하다. *다목적 클래스는 변경을 가할 때 당장 알 필요가 없는 사실까지 들이밀어 독자를 방해*한다.

다시 말하자면, **큰 큰 클래스 몇 개가 아니라 작은 클래스 여럿으로 이뤄진 시스템이 더 바람직하다**. 작은 클래스는 각자 맡은 책임이 하나며, 변경할 이유가 하나다.

#### 🦈 응집도 *Cohesion*

클래스는 **인스턴스 변수 수가 작아야 한다**. **각 클래스 메서드는 클래스 인스턴스 변수를 하나 이상 사용해야 한다**. 일반적으로 *메서드가 변수를 더 많이 사용할수록 메서드와 클래스는 응집도가 높다*. 모든 인스턴스 변수를 메서드마다 사용하는 클래스의 응집도가 가장 높다.

응집도가 가장 높은 클래스는 가능하지도 바람직하지도 않다. 그렇지만 우리는 응집도가 높은 클래스를 선호한다. *응집도가 높다는 말은 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다는 의미*기 때문이다.

함수를 작게, 매개변수 목록을 짧게라는 전략을 따르다 보면 **때때로 몇몇 메서드만이 사용하는 인스턴스 변수가 많아진다**. 새로운 클래스로 쪼개야 한다는 신호다. 응집도가 높아질수록 변수와 메서드를 적절히 분리해 새로운 클래스 두세 개로 쪼개준다.

#### 🦈🐬 응집도를 유지하면 작은 클래스 여럿이 나온다

큰 함수를 작은 함수 여럿으로 나누기만 해도 클래스 수가 많아진다. 

변수가 아주 많은 큰 함수 하나가 있다. 큰 함수 일부를 작은 함수 하나로 빼내고 싶은데, 빼내려는 코드가 큰 함수에 정의된 변수 넷을 사용한다. 이 때 변수 네 개를 새 함수에 인수로 넘기는 방식이 아니라, 네 변수를 클래스 인스턴스 변수로 승격해라. 

불행히도 이렇게 하면 클래스가 응집력을 잃는다. 몇몇 함수만 사용하는 인스턴스 변수가 점점 더 늘어나기 때문이다. 그런데 몇몇 함수가 몇몇 변수만 사용한다면 클래스로 분리해도 된다. **클래스가 응집력을 잃는다면 쪼개라**.

그래서 큰 함수를 작은 함수 여럿으로 쪼개다 보면 종종 작은 클래스로 여럿으로 쪼갤 기회가생긴다. 그러면서 프로그램에 점점 더 체계가 잡히고 구조가 투명해진다.

리팩터링 하면 프로그램 길이가 늘어나는데 이유는 이와같다.

1. 리팩터링한 프로그램은 좀 더 길고 서술적인  변수 이름을 사용한다.
2. 리팩터링한 프로그램은 코드에 주석을 추가하는 수단으로 함수 선언과 클래스 선언을 활용한다.
3. 가독성을 높이고자 공백을 추가하고 형식을 맞추었다.

리팩토링은 재구현이 아니다. 프로그램을 처음부터 다시 짜지 않는다. 

1. 원래 프로그램의 (돌아가는 소프트웨어) 정확한 동작을 검증하는 테스트 슈트를 작성한다. 
2. 그런 다음 한 번에 하나씩 수 차례에 걸쳐 조금씩 코드를 변경한다.
3. 코드를 변경할 때마다 테스트를 수행해 원래 프로그램과 동일하게 동작하는지 확인한다. 그러면 조금씩 원래 프로그램을 정리한 결과 최종 프로그램이 얻어진다.

<br>

### 변경하기 쉬운 클래스

대다수 시스템은 지속적인 변경이 가해진다. 그리고 뭔가 변경할 때마다 시스템이 의도대로 동작하지 않을 위험이 따른다. 개끗한 시스템은 클래스를 체계적으로 정리해 변경에 수반하는 위험을 낮춘다.

주어진 메타 자료로 적절한 SQL 문자열을 만드는 클래스가 있다. 아직 미완성이라 update문과 같은 일부 SQL 기능은 지원하지 않는다. 언젠가 update문을 지원할 시점이 오면 *클래스에 손대어 고쳐야 한다*. 문제는 코드에  손대면 위험이 생긴다는 사실이다. **어떤 변경이든 클래스에 손대면 다른 코드를 망가뜨릴 위험이 존재**한다. 

새로운 SQL문을 지원하려면 반드시 SQL 클래스에 손대야 한다. 기존 SQL문 하나를 수정할 때도 반드시 SQL 클래스에 손대야 한다. SQL 클래스는 변경할 이유가 두가지다. = SRP를 위반한다.

**클래스에 손대는 순간 설계를 개선하려는 고민과 시도가 필요하다**.

SQL 클래스 같은 경우는 SQL 클래스를 CreateSql, SelectSql 등의 클래스에서 상속하면 된다. 이렇게 하면 update 문을 추가할때 기존 클래스를 변경할 필요가 전혀 없다. SQL 클래스를 상속받는 새 클래스 UpdateSql만 만들면 되기 때문이다. update문을 지원해도 다른 코드가 망가질 염려는 없다.

이렇게 재구성하면 OCP도 지원한다. (SRP와 함께) 재구성한 sql 클래스는 *파생 클래스를 생성하는 방식으로 새 기능에 개방적*인 동시에 다른 클래스를 닫아놓는 방식으로 수정에 폐쇄적이다. 그저 UpdateSql 클래스를 제자리에 끼워 넣으면 끝난다.

새 기능을 수정하거나 기존 기능을 변경할 때 **건드릴 코드가 최소인 시스템 구조**가 바람직하다. 이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장 할 뿐 기존 코드를 변경하지는 않는다.

#### 변경으로부터 격리

요구사항은 변하기 마련이다. 다라서 코드도 변하기 마련이다. 상세한 구현에 의존하는 클라이언트 클래스는 구현이 바뀌면 위험에 빠진다. 그래서 우리는 인터페이스와 추상 클래스를 사용해 구현이 미치는 영향을 격리 해야 한다.
