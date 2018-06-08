# 자바 8 람다와 인터페이스 스펙 변화

자바 8은 언어적으로 많은 변화를 맞이했다. 특히 함수형 프로그래밍 지원을 위한 람다(Lambda)의 도입이 두드러진다.
<hr/>

### 람다가 도입된 이유
근래에 가장 핫한 용어 가운데 빅데이터가 있다. 기업들은 빅데이터의 분석 및 활용을 통해 기업 전략을 수립하고, 수익을 극대화하고자 한다. 이런 빅데이터
분석은 당연히 ICT 기술을 통해 이뤄질 수밖에 없다. 따라서 프로그래머들에게 빅데이터를 프로그램적으로 다룰 수 있는 방법이 필요해졌다. <br/>
> 그 방법의 중심에는 다시 멀티 코어를 활용한 분산 처리, 즉 병렬화 기술이 필요하다.

하나의 CPU안에 다수의 코어를 삽입하는 멀티 코어 프로세서들이 등장하면서 일반 프로그래머에게도 병렬화 프로그래밍에 대한 필요성이 생기기 시작했다.
이러한 추세에 대응하기 위해 자바 8에서는 병렬화를 위해 컬렉션(배열, List, Set, Map)을 강화했고, 이러한 컬렉션을 더 효율적으로 사용하기 위해 스트림을
강화했다. 또 스트림을 효율적으로 사용하기 위해 함수형 프로그래밍이, 다시 함수형 프로그래밍을 위해 람다가, 또 람다를 위해 인터페이스의 변화가 수반됐다.
람다를 지원하기 위한 인터페이스를 함수형 인터페이스라고한다.
> 빅데이터 지원 -> 병렬화 강화 -> 컬렉션 강화 -> 스트림 강화 -> 람다 도입 -> 인터페이스 명세 변경 -> 함수형 인터페이스 

### 람다란 무엇인가?
람다란 한 마디로 코드 블록이다. 기존의 코드 블록은 반드시 메서드 내에 존재해야 했다. 그래서 코드 블록만 갖고 싶어도 기존에는 코드 블록을 위해 메서드를,
 다시 메서드를 사용하기 위해 익명 객체를 만들거나 하는 식이었다. 하지만 자바 8부터는 코드 블록을 만들기 위해 이러한 수고를 할 필요가 없다.
 또한, 코드 블록인 람다를 메서드의 인자나 반환값으로 사용할 수 있게 됐다. 이것의 의미는 코드 블록을 변수처럼 사용할 수 있다는 것이다.
 ```
 public class B001 {
  public static void main(String[] args) {
    MyTest mt = new MyTest();
    
    Runnable r = mt;
    
    r.run();
  }
 }
 
 class MyTest implements Runnable {
  public void run() {
    System.out.pringln("Hello Lambda!!!");
  }
 }
 ```
 > 기존 방식의 코드 블록 사용 - 별도의 클래스와 객체 그리고 메서드를 생성해야 한다.
 ```
 public class B002 {
  public static void main(Sting[] args) {
    Runnable r = new Runnable() {
      public void run() {
        System.out.println("Hello Lambda 2!!!");
      }
    };
    
    r.run();
  }
 }
 ```
 > 기존 방식의 코드 블록 사용 - 익명 객체 생성
 ```
 public class B003 {
  public static void main(String[] args) {
    Runnable r = () -> {
      System.out.println("Hello Lambda 3!!!");
    };
    
    r.run();
  }
 }
 ```
 > 새로운 방식의 코드 블록 사용 - 람다
 
 Runnable 타입으로 참조 변수 r을 만들고 있으니 new Runnable()은 컴파일러가 알아낼 수 있다.<br/>
 public void run() 메서드가 단순하게 ()로 변경될 수 있는 이유 역시 간단하다. Runnable 인터페이스가 가진 추상 메서드가 run() 메서드 단 하나이기 때문이다.<br/>
 마지막으로 추가된 부분은 화살표 기호인 ->다. 이는 람다의 구조가 다음과 같기 때문이다.<br/>
 > (인자 목록) -> {로직}<br/>
 
 람다에서는 로직이 단 한 줄로 표기되는 경우 블록 기호 {}마저 생략할 수 있다.
 ```
 public class B004 {
  public static void main(String[] args) {
    Runnable r = () -> System.out.println("Hello Lambda 4!!!");
    
    r.run();
  }
 }
 ```
 > 코드 블록이 한 줄인 경우 블록 기호 생략 가능
 
 ### 함수형 인터페이스
 Runnable 인터페이스는 run()이라는 추상 메서드 하나만 가진다.<br/>
 이처럼 **메서드를 하나만 갖는 인터페이스를 자바 8부터는 함수형 인터페이스라고한다.**<br/>
 이런 함수형 인터페이스만을 람다식으로 변경할 수 있다.
 ```
 public class B005 {
  public static void main(String[] args) {
    MyFunctionalInterface mfi = (int a) -> { return a*a; };
    
    int b = mfi.runSomething(5);
    
    System.out.println(b);
  }
 }
 
 @FunctionalInterface
 interface MyFunctionalInterface {
  public abstract int runSomething(int count);
 }
 ```
인터페이스인 MyFunctionalInterface 위에 @FunctionalInterface 어노테이션을 붙이는 것은 옵션이다. 이 어노테이션이 붙은 경우 컴파일러는 인터페이스가
함수형 인터페이스의 조건에 맞는지 검사한다. 즉, 단 하나의 추상 메서드만을 갖고 있는지 확인한다.

###### 더 간소화 하기
a 가 int일 수 밖에 없음을 runSomething메서드 정의에서 알 수 있다. 따라서 int를 생략할 수 있다. 이를 타입 추정 기능이라 한다.
```
(int a) -> { return a * a; }
```
```
(a) -> { return a * a; }
```
람다 설계자들은 이처럼 인자가 하나이고 자료형을 포기하지 않는 경우 소괄호를 생략해도 되게끔 만들어뒀다.<br/>
코드가 단 한 줄인 경우 중괄호도 생략할 수 있다. 다만 이때 return구문도 생략해야 한다.
```
a -> a * a;
```

### 메서드 호출 인자로 람다 사용
람다식을 메서드의 인자로도 사용할 수 있다.
```
public class B007 {
 public static void main(String[] args) {
  MyFunctionalInterface mfi = a -> a * a;
  
  doIt(mfi);
 }
 
 public static void doIt(MyFunctionalInterface mfi) {
  int b = mfi.runSomething(5);
  
  System.out.println(b);
 }
}
```
> 메서드 인자로 람다식(함수형 인터페이스) 참조 변수 사용<br/>
람다식을 단 한번만 사용한다면 굳이 변수에 할당할 필요도 없다.
```
public class B007 {
 public static void main(String[] args) {
  doIt(a -> a * a);
 }
 
 public static void doIt(MyFunctionalInterface mfi) {
  int b = mfi.runSomething(5);
  
  System.out.println(b);
 }
}
```
> 메서드 인자로 람다식(함수형 인터페이스) 사용

### 메서드 반환값으로 람다 사용
```
public class B007 {
 public static void main(String[] args) {
  MyFunctionalInterface mfi = todo();
  
  int result = mfi.runSomething(3);
  
  System.out.println(result);
 }
 
 public static MyFunctionalInterface todo() {
  return num -> num * num;
 }
}
```
> 메서드 반환값으로 람다식(함수형 인터페이스) 사용

### 자바 8 API에서 제공하는 함수형 인터페이스
개발자들이 많이 쓸 것이라고 예상되는 함수형 인터페이스를 이미 java.util.function 패키지와 여러 패키지에서 제공하고 있다.

함수형 인터페이스 | 추상 메서드 | 용도
:----------------:|:------------:|:------:
Runnable | void run() | **실행**할 수 있는 인터페이스
Supplier<T> | T get() | **제공**할 수 있는 인터페이스
Consumer<T> | void accept(T t) | **소비**할 수 있는 인터페이스
Function<T, R> | R apply(T t) | **입력을 받아서 출력**할 수 있는 인터페이스
Predicate<T> | Boolean test(T t) | 입력을 받아 **참/거짓을 단정**할 수 있는 인터페이스
UnaryOperator<T> | T apply(T t) | **단항(Unary) 연산**할 수 있는 인터페이스
BiConsumer<T, U> | void accept(T t, U u) | 이항 소비자 인터페이스
BiFunction<T, U, R> | R apply(T t, U u) | 이항 함수 인터페이스
BiPredicate<T, U> | Boolean test(T t, U u) | 이항 단정 인터페이스
BinaryOperator<T, T> | T apply(T t, T t) | 이항 연산 인터페이스

> java.util.function 패키지에는 총 43개의 함수형 인터페이스를 제공한다.<br/> 만약 43개 중에서 필요로 하는 함수형 인터페이스가 없다면 사용자 정의
함수형 인터페이스를 정의해서 사용하면 된다.

### 컬렉션 스트림에서 람다 사용
```
public class B011 {
 public static void main (String[] args) {
  Integer[] args = { 20, 25, 18, 27, 30, 21, 17, 19, 34, 28 };
  
  for(int i=0; i < ages.length; i++) {
   if(ages[i] < 20) {
    System.out.format("Age %d!!! Can't enter\n", ages[i]);
   }
  }
 }
}
```
> 반복문을 이용해 루프를 제어하는 코드다. 문제는 없지만 가끔 프로그래머들은 <를 써야 할 곳에 <=를 사용한다거나, i 변수의 초깃값을 0이 아닌 1로 초기화하는 실수를 하면서 문제가 생기기도 한다.

```
public class B012 {
 public static void main(String[] args) {
  Integer[] ages = { 20, 25, 18, 27, 30, 21, 17, 19, 34, 28 };
  
  for (int age : ages) {
   if(age < 20) {
    System.out.format("Age %d!!! Can't enter\n", age);
   }
  }
 }
}
```
> for each 구문을 사용해 배열 첨자를 사용하지 않는 코드로 변경했다.
```
import java.util.Arrays;

public class B013 {
 public static void main(String[] args) {
  Integer[] ages = { 20, 25, 18, 27, 30, 21, 17, 19, 34, 28 };
  
  Arrays.stream(ages)
        .filter(age -> age <20)
        .forEach(age -> System.out.format("Age %d!!! Can't enter\n", age));
 }
}
```
> 컬렉션 스트림 이용<br/>
* 기존 배열을 이용해 스트림을 얻으려면 Arrays 클래스의 stream()정적 메서드를 사용하면 된다.
* filter 메서드는 SQL 구문에서 where 절과 같은 역할을 수행한다. where과 마찬가지로 true/false를 반환하는 조건이 필요하다. 함수형 인터페이스 중에서 true/false를 단정 짓는 Predicate 함수형 인터페이스를 filter 메서드의 인자로 제공하면 된다.
* 스트림 내부 반복을 실행하는 forEach 메서드를 사용했다. forEach 구문은 전달된 인자를 소비하는 함수형 인터페이스, 즉 Consumer를 요구한다.

> 가장 좋아진 점은 How가 아닌 What만을 지정했다는 것이다. 함수형 프로그래밍의 장점인 선언적 프로그래밍을 활용하는 것이다. 이는 SQL 구문과 유사하다. 
SQL을 작성할 때 '어떻게 하라'를 명령하는 것이 아니고 '무엇을 원한다'라고 선언하는 것과 같다. 또한 스트림은 메서드 체인 패턴을 이용해 최종 연산이 아닌 모든 중간 연산은 다시 스트림을 반환해 코드를 간략하게 작성할 수 있게 지원한다.
* 20세 미만인 경우를 선별(filter)해 주세요.
* 선별된 각 요소에 대해 입장이 불가하다고 해주세요.
> 스트림은 고객의 요구를 선언적으로 코딩할 수 있는 힘을 준다. 고객도 해당 코드가 무슨 일을 하는지 파악하는데 크게 문제가 없다. 이렇게 의사소통 내용 자체가 그대로 코드로 구현되는 것이 선언적 프로그래밍의 장점이다.

> 스트림은 컬렉션 스트림을 다른 컬렌션 스트림으로 변환하는 map, 집계 함수인 sum, count, average, min, max뿐만 아니라 특정 기준에 의한 그룹화(grouping) 지원 등 많은 메서드를 제공해준다.

### 메서드 레퍼런스와 생성자 레퍼런스
```
Arrays.stream(ages).sorted().forEach(System.out::println);
```
이 코드를 람다식으로 표현하면 아래와 같다.
```
Arrays.stream(ages).sorted().forEach(age -> System.out.println(age));
```
위 람다식은 인자를 아무런 가공 없이 그대로 출력한다. 이런 코드를 사용할 때 메서드 레퍼런스라고 하는 간략한 형식을 쓸 수 있다.
* 인스턴스::인스턴스메서드
* 클래스::정적메서드
* 클래스::인스턴스메서드

예시
```
Arrays.stream(nums).map(num -> Math.sqrt(num)).forEach(sqrtNum -> System.out.println(sqrtNum));
BiFunction<Integer, Integer, Integer> bip_lambda = (a, b) -> a.compareTo(b);
```
아래와 같다.
```
Arrays.stream(nums).map(Math::sqrt).forEach(System.out::println);
BiFunction<Integer, Integer, Integer> bip_lambda = Integer::compareTo;
```
* 클래스::정적 메서드
 * num -> Math.sqrt(num)
 * Math::sqrt
* 인스턴스::인스턴스메서드
 * sqrtNum -> System.out.println(sqrtNum)
 * System.out::println
* 클래스::인스턴스메서드
 * (a, b) -> a.compareTo(b)
 * Integer::compartTo
 
메서드 레퍼런스 유형 | 람다식의 인자
:--------------------:|:-----------:
클래스::정적메서드 | 정적 메서드의 인자가 된다
인스턴스::인스턴스메서드 | 인스턴스 메서드의 인자가 된다
클래스::인스턴스메서드 | 첫 번째 인자는 인스턴스가 되고 그다음 인자(들)는 인스턴스 메서드의 인자(들)가 된다.

생성자 레퍼런스
> 클래스::new
```
클래스 인스턴스 = 클래스::new;
```
위는 에러다. 생성자 레퍼런스로 생성한 것은 클래스의 객체가 아니라 함수형 인터페이스 구현 객체이기 때문이다. 생성자가 기본 생성자라면 이를 만족하는 Supplier함수형 인터페이스를 사용해 생성자 자체에 대한 참조가 만들어진다.
```
Supplier<클래스> 인스턴스 = 클래스::new;
```
위와 같이 대체할 수 있다. 이는 아래와 같다
```
Supplier<클래스> 인스턴스 = () -> 클래스();
```
> 기본 생성자 외의 다른 생성자가 있는 경우라면 그에 맞는 함수형 인터페이스 참조 변수를 사용해야 한다.

### 인터페이스의 디폴트 메서드와 정적 메서드
자바 8 이전 인터페이스가 가질 수 있는 멤버
* 정적 상수
* 추상 인스턴스 메서드
자바 8 이후 인터페이스가 가질 수 있는 멤버
* 정적 상수
* 추상 인스턴스 메서드
* 구체 인스턴스 메서드 - 디폴트 메서드
* (구체) 정적 메서드
> 이제 인터페이스도 구체 인스턴스 메서드, 즉 몸체를 가진 인스턴스 메서드를 가질 수 있게 됐다. 이를 디폴트 메서드라 하고 default 키워드를 메서드 정의에 사용한다. 또한 (구체) 정적 메서드를 가질 수 있게 됐고 static 키워드를 메서드 정의에 사용하면 된다.

자바 8에서 언어적으로 가장 큰 변화를 꼽으라 한다면 바로 인터페이스의 스펙 변화를 꼽을 수 있다. 이로 인해 람다가 가능해졌고, 연쇄적으로 더 강화된 컬렉션 API를 사용할 수 있게 됐을 뿐만 아니라 함수형 프로그래밍이 가능해졌기 때문이다.

컬렉션 API를 강화하면서 컬렉션의 공통 조상인 Collection의 슈퍼 인터페이스인 Iterable 인터페이스에는 많은 변화가 필요했다. 한 예로 내부 반복을 가능하게 하는 forEach의 도입이 있다. 그런데 인터페이스에 변화를 주게 되면, 즉 새로운 추상 인스턴스 메서드를 추가하게 되면 기존에 해당 인터페이스를 구현한 모든 사용자 정의 클래스는 이를 추가적으로 구현해야만 한다. 이는 자바 6이나 7로 만들어진 많은 프로그램들이 자바 8 기반 JVM에서 구동되지 않는 문제를 발생시킨다. 이에 따라 자바 8 API 설계자들은 이전 JDK를 기반으로 작성된 프로그램도 자바 8 JVM에서 구동될 수 있게 디폴트 메서드라고 하는 새로운 개념을 인터페이스 스펙에 추가한 것이다. 디폴트 메서드와 정적 메서드의 도입으로 기존에 작성한 프로그램들도 아무런 사이드 이펙트(부작용) 없이 자바 8 JVM에서 구동된다. 그리고 향후 만들어지는 프로그램들도 인터페이스 스펙에 추가된 디폴트 메서드와 정적 메서드로부터 많은 혜택을 받을 수 있을 것으로 기대된다.

### 정리
람다 = 변수에 저장 가능한 로직<br/>
변수는 지역 변수, 속성, 메서드의 인자, 메서드의 반환값으로 사용할 수 있다. 이것은 곧 람다도 지역 변수, 속성, 메서드의 인자, 메서드의 반환값으로 사용할 수 있다는 것을 의미한다.

빅데이터 지원을 위해 병렬화, 병렬화 지원을 위해 스트림을 사용한다. 이때 사용하는 것은 parallelStream()이다. 병렬 스트림을 지원하는 컬렉션에서 stream() 메서드 대신 parallelStream()을 사용하면 스트림 작업을 병렬로 처리할 수 있다.


