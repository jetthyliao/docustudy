# Single Responsibility Principle Example

Single responsibility principle: a class should have one, and only one, reason to change. Each class should have one sole purpose (responsibility) and not be filled with excessive functionality. 

???+ code "Invalid Example"
    === "C#"
        ```c# linenums="1" title="Invalid Example"
        public class Product
        {
            private string title;
            private string type;
            private decimal price;

            public Product(string title, string type, decimal price)
            {
                this.title = title;
                this.type = type;
                this.price = price;
            }

            public void GetPrice()
            {
                Console.WriteLine(price);
            }

            public void GetTitle()
            {
                Console.WriteLine(title);
            }

            public void CalculateTax()
            {
                // Special equation to calculate tax based on product type
            }
        }

        class Program
        {
            static void Main(string[] args)
            {
                Product toy = new Product("RC Car", "Toy", 42.99M);
                toy.GetPrice();
                toy.GetTitle();
                toy.CalculateTax();

                Product drizzank = new Product("Wine", "Liquor", 12.99M);
                drizzank.GetPrice();
                drizzank.GetTitle();
                drizzank.CalculateTax();
            }
        }
        ```
        ```title="Output"
        42.99
        RC Car
        "Calculate Tax for Toy Function..."
        12.99
        Wine
        "Calculate Tax for Liquor Function..."
        ```

    This breaks the single responsibility principle because there are now multiple reasons for the class to be refactored: 
    
    1. If there needs to be changes made to the behavior of Product. For example what if we wanted to add year the product was created. 
    
    2. If lawmakers change the taxes on a specific type of product.

???+ code "Valid Example"
    === "C#"
        ```c# linenums="1" title="Valid Example" hl_lines="25-31"
        public class Product
        {
            private string title;
            private string type;
            private decimal price;

            public Product(string title, string type, decimal price)
            {
                this.title = title;
                this.type = type;
                this.price = price;
            }

            public void GetPrice()
            {
                Console.WriteLine(price);
            }

            public void GetTitle()
            {
                Console.WriteLine(title);
            }
        }

        public class TaxCalculator
        {
            public static void CalculateTax(Product product)
            {
                // Special equation to calculate tax based on product type
            }
        }

        class Program
        {
            static void Main(string[] args)
            {
                Product toy = new Product("RC Car", "Toy", 42.99M);
                toy.GetPrice();
                toy.GetTitle();
                TaxCalculator.CalculateTax(toy);

                Product drizzank = new Product("Wine", "Liquor", 12.99M);
                drizzank.GetPrice();
                drizzank.GetTitle();
                TaxCalculator.CalculateTax(drizzank);
            }

        }
        ```

        ```title="Output"
        42.99
        RC Car
        "Calculate Tax for Toy Function..."
        12.99
        Wine
        "Calculate Tax for Liquor Function..."
        ```

    The creation of the class ``TaxCalculator`` fixes the issues with the invalid example. Now if lawmakers change the taxes on certain product types the ``Product`` class does not need to be touched. 