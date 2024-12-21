# Factory Method

The **Factory Method** design pattern defines an interface for creating object, but lets subclasses decide which class to instantiate. This pattern lets a class defer instantiation to subclasses.

## Problem

Lets say there is a webapp for a truck delivery company. The code for this webapp contains a ``Truck`` class to represent the trucks and has a ton of logic coupled to this ``Truck`` class. 

Now lets assume this delivery company wanted to expand to shipping with other vehicles (boats and planes). This change will require a lot of refactoring in the code due to the coupled nature of the ``Truck`` class and all the code logic tied to it.

## Solution

A **factory method** would remove the coupling of the ``Truck`` class. Instead there would be a interface with a **factory method** in which a subclass (the ``Truck`` class) can be instantiated from. Now all the code logic needed can be tied to the interface class instead of the ``Truck`` class. Also, when new objects need to be added, all it takes is creating a new subclass. 

Don't worry if this doesn't make sense, the examples below will help.

# Structure

Below is an example of the structure of a factory method design pattern. 

!!! tip "Factory Method Structure UML"
    <figure markdown>
      <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-dark)
      ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-light) -->
    </figure markdown>

- **Product:** interface for the factory method, declares all the common properties that can be created by the factory method.

- **Concrete Product:** specific implementations of product interface

- **Creator:** class that contains the factory method and any other optional additional operations. If a program has a single object to represent. The ``creator`` class would have all the functionality to represent that object.

- **Factory Method:** method that creates the subclasses
    - Normal Method: defined a default object creation (always return a specific object type)
    - Abstract: forces subclass to define object creation

- **ConcreteCreator:** these are the subclass objects that overrides the base factory method to have its own object construction.

# Factory Method Example

Imagine there is a restaurant simulator. The restaurant has two different types of burgers it can make:

- veggie burgers
- beef burgers

Below are two implementation of this restaurant order management application. 

1. Non Factory Method
2. Factory Method solution

### Non Factory Method Example

???+ Example "Non Factory Example"
    ```c# linenums="1" title="Example Non Factory Method" hl_lines="11-19 41-53"
    public class BeefBurgerRestaurant
    {
        public void orderBurger(String request)
        {
            IBurger burger =  new BeefBurger();
            burger.prepare();
            Console.WriteLine("Here is your {0} burger", burger.Btype);
        }
    }

    public class VeggieBurgerRestaurant
    {
        public void orderBurger(String request)
        {
            IBurger burger =  new VeggieBurger();
            burger.prepare();
            Console.WriteLine("Here is your {0} burger", burger.Btype);
        }
    } 

    public interface IBurger
    {
        public string Btype { get; set; }
        public void prepare();
    }

    public class BeefBurger : IBurger
    {
        private string _btype = "beef";
        public string Btype
        {
            get => _btype;
            set => _btype = value;
        }
        public void prepare()
        {
            Console.WriteLine("Preparing Beef Burger");
        }
    }

    public class VeggieBurger : IBurger
    {
        private string _btype = "veggie";
        public string Btype
        {
            get => _btype;
            set => _btype = value;
        }
        public void prepare()
        {
            Console.WriteLine("Preparing Veggie Burger");
        }
    }
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main()
    {
        BeefBurgerRestaurant beefTest = new BeefBurgerRestaurant();
        beefTest.orderBurger("BEEF");
        VeggieBurgerRestaurant veggieTest = new VeggieBurgerRestaurant();
        veggieTest.orderBurger("VEGGIE");
    } 
    ```

    ```title="Output"
    Preparing Beef Burger
    Here is your beef burger
    Preparing Veggie Burger
    Here is your veggie burger
    ```

    In this example there are the following classes: 

    - ``BeefBurgerRestaurant`` the Creator class
    - ``VeggieBurgerRestaurant`` the Creator class
    - ``IBurger`` Product interface class
    - ``BeefBurger`` a Product class
    - ``VeggieBurger`` a Product class
    
    The issue with this implementation is if a developer is building out a webapp and assume a ``BeefBurgerRestaurant`` object is the only type of restaurant there is, the code becomes coupled. 

    If a new restaurant is added such as the ``VeggieBurgerRestaurant``, every instance of ``BeefBurgerRestaurant`` needs to be refactored to handle both. For example, before ``VeggieBurgerRestaurant`` was added, the driver code would've just been running ``BeefBurgerRestaurant``. Now the driver code had to be refactored to run both. 
    
    Using the factory method design principle helps alleviate this issue!

### Factory Method Example

???+ Example "Factory Method Example"
    ```c# linenums="1" title="Example Code" hl_lines="1-10"
    public abstract class Restaurant {
        public void orderBurger()
        {
            IBurger burger = createBurger();
            burger.prepare();
            Console.WriteLine("Here is your {0} burger", burger.Btype);
        }

        public abstract IBurger createBurger();
    }

    public class BeefBurgerRestaurant : Restaurant
    {
        public override IBurger createBurger()
        {
            return new BeefBurger();
        }
    }

    public class VeggieBurgerRestaurant : Restaurant
    {
        public override IBurger createBurger()
        {
            return new VeggieBurger();
        }
    }

    public interface IBurger
    {
        public string Btype { get; set; }
        public void prepare();
    }

    public class BeefBurger : IBurger
    {
        private string _btype = "beef";
        public string Btype
        {
            get => _btype;
            set => _btype = value;
        }
        public void prepare()
        {
            Console.WriteLine("Preparing Beef Burger");
        }
    }

    public class VeggieBurger : IBurger
    {
        private string _btype = "veggie";
        public string Btype
        {
            get => _btype;
            set => _btype = value;
        }
        public void prepare()
        {
            Console.WriteLine("Preparing Veggie Burger");
        }
    }
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main()
    {
        Restaurant beefTest = new BeefBurgerRestaurant();
        Restaurant veggieTest = new VeggieBurgerRestaurant();
        beefTest.orderBurger();
        veggieTest.orderBurger();
    } 
    ```

    ```title="Output"
    Preparing Beef Burger
    Here is your beef burger
    Preparing Veggie Burger
    Here is your veggie burger
    ```

    In this example there are the following classes: 

    - ``Restaurant`` Creator class
    - ``BeefBurgerRestaurant`` Concrete Creator class
    - ``VeggieBurgerRestaurant`` Concrete Creator class
    - ``IBurger`` Product interface class
    - ``BeefBurger`` a Product class
    - ``VeggieBurger`` a Product class
    
    There is slightly more complexity since there is now a ``Restaurant`` class which contains the **factory method** ``createBurger``. In this example ``createBurger`` is an abstract method, meaning it needs to be overrided in the specific subclass implementations (``BeefBurgerRestaurant`` and ``VeggieBurgerRestaurant``.

    Now if a developer is working with this code, they can use the ``Restaurant`` class to initialize everything. Both the beef and veggie objects will fit into this implementation.

## When To Use 

- When you don't know beforehand the exact types and dependencies of the objects your code should work with. 
- When you want to provide users a way to extend its internal components

## Resources

[:material-video-outline: Factory Method Example](https://www.youtube.com/watch?v=EdFq_JIThqM)

[:material-file-document-outline: Factory Method](https://refactoring.guru/design-patterns/factory-method)

