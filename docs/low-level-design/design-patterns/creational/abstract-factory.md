# Abstract Factory

The **Abstract Factory** design pattern allows for the production of *families* of related objects without specifying their concrete classes. 

## Structure

Below is an example of the abstract factory structure

!!! tip "Abstract Factory Structure UML"
    <figure markdown>
        <!--
        ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-dark)
        ![Factory Method Diagram](../../assets/images/design-patterns/factory-method-light.svg#only-light)
        -->
    </figure>

- **Abstract Products:** abstract class which represents the distinct types of a product.
- **Concrete Products:** various implementations of the abstract products.
- **Abstract Factory:** interface which declares the method used to create the various products.
- **Concrete Factory:** implementation of the creation method. Each of these concrete factory corresponds to a **set** (or family) of concrete products.

## Abstract Factory Example

Imagine a program that simulates two computer component manufacturers that produce monitors and GPUs only. 

- Two companies: ASUS and MSI
- Components (or products) they produce: Monitors and GPUs

This is a good example of when to use the abstract factory design patterns since there are two different sets (or family) of products. 

- Set 1 = ASUS, Set 2 = MSI
- Products = monitors & GPUs

### Non Abstract Factory Example

???+ Example "Non Abstract Factory Example"

    === "C#"

        ```c# linenums="1" title="Example Code" hl_lines="50-61 63-74"
        // Product (Abstract Class)
        public abstract class Gpu
        {
            public abstract void Assemble();
        }

        // Product (Abstract Class)
        public abstract class Monitor
        {
            public abstract void Assemble();
        }


        // Product (Concrete Class)
        public class AsusGpu : Gpu
        {
            public override void Assemble()
            {
                Console.WriteLine("Assembling ASUS GPU");
            }
        }

        // Product (Concrete Class)
        public class AsusMonitor : Monitor
        {
            public override void Assemble()
            {
                Console.WriteLine("Assembling ASUS Monitor");
            }
        }

        // Product (Concrete Class)
        public class MsiGpu : Gpu
        {
            public override void Assemble()
            {
                Console.WriteLine("Assembling MSI GPU");
            }
        }

        // Product (Concrete Class)
        public class MsiMonitor : Monitor
        {
            public override void Assemble()
            {
                Console.WriteLine("Assembling MSI Monitor");
            }
        }

        public class AsusManufacturer 
        {
            public Gpu CreateGpu()
            {
                return new AsusGpu();
            }

            public Monitor CreateMonitor()
            {
                return new AsusMonitor();        
            }
        }

        public class MsiManufacturer
        {
            public Gpu CreateGpu()
            {
                return new MsiGpu();
            }

            public Monitor CreateMonitor()
            {
                return new MsiMonitor();
            }
        }
        ```

        ```c# linenums="1" title="Driver Code"
        static void Main(string[] args)
        {
            var desiredCompany = "MSI";

            if (desiredCompany == "ASUS")
            {
                AsusManufacturer asus = new AsusManufacturer();
                asus.CreateGpu();
                asus.CreateMonitor();
            }
            else if (desiredCompany == "MSI")
            {
                MsiManufacturer msi = new MsiManufacturer();
                msi.CreateGpu();
                msi.CreateMonitor();
            }
            else
            {
                throw new Exception("Invalid Company");
            }
        }
        ```

        ```title="Output"
        Assembling MSI GPU
        Assembling MSI Monitor 
        ```

    This implementation uses a design pattern similar to the factory pattern (not to be confused with the [**factory method**](factory-method.md) pattern). 
    
    - ``AsusManufacturer`` is a factory that contains methods to create ASUS products (GPU and Monitor)
    - ``MsiManufacturer`` is a factory that contains methods to create MSI products (GPU and Monitor)

    The issue with this implementation can be seen in the driver code. The code expects either a ``AsusManufacturer`` or ``MsiManufacturer`` object. Lets say the client code gets expanded with more behaviors that is dependent on the manufacturer classes, that code would need to continue to handle both type of manufacturer classes. 

    This issue becomes bigger when a new manufacturer type gets introduce. All the extended behavior would need to be updated again to handle a third manufacturer.

    This implementation breaks the following SOLID principles: 

    - Single responsibility principle: New reason to change = client code would now need to be updated if adding new family of products
    - Open-Closed principle: Adding a new family of products will break all client code

### Abstract Factory Example

???+ Example "Abstract Factory Example"

    === "C#"

        ```c# linenums="1" title="Example Code" 
        // Abstract Factory (Abstract Class)
        public abstract class Company
        {
            public abstract Gpu CreateGpu();
            public abstract Monitor CreateMonitor();
        }

        // Product (Abstract Class)
        public abstract class Gpu
        {
            public abstract void Assemble();
        }

        // Product (Abstract Class)
        public abstract class Monitor 
        {
            public abstract void Assemble();
        }

        // Factory (Concrete Class) 
        public class AsusManufacturer : Company
        {
            public override Gpu CreateGpu()
            {
                return new AsusGpu();
            }

            public override Monitor CreateMonitor()
            {
                return new AsusMonitor();        
            }
        }

        // Factory (Concrete Class)
        public class MsiManufacturer : Company
        {
            public override Gpu CreateGpu()
            {
                return new MsiGpu();
            }

            public override Monitor CreateMonitor()
            {
                return new MsiMonitor();
            }
        }

        // Product (Concrete Class)
        public class AsusGpu : Gpu
        {
            public override void Assemble()
            {
                Console.WriteLine("Assembling ASUS GPU");
            }
        }

        // Product (Concrete Class)
        public class AsusMonitor : Monitor 
        {
            public override void Assemble()
            {
                Console.WriteLine("Assembling ASUS Monitor");
            }
        }
        
        // Product (Concrete Class)
        public class MsiGpu : Gpu
        {
            public override void Assemble()
            {
                Console.WriteLine("Assembling MSI GPU");
            }
        }

        // Product (Concrete Class)
        public class MsiMonitor : Monitor 
        {
            public override void Assemble()
            {
                Console.WriteLine("Assembling MSI Monitor");
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="4 8 13 20-21"
        static void Main(string[] args)
        {
            var desiredCompany = "MSI";
            Company company;

            if (desiredCompany == "ASUS")
            {
                company = new AsusManufacturer();

            }
            else if (desiredCompany == "MSI")
            {
                company = new MsiManufacturer();
            }
            else
            {
                throw new Exception("Invalid Company");
            }

            company.CreateGpu();
            company.CreateMonitor();
        }
        ``` 

        ```title="Output"
        Assembling MSI GPU
        Assembling MSI Monitor
        ```

    This example introduces the **abstract factory** ``Company`` which acts as the interface to build a family of products. 

    The ``AsusManufacturer`` and ``MsiManufacturer`` classes are subclasses of the **abstract factory** and handles creating the respective set of products. 

    In the driver code on line 20-21, either manufacturer subclass can be assigned to the ``company`` variable without breaking the rest of the code. This is due to the abstract factory. The abstract factory ensures that each implementation has all the methods the rest of the code expects to see. 

    This implementation fixes the following SOLID principles: 

    - Single responsibility principle: new family of products can be added but it won't break the driver code
    - Open-Closed principle: family of products can be swapped without breaking (line 20-21 in driver code)
     

## When To Use 

- Usually, most people will start with a [**factory method**](factory-method.md)
- Use when code needs to work with various families of related product, but you don't want it to depend on the concrete classes of those products (might be unknown beforehand or simply want to allow for further extensibility)
- When you have a set of factory methods, abstract factory might help reorganize all of your factories

## Resources

[:material-video-outline: Abstract Factory Example](https://www.youtube.com/watch?v=QNpwWkdFvgQ)

[:material-file-document-outline: Abstract Factory](https://refactoring.guru/design-patterns/abstract-factory)

