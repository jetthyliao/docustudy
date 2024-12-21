# Open-Closed Principle Example

Open-closed principle: should be able to extend a class's behavior without modifying it. 

???+ code "Invalid Example"
    === "C#"
        ```c# linenums="1" title="Invalid Example" hl_lines="21-32"
        public class Order
        {
            public string CustomerType { get; set; }
            public double TotalAmount { get; set; }

            // ... 
        }

        public class DiscountCalculator
        {
            /**
            This breaks open-closed principle because if we need to add more customer 
            type, or need to change discount rates we would need to edit/update this 
            function
            **/
            public double CalculateDiscount(Order order)
            {
                double discount = 0;

                // Discount logic based on customer type
                if (order.CustomerType == "Regular")
                {
                    discount = order.TotalAmount * 0.1; // 10% discount for regular customers
                }
                else if (order.CustomerType == "VIP")
                {
                    discount = order.TotalAmount * 0.2; // 20% discount for VIP customers
                }
                else if (order.CustomerType == "Employee")
                {
                    discount = order.TotalAmount * 0.3; // 30% discount for employees
                }

                return discount;
            }
        }
        ```

    This breaks the open-closed principle because the ``DiscountCalculator`` class cannot be extended to handle more discounts for other customer types without having to refactor the ``DiscountCalculator`` class itself. The ``CalculateDiscount`` would need to be refactored to add more customer types and corresponding discounts, specifically the if-else statement block on line 21 to 32. 

???+ code "Valid Example"
    === "C#"
        ```c# linenums="1" title="Valid Example" hl_lines="14-20 22-28 30-36"
        public class Order
        {
            public string CustomerType { get; set; }
            public double TotalAmount { get; set; }

            // ...
        }

        public interface IDiscountStrategy
        {
            double CalculateDiscount(Order order);
        }

        public class RegularCustomerDiscount : IDiscountStrategy
        {
            public double CalculateDiscount(Order order)
            {
                return order.TotalAmount * 0.1; // 10% discount for regular customers
            }
        }

        public class VIPCustomerDiscount : IDiscountStrategy
        {
            public double CalculateDiscount(Order order)
            {
                return order.TotalAmount * 0.2; // 20% discount for VIP customers
            }
        }

        public class EmployeeDiscount : IDiscountStrategy
        {
            public double CalculateDiscount(Order order)
            {
                return order.TotalAmount * 0.3; // 30% discount for employees
            }
        }

        public class DiscountCalculator
        {
            private readonly IDiscountStrategy _discountStrategy;

            /**
            This now adheres to open-closed principle since the individual changes to 
            order types are handled by the individual discount classes.
            **/
            public DiscountCalculator(IDiscountStrategy discountStrategy)
            {
                _discountStrategy = discountStrategy;
            }

            public double CalculateDiscount(Order order)
            {
                return _discountStrategy.CalculateDiscount(order);
            }
        }
        ```

    Implementing the ``IDiscountStrategy`` interfaces fixes the issues in the invalid example that broke the open-closed principle. 

    Now the different discounts that are needed can be handled with individual classes that inherit from the ``IDiscountStrategy`` interface. The ``DiscountCalculator`` just needs the strategy to be passed in for it to behave identically to the invalid example. 

    The difference is now that the discounts are separate from the ``DiscountCalculator`` class, new discounts can be made without having to refactor the ``DiscountCalculator`` class.
