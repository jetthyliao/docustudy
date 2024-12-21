# OOP Structure 

There are four main components used to create an OOP model

- [Functions/Methods](#functions): code logic to perform a specific task
- [Properties/Attributes](#properties): pure data 
- [Classes](#classes): blueprint of an object
- [Objects](#objects): instances of a class

## Functions

Functions are code-logic that performs some certain task. Here is a list of potential behaviors of a function.

- Print something
- Update a property
- Perform a task and return some data

???+ code "Function Example"
    === "C#"
        ```c# linenums="1" title="C# Function Example" 
        public void speak()
        {
            Console.WriteLine("Borf Borf");
        }
        ```
    === "Python"
        ```python linenums="1" title="Python Function Example"  
        def speak():
            print('Borf Borf')
        ```
    In this example we have a single function called ``speak``. The function prints out "Borf Borf" to the console.
## Properties 

Properties are data that are stored within an object.

???+ code "Properties Example" 
    === "C#"
        ```c# linenums="1" title="C# Properties Example" 
        public class Dog                
        {
            public string name;         // Property
            public Dog(string name)     // Constructor
            {
                this.name = name 
            }
        }
        ```
    === "Python"
        ```python linenums="1" title="Python Properties Example"  
        class Dog: 
            def __init__(self, name: str):
                # Creates property called name
                self.name = name            
        ```
    In this example we have a class called ``Dog`` with the property ``name``. 

## Classes 
 
Classes are user-defined data types. They act as a blueprint of how an object is suppose to look like in terms of the functions and properties it contains. Below is an example of a class.

???+ code "Class Example"
    === "C#"
        ```c# linenums="1" title="C# Class Example" 
        public class Dog                
        {
            public string name;         // Property
            public Dog(string name)     // Constructor
            {
                this.name = name 
            }
        }
        ```
    === "Python"
        ```python linenums="1" title="Python Class Example"  
        class Dog: 
            def __init__(self, name: str):
                # Creates property called name
                self.name = name            
        ```

    This example has a class called ``Dog`` to represent a dog. 

    The name of the dog is not explicitly stated in the class. This is because classes are a blueprint/model. If we made an object with this class, the name would have to be defined.

## Objects 

Objects are instances of a class with specific data. The class provides us with the blueprint and an object will use a class and provide it with actual data. Below is an example of an object.

???+ code "Object Example"

    === "C#"
        ```c# linenums="1" title="C# Class Example" 
        public class Dog                
        {
            public string name;         // Property
            public Dog(string name)     // Constructor
            {
                this.name = name 
            }

            public void speak()
            {
                if (this.name == "Sir Barkington")
                {
                    Console.WriteLine("BORF BORF");
                } else {
                    Console.WriteLine("ROOH ROOH");
                }

            }
        }

        Dog puppy1 = Dog("Sir Barkington");
        Dog puppy2 = Dog("Lady Barkington");

        puppy1.speak()  // Prints BORF BORF
        puppy2.speak()  // Prints ROOH ROOH
        ```
    === "Python"
        ```python linenums="1" title="Python Class Example"  
        class Dog: 
            def __init__(self, name: str):
                # Creates property called name
                self.name = name            

            def speak():
                if self.name == "Sir Barkington"
                    print("BORF BORF")
                elif self.name == "Lady Barkington"
                    print("ROOH ROOH")

        puppy1 = Dog('Sir Barkington')
        puppy2 = Dog('Lady Barkington')

        puppy1.speak()  # Prints BORF BORF
        puppy2.speak()  # Prints ROOH ROOH
        ```

    In this example, the ``Dog`` class is used to create two dog objects. Each is a different instance of the class. 

    The ``puppy1`` object uses the constructor to create a dog with the name Sir Barkington

    The ``puppy2`` object uses the constructor to create a dog with the name Lady Barkington

    When the speak method is invoked for these two objects, they will both print out different barks. This is because they are different instances with different ``name`` properties.



