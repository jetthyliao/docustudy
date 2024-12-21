# Liskov Substitution Principle Example

Liskov substitution principle: every derived class should be substitutable for its parent class. 

???+ code "Invalid Example"
    === "C#"
        ```c# linenums="1" title="Invalid Example" hl_lines="41 45"
        // Base class: Bird
        public class Bird
        {
            public virtual void Fly()
            {
                Console.WriteLine("This bird is flying.");
            }
        }

        // Derived class: Sparrow
        public class Sparrow : Bird
        {
            public override void Fly()
            {
                Console.WriteLine("The sparrow is flying.");
            }
        }

        // Derived class: Penguin (A bird that can't fly)
        public class Penguin : Bird
        {
            public override void Fly()
            {
                // Violating Liskov Substitution Principle, as penguins can't fly.
                throw new InvalidOperationException("Penguins cannot fly!");
            }
        }

        public class Program
        {
            public static void MakeBirdFly(Bird bird)
            {
                bird.Fly();
            }

            public static void Main()
            {
                Bird sparrow = new Sparrow();
                Bird penguin = new Penguin();

                MakeBirdFly(sparrow);       // Output: The sparrow is flying.

                try
                {
                    MakeBirdFly(penguin);  // Output: Exception will be thrown
                }
                catch (InvalidOperationException ex)
                {
                    Console.WriteLine(ex.Message);  // Output: Penguins cannot fly!
                }
            }
        }
        ```
    This breaks the Liskov substitution principle since there are two derived classes ``Sparrow`` and ``Penguin`` and they cannot be used interchangeably.
    On line 41, ``MakeBirdFly`` can be called with the ``sparrow`` object being passed in without errors. However passing in a ``penguin`` object (line 45) will cause errors. 

???+ code "Valid Example"
    === "C#"
        ```c# linenums="1" title="Valid Example" hl_lines="40 41"
        // Base class: Bird
        public class Bird
        {
            public virtual void Move()
            {
                Console.WriteLine("This bird is moving");
            }
        }

        // Derived class: Sparrow (Can fly)
        public class Sparrow : Bird
        {
            public override void Move()
            {
                Console.WriteLine("The sparrow is flying.");
            }
        }

        // Derived class: Penguin (Cannot fly)
        public class Penguin : Bird
        {
            public override void Move()
            {
                Console.WriteLine("The penguin is swimming.");
            }
        }

        public class Program
        {
            public static void MakeBirdMove(Bird bird)
            {
                bird.Move();
            }

            public static void Main()
            {
                Bird sparrow = new Sparrow();
                Bird penguin = new Penguin();

                MakeBirdMove(sparrow);  // Output: The sparrow is flying.
                MakeBirdMove(penguin);  // Output: The penguin is swimming.
            }
        }
        ```

    This adheres to the Liskov substitution principle since all birds can move. This means whatever derived class that is created can be swapped into the function calls on line 40 and 41 without ever causing errors. 
