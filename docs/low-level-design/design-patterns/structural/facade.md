# Facade

The **Facade** design pattern provides a simplified interface for a library, framework, or any other complex set of classes. 

The facade pattern sets up an interface that will tie to some complex subsystem. This way, a user only needs to interact with the facade interface instead of handling all of the setup directly. This helps simplifies the code as well as decouples it from the complex subsystem. 

## Structure

Below is an example of the facade structure

!!! tip "Facade (Object) Structure UML"
    <figure markdown>
    </figure>

- **Facade:** class that deals with receiving client requests and redirecting it to the corresponding functionalities of the complex **subsystem** class
- **Additional Facade:** a class to breakdown the main **facade** class. This is done to break up unrelated features to avoid making the **facade** class into another complex subsystem.
- **Subsystem Class:** this represents a group of objects with complex implementation details and functionalities (ex. 3rd party library or framework)

## Facade Example

Imagine a program that grabs a persons tax records and generates their tax deductions for the year. This program utilizes the following set of classes to calculate the deduction: 

- Brokerage
- Bank
- Crypto
- Property
- StudentLoans

To calculate the deduction each of these classes must be implemented and the deductions pulled from it. 

Below are two implementations of this program, one with the facade pattern and one without. 

### Non Facade Example

???+ Example "Non Facade Example"

    === "C#"

        ```c# linenums="1" title="Example Code" 
        // Subsystem Class
        public class Brokerage {}

        // Subsystem Class
        public class Bank {}

        // Subsystem Class
        public class Crypto {}

        // Subsystem Class
        public class Property {}

        // Subsystem Class
        public class StudentLoans {}
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="3-7"
        public static void Main(string[] args)
        {
            Brokerage brokerage = new Brokerage();
            Bank bank = new Bank();
            Crypto crypto = new Crypto();
            Property property = new Property();
            StudentLoans studentloans = new StudentLoans();

            // Imagine all the classes above had to be used to calculate this value
            double deductions = 100;

            Console.WriteLine(deductions);
        }
        ```
        
        ```title="Output"
        100
        ```

    In this example all the classes related to generating the deductions had to be implemented in the driver code. 

    If other programmers working on the codebase need to get deductions, they too will need to implement all the following classes or reference it here. 

    This adds a lot of complexity which can be masked by using the facade pattern.  
    
### Facade Example

???+ Example "Facade Example"

    === "C#"

        ```c# linenums="1" title="Example Code" hl_lines="17-31"
        // Subsystem Class
        public class Brokerage {}

        // Subsystem Class
        public class Bank {}

        // Subsystem Class
        public class Crypto {}

        // Subsystem Class
        public class Property {}

        // Subsystem Class
        public class StudentLoans {}

        // Facade Class
        public class TaxSoftware {
            public double GetDeductions()
            {
                Brokerage brokerage = new Brokerage();
                Bank bank = new Bank();
                Crypto crypto = new Crypto();
                Property property = new Property();
                StudentLoans studentloans = new StudentLoans();

                // Imagine all the classes above had to be used to calculate this value
                double deductions = 100;

                return deductions;
            }
        } 
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="3-5 7-9"
        TaxSoftware freeUsaTax = new good.TaxSoftware();
        Console.WriteLine(freeUsaTax.GetDeductions());
        ```
        
        ```title="Output"
        100
        ```
    
    In this example the facade pattern is used, with the addition of the ``TaxSoftware`` class.

    Getting deductions is now simplified to using the ``TaxSoftware`` class and the implementation of all the other classes is of no concern. 

## When To Use

- To create a simple and limited interface to a complex subsystem
- To structure subsystems into layers (facade for one part of the subsystems, etc). This can help decouple the code as well. 

## Resources

[:material-file-document-outline: Facade](https://refactoring.guru/design-patterns/facade)



