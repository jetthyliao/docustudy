# Composite 

The **composite** design pattern composes objects into a tree structure that can be interacted with as if they are individual objects. 

The client can interact with this composition of objects uniformly meaning a client can interact with the objects whether its a subtree or a leaf.

## Structure

Below is an example of the composite structure

!!! tip "Composite (Object) Structure UML"
    <figure markdown>
    </figure>

- **Component:** describe operations that are common to both simple and complex elements of the tree
- **Leaf:** basic element of tree that does not have sub-elements. They tend to do most of the real work 
    - Usually does most of the real work as it does not have to deal with delegating actions. 
- **Composite:** element that contains sub-elements (leaves or other composites). 
    - On requests, it delegates work to its sub-elements, aggregates the results, and return it back to the client

## Composite Example

Imagine a program used to track products sold at an amazon factory. The program contains two different types of objects: 

- Boxes 
- Products

Each box can hold smaller boxes and/or products. The desired behavior for this program is whether a client is interacting with a user or a box, the client should be able to determine the price of all its content (sub-boxes and products).

Below is an implementation of this program with using the composite design pattern and without. 

### Non Composite Example

???+ Example "Non Composite Example"

    === "C#"

        ```c# linenums="1" title="Example Code"
        public class Box
        {
            public string name { get; set; }
            private List<Box> boxes;
            private List<Product> products;

            public Box(List<Box> boxes, List<Product> products, string name)
            {
                this.name = name;
                this.boxes = boxes;
                this.products = products;
            }

            public double CalculatePrice()
            {
                double total = 0;
                boxes.ForEach(box => { total += box.CalculatePrice(); });
                products.ForEach(product => { total += product.price; });

                return total;
            }
        }

        public interface Product 
        {
            public string name { get; set; }
            public double price { get; set; }
        }

        public class Book : Product
        {
            public string name { get; set; }
            public double price { get; set; }

            public Book(string name, double price)
            {
                this.name = name;
                this.price = price;
            }
        }

        public class VideoGame : Product
        {
            public string name { get; set; }
            public double price { get; set; }

            public VideoGame(string name, double price)
            {
                this.name = name;
                this.price = price;
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="1-4 6-9 34-35"
        public static void SetupOrder(Box box)
        {
            Console.WriteLine("Price of {0}: {1}", box.name, box.CalculatePrice());
        }

        public static void SetupOrder(Product product)
        {
            Console.WriteLine("Price of {0}: {1}", product.name, product.price);
        }

        static void Main(string[] args)
        {
            VideoGame game1 = new VideoGame("Pokemon", 60);
            VideoGame game2 = new VideoGame("Zelda", 59);
            VideoGame game3 = new VideoGame("Pikmin", 27);
            Book book1 = new Book("Anathem", 12);
            Book book2 = new Book("Eragon", 16);

            // Constructing Small Box
            List<Box> amazonListMiniBoxList = new List<Box>();
            List<Product> amazonListMiniProductList = new List<Product>();
            amazonListMiniProductList.Add(book2);
            Box amazonListMiniBox = new Box(amazonListMiniBoxList, amazonListMiniProductList, "Amazon Mini Box");

            // Constructing Big Box
            List<Box> amazonBoxBoxList = new List<Box>();
            amazonBoxBoxList.Add(amazonListMiniBox); // Putting small box into big box
            List<Product> amazonBoxProductList = new List<Product>();
            amazonBoxProductList.Add(game1);
            amazonBoxProductList.Add(game2);
            amazonBoxProductList.Add(book1);
            Box amazonBox = new Box(amazonBoxBoxList, amazonBoxProductList, "Amazon Box");
            
            SetupOrder(amazonBox);
            SetupOrder(game3);
        }
        ```
        
        ```title="Output"
        Price of Amazon Box: 147
        Price of Pikmin: 27
        ```
    
    In this example there are two classes to represent the two type of objects that will be packaged in the amazon factory, ``Box`` and ``Product``. 

    The ``Box`` class has a ``boxes`` field and a ``products`` field to represent that both smaller boxes and products can be placed within a box. 

    The problem with this implementation can be seen in the driver code. The client code is tightly coupled with the specific class implementations. This is evident in the two methods in lines 1-4 and 6-9 in the driver code which demonstrates the use of method overloading to handle the two class types. 

    This code also breaks the following SOLID principles: 

    - Open-close principle: introducing a new element type into the app will break the currently existing code. For example what if a box can also contain a tracker. A new class called ``Tracker`` would be made but the ``Box`` class would have to be refactored to include calculating the price for that as well. 

### Composite Example

???+ Example "Composite Example"

    === "C#"

        ```c# linenums="1" title="Example Code" hl_lines="1-7"
        // Component (Interface)
        public interface Box
        {
            public string name { get; set; }

            public double CalculatePrice();
        }

        // Composite (Class)
        public class CompositeBox : Box
        {
            public string name { get; set; }  
            private List<Box> children = new List<Box>();

            public CompositeBox(List<Box> boxes, string name)
            {
                this.name = name;
                children.AddRange(boxes);
            }

            public double CalculatePrice()
            {
                // Handles aggregating prices from sub composite boxes and products
                double total = 0;
                children.ForEach(c => total += c.CalculatePrice());
                return total;
            }
        }

        // Interface for Leafs (Interface)
        public interface Product : Box
        {
            public double price { get; set; }
        }

        // Leaf (Class)
        public class Book : Product
        {
            public string name { get; set; }
            public double price { get; set; }

            public Book(string name, double price)
            {
                this.name = name;
                this.price = price;
            }

            public double CalculatePrice()
            {
                return price;
            }
        }

        // Leaf (Class)
        public class VideoGame : Product
        {
            public string name { get; set; }
            public double price { get; set; }

            public VideoGame(string name, double price)
            {
                this.name = name;
                this.price = price;
            }

            public double CalculatePrice()
            {
                return price;
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="1-4"
        public static void SetupOrder(Box box)
        {
            Console.WriteLine("Price of {0}: {1}", box.name, box.CalculatePrice());
        }

        static void Main(string[] args)
        {
            // Products
            VideoGame game1 = new VideoGame("Pokemon", 60);
            VideoGame game2 = new VideoGame("Zelda", 59);
            VideoGame game3 = new VideoGame("Pikmin", 27);
            Book book1 = new Book("Anathem", 12);
            Book book2 = new Book("Eragon", 16);

            // Constructing Small Box
            List<Box> amazonListMini = new List<Box>();
            amazonListMini.Add(book2);
            Box amazonBoxMini = new CompositeBox(amazonListMini, "Amazon Mini Box");

            // Constructing Big Box
            List<Box> amazonList = new List<Box>();
            amazonList.Add(game1);
            amazonList.Add(game2);
            amazonList.Add(book1);
            amazonList.Add(amazonBoxMini); // Putting small box into big box
            Box amazonBox = new CompositeBox(amazonList, "Amazon Box");

            SetupOrder(amazonBox);
            SetupOrder(game3);
        }
        ```
        
        ```title="Output"
        Price of Amazon Box: 147
        Price of Pikmin: 27
        ```
    
    In this example the composite design pattern is used. With the implementation of the ``Box`` **component**, each object (whether its a box or a product) will now be expected to give the name and total price of the object. 

    The ``CompositeBox`` still handles aggregating price from sub-boxes and products, but thanks to the ``Box`` interface the interaction between the two is now uniformed. 

    This can be seen in the driver code. In the driver code there is a ``amazonBox`` object which is a box that contains a nested box and products. When running the ``SetupOrder`` method, the method behaves properly whether passing in a single product or a potentially nested box. 

    This solves the issue in the previous example that broke the SOLID principles: 

    - Open-close principle: If the ``Tracker`` class is added now, nothing would need to be refactored. It would just need to inherit the ``Box`` **component**.
    
## When To Use

- When dealing with objects that naturally fit a tree-like structure
- When the client needs to interact with simple and complex elements uniformly

## Resources

[:material-file-document-outline: Composite](https://refactoring.guru/design-patterns/composite)

[:material-file-document-outline: Composite - GFG](https://www.geeksforgeeks.org/composite-design-pattern-in-java/)

[:material-video-outline: Composite](https://www.youtube.com/watch?v=oo9AsGqnisk)


