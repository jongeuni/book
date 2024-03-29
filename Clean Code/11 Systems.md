## 시스템

> 복잡성은 죽음이다. 개발자에게서 생기를 앗아가며, 제품 계획 제작 테스트를 어렵가 만든다.
>
> -*레이 오지 Ray Ozzie*

<br>

#### 도시를 세운다면?

도시의 세세한 사항을 혼자 관리하기는 불가능 하다. 수도 관리 팀, 교통 관리 팀 등 각 분야를 관리하는 팀이 있기에 도시는 잘 돌아간다. 도시에는 큰 그림을 그리는 사람들도 있으며 작은 사항에 집중하는 사람들도 있다.

도시가 돌아가는 또 다른 이유는 적절한 추상화와 모듈화 때문이다. 큰 그림을 이해하지 못할지라도 개인과 개인의 구성요소는 효율적으로 돌아간다.

소프트웨어 팀도 도시처럼 구성한다. 하지만 추상화를 잘 이뤄내지 못한다. 깨끗한 코드를 구현하면 낮은 추상화 수준에서 관심사를 분리하기 쉬워진다. 이 장에서는 높은 추상화 수준, 즉 시스템 수준에서도 깨끗함을 유지하는 방법을 살펴본다.

<br>

### 시스템 제작과 시스템 사용을 분리하라

우선 제작은 사용과 아주 다르다는 사실을 명심한다. **시작 단계**는 모든 애플리케이션이 풀어야 할 관심사다. 시작 단계 관심사가 우리가 맨 처음 살펴볼 관심사다. 관심사 분리는 우리 분야에서 가장 오래되고 가장 중요한 설계 기법 중 하나다.

불행히도 대다수 애플리케이션은 시작 단계라는 관심사를 분리하지 않는다. *준비 과정 코드를 주먹구구식으로 구현할 뿐만 아니라 런타임 로직과 마구 뒤섞는다*.

```java
public Service getService(){
    if (service == null)
        service = new MyServiceImpl(...);
    return service;
}
```

이것은 *초기화 지연 Lazy Initialization* 혹은 *계산 지연 Lazy Evaluation* 기법이다. 장점은 여러가지다. 1. 실제로 필요할 때까지 (메서드 호출 전까지) 객체를 생성하지 않으므로 불필요한 부하가 걸리지 않는다. 2. 따라서 애플리케이션을 시작하는 시간이 그만큼 빨라진다. 3. 어떤 경우에도 null을 반환하지 않는다.

> 초기화 지연 기법이란?
>
> 초기화 지연은 필드 초기화를 실제로 그 값이 쓰일 때까지 미루는 것이다. 값을 사용하는 곳이 없다면 필드는 결코 초기화되지 않을 것이다.

하지만 위 메서드는 MyServiceImpl과 *MyServiceImpl의 생성자 인수에 의존*한다. 런타임에서 MyServiceImpl을 사용하지 않더라도 생성자 인수가 없으면 컴파일이 안 된다.

무엇보다 MyServiceImpl이 모든 상황에 적합한 객체인지 모른다. 그렇다고 getService 메서드를 포함한 클래스가 전체 문맥을 알 필요가 없다. 이 시점에서 어떤 객체를 사용할지 어떤 객체를 사용할지 알 수 없다.

초기화 지연 기법을 한 번 정도 사용한다면 별로 심각한 문제가 아니다. 하지만 이 설정 기법을 수시로 사용한다면 *전반적인 설정 방식이 애플리케이션 **곳곳에 흩어져 있다***. 모듈성은 저조하며 대개 중복이 심각하다.

> 모듈성이란 모듈들이 서로 독립적이고 닫혀있고 영역에 따라 다른 역할을 하는 성질을 말한다.

체계적이고 탄탄한 시스템을 만들고 싶다면 모듈성을 깨서는 절대로 안 된다. 객체를 생성하거나 의존성을 연결할 때도 마찬가지다. **설정 논리는 일반 실행 논리와 분리해야 모듈성이 높아진다**. 

#### - main  분리

시스템 생성과 시스템 사용을 분리하는 방법으로, 생성과 관련된 코드는 모두 main이나 main이 호출하는 모듈로 옮기고, 나머지 시스템은 모든 객체가 생성되었고 모든 의존성이 연결되었다고 가정한다.

흐름은 따라가기 쉽다. main에서 시스템에 필요한 객체를 생성한 후 이를 애플리케이션에 넘긴다. 애플리케이션은 main이나 객체가 생성되는 과정을 전혀 모른다. 단지 모든 객체가 적절히 생성되었다고 가정하고 그저 객체를 사용할 뿐이다.

#### - 팩토리

물론 때로는 객체가 생성되는 시점을 애플리케이션이 결정할 필요도 생긴다. 예를 들어, 주문 처리 시스템에서 애플리케이션은 LineItem 인스턴스를 생성해 Order에 추가한다. 이때는 *Abstract factory 패턴*을 사용한다. 그러면 객체를 생성하는 시점은 애플리케이션이 결정하지만 생성하는 코드는 애플리케이션이 모른다.

#### - 의존성 주입

사용과 제작을 분리하는 강력한 원리는 의존성 주입이다. 의존성 주입은 *제어 역전 IOC* 기법을 의존성 관리에 적용했다. 제어 역전에서는 한 객체가 맡은 보조 책임을 새로운 객체에 전적으로 떠넘긴다. 새로운 객체는 넘겨받은 책임만 맡으므로 단일 책임 원칙  SRP를 지키게 된다.

진정한 의존성 주입은 클래스가 의존성을 해결하려 시도하지 않는다. 클래스는 완전히 수동적이다. 대신에 의존성을 주입하는 방법으로 setter 메서드나 생성자 인수를 제공한다. 

<br>

### 확장

'처음부터 올바르게' 시스템을 만들 수 있다는 믿음은 미신이다 우리는 오늘 주어진 사용자 스토리에 맞춰 시스템을 구현해야 한다. 내일은 새로운 스토리에 맞춰 시스템을 조정하고 확장하면 된다. TDD, 리팩터링으로 얻어지는 개끗한 코드는 코드 수준에서 시스템을 조정하고 확장하기 쉽게 만든다.

소프트웨어 시스템은 수명이 짧다는 본질로 인해 아키텍쳐의 점진적인 발전이 가능하다. 

- 횡단 관심사

  모든 핵심관심사항에 **공통적으로 들어가는 코드**를 횡단 관심사라고 한다.

  하지만 횡단관심사도 프로그래밍 관점에서 보면 **중복이라는 문제가 존재**한다.이런 문제점을 해결하기 위해 AOP 기법이 나오게 되었다.

  영속성과 같은 관심사는 애플리케이션의 자연스러운 객체 경계를 넘나드는 경향이 있다. 모든 객체가 전반적으로 동일한 방식을 이용하게 만들어야 한다. 예를 들어 *특정 DBMS를 사용하고 테이블과 열은 같은 명명 관례*를 따라야 한다.

  *AOP는 횡단 관심사에 대처해 모듈성을 확보하는 일반적인 방법론*이다. AOP에서 관점이라는 모듈 구성 개념은 "특정 관심사를 지원하려면 시스템에서 특정 지점들이 동작하는 방식을 일관성 있게 바꿔야 한다"라고 명시한다. 명시는 간결한 선언이나 프로그래밍 매커니즘으로 수행한다.

  영속성을 예로 들면, 프로그래머는 영속적으로 저장할 객체와 속성을 선언한 후 영속성 책임을 영속성 프레임워크에 위임한다. 그러면 AOP 프레임워크는 대상 코드에 영향을 미치지 않는 상태로 동작 방식을 변경한다. 자바에서 사용하는 관점 혹은 관점과 유사한 메커니즘 세 개를 살펴보자. 
  
  1. 자바 프록시
  
     자바 프록시는 단순한 상황에 적합하다. 개별 객체나 클래스에서 메서드 호출을 감싸는 경우가 좋은 예다. 하지만 코드의 양과 크기는 프록시의 두 가지 단점이다. 프록시를 사용하면 깨끗한 코드를 작성하기 어렵다.
  
  2. 순수 자바 AOP 프레임워크
  
  3. AspectJ 관점
  
     마지막으로, 관심사를 관점에서 분리하는 가장 강력한 도구는 AspectJ 언어다. AspectJ는 언어 차원에서 관점을 모듈화 구성으로 지원하는 자바 언어 확장이다. AspectJ는 관점을 분리하는 강력하고 풍부한 도구 집합을 제공하지만, 새 도구를 사용하고 새 언어 문법과 사용법을 익혀야 한다는 단점이 있다.

 <br>

### 테스트 주도 시스템 아키텍처 구축 

관점으로 (혹은 유사한 개념으로) 관심사를 분리하는 방식은 그 위력이 막강하다. 코드 수준에서 아키텍처 관심사를 분리할 수 있다면, 진정한 테스트 주도 아키텍처 구축이 가능해진다. 그때그때 새로운 기술을 채택해 단순한 아키텍처를 복잡한 아키텍처로 키워갈 수 있다. BDUF를 추구할 필요가 없다. 실제로 BDUF는 해롭기까지 하다. **처음에 쏟아부은 노력을 버리지 않으려는 심리적 저항**으로 인해, 그리고 처음 선택한 아키텍처가 향후 사고 방식에 미치는 영향으로 인해 변경을 쉽사리 수용하지 못하는 탓이다.

> BDUF는 구현을 시작하기 전에 앞으로 벌어질 모든 사항을 설계하는 기법이다.

건축가는 BDUF 방식을 취한다. 물리적 구조는 일단 짓기 시작하면 극적인 변경이 불가능하기 때문이다. 소프트웨어 역시 나름대로 형체가 있지만 소프트웨어 구조가 관점을 효과적으로 분리한다면, 극적인 변화가 가능하다.

프로젝트를 시작할때는 일반적인 범위, 목표, 일정은 물론이고 결과로 내놓을 시스템의 일반적인 구조도 생각해야 한다. 하지만 **변하는 환경에 대처해 진로를 변경할 능력도 반드시 유지**해야 한다.

설계가 아주 멋진 api라도 정말 필요하지 못하면 과유불급이다. **좋은 API는 걸리적거리지 않아야 한다**. 그러지 않으면 아키텍처에 발이 묶여 고객에게 최적의 가치를 효율적으로 제공하지 못한다.

<BR>

### 의사 결정을 최적화하라

모듈을 나누고 관심사를 분리하면 부차적인 것들에 대한 관리와 결정이 가능해진다. 도시든 소프트웨어 프로젝트든, *아주 큰 시스템에서는 한 사람이 모든 결정을 내리기 어렵다*.

가장 적합한 사람에게 책임을 맡기면 가장 좋다. 우리는 때대로 가능한 마지막 순간까지 결정을 미루는 방법이 최선이라는 사실을 까먹곤 한다. 게으르거나 무책임해서가 아니다. **최대한 정보를 모아 최선의 결정을 내리기 위해서다**. 성급한 결정은 불충분한 지식으로 내린 결정이다. *너무 일찍 결정하면 고객 피드백을 더 모으고, 프로젝트를 고민하고, 구현 방안을 더 탐험할 기회가 사라진다/*.

<BR>

### 명백한 가치가 있을 때 표준을 현명하게 사용하라

가볍고 간단한 설계로 충분했을 프로젝트에서 표준이라는 이유만으로 기술을 채택하는 경우가 있다. 업계에서 여러 형태로 아주 과장되게 포장된 표준에 집착하는 바람에 고객 가치가 뒷전으로 밀려난 사례가 많이 있다는 것을 기억하자.

<BR>

### 시스템은 도메인 특화 언어가 필요하다

대다수 도메인과 마찬가지로 건축 분야도 필수적인 정보를 정확하게 전달하는 어휘, 관용구, 패턴 등이 풍부하다. 소프트웨어 분야에서도 최근 DSL이 조명 받기 시작했다.

> DSL이란?
>
> *Domain-Specific Language 도메인 특화 언어*
>
> DSL은 간단한 스크립트 언어나 표준 언어로 구현한 API를 가리킨다 DSL로 짠 코드는 도메인 전문가가 작성한 구조적인 산문처럼 읽힌다.

좋은 DSL은 도메인 개념과 그 개념을 구현한 코드 사이에 존재하는 의사소통 간극을 줄여준다. 

<BR>

### 결론

시스템 역시 개끗해야 한다. 깨끗하지 못한 아키텍처는 도메인 논리를 흐리며 기민성을 떨어뜨린다. 도메인 논리가 흐려지면 제품 품질이 떨어진다. 버그가 숨어들기 쉬워지고 스토리를 구현하기 어려워지는 탓이다. 기민성이 떨어지면 생산성이 낮아져 TDD가 제공하는 장점이 사라진다. 

시스템을 설계하든 개별 모듈을 설계하든, 실제로 돌아가는 가장 단순한 수단을 사용해야 한다는 사실을 명심하자.
