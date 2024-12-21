# Abstract Factory

The **Abstract Factory** design pattern allows for the production of *families* of related objects without specifying their concrete classes.

## Problem

Lets say there's a webapp representing a furniture store. The furniture has three types of furniture that they sell:

- Sofa
- Chairs
- Tables

For each furniture type they sell, they also have the following styles:

- Modern
- Victorian

A webapp therefore would need an object for each of the furniture type. Within each of these furniture objects there would be a method that would have logic for creating the different furniture styles. 

What happens when a new furniture type needs to be added? Same as **factory methods** if a large portion of the code only handles for specifically Sofa, Chairs, and Tables object, those would need to be refactored to handle any new objects that needs to be added. Not only that, if a new style is implemented, it needs to handle those changes as well. 

## Solution

Utilizing an **abstract factory** an interface can be created. Subclasses which represents the different furniture types can be made from the abstract factory. The **abstract factory** interface describes the functionalities shared between furniture types, and the styles that it expects to have. 

This way, all future code can reference the **abstract factory** interface, but new implementations can be created just be adding new subclasses. 

# Structure

Below is an example of the abstract factory structure

!!! tip "Abstract Factory Structure UML"
    <figure markdown>
      <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-dark) -->
      <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method-light.svg#only-light) -->
    </figure>

- **Abstract Products:** interface which represents the distinct types of a product 
- **Concrete Products:** various implementations of the abstract products. (Concrete products = furniture types, abstract products = furniture style). 
- **Abstract Factory:** interface that declares methods for each abstract product.
- **Concrete Factory:** implementation of creation methods declared in abstract factory. Each of these concrete factory corresponds to a specific concrete product.

# Abstract Factory Example

Imagine a computer component manufacturer simulator. The code will represent the following:

- Two companies, ASUS and MSI
- Components (or products) they produce, which will be GPUs and monitors.

Below are two implementation of this computer component manufacturer simulator. 

1. Non Abstract Factory Solution
2. Abstract Factory Solution

### Non Abstract Factory Example

???+ Example "Non Abstract Factory Example"
    ```c# linenums="1" title="Example Code"
    public class AsusManufacturer
    {
        public Component createComponent(string type)
        {
            if (type == "GPU")
            {
                return new AsusGpu();
            }
            else if (type == "Monitor")
            {
                return new AsusMonitor();
            }

            return null;
        }
    }

    public class MsiManufacturer
    {
        public Component createComponent(string type)
        {
            if (type == "GPU")
            {
                return new MsiGpu();
            }
            else if (type == "Monitor")
            {
                return new MsiMonitor();
            }

            return null;
        }
    }

    public class AsusMonitor : Component
    {
        public override void assemble()
        {
            Console.WriteLine("Assembling ASUS Monitor");
        }
    }

    public class MsiMonitor : Component
    {
        public override void assemble()
        {
            Console.WriteLine("Assembling Msi Monitor");
        }
    }

    public class AsusGpu : Component
    {
        public override void assemble()
        {
            Console.WriteLine("Assembling ASUS GPU");
        }
    }

    public class MsiGpu : Component
    {
        public override void assemble()
        {
            Console.WriteLine("Assembling MSI GPU");
        }
    }

    public abstract class Component
    {
        public abstract void assemble();
    }
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main()
    {
        Company asus = new AsusManufacturer();
        Component asusGpu = asus.createComponent("GPU");
        asusGpu.assemble();

        Company msi = new MsiManufacturer();
        Component msiGpu = msi.createComponent("GPU");
        msiGpu.assemble();
    } 
    ```

    ```title="Output"
    Assembling ASUS GPU
    Assembling MSI GPU
    ```

    In this example there are the following classes: 

    - ``AsusManufacturer`` a Concrete Factory class
    - ``MsiManufacturer`` a Concrete Factory class
    - ``Component`` an Abstract Product class
    - ``AsusGpu`` a Concrete Product class
    - ``MsiGpu`` a Concrete Product class
    - ``AsusMonitor`` a Concrete Product class
    - ``MsiMonitor`` a Concrete Product class

    This design pattern fits the [**factory method**](factory-method.md) pattern. 
    
    - Asus createComponent handles both monitors and GPU (factory method design)
    - MSI createComponent handles both monitors and GPU (factory method design)

    The issue with this implementation is that if a developer only assumed the ``MsiManufacturer`` class existed and built their webapp around this assumption, it would create a tight coupling. 

    When a new manufacturer needs to be added like ``AsusManufacturer`` all code that assumed ``MsiManufacturer`` would need to be updated to handle both. 

    This violates the open/closed principle.

### Abstract Factory Example

???+ Example "Abstract Factory Example"
    ```c# linenums="1" title="Example Code" 
    public abstract class Company
    {
        public abstract Gpu createGpu();
        public abstract Monitor createMonitor();
    }

    public class AsusManufacturer : Company
    {
        public override Gpu createGpu()
        {
            return new AsusGpu();
        }

        public override Monitor createMonitor()
        {
            return new AsusMonitor();
        }
    }

    public class MsiManufacturer : Company
    {
        public override Gpu createGpu()
        {
            return new MsiGpu();
        }

        public override Monitor createMonitor()
        {
            return new MsiMonitor();
        }
    }

    public class AsusMonitor : Monitor
    {
        public override void assemble()
        {
            Console.WriteLine("Assembling ASUS Monitor");
        }
    }

    public class MsiMonitor : Monitor
    {
        public override void assemble()
        {
            Console.WriteLine("Assembling Msi Monitor");
        }
    }

    public class AsusGpu : Gpu
    {
        public override void assemble()
        {
            Console.WriteLine("Assembling ASUS GPU");
        }
    }

    public class MsiGpu : Gpu
    {
        public override void assemble()
        {
            Console.WriteLine("Assembling MSI GPU");
        }
    }

    public abstract class Gpu
    {
        public abstract void assemble();
    }

    public abstract class Monitor
    {
        public abstract void assemble();
    }
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main()
    {
        Company asus = new AsusManufacturer();
        Gpu asusGpu = asus.createGpu();
        asusGpu.assemble();
        
        Company msi = new MsiManufacturer();
        Gpu msiGpu = msi.createGpu();
        msiGpu.assemble(); 
    } 
    ``` 

    ```title="Output"
    Assembling ASUS GPU
    Assembling MSI GPU
    ```

    Using the abstract factory design there are the following classes: 

    - ``Company`` the Abstract Factory
    - ``AsusManufacturer`` a Concrete Factory class
    - ``MsiManufacturer`` a Concrete Factory class
    - ``Gpu`` an Abstract Product class
    - ``AsusGpu`` a Concrete Product class
    - ``MsiGpu`` a Concrete Product class
    - ``Monitor`` an Abstract Product class
    - ``AsusMonitor`` a Concrete Product class
    - ``MsiMonitor`` a Concrete Product class 

    Now there is an **abstract factory** ``Company`` which can be used instead of specifying the manufacturer.

    Within the **abstract factory** the different types of components are declared. This ensures that any subclass of the ``Company`` class creates the right components.

    Now the entire code can be created off of the ``Company`` class and new subclasses can be created without breaking everything.

    
## When To Use 

- Usually, most people will start with a [**factory method**](factory-method.md)
- Use when code needs to work with various families of related product, but you don't want it to depend on the concrete classes of those products (might be unknown beforehand or simply want to allow for further extensibility)
- When you have a set of factory methods, abstract factory might help reorganize all of your factories

## Resources

[:material-video-outline: Abstract Factory Example](https://www.youtube.com/watch?v=QNpwWkdFvgQ)

[:material-file-document-outline: Abstract Factory](https://refactoring.guru/design-patterns/abstract-factory)

