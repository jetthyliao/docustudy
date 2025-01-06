# Bridge

The **bridge** design pattern allows for large classes or sets of closely related classes to be split into two separate hierarchies - abstraction and implementation - which can be developed independently of each other.

- Both the abstraction and implementation can be an interface or an abstract class. 
- This pattern decouples the relationship between the abstraction and implementation. 

## Structure

Below is an example of the bridge structure

!!! tip "Adapter (Object) Structure UML"
    <figure markdown>
    </figure>

- **Abstraction:** high level control logic. Relies on implementation for the actual low level work.
- **Implementation:** Declares interface common to all concrete implementations. An abstraction can only communicate with the implementation objects through the methods declared here. 
- **Refined Abstraction:** Extends abstraction and adds more details on how code interacts with concrete implementation.
- **Concrete Implementation:** Actual code for the implementation 

## Bridge Example

Consider a program that represented the following shapes that come in a variety of colors: 

- There are two shapes (Rectangle and Triangle)
- There are two colors (Red and Blue)

Below is an example of designing this program with and without the bridge pattern. 

### Non Bridge Example

???+ Example "Non Bridge Example"

    === "C#"

        ```c# linenums="1" title="Example Code"
        public interface Shape
        {
            public void CreateShape();
        }

        public interface Rectangle : Shape
        {
            public void MakeForm();
        }

        public interface Triangle : Shape
        {
            public void MakeForm();
        }

        public class RedRectangle : Rectangle
        {
            public void CreateShape()
            {
                Console.Write("Creating ");
                MakeColor();
                MakeForm();
                Console.WriteLine("");
            }

            public void MakeColor()
            {
                Console.Write("Red ");
            }

            public void MakeForm()
            {
                Console.Write("Rectangle");
            }
        }

        public class BlueRectangle : Rectangle
        {
            public void CreateShape()
            {
                Console.Write("Creating ");
                MakeColor();
                MakeForm();
                Console.WriteLine("");
            }

            public void MakeColor()
            {
                Console.Write("Blue ");
            }

            public void MakeForm()
            {
                Console.Write("Rectangle");
            }
        }

        public class RedTriangle : Triangle
        {
            public void CreateShape()
            {
                Console.Write("Creating ");
                MakeColor();
                MakeForm();
                Console.WriteLine("");
            }

            public void MakeColor()
            {
                Console.Write("Red ");
            }

            public void MakeForm()
            {
                Console.Write("Triangle");
            }
        }

        public class BlueTriangle : Triangle
        {
            public void CreateShape()
            {
                Console.Write("Creating ");
                MakeColor();
                MakeForm();
                Console.WriteLine("");
            }

            public void MakeColor()
            {
                Console.Write("Blue ");
            }

            public void MakeForm()
            {
                Console.Write("Triangle");
            }
        }
        ```

        ```c# linenums="1" title="Driver Code"
        static void Main(string[] args)
        {
            Shape shape1 = new RedRectangle();
            Shape shape2 = new BlueTriangle();

            shape1.CreateShape();
            shape2.CreateShape();
        }
        ```
        
        ```title="Output"
        Creating Red Rectangle
        Creating Blue Triangle
        ```

    In this implementation the classes are in a hierarchical structure. This is a working example of the program as all four permutations of the shapes can be created (Red rectangle, blue rectangle, and red triangle blue triangle).

    The issue with this implementation is if the ``Rectangle`` class needed to be updated this could effect both the ``RedRectangle`` and ``BlueRectangle`` subclasses. 

    Additionally, if changes were made to the colors then both the ``Rectangle`` and ``Triangle`` classes may be effected.

    This is due to the hierarchy structure causing all the classes to be tightly coupled to one another. 

    This breaks the following SOLID principles: 

    - Single responsibility principle: changes to the ``Rectangle`` and ``Triangle`` classes can cascade down to the subclasses

### Bridge Example

???+ Example "Bridge Example"

    === "C#"

        ```c# linenums="1" title="Example Code" hl_lines="1-20 22-26"
        // Abstraction (Abstract Class)
        public abstract class Shape
        {
            protected Color color;

            public Shape(Color color)
            {
                this.color = color;
            }

            public abstract void MakeForm();

            public void CreateShape() 
            {
                Console.Write("Creating ");
                this.color.MakeColor();
                MakeForm();
                Console.WriteLine("");
            }
        }

        // Implementation (Interface)
        public interface Color
        {
            public void MakeColor();
        }

        // Refined Abstraction (Class)
        public class Rectangle : Shape
        {
            public Rectangle(Color color) : base(color) { }

            public override void MakeForm()
            {
                Console.Write("Rectangle");
            }
        }

        // Refined Abstraction (Class)
        public class Triangle : Shape
        {
            public Triangle(Color color) : base(color) { }

            public override void MakeForm()
            {
                Console.Write("Triangle");
            }
        }

        // Concrete Implementation (Class)
        public class Red : Color
        {
            public void MakeColor()
            {
                Console.Write("Red ");
            }
        }

        // Concrete Implementation (Class)
        public class Blue : Color
        {
            public void MakeColor()
            {
                Console.Write("Blue ");
            }
        }
        ```

        ```c# linenums="1" title="Driver Code"
        static void Main(string[] args)
        {
            Shape shape1 = new Rectangle(new Red());
            Shape shape2 = new Triangle(new Blue());

            shape1.CreateShape();
            shape2.CreateShape();
        }
        ```
        
        ```title="Output"
        Creating Red Rectangle
        Creating Blue Triangle
        ```
    
    This implementation fixes the issues in the last example by introducing the **abstraction** class ``Shape`` and the **implementation** interface ``Color``. 

    With the implementation and abstraction the form (rectangle and triangle) logic can be separated from the color (red and blue) logic. 

    This also means both of these can easily be extended (new forms and new colors added) without them breaking each other. 

    The following SOLID principles are now fixes from the previous example:

    - Single responsibility principle: the form and color are now decoupled from one another and won't effect each other. 
