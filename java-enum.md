## Java Enum Types

``` java
<access-modifier> enum <Type> {
    Name1, Name2, ...;
    
    filed1, filed2, ...;
    
    <package private or private> constructor(params) {
        ...
    }
    
    method1, method2, ...;
}
```

> The enum class body can include methods and other fields.

The compiler automatically adds some special methods when it creates an enum. For example, they have a static values method that returns an array containing all of the values of the enum in the order they are declared. This method is commonly used in combination with the for-each construct to iterate over the values of an enum type:

``` java
for (Planet p : Planet.values()) {
    System.out.printf("Your weight on %s is %f%n",
                      p, p.surfaceWeight(mass));
}
```

> Enum cannot extend another class.

All enums implicitly extend `java.lang.Enum`. Because a class can only extend one parent (see Declaring Classes), the Java language does not support multiple inheritance of state, and therefore an enum cannot extend anything else.

> The constructor for an enum type must be package-private or private access.

It automatically creates the constants that are defined at the beginning of the enum body. You cannot invoke an enum constructor yourself.