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
- each should implement one of the interfaces |
---
### Aside 1.3
- Allow duck behavior to change dynamically |
- Put setters on ducks to load these behaviors |
- ex. Wounded ducks might not fly |
---
### Solution 1.3
```java
interface QuackBehavior{
    public void fly();
}
class Quack implements QuackBehavior {
    public void fly(){
        //implement 'normal' quacking
    }
}
class Squeak implements QuackBehavior {
    public void fly(){
        //squeak
    }
}
class Mute implements QuackBehavior {
    public void fly(){
        //don't quack
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
### Problem 2.1
- 
---
### Solution 2.1
- 
---



## Based on the book : 'Intro to Design Patterns'

---

## Thank you!
### Be sure to check out the rest of our classes!
