# Effective Java 3e

( 조슈아 블로크| 개앞맵시 옮김)

 이 페이지는 책에 대한 주관적인 해석과 요약이 되어있으니 참고하시기 바랍니다. 큰 목차는 모두 작성하였지만 세부 목차는 필요에 따라 뺀 경우도 있습니다. 책의 내용을 그대로 실었을 경우에는 인용 기호를 사용하였습니다. 그 이외는 주관적인 생각이 들어있을 수 있음을 알려드립니다. 좀 더 자세한 내용을 원하시는 분은 꼭 원책을 읽어보시길 추천드립니다.
 이 책은 각 분야별로 아이템으로 구성되어 있습니다. 하지만 아이템별로 독립성이 유지되기 때문에 꼭 순차적으로 읽을 필요가 없다고 명시되어 있습니다. 또한 각 아이템별로 다른 아이템들을 적절이 인용하게 되어있다고 설명되어 있습니다. 따라서 요약을 순차적으로 하겠지만 내용 중 인용된 아이템이 있다면 그 아이템 먼저 요약후 다시 순차적으로 진행할 것입니다. 다소 요약이 순차적으로 진행되지 않더라도 이해해주시기 바랍니다. 



## 목차

1. [들어가기](#1장-들어가기)

2. [객체 생성과 파괴](2장-객체-생성과 파괴)

   - [아이템 1. 생성자 대신 정적 팩터리 메서드를 고려하라](#아이템-1.-생성자-대신-정적-팩터리-메서드를-고려하라)
   - [아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라](#아이템-2.-생성자에-매개변수가-많다면-빌더를-고려하라)
   - [아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라](#아이템-3.-private-생성자나-열거-타입으로-싱글턴임을-보증하라)
   - [아이템 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라](#아이템-5.-자원을-직접-명시하지-말고-의존-객체-주입을-사용하라)
   - [아이템6. 불필요한 객체 생성을 피하라](#아이템-6.-불필요한-객체-생성을-피하라)
   - [아이템 7. 다 쓴 객체 참조를 해제하라](#아이템-7.-다-쓴-객체-참조를-해제하라)

3. [모든 객체의 공통 메서드]()

4. [클래스와 인터페이스](#4장-클래스와-인터페이스)

   - [아이템 17. 변경 가능성을 최소화하라](#아이템-17.-변경-가능성을-최소화하라)

5. [제네릭]()

6. [열거 타입과 애너테이션]()

7. [람다와 스트림]()

8. [메서드]()

9. [일반적인 프로그래밍 원칙]()

10. [예외]()

11. [동시성]()

12. [직렬화]()


## 들어가기

 기술 용어의는 대부분 자바 8용 언어 명세를 따른다. 자바가 지원하는 타입(자료형)은 **인터페이스**, **클래스**, **배열**, **기본 타입** 총 네가지이다. **애너테이션은** 인터페이스의 일종, **열거타입**은 클래스의 일종이다. 처음 세가지는 **참조 타입**이라고 한다. 즐 클래스의 인스턴스와 배열은 **객체**인 반면 기본 타입 값은 그렇지 않다. 클래스의 **멤버**로는 **필드**, **메서드**, **멤버 클래스**,**멤버 인터페이스**가 있다. **메서드 시그니처**는 메서드 이름과 입력 매개변수의 타입으로 이루어진다. 

 단 일부 용어는 자바 언어 명세와 다르게 한다. 이 책은 **상속**을 **서브클래싱**과 동의어로 쓴다. 인터페이스 상속 태신 "클래스가 인터페이스를 **구현한다**" 혹은 "인터페이스가 다른 인터페이스를 **확장한다**"고 표현한다. 아무것도 명시하지 않은 접근 수준을 이야기할 때는 **패키지 접근** 대신 **패키지-프라이빗**을 쓴다. 



## 2장 객체 생성과 파괴

- 객체를 만들어야 할 때와 만들지 말아야 할 때를 구분하는 법
- 올바른 객체 생성 방법, 불필요한 생성을 피하는 방법
- 제때 파괴됨을 보장하고 파괴 전에 수행해야 할 정리 작업



### 아이템 1. 생성자 대신 정적 팩터리 메서드를 고려하라





### 아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라

선택적 매개변수가 많을 때

- **점층적 생성자 패턴**을 사용할 수도 있지만, 매개변수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.

  ```java
  public class NutritionFacts{
      private final int servingSize;  // 필수
      private final int serving;  // 필수
      private final int calories; // 선택
      private final int fat; // 선택
      private final int sodium; // 선택
      private final int carbohydrate; // 선택
      
      public NutritionFacts(int servingSize, int servigs) {
          this(servingSize, serving, 0);
      }
      
      public NutritionFacts(int servingSize, int servings, int calories) {
          this(servingsize, serving, calories, 0);
      }
      
      public NutritionFacts(int servingSize, int servings, int calories,
                            int fat) {
          this(servingsize, serving, calories, fat, 0);
      }
      
      public NutritionFacts(int servingSize, int servings, int calories,
                           int fat, int sodium) {
          this(servingsize, serving, calories, fat, sodium, 0);
      }
      
      public NutritionFacts(int servingSize, int servings, int calories,
                           int fat, int sodium, int carbohydrate) {
          this.servingSize = servingSize;
          this.servings = servings;
          this.calories = calories;
          this.fat = fat;
          this.sodium = sodium;
          this.carbohydrate = carbohydrate;
      }
  }
  ```

  클라이언트 입장에서 매개변수가 여러개가된다면 헛갈릴 수 있다. 새로운 매개변수들이 추가되게 된다면 코드의 확장성도 좋지않다.

- **자바빈즈 패턴**를 사용하면 객체 하나를 만들려면 메서드를 여러개 호출해야 하고, 객체가 완전히 생성되기 전까지 일관성이 무너진 상태에 놓이게 된다.

  ```java
  public class NutritionFacts {
      private int servingSize = -1;  // 필수
      private int serving = -1;  // 필수
      private int calories = 0; // 선택
      private int fat = 0; // 선택
      private int sodium = 0; // 선택
      private int carbohydrate = 0; // 선택
      
      public NutritionFacts() {}
      
      //세터 메서드들
      public void setServingSize(int val) { servingSize = val};
      public void setServing(int val) { serving = val};
      public void setCalories(int val) { servingSize = val};
      public void setFat(int val) { calories = val};
      public void setSodium(int val) { fat = val};
      public void setCarbonhydrate(int val) { carbohydrate = val};
      
  }
  ```

  인스턴스를 만들기 쉽고, 코드 읽기가 쉬워졌다. 

  ```java
  NutritionFacts cocaCola = new NutritionFacts();
  cocaCola.setServingSize(240);
  cocaCola.setServing(8);
  cocaCola.setCalories(100);
  cocaCola.setSodium(35);
  cocaCola.setCarbohydrate(27);
  ```

  하지만 객체 하나를 만들려면 메서드를 여러 개 호출해야하고, 필수 요소은 servingSize와 serving을 꼭 설정하도록 강제할 수 없다. 일관성이 무너지는 문제 때문에 빈즈 패턴에서는 클래스를 불변([아이템17](#아이템-17.-변경-가능성을-최소화-하라))으로 만들 수 없다.

- 점층적 생성자 패턴의 안전성과 자바 빈즈 패턴의 가독성을 겸비한 **빌더 패턴**을 사용할 수 있다.

  -  클라이언트는 필요한 객체를 직접 마드는 대신,  필수 매개변수만으로 생성자를 호출해 빌더 객체를 얻는다. 그런다음 빌더 객체가 제공하는 일종의 세터 메서드들로 원하는 선택 매개변수들을 설정한다. 마지막으로 매개변수가 없는 **build**메서드를 호출해 필요한 객체를 얻게 된다.

  ```java
  public class NutiritionFacts {
      private final int servingSize;  // 필수
      private final int serving;  // 필수
      private final int calories; // 선택
      private final int fat; // 선택
      private final int sodium; // 선택
      private final int carbohydrate; // 선택
      
      public static class Builder {
          // 필수 매개변수
          private final int servingSize;
          private final int servings;
      
          // 선택 매개변수 - 기본값으로 초기화 한다.
          private int calories = 0;
          private int fat = 0;
          private int sodium = 0;
          private carbohydrate = 0;
          
          public Builder(int servingSize, int servings){
              this.servingSize = servingSize;
              this.servings = servings;
          }
          
          public Builder calories(int val){
              calories = val;
              return this; // 자기자신을 리턴하여 메소드를 연속적으로 사용할 수 있도록 한다.
          }
         
          public Builder fat(int val){
              fat = val;
              retur this;
          }
          public Builder sodium(int val){
              sodium = val;
              retur this;
          }
          public Builder carbohydrate(int val){
              carbohydrate = val;
              retur this;
          }
          
          public NutritionFacts build() {
              return new NutritionFacts(this);
          }
      }
          
  	private NutiritionFacts(Builder builder){
  		servingSize = builder.servingSize;
          servings = builder.servings;
          calories = builder.calories;
          fat = builder.fat;
          sodium = builder.sodium;
          carbohydrate = builder.carbohydrate;
  	}
      
  }
  ```

  ```java
  NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
      .calories(100).sodium(35).carbohydrate(27).build();
  ```





### 아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

* 싱글턴 : 인스턴스를 오직 하나만 생성할 수 있는 클래스

* 싱글턴을 만드는 방식은 보통 두개지가 있으면 둘 방식 모두 생성자를 private로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련해 두는 것이다.

  ```java
  public class Elvis {
      public static inal Elvis INSTANCE = new Elvis();
      private Elvis() { ... }
      
      public void leaveTheBuilding() { ... }
  }
  ```

  생성자는 private으로 접근이 제어되면 Elvis는 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.



  ```jvav
  public class Elvis {
      private static final Elvis INSTANCE = Elvis();
      private Elvis() { ... }
      public satic Elvis getInstance() { return INSTANCE; }
      
      public void leaveTheBuilding() { ... }
  }
  ```





### 아이템 4. 인스턴스화를 막으려거든 private 생성자를 사용하라





### 아이템 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

>  클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 이 자원들을 클래스가 직접 만들게 해서도 안된다. 대신 필요한 자원을 (혹은 그 자원을 만들어주는 팩터리를)생성자에 (혹은 정적 팩터리나 빌더에) 넘겨주자. 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해준다.



* spell을 체크하는 메소드에는 사전이라는 자원이 필요하다. 

  * 정적 유틸리티를 잘못 사용한 예:

    ```java
    public class SpellChecker {
        private static final Lexicon dictionary = .... ; // 필요한 자원을 정적으로
        
        private SpellChecker(); // 객체 생성방지
        
        public static boolean isValid(String word) {...}
        public static List<String> suggestions(String typo) {...}
       
    }
    ```

  * 싱글턴을 잘못 사용한 예

    ```java
    public class SpellChecker {
        private final Lexicon dictionary = .... ;
        
        private SpellChecker(); // 객체 생성방지
        public static SpellChecker INSTANCE = new SpellChecker();
        
        public static boolean isValid(String word) {...}
        public static List<String> suggestions(String typo) {...}
       
    }
    
    ```

  * 사전 특성상 언어에 따라 여러 사전이 필요하다고 가정한다면 위의 방법은 올바르지 않다.

  * 단순히 dictionary에 final을 제거하고 변경가능한 메서드를 추가한다 하더라도,  오류를 내기 쉽고 멀티스레드 환경에서 쓸 수 없다. 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.

  * 클래스가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원을 사용해야 한다. 

* 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식을 사용

  ```java
  public class SpellChecker {
      private final Lexicon dictionary;
      
      public SpellChecker(Lexicon dictionary) {
          this.dictionary = Objects.requireNonNull(dictionary); // null 체크함수
      }
      
      public boolean isValid(String word) { ... };
      public List<String> suggestions(String typo) { ... };
  }
  ```

  * requireNonNull() 매개변수가 null이 아니면 obj 객체를 반환 null일 경우 NullPointException을.. 





### 아이템6. 불필요한 객체 생성을 피하라

> 똑같은 기능의 객체를 매번 생성하기 보다는 객체 하나를 재사용하는 편이 더 낫다.



* `String s = new String("theodore");`는 매번 새로운 인스턴스를 생성한다. 따라서 `String s = "theodore"`를 사용하는 것이 더욱 효율적이다.

* boxer클래스의 경우 생성자를 사용하여 새로운 인스턴스를 사용하는 것보다(new Boolean(String);) 팩터리 메서드를 사용하는 것이 좋다.(Boolean.valueOf(String);)

* 생성 비용이 아주 비싼 객체는 캐싱을 사용하자

  ```java
  static boolean isRomanNumberal(String s){
      return s.matches("^(?=.)M*(C[MD]|D?C{0,3})"
                       + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
  }
  ```

  String.matches는 정규표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이다. 하지만 내부적으로 인스턴스 비용이큰 Pattern.mathche()함수를 사용하기 때문에 반복에 사용하면 좋지 않다. 성능을 개선하려면 필요한 정규표현식을 표현하는 Pattern 인스턴스를 클래스 초기화 과정에서 직접 생성해 캐싱해도구, 나중에 isRomanNumeral 메서드가 호출될 때마다 이 인스턴스를 재사용 할 수 있도록 한다.

  ```java
  public class RomanNumerals {
      private static final Pattern ROMAN = Pattern.compile(
      				"^(?=.)M*(C[MD]|D?C{0,3})"
                       + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
      
      static boolean isRomanNumberal(String s){
          return ROMAN.matcher(s).matcches();
      }
  }
  ```

* 박싱된 기본 타입보다 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의

* 객체 생성은 비싸니 무조건 지양해야 한다는 것이 아니라 적절히 사용해야 하는 것이다.  필요한경우 객체를 생성하는 것이 좋은 경우도 있다.





### 아이템 7. 다 쓴 객체 참조를 해제하라

> 메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있다. 이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다. 그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요하다.



```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITIY = 16;
    
    public Stack(){
        elements = new Object[DEFAULT_INITIAL_CAPACITIY];
    }
    
    public void push(Object e) {
        ensureCapacitiy();
        elements[size++] = e;
    }
    
    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        return elements[--size];
    }
    
    private void ensurCapacitiy(){
        if(elements.length == size)
            elements = Arrays.copyOf(elements, 2*size + 1);
    }
}
```

 특별한 문제가 없어보이지만 이 스택을 사용하는 프로그램을 오래 실행하다 보면 가비지 컬렉션 활동과 메모리 사용량이 늘어라 성능이 저하될 것이다. pop() 메소드에서 더이상 사용되지 않은 객체들이 아직 남아 있기 때문이다.  가비지 컬렉션이 활성영역(size이상에 포함되는 영역)에 벗어나는 영역에 대해서 의미상 더이상 필요없는 개체라고 생각하지 못한다. 

 해법은 pop() 메소드 과정에서 해당 참조를 다 썼을 때 null 처리를 해준다.

```java
public Object pop() {
    if (size == 0)
        throw new EmptyStackException();
    Object resutl = elements[--size];
    elements[size] = null;
    return result;
}
```





## 4장. 클래스와 인터페이스

### 아이템17. 변경 가능성을 최소화 하라

