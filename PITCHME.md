## Design Patterns

#### **NIC** Practical Programmer Series

---

### Practical Programmer Series

- Clean Code
- Code Complete
- Testable Code
- Code Reviewing
- **Design Patterns**
- Functional Programming


---

### Audience

- You try to practice object oriented design |
- You want to become a better programmer |

---

### Why are Design Patterns Important

- Because object oriented design is hard |
- Because it is nice to have words for things |
- Someone else has already done the hard work |

---

### Agenda

- Strategy |
- Factory |
- Decorator |

---

### SimUDuck

- Duck pond simulation App
- large number of duck species
    - these species swim, fly and quack
- Designed OO, one duck superclass, all others inherit

---

### SimUDuck

```java
abstract class Duck {
    public void quack(){}
    public void swim(){}
    public abstract void display();
    //other things ducks do...
}
```
---
```java
class RedHeadDuck extends Duck {
    public void display(){
        //look like a RedHead duck
    }
}
class MallardDuck extends Duck {
    public void display(){
        //look like a mallard duck
    }
}
```
---
### Problem:
- Flight |
---
### Solution 1:
- Add a fly method to the Duck class |
- All ducks will inherit the fly behavior |
- Much the same way as quack or swim |
---

### SimUDuck

```java
abstract class Duck {
    public void quack(){}
    public void swim(){}
    public abstract void display();
    //other things ducks do...
}
```
---

### SimUDuck

```java
abstract class Duck {
    public void quack(){}
    public void swim(){}
    public void fly(){}
    public abstract void display();
    //other things ducks do...
}
```
---
### Problem 1.1:
- Not all ducks should fly |

---
### Problem 1.1: Rubber Ducks
```java
class RubberDuck extends Duck {
    public void quack(){
        // overwritten to squeak
    }
    public void display(){
        //look like a rubber duck
    }
}
```
---
### Solution 1.1: Overwrite fly method
```java
class RubberDuck extends Duck {
    public void fly(){
        //don't
    }
    public void quack(){
        // overwritten to squeak
    }
    public void display(){
        //look like a mallard duck
    }
}
```
---
### Problem 1.2: More Flightless, Quackless Ducks
- You have plans to add wooden, plastic, decoy and baby duck in an upcoming release |
- As more flightless ducks are added, more and more overwriting will be necessary |
---
### Solution 1.2: Interfaces
- Have ducks implement the Flyable or Quackable interface  |
- Ducks which don't fly or quack never need worry about that |
---
### Problem 1.3: That is a bad idea
- Code duplication like crazy |
- Any time you want to change how fly works you must change all 'normal' ducks |
---
### Problem 1.3: That is a bad idea
- Inheritance isn't going to be a good solution  |
- Loading up on interfaces isn'y going to be a good solution |
---
### Thinking 1.3
- Encapsulate (Separate)
    - Changes
    - That which stays the same
- Duck seems to be mostly OK
---
### Solution 1.3
- Fly and Quack are what change between ducks
- Leave Duck alone, other than fly and quack
- Encapsulate each behavior
---
### Solution 1.3
- Interface for Fly based behaviors
- Interface for Quack based behaviors
- Create classes for the different implementations |
- Each should implement one of the interfaces |
---
### Aside 1.3
- Allow duck behavior to change dynamically |
- Put setters on ducks to load these behaviors |
- ex. Wounded ducks might not fly |
---
### Solution 1.3
```java
interface FlyBehavior{
    public void fly();
}
class FlyWithWings implements FlyBehavior {
    public void fly(){
        //implement 'normal' flying
    }
}
class FlyNoWay implements FlyBehavior {
    public void fly(){
        //don't fly
    }
}
```
---

### Solution 1.3
```java
interface QuackBehavior{
    public void quack();
}
class Quack implements QuackBehavior {
    public void quack(){
        //implement 'normal' quacking
    }
}
class Squeak implements QuackBehavior {
    public void quack(){
        //squeak
    }
}
class Mute implements QuackBehavior {
    public void quack(){
        //don't quack
    }
}
```
---
### Solution 1.3
- New ducks can reuse our existing behaviors |
- We can add new behaviors without changing existing behaviors |
---
### Aside 1.3
- What would need to change to add rocket-powered flying? |
- What might want quacking behavior that isn't at all a duck? |
---
### Solution 1.3
- How do we plug in these behaviors?
---
### Solution 1.3
```java
abstract class Duck {
    protected FlyBehavior flyBehavior;
    protected QuackBehavior quackBehavior;
    public void quack(){
        quackBehavior.quack();
    }
    public void swim(){}
    public void fly(){
        flyBehavior.fly();
    }
    public abstract void display();
    public void setFlyBehavior(FlyBehavior flyBehavior){
        this.flyBehavior = flyBehavior;
    }
    public void setQuackBehavior(QuackBehavior quackBehavior){
        this.quackBehavior = quackBehavior;
    }
    //other things ducks do...
}
```
---
### Solution 1.3
```java
class MallardDuck extends Duck {
    public MallardDuck(){
        quackBehavior = new Quack();
        flyBehavior = new FlyWithWings();
    }
    public void display(){
        //look like a mallard duck
    }
}
```
---
### Solution 1.3
```java
class MallardDuck extends Duck {
    public MallardDuck(){
        this.setQuackBehavior(new Quack());
        this.setFlyBehavior(new FlyWithWings());
    }
    public void display(){
        //look like a mallard duck
    }
}
```
---
### Solution 1.3
```java
class MallardDuck extends Duck {
    public MallardDuck(){
        super(new Quack(), new FlyWithWings());
        //in case you are worried that one might not be set
    }
    public void display(){
        //look like a mallard duck
    }
}
```
---
### Strategy Pattern
- You think of a group of behaviors as a family of algorithms |
- This is called using 'Composition' |
    - Instead of inheriting behaviors |
- Favour composition over inheritance |
---
### Where have you seen this
- Services in java
    - Construct service with dependent services
    - Can be swapped out at run time
    - Can be swapped out for testing
- Modules in angular
    - Dependencies pulled in can be swapped out
- Everywhere because this is design pattern #1
---
### Coffee Shop
- First structured possible drinks in basic OO way
- Beverage class from which all drinks inherit
- A series of drinks which extend this class
    - ex. Espresso, DarkRoast, HouseBlend, Decaf
- A number of condiments to be added to the drinks
    - ex. milk, soy, mocha, whipped milk
---
### Problem 2
- The solution for Coffee shop was to create a class for each condiment
- Espresso with milk, Espresso with mocha, Espresso with soy and mocha
- Class explosion happens pretty quick
- If price of milk goes up, hundreds of classes need to be changed
---
### Solution 2
- Create boolean on Beverage for each condiment
- Create cost method in Beverage
- Have cost method in each subclass overwrite and incorporate
---
### Solution 2
```java
abstract class Beverage {
    boolean milk;
    boolean soy;
    boolean mocha;
    boolean whip;
    double getCost(){
        //calculate a base price based on the booleans;
        return 0;
    }
}
```
---
### Solution 2
```java
class DarkRoast extends Beverage {
    public double getCost(){
        double baseCost = super.getCost();
        return baseCost + 4;
    }
}
```
- 5 Classes total|
### Problem 2.1
- This structure is very rigid
- This structure wouldn't support double mocha
- Something like IcedTea maintains a whip bool
- As we add condiments we have to change the superclass
---
### Open-Closed Principle
- Classes should be open for extension
- Classes should be closed for modification
- Applying this everywhere is excessive and wasteful
    - Produces complex, hard-to-understand code
---
### Decorator Pattern
- Construct objects procedurally
- Instantiate with other instances of same interface
---
### Solution 2.1
- Create class for each beverage type
- Create class for each condoment
- Each condiment is a beverage
- Each condiment takes a beverage
---
### Solution 2.1
```java
abstract class Beverage {
    String description = "Unknown Beverage";
    public String getDescription(){
        return description;
    }
    abstract double getCost();
}
class DarkRoast implements Beverage {
    public double getCost(){
        return 4;
    }
}
class Mocha implements Beverage {
    Beverage baseBeverage;
    public Mocha(Beverage baseBeverage){
        this.baseBeverage = baseBeverage;
    }
    public double getCost(){
        return baseBeverage.getCost() + 1;
    }
}
```
---
### Solution 2.1
- We can decorate dynamically at any time
- All custom logic happens one side of base
- Decorators and objects they decorate are used the same way
---
### Solution 2.1
- "The Decorator pattern attaches additional Responsibilities to an object dynamically.
- Decorators provide a flexible alternative ot subclassing for extending functionality."
---
### Kata 2.1
- Design the above for Espresso, HouseBlend and DarkRoast
- Mocha, whip, Soy and milk
- What other classes might you want to make?
- How do you handle sizes, and varied condiment price by size
---
### Places you've seen decorator
- Java I/O
    - InputStream has concrete Children
        - StingBufferedInputStream
    - Concrete Decorator Classes
        - PushBackInputStream
    - OutputStream, Reader, Writer etc.
---
### Kata 2.2
- LowerCaseInputStream
- Create InputStream decorator
- Transform input to lower case
---
### Statement 3
- Every time you use new you are programming to an implementation
- We want to separate our concerns to make our class "closed for modification"
- Once the instantiation code is isolated we can swap it out
---
### Problem 3
```java
class PizzaStore {
    public Pizza orderPizza(){
        Pizza pizza = new Pizza();

        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();
        return pizza;
    }
}
```
---
### Problem 3: Type
```java
class PizzaStore {
    public Pizza orderPizza(String type){
        Pizza pizza;

        if(type.equals("cheese")){
            pizza = new ChesePizza();
        } else if (type.equals("greek")){
            pizza = new GreekPizza();
        } else if (type.equals("pepperoni")){
            pizza = new PepperoniPizza();
        }

        ...
    }
}
```
---
### Problem 3: Type
- Every type we add needs changes in this class
- The switching takes up most of the class
- We need to encapsulate
---
### Factory Pattern
- We move out all code concerned with instantiating concrete types
- This factory will take out concerns and return to us a Pizza
- This factory can be swapped out and the Pizza process won't care
---
### Solution 3
```java
interface PizzaFactory {
    Pizza createPizza(String type);
}
```
---
### Solution 3
```java
class FirstPizzaFactory implements PizzaFactory{
    public Pizza createPizza(String type){
        Pizza pizza = null;
        if(type.equals("cheese")){
            pizza = new ChesePizza();
        } else if (type.equals("greek")){
            pizza = new GreekPizza();
        } else if (type.equals("pepperoni")){
            pizza = new PepperoniPizza();
        }
        return pizza;
    }
}
```
---
### Solution 3
```java
class PizzaStore {
    PizzaFactory pizzaFactory;
    public PizzaStore(PizzaFactory pizzaFactory){
        this.pizzaFactory = pizzaFactory;
    }
    public Pizza orderPizza(){
        Pizza pizza = pizzaFactory.createPizza();
        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();
        return pizza;
    }
}
```
---
### Static Factory Pattern
- Pros: You don't instantiate Factory
- Cons: No Subclassing
---
### Possible Future Factories
- ChicagoPizzaFactory
- NewYorkPizzaFactory
- CaliforniaPizzaFactory
- NeapolitanPizzaFactory
- SushiPizzaFactory
- etc...
---
### Possible Future Factories
```java
class PizzaFranchise {
    PizzaFactory nyFactory = new NewYorkPizzaFactory();
    PizzaStore nyStore = new PizzaStore(nyFactory);
    nyStore.orderPizza("veggie");

    PizzaFactory chicagoFactory = new ChicagoPizzaFactory();
    PizzaStore chicagoStore = new PizzaStore(chicagoFactory);
    chicagoStore.orderPizza("veggie");
}
```
---
### Possible Future Factories
- The two stores make their different Pizzas
- The rest of the Pizza making process remains in tact
---
### Better Idea
- Each Factory is really tied to the type of store
- Are the factory declarations really neccessary
---
### Better Idea
```java
abstract class PizzaStore {
    public Pizza orderPizza(){
        Pizza pizza = this.createPizza();
        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();
        return pizza;
    }
    protected abstract Pizza createPizza(String type);
}
```
---
### Better Idea
```java
class NYPizzaStore extends PizzaStore {
    public Pizza createPizza(String type){
        Pizza pizza = null;
        if(type.equals("cheese")){
            pizza = new ChesePizza();
        } else if (type.equals("greek")){
            pizza = new GreekPizza();
        } else if (type.equals("pepperoni")){
            pizza = new PepperoniPizza();
        }
        return pizza;
    }
}
```
---
### Better Idea
```java
class PizzaFranchise {
    PizzaStore nyStore = new NYPizzaStore();
    nyStore.orderPizza("veggie");

    PizzaStore chicagoStore = new ChicagoPizzaStore();
    chicagoStore.orderPizza("veggie");
}
```
---
### Factory Method Pattern
- Defines interface for creating an object
- Subclasses decide which class to instantiate
---
### Kata

---
### Abstract Factory Pattern
- If we have extra time I might look at this
---






## Based on the book :
## 'Intro to Design Patterns'

---

## Thank you!
### Be sure to check out the rest of our classes!
