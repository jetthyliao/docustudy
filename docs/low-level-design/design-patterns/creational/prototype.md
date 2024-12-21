# Prototype

The **prototype** design patterns allow for copying existing objects without making the code dependent on the classes. 

## Problem

Lets say a user wants to copy an object. This is normally done by creating a new instance and copying the fields over. However there are a few issues with doing it this way: 

- What if some of the fields are private (can't access and thus can't copy)
- The class must be known to create a duplicate (dependency)
- Sometimes only an interface is exposed and the class is unknown

These scenarios stops an object from being copied trivially. 

## Solution 

The prototype design pattern delegates the cloning process to the actual objects that are being cloned. The pattern uses a common interface for all objects that support cloning. 

The interface outlines cloning must be implemented. The objects created from the interface all implement the cloning. 

# Structure

Below is an example of the structure of a prototype design pattern. 

!!! tip "Prototype Structure UML"
    <figure markdown> 
    </figure>

- **Prototype:** interface that declares cloning method (usually just this single cloning method in the prototype).
- **Concrete Prototype:** class that implements cloning method. This is a normal object, but contains function that handles cloning of itself.
- **Subclass Prototype:** all children of the concrete also implement cloning. 

# Prototype Example

Lets say there is a program that kept track a list of shapes. There are two different types of shapes: 

1. Circles
2. Rectangles

Below are three implementations of this program: 

1. Non Prototype Example 
2. Prototype Example
3. Prototype Registry Example

### Non Prototype Example

???+ Example "Non Prototype Example"
    ```c# linenums="1" title="Example Code"
    public class Rectangle
    {
        public string Color;
        private int Width;
        private int Height;
        public int PosX { get; set; } = 0;
        public int PosY { get; set; } = 0;

        public Rectangle(string color, int width, int height)
        {
            this.Color = color;
            this.Width = width;
            this.Height = height;
        }

        public void getDetails()
        {
            Console.WriteLine("{0} Rectangle ({1} x {2}) is at ({3}, {4})",
                    this.Color, this.Height, this.Width, this.PosX, this.PosY);
        }
    }
    ```

    ```c# linenums="1" title="Driver Code" hl_lines="8"
    public void Main()
    {
        Rectangle rect1 = new Rectangle("Yellow", 20, 20);
        rect1.PosX = 100;
        rect1.PosY = 100;
        rect1.getDetails();

        Rectangle rect2 = new Rectangle(rect1.Color, rect1.Width, rect1.Height); // FAIL
        // Rectangle rect2 = new Rectangle(rect1.Color, 20, 20); 
        rect2.PosX = rect1.PosX;
        rect2.PosY = rect1.PosY;
        rect2.getDetails();
         
    } 
    ```

    ```title="Output"
    Yellow Rectangle (20 x 20) is at (100, 100)
    --- CODE FAIL ---
    ```

    In this example cloning a rectangle object results in a failure. This is due to the width and hieght properties in the object being private. 

    In line 8 of the driver code, the width and height from ``rect1`` is being read, but since it is private there is no way to read that value to perform the copy. Either the values must be known (not always possible) or its impossible.

### Prototype Example

???+ Example "Prototype Example"
    ```c# linenums="1" title="Example Code"
    public interface IShape
    {
        IShape clone();
    }

    public class Rectangle : IShape
    {
        public string Color;
        private int Width;
        private int Height;
        public int PosX { get; set; } = 0;
        public int PosY { get; set; } = 0;

        public Rectangle(string color, int width, int height)
        {
            this.Color = color;
            this.Width = width;
            this.Height = height;
        }

        public IShape clone()
        {
            // Returns a SHALLOW copy of rectangle object
            return (IShape)this.MemberwiseClone();
        }

        public void getDetails()
        {
            Console.WriteLine("{0} Rectangle ({1} x {2}) is at ({3}, {4})", 
                    this.Color, this.Height, this.Width, this.PosX, this.PosY);
        }
    }

    public class Circle : IShape
    {
        public string Color;
        private int Radius;
        public int PosX { get; set; } = 0;
        public int PosY { get; set; } = 0; 

        public Circle(string color, int radius)
        {
            this.Color = color;
            this.Radius = radius;
        }

        public IShape clone()
        {
            // Returns a SHALLOW copy of circle object
            return (IShape)this.MemberwiseClone();
        }

        public void getDetails()
        {
            Console.WriteLine("{0} Circle ({1} R) is at ({2}, {3})", 
                    this.Color, this.Radius, this.PosX, this.PosY);
        } 
    }
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main() 
    {
        Rectangle rect1 = new Rectangle("Red", 20, 30);
        rect1.PosX = 40;
        rect1.PosY = 120;
        rect1.getDetails();

        Rectangle rect2 = (Rectangle)rect1.clone();
        rect2.color = "Blue"
        rect2.PosX = 0;
        rect2.getDetails();

        Console.WriteLine("------------------");
        
        Circle circ1 = new Circle("Green", 5);
        circ1.PosX = 80;
        circ1.PosY = 80;
        circ1.getDetails(); 

        Circle circ2 = (Circle)circ1.clone();
        circ2.Color = "Purple"
        circ2.PosY = 0;
        circ2.getDetails(); 
         
    }
    ```

    ```title="Output"
    Red Rectangle (20 x 30) is at (40, 120)
    Blue Rectangle (20 x 30) is at (0, 120)
    ------------------ 
    Green Circle (5 R) is at (80, 80)
    Yellow Circle (5 R) is at (80, 0)
    ```

    In this example the ``IShape`` interface ensures that all subclasses of ``IShape`` implement their own cloning function. 

    Using the cloning function, the private ``width`` and ``height`` properties are copied over correctly. 

### Prototype + Registry Example

???+ Example "Prototype Example"
    ```c# linenums="1" title="Example Code"
    public interface IShape
    {
        IShape clone();
    }

    public class Rectangle : IShape
    {
        public string Color;
        private int Width;
        private int Height;
        public int PosX { get; set; } = 0;
        public int PosY { get; set; } = 0;

        public Rectangle(string color, int width, int height)
        {
            this.Color = color;
            this.Width = width;
            this.Height = height;
        }

        public IShape clone()
        {
            // Returns a SHALLOW copy of rectangle object
            return (IShape)this.MemberwiseClone();
        }

        public void getDetails()
        {
            Console.WriteLine("{0} Rectangle ({1} x {2}) is at ({3}, {4})", 
                    this.Color, this.Height, this.Width, this.PosX, this.PosY);
        }
    }

    public class Circle : IShape
    {
        public string Color;
        private int Radius;
        public int PosX { get; set; } = 0;
        public int PosY { get; set; } = 0; 

        public Circle(string color, int radius)
        {
            this.Color = color;
            this.Radius = radius;
        }

        public IShape clone()
        {
            // Returns a SHALLOW copy of circle object
            return (IShape)this.MemberwiseClone();
        }

        public void getDetails()
        {
            Console.WriteLine("{0} Circle ({1} R) is at ({2}, {3})", 
                    this.Color, this.Radius, this.PosX, this.PosY);
        } 
    }
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main() 
    {
        Rectangle rect1 = new Rectangle("Red", 20, 30);
        rect1.PosX = 40;
        rect1.PosY = 120;
        rect1.getDetails();

        Rectangle rect2 = (Rectangle)rect1.clone();
        rect2.color = "Blue"
        rect2.PosX = 0;
        rect2.getDetails();

        Console.WriteLine("------------------");
        
        Circle circ1 = new Circle("Green", 5);
        circ1.PosX = 80;
        circ1.PosY = 80;
        circ1.getDetails(); 

        Circle circ2 = (Circle)circ1.clone();
        circ2.Color = "Purple"
        circ2.PosY = 0;
        circ2.getDetails(); 
         
    }
    ```

    ```title="Output"
    Red Rectangle (20 x 30) is at (40, 120)
    Blue Rectangle (20 x 30) is at (0, 120)
    ------------------ 
    Green Circle (5 R) is at (80, 80)
    Yellow Circle (5 R) is at (80, 0)
    ```

    In this example the ``IShape`` interface ensures that all subclasses of ``IShape`` implement their own cloning function. 

    Using the cloning function, the private ``width`` and ``height`` properties are copied over correctly. 

### Prototype + Registry Example

???+ Example "Prototype Example"
    ```c# linenums="1" title="Example Code"
    public interface IShape
    {
        IShape clone();
    }

    public class Rectangle : IShape
    {
        public string Color;
        private int Width;
        private int Height;
        public int PosX { get; set; } = 0;
        public int PosY { get; set; } = 0;

        public Rectangle(string color, int width, int height)
        {
            this.Color = color;
            this.Width = width;
            this.Height = height;
        }

        public IShape clone()
        {
            // Returns a SHALLOW copy of rectangle object
            return (IShape)this.MemberwiseClone();
        }

        public void getDetails()
        {
            Console.WriteLine("{0} Rectangle ({1} x {2}) is at ({3}, {4})", 
                    this.Color, this.Height, this.Width, this.PosX, this.PosY);
        }
    }

    public class Circle : IShape
    {
        public string Color;
        private int Radius;
        public int PosX { get; set; } = 0;
        public int PosY { get; set; } = 0; 

        public Circle(string color, int radius)
        {
            this.Color = color;
            this.Radius = radius;
        }

        public IShape clone()
        {
            // Returns a SHALLOW copy of circle object
            return (IShape)this.MemberwiseClone();
        }

        public void getDetails()
        {
            Console.WriteLine("{0} Circle ({1} R) is at ({2}, {3})", 
                    this.Color, this.Radius, this.PosX, this.PosY);
        } 
    }

    public class ShapeManager
    {
        private Dictionary<string, IShape> shapes = new Dictionary<string, IShape>();

        public IShape this[string key]
        {
            get { return shapes[key] }
            set { shapes.Add(key, value); }
        }
    }
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main() 
    {
        ShapeManager shapes = new ShapeManager();
        Rectangle rect1 = new Rectangle("Red", 20, 30);
        rect1.PosX = 40;
        rect1.PosY = 120;
        rect1.getDetails();
        shapes["rect-001"] = rect1;

        Circle circ1 = new Circle("Green", 5);
        circ1.PosX = 80;
        circ1.PosY = 80;
        circ1.getDetails();  
        shapes["circ-001] = circ2;

        Console.WriteLine("------------------");
 
        Rectangle rect2 = (Rectangle)shapes["rect-001"].clone();
        rect2.color = "Blue"
        rect2.PosX = 0;
        rect2.getDetails();

        Circle circ2 = (Circle)shapes["circ-001"].clone();
        circ2.Color = "Purple"
        circ2.PosY = 0;
        circ2.getDetails(); 
    }
    ```

    ```title="Output"
    Red Rectangle (20 x 30) is at (40, 120)
    Green Circle (5 R) is at (80, 80)
    ------------------ 
    Blue Rectangle (20 x 30) is at (0, 120)
    Yellow Circle (5 R) is at (80, 0)
    ```
    
    This example is similar to the basic prototype solution except there is now a manager that handles all shapes. This is the true power of prototypes as previous knowledge of different shape types were not needed to construct the manager. 

## When To Use 

- When code shouldn't depend on the concrete classes of objects needed to be copied.
- Note: prototype implementation with a deep copy behaves very closely to an abstract/factory method. If the goal of the code is to create completely new copies (instances and all) of an object, an abstract factory might be better.

## Resources

[:material-file-document-outline: Prototype Method Example](https://www.dotnettricks.com/learn/designpatterns/prototype-design-pattern-dotnet)

[:material-file-document-outline: Prototype](https://refactoring.guru/design-patterns/factory-method)

