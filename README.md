# Algebraic Data Types

We will model:
* `has-a and` relasionship
* `is-a or` relashionship
* `has-a or` relasionship
* `is-a and` relashionship


|                | AND                    | OR            |
| -------------- |:----------------------:| -------------:|
|     IS-A       |                        | Sum type      |
|     HAS-A      | Product type           |               |


### Product Type Pattern
#### `has-a and` relasionship

If A `has a` b (with type B) `and` a c (with type C) write  
```scala
case class A(b: B, c: C)    
```  

or 
```scala
trait A {
    def b: B
    def C: C
}
```
### Sum Type Pattern
#### `is-a or` relasionship

If A is a B or C write
```scala
sealed trait A
final case class B() extends A
final case class C() extends A
```


### IS-A AND Pattern
#### `is-a and` relasionship
A is B and C
```scala
trait B
trait C
trait A extends  B with C
```

Notice: If we want to represent that some data conforms to a number of different interfaces we'll often be better off
usinf a `type class`.  
There are, however, several legitimate uses of this pattern:
* for modularity, using what's known as the [cake pattern](http://jonasboner.com/real-world-scala-dependency-injection-di/)
* sharing implementation across several classes where it doesn't make sense to make default implementation in the main trait


### HAS-A OR Pattern
#### `has-a or` relasionship
A has a B or C

```scala
trait A {
 def d: D 
}
sealed trait D
final case class B() extends D
final case class C() extends D
``` 

Alternatively we could implement this as A is a D or E and D has a B and E has a C
```scala
sealed trait A
final case class D(b: B) extends A
final case class E(b: B) extends A
```


## Structural Recursion
Two variants of this patterns:
* polymorphism
* pattern matching

### The Product Type Polymorphism Pattern

If A has a b (with type B) and a c (with type C), and we want to write a method f
returning an F, simply write the method is the usual way.
```scala
case class A(b: B, c:C) {
  def f: F =???
}
``` 

### The Sum Type Polymorphism Pattern
If A is A B or C, and we want to write a method f returning an F, 
define f as an abstract method on A and provide concrete implementation in B and C
```scala
sealed trait A {
  def f: F

final case class B() extends A {
  override f: F = ???

final case class C() extends A {
  override f: F = ???
}
}
```

### The Product Type Pattern Matching Pattern
If A has a b (with type B) and a c (with type C) and we want to write a method f that accepts an A and returns
an F, write
```scala
def f(a: A): F = 
    a match {
      case A(b,c) => ???
}
```

### The Sum Type Pattern Matching Pattern
If A is a B or C and we want to write a method f accepting an A and returning an F,
define a pattern matching case for B and C
```scala
def f(a: A) =
    a match {
      case B() => ???
      case C() => ???
}   
```

### Choosing Which Pattern to Use
We have three way of implementing structural recursion:
* polymorphism
* pattern matching in the base trait
* pattern matching in an external object

#### Polymorphism vs Pattern Matching

|                     | Add new method         | Add new data            |
| ------------------  |:-----------------------| :-----------------------|
|   Polymorphism      | Change existing code   | Existing code unchanged |
|   Pattern Matching  | Existing code unchanged| Change existing code    |

#### Base Trait vs External Object
* If a method only depends on other fields and methods in a class it is a good candidate to be implemented inside the class.
* If the method depends on other data consider implementing it using pattern matching outside of the classes
* If we want to have more then one implementation we should use pattern matching and implement it outside the classes


##Recursive Algebraic Data Types
When defining recursive algebraic data types, there must be at least two cases:
* one thet is recursive
* and one that is not (base case)
Skeleton is:
```scala
sealed trait RecursiveExample
final case class RecursiveCase(recursion: RecursiveExample) extends RecursiveExample
case object BaseCase extends RecursiveExample
``` 
####Dealing with Recursive Algebraic Data Types
* whenever we encounter a `recursive element` in the data we make a recusive call to our method
* whenever we encounter a `base case` in the data we return the identity for the operation we are performing.
The identity is an element that doesn't change the result. E.g. 0 is the identity for addition, because a+0=0 

