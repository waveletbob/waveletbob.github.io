---
layout: post
title: Scala偏函数与偏应用函数及case语句模式匹配
categories: Scala	
tags: Scala

---
@(Scala总结)[知识点|面试|总结]

## Scala偏函数Partial Function

艺术地说，Scala中的Partial Function就是一个“残缺”的函数，就像一个严重偏科的学生，只对某些科目感兴趣，而对没有兴趣的内容弃若蔽履。Partial Function做不到以“偏”概全，因而需要将多个偏函数组合，最终才能达到全面覆盖的目的。所以这个Partial Function确实是一个“部分”的函数。

对比Function和Partial Function，更学术味的解释如下：

- **对给定的输入参数类型，函数可接受该类型的任何值。换句话说，一个(Int) => String 的函数可以接收任意Int值，并返回一个字符串。**
- **对给定的输入参数类型，偏函数只能接受该类型的某些特定的值。一个定义为(Int) => String 的偏函数可能不能接受所有Int值为输入。**

在Scala中，所有偏函数的类型皆被定义为PartialFunction[-A, +B]类型，PartialFunction[-A, +B]又派生自Function1。由于它仅仅处理输入参数的部分分支，因而它通过isDefineAt()来判断输入值是否应该由当前偏函数进行处理。PartialFunction的定义如下所示：
```scala
trait PartialFunction[-A, +B] extends (A => B) { self =>
  import PartialFunction._
  def isDefinedAt(x: A): Boolean
  def applyOrElse[A1 <: A, B1 >: B](x: A1, default: A1 => B1): B1 =
    if (isDefinedAt(x)) apply(x) else default(x)
} 
```
既然偏函数仅处理部分分支，自然可以与模式匹配结合起来。case语句从本质上讲就是PartialFunction的子类。当我们定义了如下值：
```scala
val p:PartialFunction[Int, String] = { case 1 => "One" }
```
实际上就是创建了一个PartialFunction[Int, String]的子类，其中isDefineAt方法提供类似这样的实现：
```scala
def isDefineAt(x: Int):Boolean = x == 1
```
当我们通过p(1)去调用该偏函数时，就相当于调用了Int => String函数的apply()方法，从而返回转换后的值“one”。如果传入的参数使得isDifineAt返回false，就会抛出MatchError异常。追本溯源，是因为这里对偏函数值的调用，实则是调用了AbstractPartialFunction的apply()方法(case语句相当于是继承AbstractPartialFunction的子类)：
```scala
abstract class AbstractPartialFunction[@specialized(scala.Int, scala.Long, scala.Float, scala.Double, scala.AnyRef) -T1, @specialized(scala.Unit, scala.Boolean, scala.Int, scala.Float, scala.Long, scala.Double, scala.AnyRef) +R] extends Function1[T1, R] with PartialFunction[T1, R] { self =>
    def apply(x: T1): R = applyOrElse(x, PartialFunction.empty)
}
```
apply()方法内部调用了PartialFunction的applyOrElse()方法。若isDefineAt(x)返回为false，就会将x值传递给PartialFunction.empty。这个empty等于类型为PartialFunction[Any, Nothong]的值empty_pf，定义如下:
```scala
private[this] val empty_pf: PartialFunction[Any, Nothing] = new PartialFunction[Any, Nothing] {
    def isDefinedAt(x: Any) = false
    def apply(x: Any) = throw new MatchError(x)
    override def orElse[A1, B1](that: PartialFunction[A1, B1]) = that
    override def andThen[C](k: Nothing => C) = this
    override val lift = (x: Any) => None
    override def runWith[U](action: Nothing => U) = constFalse
  }
```
这正是执行p(2)会抛出MatchError的由来。为什么要用偏函数呢？以我个人愚见，还是一个重用粒度的问题。函数式的编程思想是以一种“演绎法”而非“归纳法”去寻求解决空间。也就是说，它并不是要去归纳问题然后分解问题并解决问题，而是看透问题本质，定义最原初的操作和组合规则，面对问题时，可以通过组合各种函数去解决问题，这也正是“组合子（combinator）”的含义。偏函数则更进一步，将函数求解空间中各个分支也分离出来，形成可以被组合的偏函数。偏函数中最常见的组合方法为orElse、andThen与compose。orElse相当于一个或运算，如果通过它将多个偏函数组合起来，就相当于形成了多个case合成的模式匹配。倘若所有偏函数满足了输入值的所有分支，组合起来就形成一个函数了。例如写一个求绝对值的运算，就可以利用偏函数：
```scala
val positiveNumber:PartialFunction[Int, Int] = { case x if x > 0 => x }
val zero:PartialFunction[Int, Int] = { case x if x == 0 => 0 }
val negativeNumber:PartialFunction[Int, Int] = { case x if x < 0 => -x }

def abs(x: Int): Int = {
    (positiveNumber orElse zero orElse negativeNumber)(x)
} 
```
利用orElse组合时，还可以直接组合case语句，例如：
```scala
val pf: PartialFunction[Int, String] = {
  case i if i%2 == 0 => "even"
}
val tf: (Int => String) = pf orElse { case _ => "odd" }
```
orElse被定义在PartialFunction类型中，而andThen与compose却不同，它们实则被定义在Function中，PartialFunction只是重写了这两个方法。这意味着函数之间的组合可以使用andThen与compose，偏函数也可以。这两个方法的功能都是将多个（偏）函数组合起来形成一个新函数，只是组合的顺序不同，andThen是组合第一个，接着是第二个，依次类推；而compose则顺序相反。利用andThen组合偏函数，设计本质接近Pipe-and-Filter模式，每个偏函数都可以理解为是一个Filter。因为要将这些偏函数组合起来形成一个管道，这就要求被组合的偏函数其输入值与输出值必须支持可串接，即上一个偏函数的输出值会作为下一个偏函数的输入值。对比orElse，则有所不同，orElse要求组合的所有偏函数必须是同样类型的偏函数定义，例如都是Int => String，或者String => CustomizedClass。在PartialFunction中，andThen方法返回的是一个名为AndThen的偏函数：
```scala
trait PartialFunction[-A, +B] extends (A => B) {
  override def andThen[C](k: B => C): PartialFunction[A, C] =
    new AndThen[A, B, C] (this, k)
}
object PartialFunction {
  private class AndThen[-A, B, +C] (pf: PartialFunction[A, B], k: B => C) extends PartialFunction[A, C] {
    def isDefinedAt(x: A) = pf.isDefinedAt(x)

    def apply(x: A): C = k(pf(x))

    override def applyOrElse[A1 <: A, C1 >: C](x: A1, default: A1 => C1): C1 = {
      val z = pf.applyOrElse(x, checkFallback[B])
      if (!fallbackOccurred(z)) k(z) else default(x)
    }
  }
} 
```
注意看，andThen接收的参数为k: B => C，即函数类型而非偏函数类型。当然，由于偏函数继承自函数，它也可以组合偏函数。如果andThen组合了偏函数，则要求输入参数必须满足所有参与组合的偏函数，否则就会抛出MatchError错误。例如编写一个函数，要求将字符串中的数字替换为对应的英文单词，则可以实现为：
```scala
val p1:PartialFunction[String, String] = { case s if s.contains("1") => s.replace("1", "one") }
val p2:PartialFunction[String, String] = { case s if s.contains("2") => s.replace("2", "two") }
val p = p1 andThen p2
```
如果调用p("123")，返回结果为"onetwo3"，但如果传入p("13")，由于p2偏函数的isDefineAt返回false，就会抛出MatchError错误。偏函数可以用在很多场景。例如我们可以利用orElse之类的语义，编写DSL风格的代码，使其更加灵活且可读。《DSL in Action》一书中就是用了orElse来处理金融行业的需求：
```scala
val forHKG:PartialFunction[Market, List[TaxFee]] = ...
val forSGP:PartialFunction[Market, List[TaxFee]] = ...
val forAll:PartialFunction[Market, List[TaxFee]] = ...

def forTrade(trade: Trade): List[TaxFee] = 
    (forHKG orElse forSGP orElse forAll)(trade.market)
```
也可以有效地利用偏函数的开放性，使得API的调用者可以根据具体的需求场景传入自己的case语句。例如Twitter的Effective Scala给出的案例：
```scala
trait Publisher[T] {
  def subscribe(f: PartialFunction[T, Unit])
}

val publisher: Publisher[Int] = ...
publisher.subscribe {
  case i if isPrime(i) => println("found prime", i)
  case i if i%2 == 0 => count += 2
  /* ignore the rest */
}
```
定义在AKKA的Actor中的receive()方法也是一个偏函数：
```scala
trait Actor {
    type Receive = Actor.Receive
    def receive: Actor.Receive
}
object Actor {
  type Receive = PartialFunction[Any, Unit]
}
```
由于偏函数继承自函数，因而，如果一个方法要求接收函数，那么它也可以接收偏函数。例如我们常常使用的map、filter等方法，就可以接收偏函数：
```scala
val sample = 1 to 10
sample map {
    case x if x % 2 == 0 => x + " is even"
    case x if x % 2 == 1 => x + " is odd"
}
```
在Twitter的Effetive Scala中，给出了一个使用map的编码风格建议：
```scala
//avoid
list map { item =>
  item match {
    case Some(x) => x
    case None => default
  }
}
//recommend
list map {
  case Some(x) => x
  case None => default
}
```scala
从本质上讲，假设这个list的类型为List[Option[String]]，则前者传给map的其实是一个形如Option[String] => String的函数，后者则通过case语句创建了PartialFunction[Option[String], String]的实例传递给了map。

## 偏应用函数（部分应用函数）

提供少于定义的参数个数，就得到了一个部分应用函数

```scala
def add(x:Int,y:Int,z:Int) = x+y+z
def addX = add(1,_:Int,_:Int)
addX(2,3)
addX(3,4)
def addXAndY = add(10,100,_:Int)
addXAndY(1)
def addZ = add(_:Int,_:Int,10)
addZ(1,2)
```






