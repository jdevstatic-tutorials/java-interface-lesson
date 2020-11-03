# Java Interface
[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Fxdvrx1%2Fjava-interface-lesson&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=PAGE+VIEWS&edge_flat=false)](https://hits.seeyoufarm.com)

Interface in Java is actually easy to be understood but my very big question the very
first time I encountered this: why use interface when you can define and declare
methods in concrete classes? 

It is actually for abstraction and polymorphism.

Consider this scenario:

> *Say, you are programming a robot with a color sensor. Depending on the color,
it has a specific behavior to be expressed.*
> 
> *Without an interface, you will normally write separate codes to handle
each color. The process (detect the color then decide depending on that color
the behavior of the robot)
is being repeated for each color but the logic is the same:
the color sensor is the interface to trigger different behaviors. Why not just declare
a common method signature and implement this depending on the color? This simplifies
the situation. This preserves the logic of the program.*

So, interface is there for you to separate a class interface from its implementation.
That is separating the logic from the actual codes. Knowing the interface, you can
easily understand the classes that implemented the interface, just like 
a concrete class that implemented an abstract class.
Don't you know that for security reasons, some classes in Java only show the
abstract superclass and hide the concrete subclasses? And even if you don't see the 
actual implementation of each concrete subclass, you can use them as long as you understood
its superclass. That's the power of **abstraction**. For example, in `java.net`, `URLConnection`
is abstract, its subclasses are all abstract and the actual
implementations that depend on a lot of factors are actually hidden but once you understand 
those abstract classes, you can easily use them. 

But the main difference is that an abstract class is tied to its class hierarchy
but an interface is detached from any class hierarchy.  

## Method Resolution at Run Time
Another important thing: interface was made available to support 
dynamic method resolution at run time.
That is, one method signature for multiple implementations. 

For example, there are three classes with overloaded methods.
`C` extends `B` and `B` extends `A`. What is the effect? During run time,
in order for `C` to successfully extend `B`, `B` should be present and `A` also.
`A` is needed for `B` to successfully extend it.
Meaning, you simply put these three classes in the same directory.

Now, say, `C` calls a method from `A`. `B` should still
be present because it is an extension of `C`. 

Then, say, `C` calls a method from `B`. `A` should still be
present because it is an extension of `B`.
 
Try to not place them in the same directory and the run time environment 
will return an error because it cannot decide which is which. But, say, both `A` and
`B` is in the same directory and `C` is in another location, once your IDE opened
`A`, it can now locate `B` without opening it because the run time environment
now knows the directory of `B`. The formal way we do this is through `import` and
Java `package`.

That is the nature how a method signature is being resolved
at run time.

`A.java`

```
//the root of this sample hierarchy 
class A {
   void sampleMeth1() {
      System.out.println("this is A class sampleMeth1()");  
   }
   
   void sampleMeth2() {
      System.out.println("this is A class sampleMeth2()");  
   }
}
```

`B.java`

```
class B extends A {
   //inherited from class A but overridden        
   @Override
   void sampleMeth2() {
      System.out.println("this is B class sampleMeth2()");
   }
   
   //not inherited from A
   void sampleMeth3() {
      System.out.println("this is B class sampleMeth3()");  
   }
}
```

`C.java`

```
class C extends B {
   //inherited from class B but overridden
   @Override
   void sampleMeth2() {
      System.out.println("this is C class sampleMeth2()");
   }
   
   public static void main(String[] args) {
      C c = new C();
      System.out.println("All calls coming from " +
                         "an instance of C:");
      System.out.println("");
      
      //class C did not override sampleMeth3
      //class B did not override this method too
      //so, this will call the method in A
      System.out.println("sampleMeth1() was not overridden " +
                         "by the two subclasses, so the result is:");
      c.sampleMeth1();
      System.out.println("");
      
      //overridden from class B
      System.out.println("sampleMeth2() was overridden by C class, " +
                         "so the result is:");                       
      c.sampleMeth2();
      System.out.println("");
      
      //not overridden from class B
      System.out.println("sampleMeth3() was not overridden by C class, " +
                         "so the result is:");
      c.sampleMeth3();    
      
   }
}
```

the result:

```
> run C
All calls coming from an instance of C:

sampleMeth1() was not overridden by the two subclasses, so the result is:
this is A class sampleMeth1()

sampleMeth2() was overridden by C class, so the result is:
this is C class sampleMeth2()

sampleMeth3() was not overridden by C class, so the result is:
this is B class sampleMeth3()
```

Again, in a regular class hierarchy,
when your written code calls for a specific method in another class, both should
be present at run time to check for its signature. That class may be a subclass
of another class until such time it reaches the root of the class hierarchy. 

That would mean,
we rather declare everything as static methods to break the chain of the hierarchy,
so as to call them in their shortest routes. But a member declared as 
static in a superclass cannot be overridden by a subclass, 
thereby breaking the extensibility nature of a class. If your subclass
has the same method signature, it cannot override the static method from the superclass,
the static method in the superclass still exists from the perspective 
of an instance of the subclass. This will be not so helpful in a multi-level hierarchy 
when you can't tell the origin of a specific method. 

Another way is for the 
superclass to declare every possible method so that each will be available
for its subclasses. This is also problematic, since a class hierarchy will start from
a general idea to specific. Again, this will break the extensibility nature
of a class: a subclass cannot remove an inherited method that it does not need.
It can only override it with a blank body but this is not the concept of inheritance. 

This is where Java interface comes in. Method signatures will be
available through an interface detached from the class hierarchy. You can group several
unrelated methods to define the behavior of a class and a class can implement several
interfaces.  

## Interface Reference Variable
With interface reference variable, an object of a class that implemented an interface
can reference this name and call the method. The effect is that it's just like you are 
using the interface directly but generates different results depending on the situation.

Let us have a simple implementation of the mentioned scenario a while ago. Take note,
this is for demonstration purposes only not an actual programming of a robot in Java. 
 
`interface RobotColorSensor.java`

```
//for demonstration purposes only,
//a very simple example
interface RobotColorSensor {
   void detectColor();   
}
```

An example class that implements RobotColorSensor interface:
 
`RedColorReading.java`

```
class RedColorReading 
   implements RobotColorSensor {   
   public void detectColor() {
     System.out.print("color is red: ");
     System.out.println("backward movement.");
   }
}
```

Another example class that implements RobotColorSensor interface:
 
`GreenColorReading.java`

```
class GreenColorReading 
   implements RobotColorSensor {
   public void detectColor() {
     System.out.print("color is green: ");
     System.out.println("forward movement.");
   }
}
```

Now, we run them in main method:

```
class MainMethod {

   public static void main(String[] args) {
   //interface reference variable
   RobotColorSensor sensor;
   System.out.println("interface reference variable is `sensor`");  
   
   //an instance of RedColorReading
   RedColorReading redDetected = new RedColorReading();   
   sensor = redDetected;
   
   System.out.print("`sensor.detectColor()` says ");
   sensor.detectColor();
   
   //an instance of GreenColorReading
   GreenColorReading greenDetected = new GreenColorReading();
   sensor = greenDetected;
   
   System.out.print("`sensor.detectColor()` says ");
   sensor.detectColor();
   
   }
}
```

the result:

```
> run MainMethod
interface reference variable is `sensor`
`sensor.detectColor()` says color is red: backward movement.
`sensor.detectColor()` says color is green: forward movement.
```

See the importance of an interface? This is **run-time polymorphism**: 
one interface for a general class of actions.
 
## Interface Default Methods
Back then, an interface only holds the method signature (without body). 
Now, an interface can have a method with body. We call this as *default method*.
So, it's almost correct to say that through this, there can be multiple inheritance.
But it is not actually the purpose of this default method.

Rather, it is the solution for an interface to be updated later
without breaking the codebase of any project that implemented it.
Remember that the rule for interface is that all defined methods must be implemented
or else a class that partially implements an interface should be declared abstract.
With default methods, the rule has not changed. It is just already implemented
from the very start.

In the above example, say, `RobotColorSensor` is updated with a new method 
`detectColorHue()`. Without default method,
every class that implemented this interface should override this new
method. Imagine the pain of doing so. This is truly problematic for large-scale projects.

## Multiple Inheritance through Interface 
As was mentioned, with interface default method, it is almost correct 
(but still not correct) to say that it also
enables multiple inheritance. Multiple inheritance is quite an obscure concept.
What if a certain class implemented two interfaces with the same method name? 
It may be resolved by the run time environment but will return unexpected results. 

Rather, always remember that an interface does **define** (not inherit) 
the behavior of a class.
