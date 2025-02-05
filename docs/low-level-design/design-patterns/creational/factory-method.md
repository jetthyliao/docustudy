# Factory Method

The **Factory Method** design pattern defines an interface for creating object, but lets subclasses decide which class to instantiate. This pattern lets a class defer instantiation to subclasses.

## Structure

Below is an example of the structure of a factory method design pattern. 

!!! tip "Factory Method Structure UML"
    <figure markdown>
      <!-- 
      ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-dark)
      ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-light) 
      -->
    </figure markdown>

- **Creator:** class that contains the factory method and any other optional additional operations. The role of the ``Creator`` class is not solely product creation, it has other operations that related to the product as well. 

    - **Factory Method:** method that creates the subclasses, the return type should be the Product interface. This can be set to ``abstract`` so subclasses implement their own versions of the method, or it can return some sort of default.

- **Product:** interface that declares all the common properties between objects that can be produced by the ``Creator``

- **Concrete Creator:** these are the subclass objects that overrides the ``factory method`` to have its own object construction.

- **Concrete Product:** specific implementations of product interface

## Factory Method Example

Imagine a program that simulates going out to eat at a restaurant. 

In the following examples there are two meal preferences: 

- Italian meal
- Chinese meal

The program also has a ``Restaurant`` class that will prepare a meal based on the type of preference.

### Non Factory Method Example

???+ Example "Non Factory Method Example"

    === "C#"

        ```c# linenums="1" title="Example Code" hl_lines="19-35" 
        public class ItalianMeal 
        {
            public void Cook()
            {
                Console.WriteLine("Preparing a delizioso Italian meal");
            }
        }

        public class ChineseMeal
        {
            public void Cook()
            {
                Console.WriteLine("Preparing a succulent Chinese meal");
            }
        }

        public class Restaurant
        {
            public void OrderMeal(string mealPreference)
            { 
                if (mealPreference == "Chinese")
                {
                    ChineseMeal chineseMeal = new ChineseMeal();
                    chineseMeal.Cook();
                }
                else if (mealPreference == "Italian")
                {
                    ItalianMeal italianMeal = new ItalianMeal();
                    italianMeal.Cook();
                }
                else
                {
                    throw new Exception("Invalid meal preference");
                }
            }

            // Additional Business Logic...
        }
        ```

        ```c# linenums="1" title="Driver Code"
        static void Main(string[] args)
        { 
            // var mealPreference = "Italian";
            var mealPreference = "Chinese";
            Restaurant restaurant = new Restaurant();
            restaurant.OrderMeal(mealPreference);
        }
        ```

        ```title="Output"
        Preparing a delizioso Italian meal
        Preparing a succulent Chinese meal
        ```

    Thl main issue with this implementation is the ``OrderMeal`` function in lines 19-35. Every time the restaurant needs to perform a task (such as cooking the meal), it must first know what type of meal (``ItalianMeal`` or ``ChineseMeal``) its dealing with. This causes a tight coupling between various meals and the ``Restaurant`` class since adding new meals or changes to the meals constructors require updating the ``Restaurant`` class. 

    This implementation breaks the following SOLID principles:

    - Single Responsibility Principle: ``Restaurant`` class has two reasons to be modified now: new meal classes being added and business logic updates to ``Restaurant`` class
    - Open-Closed Principle: if new meal classes are added, the ``Restaurant`` class needs to be modified. 
    - Dependency Inversion Principle: the ``Restaurant`` class depends on the ``ChineseMeal`` and ``ItalianMeal`` class.

### Factory Method Example

???+ Example "Factory Method Example"

    === "C#"

        ```c# linenums="1" title="Example Code" hl_lines="4-5 16-20"
        // Creator (Interface)
        public interface Restaurant
        {
            // Factory Method
            public abstract Meal CreateMeal();

            public void OrderMeal()
            {
                Meal meal = CreateMeal();
                meal.Cook();
            }

            // Additional Business Logic...
        }

        // Product (Interface)
        public interface Meal
        {
            public void Cook();
        }

        // Creator (Concrete Class)
        public class ItalianRestaurant : Restaurant
        {
            public Meal CreateMeal()
            {
                return new ItalianMeal();
            }
        }

        // Creator (Concrete Class)
        public class ChineseRestaurant : Restaurant
        {
            public Meal CreateMeal()
            {
                return new ChineseMeal();
            }
        }

        // Product (Concrete Class)
        public class ItalianMeal : Meal
        {
            public void Cook()
            {
                Console.WriteLine("Preparing a delizioso Italian meal");
            }
        }

        // Product (Concrete Class)
        public class ChineseMeal : Meal
        {
            public void Cook()
            {
                Console.WriteLine("Preparing a succulent Chinese meal");
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="6 10 14 21"
        static void Main(string[] args)
        {
            // var mealPreference = "Italian";
            var mealPreference = "Chinese";

            Restaurant restaurant;

            if (mealPreference == "Chinese")
            {
                restaurant = new ChineseRestaurant();
            }
            else if (mealPreference == "Italian")
            {
                restaurant = new ItalianRestaurant();
            }
            else
            {
                throw new Exception("Invalid meal preference");
            }

            restaurant.OrderMeal();
        }
        ```

        ```title="Output"
        Preparing a delizioso Italian meal
        Preparing a succulent Chinese meal
        ```
    
    This example introduces a slight amount of complexity with interfaces to abstract the ``Restaurant`` class and the various ``Meal`` classes. 

    The ``Restaurant`` class (**Creator**) now has a **factory method** to handle creation of ``Meal`` subclasses (**Products**).

    As seen on line 21 of the driver code, it does not matter whether the ``restaurant`` variable (line 6) is defined with a ``ChineseRestaurant`` or ``ItalianRestaurant`` class, both will not break the rest of the code. This is because both subclasses inherit from the ``Restaurant`` class.

    With this implementation we've fixed issues that broke the following SOLID principles: 

    - Single responsibility principle: if new meals need to be added, none of the classes need to be modified. Instead, all that needs to be done is to create a new ``Restaurant`` subclass and a new ``Meal`` subclass. 
    - Open-Closed principle: same as single responsibility principle
    - Dependency Inversion: the factory method moved the creation of meals to the concrete ``Restaurant`` classes.

## When To Use 

- When you don't know beforehand the exact *Products* or types of the objects your code should work with. 
- When you want to provide users a way to extend its internal components

## Resources

[:material-video-outline: Factory Method Example](https://www.youtube.com/watch?v=EdFq_JIThqM)

[:material-file-document-outline: Factory Method](https://refactoring.guru/design-patterns/factory-method)

