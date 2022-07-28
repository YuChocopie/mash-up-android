---
title: "확장 함수, 람다 함수, 고차 함수의 기초"
date: "2021-10-31"
tags: ["mash-up", "확장 함수","람다 함수","고차 함수"] 
description: "확장 함수, 람다 함수, 고차 함수에 대해서 알아봅니다."
cover: "./images/tumb_nail.png"
---

Kotlin에서는 확장함수, 람다 식, 고차함수를 적절하게 사용하면 코드가 깔끔해지고, 공통 코드 구조를 뽑아낼 수 있습니다. 
# 확장 함수의 기본
확장 함수는 간단합니다! 확장 함수는 어떤 클래스의 멤버 메소드인 것처럼, 즉 원래 클래스 안에 선언되어 있는 것처럼 호출이 가능하지만 사실은 그 클래스 밖에 선언된 함수입니다. 

아주 쉬운 예제로 확인을 해보면, 한 문자열의 가장 마지막 문자를 출력해보는 예제입니다.
``` fun String.lastChar(): Char = this.get(this.length - 1)```
확장 함수는 일반 함수의 선언처럼 ```fun``` 키워드로 시작합니다. 이후에 확장할 클래스 이름( 이를 수신 객체 타입이라고 한다 )을 적고 점(.) 뒤에 함수의 이름을 적어주고, 함수 답게! 괄호 ()를 넣어주고, 마지막 리턴타입을 작성해줍니다. 

뒤에는 꼭 =이 아니여도 된다! 중괄호 ({ })로 함수의 몸체를 적어주어도 됩니다. 
여기서 this는 수신 객체로 확장 함수가 호출되는 대상이 되는 값을 뜻합니다.
즉 이 확장함수를 통해 ```"abcde".lastChar()```이라는 코드를 실행하면, "abcde"가 수신 객체가 됩니다! 

위의 내용을 수행하면 ```"abcde"```의 ```length -1 ``` , 즉 마지막 인덱스를 ```get```하여 마지막 단어를 리턴해줍니다. 

이처럼 확장 함수를 사용하면 String 클래스에 없는 lastChar() 함수를 마치 String 클래스 안에 있는 것 처럼 사용이 가능합니다! 

또한 일반 함수 처럼 수신 객체 멤버에 this 없이 접근도 가능합니다!
```fun String.lastChar(): Char = get(length - 1)```

이를 통해 다양한 함수를 만들어 보는 것도 재밌을 것입니다! 아주 간단한 예제를 통해 어떤 함수인지 유추해보시길 바랍니다! (물론 대부분 함수 이름을 보면 유추가 가능합니다.)
```kotlin
1.
fun Iterable<Int>.addAll(): Int {
    var result = 0
    this.forEach {
        result += it
    }
    return result
}

val listData = listOf(1, 2, 3, 4, 5)
println(listData.addAll())

2. 
// import문을 통해 함수를 import 할 수 있고, as를 통해 원하는 이름으로 변경 또한 가능합니다!
import strings.splitData as SD

fun String.splitData(): String {
    val text = this.split(" ")
    var result = ""
    for ((index, data) in text.withIndex()) {
        if (index > 0) {
            result += "$data "
        }
    }
    return result
}

val data = "Spring Summer Winter Fall are you still"
println(data.SD())   

3.
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }
    result.append(postfix)
    return result.toString()
}

val list = listOf(1, 2, 3)
println(list.joinToString(separator = " "))
```

# 람다의 기본
람다는 기본적으로 다른 함수에 넘길 수 있는 "작은 코드 조각"을 뜻합다.
이전 Java에서는 SAM방식의 인터페이스를 무명 내부 클래스를 사용하여 구현했었는데, 단 하나의 함수를 구현하는데도 꽤 길고 복잡한 코드가 필요했습니다. SAM방식의 대표인 onClickListener의 예로 보면,
```java
button.setOnClickListener(new OnClickListener(){
    @Override
    public void onClick(View v){
    	  System.out.println("너무 길다")
        }
    });
```
간단한 ClickListener지만 꽤 길지만, 코틀린에서는 더 간결하게 가능합니다! 
```kotlin
button.setOnClickListener{ 
     println("너무 짧다")
  }
```
### 람다의 기본 문법
이처럼 람다 식은 항상 중괄호({ }) 안에 존재합니다! 또한 위의 Java와는 다르게 중괄호 앞 뒤에 괄호( )가 존재하지 않습니다! 그리고 위의 예제에서는 확인이 불가하지만, 화살표 (->)를 이용해 인자와 람다의 본문을 구분해줍니다. 인자는 타입과 함께 화살표의 앞에 적어주며 한개 이상의 인자가 있을 경우 ,를 이용해 구분해줍니다. 아주 쉬운 예제를 통해 람다를 확인해봅니다!
```kotlin
val sum = { x: Int, y: Int -> x + y }
println(sum(1,2))
 => 3
```
이처럼 람다 식을 변수에 할당해 줄 수도 있습니다.
또한 람다 식은 본문이 여러줄인 경우 가장 마지막 줄이 람다의 결과 값이 됩니다.
```kotlin
val sum = { x: Int, y: Int ->
	print(" x와 y의 합은: ")
    	x + y
    }
println(sum(1,2))
-> x와 y의 합은: 3
```

또한 람다식을 더 짧게 만들어주는 기능을 컬렉션에 있는 maxBy 함수를 통해 예시로 보겠습니다.
```kotlin
val people = listOf(Person("KeiG", 29), Person("Coco", 21))
println(people.maxBy({ p: Person -> p.age })
```
위에 나온 ```maxBy({ p: Person -> p.age}) ```는 더 짧게 표현이 가능합니다.

코틀린에서 함수 호출 시에 람다 식이 인자의 가장 마지막에 있다면 람다 식을 괄호 밖으로 꺼낼 수 있습니다! ```maxBy() { p: Person -> p.age}```와 같이 말이죠.

그리고, 위의 코드처럼 함수의 유일한 인자가 람다식이며, 괄호 뒤에 썼다면 괄호를 없애주어도 됩니다. 즉 ```maxBy { p: Person -> p.age }``` 가 됩니다.

그리고 ```people.maxBy```에서 ```people```을 통해 타입을 컴파일러가 추론할 수 있기 때문에 
```maxBy{ p -> p.age }```로 축약이 가능하며, 마지막으로 ```it```을 통해 ```maxBy{ it.age }```로 표현이 가능합니다.

길었던 람다 식이 코틀린에서는 ```people.maxBy { it.age }``` 와 같은 짧은 코드로 동일한 결과를 낼 수 있게 됩니다!
### 멤버 참조
이렇게 다른 함수(maxBy) 에 코드 블록을 인자로 넘기는 방법을 알아봤습니다. 그런데 이미 그 코드가 함수로 선언되어 있다면 어떻게 할까요? 그 함수를 호출하는 람다를 만들면 되지만, 이는 중복입니다. 그런 경우 함수를 직접 넘기면 되는데, 코틀린에서는 함수를 값으로 바꿀 수 있습니다. 이때 이중 콜론을 사용합니다. 

::를 사용하는 경우를 멤버 참조라고 부릅니다. 멤버 참조는 프로퍼티 또는 메소드를 단 하나만 호출하는 함수 값을 만들어 줍니다.
```kotlin
val getAge = Person::age // Person 클래스의 age를 호출
```


또한 이러한 참조는 멤버 뿐 아니라 최상위에 선언 된 함수를 참조할 수도 있습니다! (이는 뒤에 고차함수에서도 사용되니 꼭 알아두어야 합니다!)
```
fun sayHello() = println("Hello Hello")
run(::sayHello)
-> Hello Hello
```
이 처럼 :: 를 이용해 함수 참조 또한 가능합니다! run은 인자로 받은 람다를 호출해줍니다.

### 함수형 프로그래밍과 람다
이러한 람다 식을 이용한 프로그래밍은 함수형 프로그래밍에서 컬렉션을 다룰 때 매우 편리함을 줍니다! 
그 중 기본이 되는 몇 가지 함수를 보겠습니다.
#### filter
```kotlin
val list = listOf(1,2,3,4)
println(list.filter{ it % 2 == 0 } => 짝수만 출력
[2, 4]
```
이 filter 함수는 컬렉션을 이터레이션하면서 주어진 람다에 각 원소를 넘겨 람다가 true를 반환하는 원소만 모읍니다. 그리고 그 결과는 안의 조건을 만족하는 원소로만 이루어진 새로운 컬렉션을 반환해줍니다.
이처럼 filter 함수를 통해 원하지 않는 원소는 제거, 원하는 원소 값만 뽑아낼 수 있습니다.
하지만 새로운 값을 만들어내기 위해 원소를 "변환" 하고싶다면

#### map
map 함수를 이용합니다. map 함수는 주어진 람다를 컬렉션의 각 원소에 적용한 결과를 모아서 새 컬렉션을 만듭니다. 다음과 같이 하면 숫자로 이뤄진 리스트를 각 숫자의 제곱이 모인 리스트로 바꿀 수 있습니다.
```kotlin
val list = listOf(1,2,3,4)
println(list.map{ it * it } )
[1, 4, 9, 16]
```
이 처럼 변환된 새로운 컬렉션을 필요로 한다면 map을 사용합니다. 
또한 key와 value를 저장하는 Map에도 filter와 map을 적용할 수 있습니다!( 이 부분은 찾아보길 권장드립니다!!! )

#### all
이 함수는 컬렉션이 모든 원소가 어떤 조건을 모두 만족하는지 판단하는 연산자입니다!
나이가 27 이하인지 판단하는 람다 변수를 선언한 뒤 모두 만족하는지 all을 통해 확인해보도록 하겠습니다.
```kotlin
val canBeInClub27 = { p: Person -> p.age <= 27 }
val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.all(canBeInClub27))
=> false
```
#### any
any는 하나라도 만족한다면 true를 반환합니다!
```kotlin
val canBeInClub27 = { p: Person -> p.age <= 27 }
val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.any(canBeInClub27))
=> true
```
#### count 
count는 술어(조건식)를 만족하는 원소의 개수를 구합니다!
```kotlin
val canBeInClub27 = { p: Person -> p.age <= 27 }
val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.count(canBeInClub27))
=> 1
```
#### find
find는 술어를 만족하는 원소를 하나 찾아(가장 앞의) 반환하고, 없을 경우 null을 반환합니다.
```kotlin
val canBeInClub27 = { p: Person -> p.age <= 27 }
val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.find(canBeInClub27))
=> Person(name=Alice, age=27)
```
#### groupBy
groupBy는 리스트를 여러 그룹으로 이루어진 맵으로 변경해주는 함수입니다. 예를 들어 사람의 나이에 따라 그룹을 만들고 싶다면,
```kotlin
val people = listOf(Person("Alice", 27), Person("Bob", 31), Person("Chris",31))
println(people.groupBy{ it.age })
=> { 27=[Person("Alice", 27)], 31=[Person("Bob", 31), Person("Chris",31)] }
```
### 지연 계산 컬렉션 연산
그런데 map이나 filter같은 컬렉션 함수들은 결과를 즉시 생성합니다. 즉, 매 단계 연산마다 중간 결과를 새로운 컬렉션에 임시로 담습니다. 이는 매우 불필요합니다. 그래서 Sequence를 사용하면 중간 임시 컬렉션을 생성하지 않고 처리가 가능합니다!

이를 코드로 보면 ```people.map(Person::name).filter{ it.startsWith("A") }``` 이 예시는 filter와 map에서 리스트를 총 2개를 만들어 냅니다. 그런데 이러한 원소의 개수가 수 백만개가 된다면 매우 비효율적일 수 있습니다. 그럴때는 Sequence를 사용하면 좋습니다. 

```people.asSequence().map(Person::name).filter{ it.startsWith("A") }.toList() ``` 
이 코드는 기능은 똑같으나 중간 결과를 저장하는 컬렉션이 생성되지 않아 성능이 훨씬 더 좋습니다.

이러한 시퀀스 연산은 중간 연산, 최종 연산이 있습니다. 또한 중간 연산은 항상 지연 계산됩니다. 이는 무슨 뜻이냐면,
```kotlin
listOf(1, 2, 3, 4).asSequence()
.map{ print("map $(it) "); it* it }
.filter{ print("filter $(it) "); it % 2 == 0 }
```
이는 아무런 출력도 하지 않습니다. 즉, Sequence에서 map 또는 filter같은 중간 연산은 결과를 얻을 필요가 있을 때, 결과를 사용해야할 때 적용이 됩니다. (출력은 모두 되지만 리스트에는 filter의 조건에 맞는 값만 들어가게 됩니다!)
```kotlin
listOf(1, 2, 3, 4).asSequence()
.map{ print("map $(it) "); it* it }
.filter{ print("filter $(it) "); it % 2 == 0 }
.toList()
=> map(1) filter(1) map(2) filter(4) map(3) filter(9) ...
```

일반 이터레이터였다면 map에 대해 모든 원소가 동작하고, 후에 모든 원소에 대해 filter가 적용되겠지만 Sequence는 다릅니다. 이를 예제를 통해 보면, 리스트의 값을 제곱한 뒤 3보다 큰 값을 찾는다고 해보겠습니다.
```kotlin
println(listOf(1, 2, 3, 4).asSequence().map{ it * it }.find{ it > 3 })
=> 4
```
원래의 컬렉션이라면 1,2,3,4를 모두 1, 4, 9, 16으로 변환한 뒤 find를 하여 4를 찾아냈겠지만, 
Sequence는 1을 제곱한 1, 그리고 find하여 false인 것을 확인, 
2를 제곱한 4, 그리고 find하여 true인것을 확인, 4를 반환하고 끝냅니다.

![](https://images.velog.io/images/seokzoo/post/dac98bbd-6399-4716-b131-d0fbc6b8a60c/image.png)
일반 Collection과 Sequence에서의 진행 순서의 차이에 대해서 잘 설명되어있는 그림입니다.

이를 잘 이해하고 활용하면 큰 용량의 컬렉션에서 메모리 이점을 취할 수 있을 것입니다.

### 람다의 끝, 수신 객체 지정 람다 - with, apply
맨 앞에서 봤던 확장 함수에서 수신 객체가 기억이 나시나요?(안난다면 다시 보고오길!) 지금부터는 수신 객체를 명시하지 않고, 람다의 본문 안에서 다른 객체의 메소드를 호출할 수 있습니다! 그러한 람다를 수신 객체 지정 람다라고 부르고, 먼저 with부터 보겠습니다.
#### with
저는 개인적으로 with를 정말 좋아하고, 자주 사용합니다! 너무나도 편합니다. 특히나 안드로이드 개발시에 binding을 자주쓰게 되는데, 이 with를 이용하면 모든 코드에 binding. 을 써주지 않고도 그 객체에 대하여 다양한 연산을 수행이 가능합니다. 아래의 코드는 with를 사용하는 것과 안하는 것의 차이를 보도록 하겠습니다.
```kotlin
1. 
fun alphabet(): String{
    val result = StringBuilder()
    for (letter in 'A'..'Z'){
    	result.append(letter)
    }
    result.append("\nNow I know the alphabet! ")
    return result.toString()
}

2.
fun alphabet() = with(StringBuilder()){
	for (letter in 'A'..'Z'){
    	append(letter)
    }
    append("\nNow I know the alphabet! ")
    this.toString()
}
```
1번과 2번의 차이를 보면, ```with(StringBuilder())```를 통해 StringBuilder의 선언부, 그리고 앞에 수신 객체를 모두 사용하지 않고 처리할 수 있게 됐습니다!
또한 식으로 만들어 마지막의 this.toString으로 "값"을 반환하여 식을 본문으로 하는 함수로 표현이 가능합니다. 너무나도 간결하고 편합니다. 
추가적으로, 마지막의 this 또한 그 수신 객체에 접근할 수 있으며 생략 또한 가능합니다.

#### apply
그런데 이런 with가 반환하는 값은 람다 코드를 실행한 "결과"이며, 그 "결과"는 람다식 본문에 있는 마지막 "값"입니다. 그런데 이런 결과보다는 수신 객체가 필요한 경우에는 apply를 사용합니다.
apply는 내부적으로 확장 함수로 구현되어 있습니다.(T.apply로 선언부가 구현되어 있다!) 

그리고 with와 유일한 차이점은 항상 자신에게 전달된 객체를 반환한다는 점입니다. apply를 이용해 위의 alphabet()을 리팩토링 해보겠습니다!
```kotlin
fun alphabet() = StringBuilder().apply{
	for (letter in 'A'..'Z'){
    	append(letter)
    }
    append("\nNow I know the alphabet! ")
}.toString()
```
이러한 apply함수는 보통 객체의 인스턴스를 만들면서, 프로퍼티 일부를 초기화 할때 가장 유용합니다. 안드로이드에서 사용하기 쉽게, TextView 컴포넌트를 생성하며 특성 일부를 지정해보겠습니다.
```kotlin
fun createViewWithCustomAttributes(context: Context) =
	TextView(context).apply{
    	text = "Sample Text"
        textSize = 20.0
        setPadding(10, 0, 0, 0)
    }

```
이는 새로운 TextView 인스턴스를 만들고, 즉시 그 인스턴스를 apply에 넘깁니다. apply에 전달된 람다 안에서 TextView가 수신 객체가 됩니다. 
따라서 TextView의 메소드를 호출하거나 프로퍼티를 설정할 수 있습니다. 그리고 후에 apply는 람다에 의해 초기화된 TextView의 인스턴스를 반환합니다. 그 인스턴스는 함수 createViewWithCustomAttributes의 결과가 됩니다. 

여기서는 with와 apply만 알아봤지만, run, also, let도 자주쓰이니 꼭 공부해보시길 바랍니다!

# 고차함수란?
코틀린에서 함수는 일급 함수입니다. 어떤 프로그래밍 언어에서 함수가 일급 객체라면 그 언어의 함수는 일급 함수라 불리는데, 이때 일급 객체는 다른 객체들에 적용 가능한 연산을 모두 지원하는 객체를 의미합니다.
 - 일급 객체는 함수의 매개변수가 될 수 있다.
 - 일급 객체는 함수의 return값이 될 수 있다.
 - 일급 객체는 할당 명령문(=, 대입)의 대상이 될 수 있다.
 - 일급 객체는 동일 비교(==, equal)의 대상이 될 수 있다.
 
즉 함수는 파라미터로 쓰일수도, 리턴타입에 쓰일수도, 비교 또는 변수에 대입까지 모두 가능한 것이 바로 코틀린 함수의 특징입니다.

이전에 위에서 본 map, with같은 함수들 모두 고차함수입니다! 

## 고차함수의 기본
먼저 함수를 변수에 초기화해줄 때를 예로들어보겠습니다.
```kotlin
val sum = { x:Int, y:Int -> x + y }
```
이 경우 컴파일러가 sum이 함수 타입이라는 것을 추론해줍니다. 그래서 명시적으로 타입을 작성해주면,
```kotlin
val sum: (Int, Int) -> Int = { x, y -> x + y }
```
이런 식으로 작성이 됩니다. 함수의 파라미터 타입을 괄호 안에 넣고, 리턴 타입은 화살표 뒤에 작성해줍니다.
즉 함수를 인자로 넘겨주고 싶다면 아래의 형태를 꼭 기억해야 합니다!
```kotlin
(Int, String) -> Unit   // Int, String 순서로 인자를 받고 Unit으로 return 합니다!
```
Unit은 의미 있는 값을 반환하지 않을 때 선언하는데(Println 같은..), 함수의 선언에서는 Unit은 생략이 가능합니다. 하지만 함수 타입의 리턴 타입 선언을 할때는 Unit은 생략할 수 없습니다.

아래와 같이 타입을 미리 작성해주면 뒤의 함수 형태 본체에서는 타입을 명시해줄 필요가 없습니다!
```kotlin
val sum: (Int, Int) -> Int = { x, y -> x + y }
```
또한 반환 타입을 null이 될 수 있는 함수 타입 변수를 정의할 수도 있습니다.
```kotlin
val canRetrunNull: (Int, Int) -> Int? = { x, y -> null }
```
반환 타입이 아닌 함수 타입 전체가 null이 될 수 있는 타입을 지정할 수 있습니다. 이는 괄호를 통해 전체를 묶어준 뒤 ?연산자를 통해 가능합니다.
```kotlin
val funOrNull: ((Int, Int) -> Int)? = null 
```
### 인자로 받았으니 실행은?
이제 인자로 함수를 파라미터로 받는 방법을 알았으니 받은 함수를 실행해보겠습니다.
```kotlin
fun twoAndThree(opertaion: (Int, Int) -> Int){
    val result = operation(2, 3)
    println("the result is $result")
}
fun main(){
    twoAndThree{ a, b -> a + b }
    twoAndThree{ a, b -> a * b }
}
=> the result is 5    
   the result is 6
```
operation이라는 함수는 Int형으로 이루어진 파라미터를 두개 가지고 있고, 리턴 값으로 Int형인 함수는 모두 들어갈 수 있습니다. main 함수에서는 람다 함수로 그러한 a+b, a*b 함수를 넘겨주었고, 함수안에서 인자로 받은 함수를 실행해주었습니다.

## 고차 함수와 확장 함수
이제 실행 방법은 알게됐습니다. 그렇다면 이전에 사용했던 String에 대한 filter를 구현해보겠습니다.
이전에 알아봤던 확장 함수와 함께 사용해봅니다.
```kotlin
// predicate라는 함수는 char을 받아 return 타입으로 true, false를 리턴하고,
// filter라는 함수는 string을 리턴한다.
fun String.filter(predicate: (Char) -> Boolean) : String {
    val sb = StringBuilder()
    for (index in 0 until length) {
        val element = get(index)
        if (predicate(element)) sb.append(element)
    }
	return sb.toString()
}

println("ab1c".filter{ it in 'a'..'z' }
=> abc
```
String의 확장함수로 filter라는 확장 함수를 생성했고, 고차함수를 통해 사용하는 부분에서 람다(코드 조각)를 입력 받을 수 있습니다. 이 입력 받을 수 있는 람다는 위에 선언한 Char을 받아 Boolean을 리턴하는 함수는 모두 입력받을 수 있습니다.

즉 순서대로 나열 해보면
1. String.filter 함수에서 for문이 실행되고, String의 수신 객체인 "ab1c"가 element에 get(index)를 통해 한글자씩 담긴다.
2. element를 predicate라는 함수에 전달해주면, 위에 언급했듯이 Char형으로 input이 들어오고,
3. filter의 람다에서 ```it in 'a'..'z'```의 값으로 true 또는 false를 리턴하여준다.
4. 이를 통해 'a'부터 'z'사이의 값만 true로 되어 sb에 append되고,
5. 마지막 함수의 리턴값은 String이기 때문에 sb를 String으로 만들어 출력해준다.

## 함수에서 함수 반환
이번에는 함수에서 리턴 값으로 함수를 이용하는 방법을 알아봅니다.
보통은 함수를 파라미터로 받아 사용하는 케이스가 많지만, 함수를 반환하는 것도 필요한 경우가 있으니 짧은 예시를 통해 알아보겠습니다!
예를 들어 사용자가 선택한 배송 수단에 따른 배송비를 계산하는 방법이 달라지는 함수를 만들어보도록 하겠습니다.
```kotlin
enum class Delivery { STANDARD, EXPEDITED }
class Order(val itemCount: Int)

fun getShippingCostCalculator(
    delivery: Delivery
): (Order) -> Double {
    if (delivery == Delivery.EXPEDITED) {
        return { order -> 6 + 2.1 * order.itemCount }
    }
    return { order -> 1.2 * order.itemCount }
}

fun main() {
    val calculator = getShippingCostCalculator(Delivery.EXPEDITED)
    println(calculator(Order(3)))
}
=> 12.3
```
이를 하나씩 보면, getShippingCostCalculator 함수의 파라미터로 Delivery의 형태를 입력받고, 그에 따른 람다 함수를 리턴합니다. 그렇게 해서 리턴 한 람다 함수에 작성한 그대로 Order형식의 파라미터를 전달 후 Double형의 리턴 값을 받습니다. 
위의 예제에서는 Order(3)을 전달, 결과로 12.3을 리턴받습니다!

## 후기
> 사실 더 잘 정리하고 싶었는데... 내용이 많이 부족한 것 같습니다... 다음 달에는 더 좋은 글로 나타나겠습니다...
2021/10/31

# 출처 및 참고:
https://choheeis.github.io/newblog//articles/2020-12/kotlinHigherOrderFunctionAndLambda
https://developer.android.com/kotlin/learn?hl=ko#anonymous
Dmitry Jemerov 『Kotlin in Action』, 에이콘, p103-140, p197-242.

