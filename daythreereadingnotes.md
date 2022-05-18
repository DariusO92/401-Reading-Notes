# Java Primitives versus Objects

- Java has a two-fold type system consisting of primitives such as int, boolean and reference types such as Integer, Boolean. Every primitive type corresponds to a reference type.

Every object contains a single value of the corresponding primitive type. The wrapper classes are immutable (so that their state can't change once the object is constructed) and are final (so that we can't inherit from them).

Under the hood, Java performs a conversion between the primitive and reference types if an actual type is different from the declared one:

Integer j = 1;          // autoboxing
int i = new Integer(1); // unboxing
The process of converting a primitive type to a reference one is called autoboxing, the opposite process is called unboxing.

## Single Item Memory Footprint

- Just for the reference, the primitive type variables have the following impact on the memory:

boolean – 1 bit
byte – 8 bits
short, char – 16 bits
int, float – 32 bits
long, double – 64 bits
In practice, these values can vary depending on the Virtual Machine implementation. In Oracle's VM, the boolean type, for example, is mapped to int values 0 and 1, so it takes 32 bits, as described here: Primitive Types and Values.

Variables of these types live in the stack and hence are accessed fast. For the details, we recommend our tutorial on the Java memory model.

The reference types are objects, they live on the heap and are relatively slow to access. They have a certain overhead concerning their primitive counterparts.

The concrete values of the overhead are in general JVM-specific. Here, we present results for a 64-bit virtual machine with these parameters:

java 10.0.1 2018-04-17
Java(TM) SE Runtime Environment 18.3 (build 10.0.1+10)
Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10.0.1+10, mixed mode)
To get an object's internal structure, we may use the Java Object Layout tool (see our another tutorial on how to get the size of an object).

It turns out that a single instance of a reference type on this JVM occupies 128 bits except for Long and Double which occupy 192 bits:

Boolean – 128 bits
Byte – 128 bits
Short, Character – 128 bits
Integer, Float – 128 bits
Long, Double – 192 bits
We can see that a single variable of Boolean type occupies as much space as 128 primitive ones, while one Integer variable occupies as much space as four int ones.

## Exceptions

- The term exception is shorthand for the phrase "exceptional event."

Definition: An exception is an event, which occurs during the execution of a program, that disrupts the normal flow of the program's instructions.
When an error occurs within a method, the method creates an object and hands it off to the runtime system. The object, called an exception object, contains information about the error, including its type and the state of the program when the error occurred. Creating an exception object and handing it to the runtime system is called throwing an exception.

After a method throws an exception, the runtime system attempts to find something to handle it. The set of possible "somethings" to handle the exception is the ordered list of methods that had been called to get to the method where the error occurred.
