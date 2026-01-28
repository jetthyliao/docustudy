<!-- list of things to do 
TODO: Add python non factory example
TODO: Redo c# factory example
TODO: Redo c# non factory example
TODO: read over and fix mistakes
-->
# Factory Method

## Overview

The **Factory Method** design pattern defines an interface for creating objects, but allows subclasses to alter the type of object that is created.

### Structure

- **Creator:** class that contains the *factory method* and additional optional operations. The role of the ``Creator`` class is not solely product creation, it has other operations that are related to the product as well. 

    - **Factory Method:** method that creates the subclasses, the return type should be the Product interface. This can be set to ``abstract`` so subclasses implement their own versions of the method, or it can return some sort of default.

- **Concrete Creator:** these are the subclass objects that overrides the ``factory method`` to have its own object construction. The object it constructs is a concrete product.
- **Product:** interface that declares all the common properties between objects that can be produced by the ``Creator``
- **Concrete Product:** specific implementations of product interface

## Factory Method Example

### Problem

Imagine a new feature to send notifications. It initially supports Microsoft Teams, but is extended later to also support SMS notifications.

This program has two classes to handle notifications: 

- ``TeamsNotification``
- ``SMSNotification``

It also has a class ``Sender`` to construct ``Notifications`` and trigger sending messages on them.

!!! note "Try It Out!"
    Try to integrate the factory method to create this feature! You can take a look at the ``Non Factory Example`` to get started.


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
    
    === "Python"

        ```python title="Example Code"
        # Product Class
        class Notification:
            # Abstract
            def send(self): ...


        # Concrete Product Subclass
        class TeamsNotification(Notification):
            def send(self):
                print("TEAMS NOTIFICATION: sup")


        # Concrete Product Subclass
        class SMSNotification(Notification):
            def send(self):
                print("SMS NOTIFICATION: yo")


        # Creator Class
        class Sender:
            # Abstract Factory Method
            def createNotification(self): ...  # Abstract or can return some default

            def notify(self):
                notification: Notification = self.createNotification()
                notification.send()


        # Concrete Creator Subclass
        class TeamsSender(Sender):
            def createNotification(self):
                return TeamsNotification()


        # Concrete Creator Subclass
        class SMSSender(Sender):
            def createNotification(self):
                return SMSNotification()
        ```

        ```python title="Driver Code"
        sender_type = "teams"
        sender: Sender = Sender()

        if sender_type == "teams":
            sender = TeamsSender()
        elif sender_type == "sms":
            sender = SMSSender()
        else:
            raise ValueError(f"Sender type {sender_type} not supported")

        sender.notify()
        ```

        ```title="Output"
        TEAMS NOTIFICATION: sup
        ```

        The ``Sender`` class acts as a guideline for the various **concrete creator subclasses** (``TeamsSender`` and ``SMSSender``). Both must implement the **abstract factory method** ``createNotification``, each creating their respective ``Notification`` product (``TeamsNotification`` or ``SMSNotification``). 

        A user can safely assume the functions in ``Sender`` will be available for any ``Sender`` subclasses. This enables them to extend the code and handle multiple subclasses, but never having to touch the central parent ``Sender`` class.

        !!! note "python specific implementation notes"

            In traditionally strongly-typed OOP languages, the **creator parent class** is an *abstract class* and the **product parent class** is an *interface*

            In python, we can duck type everything so theres multiple ways we can implement them: 
                - python protocol 
                - ABC library: indicate its an abstract class
                - regular duck typing 

## Analysis

### SOLID principles

- Single responsibility principle: the pattern utilizes the *concrete create subclasses* to extend supporting additional *product subclasses*. This means the primary *concrete class* does not need to be refactored when extending. 
- Open-Closed principle: (same as single responsibility principle) no need to edit primary *concrete class* when extending.
- Liskov substitution principle: because client depends on abstractions (concrete class and product interface), the subclasses can be replaced without breaking the workflow.
- Dependency inversion: actual creation of *products* are in the *concrete subclass* and depends on the abstraction set by the *abstract factory method*.  

### When to use?

- When you don't know beforehand the exact *Products* or types of the objects your code should work with. 
- When you want to provide users a way to extend its internal components

### Pros & Cons

Pros: 

- Decouples creation and usage (clients only interact with abstractions in parent classes)
- Supports Open/Closed principle (new products doesn't require changing existing code)
- Flexible and extensible
- Centralize object creation (all in factory method)
- Encourages single responsibility (each product subclass handles its own behavior)

Cons:

- Class explosion (need new *creator subclass* and *product subclass* for each new product)
- Increased complexity

## Resources

[:material-video-outline: Factory Method Example](https://www.youtube.com/watch?v=EdFq_JIThqM)

[:material-file-document-outline: Factory Method](https://refactoring.guru/design-patterns/factory-method)

