# 4 Principles of OOP

There are four pillars to object oriented programming:

- [Inheritance](#inheritance): child class inherits data and behavior from parent
- [Encapsulation](#encapsulation): contain information in an object, exposing only selected information
- [Abstraction](#abstraction): only exposing high-level public methods for accessing an object
- [Polymorphism](#polymorphism): many methods can do the same task

## Inheritance

Inheritance: allow classes to inherit features of other classes. This is done by declaring a child class a children of some parent class.

- ``children classes`` have access to functions/properties within ``parent classes``
- This allows the creation of new classes and building of additional functionalities on top of already existing classes. 

Benefits: 

- Provides better code re-usability and organization

???+ code "Inheritance Example"

    === "C#"
        ```c# linenums="1" title="Inheritance Example" hl_lines="35 73"

        // Base Class
        public class Bicycle 
        {
            // Fields in bicycle
            public int gear;
            public int speed;

            // Constructor
            public Bicycle(int gear, int speed)
            {
                this.gear = gear;
                this.speed = speed;
            }

            // Three functions
            public void ApplyBrake(int decrement)
            {
                speed -= decrement;
            }

            public void SpeedUp(int increment)
            { 
                speed += increment;
            }

            // Virtual function that will be overridden
            public virtual string GetSpecs()
            {
                return ("Gear: " + gear + "\n"
                        + "Speed: " + speed);
            }
        }

        // Derived Class
        public class RoadBike : Bicycle
        {
            // One new field
            public int seatHeight;

            // Constructor
            public RoadBike(int gear, int speed, int seatHeight) : base(gear, speed)
            {
                this.seatHeight = seatHeight;
            }

            // New function
            public void SetSeatHeight(int newSeatHeight)
            {
                seatHeight = newSeatHeight;
            }

            // Override virtual function from Bicycle class
            public override string GetSpecs()
            { 
                return ("Gear: " + gear + "\n"
                        + "Speed: " + speed + "\n"
                        + "Seat Height: " + seatHeight);
            }
        }

        // Driver code
        class Program
        {
            static void Main(string[] args)
            {
                Bicycle bike = new Bicycle(3, 0);
                bike.SpeedUp(25);
                Console.WriteLine(bike.GetSpecs());

                Console.WriteLine("--------");

                RoadBike trek = new RoadBike(12, 0, 5);
                trek.SpeedUp(30);
                trek.SetSeatHeight(7);
                Console.WriteLine(trek.GetSpecs());
            }
        }
        ```

        ```title="Output"
        Gear: 3
        Speed: 25
        --------
        Gear: 12
        Speed: 30
        Seat Height: 7
        ```

    Line 35 shows the ``RoadBike`` class which is a derived class of ``Bicycle`` which is the base class. 

    All fields and functions in the ``Bicycle`` class exists in the ``RoadBike`` class. This can be seen on line 73. The ``RoadBike`` object "Trek" has the ``SpeedUp`` function even though it is not in the explicitly defined in the ``RoadBike`` class. 


## Encapsulation

Encapsulation: keeping all important info within an object revealing only necessary information. This is done by defining fields with access modifiers (public, private, protected, etc...).

- Access modifiers can be added onto classes, methods, or properties. It is a keyword which states who can see the code it is attached to. Here are some popular access modifiers:
    - Public: methods and properties accessible from outside of the class
    - Private: methods and properties accessible from other methods in the same class
    - Protected: methods and properties accessible from derived classes (child classes)

Benefits:

- Provides security (hides important information)
- Protects against mistakes (hides important functionality, programmers won't invoke something important on accident)

???+ code "Encapsulation Example"

    === "C#"
        ```c++ linenums="1" title="Encapsulation Example" hl_lines="78-79 81"
        public class BankAccount
        {
            // Private fields, cannot edit directly
            private string accountHolder;
            private decimal balance;

            public BankAccount(string accountHolder, decimal initialBalance)
            { 
                this.accountHolder = accountHolder;
                balance = initialBalance;
            }

            // Property to set/get the balance field
            public decimal Balance
            {
                get { return balance; } // Getter is public
                private set             // Setter is private
                {
                    if (value >= 0)     // Only allow non-negative balances
                    {
                        balance = value;
                    }
                    else
                    {
                        Console.WriteLine("Balance cannot be negative");
                    }
                }
            }

            // Public function to deposit money and update balance
            public void Deposit(decimal amount)
            {
                if (amount > 0) {
                    Balance += amount;
                    Console.WriteLine($"Deposited {amount:C}. New Balance is {Balance:C}. \n" +
                        $"Thank you, {accountHolder}.");
                }
                else
                {
                    Console.WriteLine($"Deposit amount must be positive.");
                }
            }

            // Public function to withdraw money and update balance
            public void Withdraw(decimal amount)
            {
                if (amount > 0 && amount <= balance)
                {
                    Balance -= amount;
                    Console.WriteLine($"Withdrew {amount:C}. New Balance is {Balance:C}. \n" +
                        $"Thank you, {accountHolder}.");
                }
                else
                { 
                    Console.WriteLine($"Insufficient funds or invalid withdraw amount" +
                        $"You broke, {accountHolder}.");
                }
            }

            // Public function to view account into
            public void DisplayAccountInfo()
            {
                Console.WriteLine($"Account Holder: {accountHolder}, Balance: {Balance:C}");
            }
        }

        class Program
        {
            static void Main(string[] args)
            {
                // Creating a BankAccount object
                BankAccount account = new BankAccount("John Doe", 500.00m);

                // Display account information
                account.DisplayAccountInfo();

                // Performing some transactions
                account.Deposit(200.00m); // Deposit
                account.Withdraw(100.00m); // Withdraw

                // account.Balance = 100; // This line would cause a compile-time error
            }
        }
        ```

        ```title="Output"
        Account Holder: John Doe, Balance: $500.00
        Deposited $200.00. New Balance is $700.00.
        Thank you, John Doe.
        Withdrew $100.00. New Balance is $600.00.
        Thank you, John Doe.
        ```
    
    In this example there are two private fields ``accountHolder`` and ``balance`` in the ``BankAccount`` class. The *private* access modifier protect these fields from being directly manipulated. 

    Instead, there is a ``Balance`` property which allows for ``BankAccount`` objects to update their own ``balance`` field, and lets code outside of that class to retrieve that field.

    There is also public functions ``Deposit`` and ``Withdraw`` that can be used outside of the ``BankAccount`` object to update the ``balance`` field. This is done in line 78 and 79. 

    Line 81 shows an example of an error. This is because it is trying to access the ``balance`` field which is set to private. 

## Abstraction
Abstraction: uses encapsulation to hide complex tasks and present users with more simple functionalities. 

- This is done also with access modifiers. 
- Abstraction can be implemented by presenting a public function that calls a complex function that is set to private.

Benefits: 

- Reduces complexity (high-level representation of code)
- Helps with security (don't want to expose inner workings of a program)

???+ code "Abstraction Example"

    === "C#"
        ```c++ linenums="1" title="Abstraction Example" hl_lines="5 8 18 32"
        // Abstract Base Class
        public abstract class Animal
        {
            // Abstract function with no implementation
            public abstract void AnimalSound();

            // Normal function with implementation
            public void Sleep()
            {
                Console.WriteLine("zzz");
            }
        }

        // Derived Class
        public class Pig : Animal
        {
            // Defines the abstract function
            public override void AnimalSound()
            {
                Console.WriteLine("Oink");
            }
        }
        public class Program
        {
            static void Main(string[] args)
            {
                Pig myPig = new Pig();  // Create a Pig object
                myPig.AnimalSound();    // Call the abstract method
                myPig.Sleep();          // Call the regular method

                // Throws an error can't make an instance of an abstract class
                // Animal test = new Animal();
            }
        }
        ```

        ```title="Output"
        Oink
        zzz
        ```

    This example shows an abstract class ``Animal`` and a class ``Pig`` which inherits from the abstract class. An abstract class can't be instantiated on it own, as shown on line 32. 

    An abstract class has an abstract function (line 5). Whenever a class inherits from an abstract class, it will expect the abstract function to be defined. This can be seen in the ``Pig`` example on line 18.

    The regular function ``Sleep`` on line 8 displays one of the benefits of abstraction. Lets assume the ``Animal`` class needed functions to determine if an animal is healthy. These functions could be complex such as "GetBloodPressure", "CalculateRestingHeartRate", and "CalculateAverageLifeExpectancy". The ``Pig`` class could use abstraction by creating a function called ``IsHealthy`` and then within that call all the other functions related to health. This makes it so a user interacting with the ``Pig`` class would only need to interact with one function instead of all the complicated ones to determine health.

## Polymorphism
Polymorphism: design objects to share behaviors. Using inheritance, objects can override shared behaviors with specific child behaviors. Basically, given one method, there could be multiple ways that function can behave.

- Method overriding: child class implements different function than the parent
    - This is a compile time polymorphism
- Method overloading: same method names, but different parameters, thus different behaviors
    - This is a run time polymorphism

Benefits: 

- Allows the use of interface, but adjust individual behavior for individual classes

???+ code "Polymorphism Example"

    === "C#"
        ```c++ linenums="1" title="Polymorphism Example" hl_lines="14 20 26"
        // Base Class 
        public class Dog 
        {
            // Virtual method that will be overridden
            public virtual void Bark()
            {
                Console.WriteLine("Woof Woof");
            }
        }

        public class GuardDog : Dog
        {
            // Method override - new bark command
            public override void Bark()
            {
                Console.WriteLine("GRAHHHH");
            }

            // Method overload - handles a string input
            public void Bark(string verbal_command)
            {
                Console.WriteLine("roo roo, GRAHH GRAHH");
            }

            // Method overload - handles a boolean input
            public void Bark(bool point)
            {
                Console.WriteLine("roh, WROOF");
            }
        }

        public class Program
        {
            static void Main(string[] args)
            {
                Dog Bunson = new Dog();
                GuardDog Rufus = new GuardDog();

                Bunson.Bark();
                Rufus.Bark();
                Rufus.Bark("Intruder!");
                Rufus.Bark(true);
            }
        }
        ```

        ```title="Output"
        Woof Woof
        GRAHHHH
        roo roo, GRAHH GRAHH
        roh, WROOF
        ```

    The ``Bark`` function on line 14 is an example of **method overriding**. It is overriding the currently existing function in the ``Dog`` class. 

    The ``Bark`` function on line 20 and 26 is an example of method overloading. They handle the cases of when the ``Bark`` command is called with a boolean or a string input. 
