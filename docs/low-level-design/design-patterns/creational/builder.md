# Builder

The **Builder** design patterns allow for the construction of complex objects step by step. It allows for producing different types and representation of an object using the same construction code.

The separation between the construction of a complex object and its representation allows for the construction process to be reused for different representations. 

## Structure

Below is an example of a simple builder structure

!!! tip "Simple Builder Structure UML"
    <figure markdown>
          <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-dark) -->
          <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method-light.svg#only-light) -->
    </figure>

- **Products:** object which represents some sort of product.
- **Builders:** object that constructs the instance of a product and contains method to set all the fields.

Below is an example of a builder structure

!!! tip "Builder Structure UML"
    <figure markdown>
          <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-dark) -->
          <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method-light.svg#only-light) -->
    </figure>

- **Products:** (see simple builder above)
- **Builders:** (see simple builder above)
- **Builder Interface:** defines common product construction that is shared between all types of builder (default settings) 
- **Director:** defines the order in which to call construction steps. Basically contains presets of certain products to create.

## Builder Example

Imagine a program that constructs two different products: 

1. Car: object that represents a physical car
2. Car Schema: object that represents a schema linked to a car

Both of these products have the follow fields: 

- Model (string)
- Engine (string)
- Seats (int)
- Color (string)
- Spoiler (string) 

Below are three implementations for this car example.

1. Non Builder Example
2. Simple Builder Example
3. Builder Example

### Non Builder Example

???+ Example "Non Builder Example"

    === "C#"

        ```c# linenums="1" title="Example Code" 
        public class Car
        {
            public string model;
            public string engine;
            public int seats;
            public string color;
            public string spoiler;

            public Car(string model, string engine, int seats, string color, string spoiler)
            {
                this.model = model;
                this.engine = engine;
                this.seats = seats;
                this.color = color;
                this.spoiler = spoiler;
            }

            public override string ToString()
            {
                return string.Format("Model: {0}, Engine: {1}, Seats: {2}, Color: {3}, Spoiler: {4}", 
                    model, engine, seats, color, spoiler);
            }


            // ... Additional car related behavior ...
        }

        public class CarSchema
        {
            public string model;
            public string engine;
            public int seats;
            public string color;
            public string spoiler;

            public CarSchema(string model, string engine, int seats, string color, string spoiler)
            {
                this.model = model;
                this.engine = engine;
                this.seats = seats;
                this.color = color;
                this.spoiler = spoiler;
            }
            
            public override string ToString()
            {
                return string.Format("Model: {0}, Engine: {1}, Seats: {2}, Color: {3}, Spoiler: {4}", 
                    model, engine, seats, color, spoiler);
            }

            // ... Additional car schema related behavior ...
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="3 6"
        static void Main(string[] args)
        {
            Car toyotaTacoma1 = new Car("Tacoma", "4 Cylinder", 4, "Tan", "");
            Console.WriteLine(toyotaTacoma1);

            CarSchema toyotaTacoma2 = new CarSchema("Tacoma", "4 Cylinder", 4, "Green", "");
            Console.WriteLine(toyotaTacoma2);
            
            CarSchema hondaS2000 = new CarSchema("S2000", "Inline 4 Cylinder Turbo-charged", 2, "Yellow", "Carbon Fiber");
            Console.WriteLine(hondaS2000);
        }
        ```

        ```title="Output"
        Model: Tacoma, Engine: 4 Cylinder, Seats: 4, Color: Tan, Spoiler:
        Model: Tacoma, Engine: 4 Cylinder, Seats: 4, Color: Green, Spoiler:
        Model: S2000, Engine: Inline 4 Cylinder Turbo-charged, Seats: 2, Color: Yellow, Spoiler: Carbon Fiber
        ```

    In this implementation there are two types of objects that is created ``Car`` and ``CarSchema``. 

    In the driver code, three instances were made out of these two classes. They are the following: 

    - Tan Toyota Tacoma
    - Green Toyota Tacoma
    - Yellow Honda S2000

    On lines 3 and 6 of the driver code, both these lines are initializing Toyota Tacoma's. They both require an empty value for ``Spoiler`` since the ``Car`` and ``CarSchema`` class expects one. This is the main issue that the builder design pattern addresses. 

    Assume the rest of the program builds on top of the Toyota Tacoma objects. If a new car such as **Semi-Truck** were added and it required a new field called ``CargoBox`` this would require refactoring any code that uses the ``Car``class. 

    This breaks the following SOLID principles: 

    - Single responsibility principle: new reason to refactor = updates to construction of an object (ex. adding a new field)
    
### Simple Builder Example

???+ Example "Simple Builder Example"

    === "C#" 

        ```c# linenums="1" title="Example Code" hl_lines="1-9"
        // Builder (Interface)
        public interface Builder
        {
            public Builder AddModel(string model);
            public Builder AddEngine(string engine);
            public Builder AddSeats(int seats);
            public Builder AddColor(string color);
            public Builder AddSpoiler(string material);
        }


        // Product (Concrete Class)
        public class Car
        {
            public string Model { get; set; }
            public string Engine { get; set; }
            public int Seats { get; set; }
            public string Color { get; set; }
            public string Spoiler { get; set; }
            public override string ToString()
            {
                return string.Format("Model: {0}, Engine: {1}, Seats: {2}, Color: {3}, Spoiler: {4}", 
                    Model, Engine, Seats, Color, Spoiler);
            }


            // ... Additional car related behavior ...
        }

        // Product (Concrete Class)
        public class CarSchema
        {
            public string Model { get; set; }
            public string Engine { get; set; }
            public int Seats { get; set; }
            public string Color { get; set; }
            public string Spoiler { get; set; }
            
            public override string ToString()
            {
                return string.Format("Model: {0}, Engine: {1}, Seats: {2}, Color: {3}, Spoiler: {4}", 
                    Model, Engine, Seats, Color, Spoiler);
            }

            // ... Additional car schema related behavior ...
        }

        // Builder (Concrete Class)
        public class CarBuilder : Builder
        {
            private Car _car = new Car();

            public Builder AddColor(string color)
            {
                _car.Color = color;
                return this;
            }

            public Builder AddEngine(string engine)
            {
                _car.Engine = engine;
                return this;
            }

            public Builder AddModel(string model)
            {
                _car.Model = model;
                return this;
            }

            public Builder AddSeats(int seats)
            {
                _car.Seats = seats;
                return this;
            }

            public Builder AddSpoiler(string material)
            {
                _car.Spoiler = material;
                return this;
            }

            public Car Build()
            {
                return _car;
            }
        }

        // Builder (Concrete Class)
        public class CarSchemaBuilder : Builder
        {
            private CarSchema _carSchema = new CarSchema();

            public Builder AddColor(string color)
            {
                _carSchema.Color = color;
                return this;
            }

            public Builder AddEngine(string engine)
            {
                _carSchema.Engine = engine;
                return this;
            }

            public Builder AddModel(string model)
            {
                _carSchema.Model = model;
                return this;
            }

            public Builder AddSeats(int seats)
            {
                _carSchema.Seats = seats;
                return this;
            }

            public Builder AddSpoiler(string material)
            {
                _carSchema.Spoiler = material;
                return this;
            }

            public CarSchema Build()
            {
                return _carSchema;
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="6-11 13-18 20-26"
        static void Main(string[] args)
        {
            CarBuilder carMaker = new CarBuilder();
            CarSchemaBuilder carSchemaMaker = new CarSchemaBuilder();

            // Building a Toyota Tacoma Car
            carMaker.AddModel("Tacoma")
                .AddEngine("4 Cylinder")
                .AddColor("Tan")
                .AddSeats(4);
            Console.WriteLine(carMaker.Build());

            // Building a Toyota Tacoma Car Schema
            carSchemaMaker.AddModel("Tacoma")
                .AddEngine("4 Cylinder")
                .AddColor("Green")
                .AddSeats(4);
            Console.WriteLine(carSchemaMaker.Build());

            // Building a S2000 Car Schema
            carSchemaMaker.AddModel("S2000")
                .AddEngine("Inline 4 Cylinder Turbo-charged")
                .AddColor("Yellow")
                .AddSeats(2)
                .AddSpoiler("Carbon Fiber");
            Console.WriteLine(carSchemaMaker.Build());

        }
        ```

        ```title="Output"
        Model: Tacoma, Engine: 4 Cylinder, Seats: 4, Color: Tan, Spoiler:
        Model: Tacoma, Engine: 4 Cylinder, Seats: 4, Color: Green, Spoiler:
        Model: S2000, Engine: Inline 4 Cylinder Turbo-charged, Seats: 2, Color: Yellow, Spoiler: Carbon Fiber
        ```
    
    In this example the ``Car`` and ``CarSchema`` class now doesn't expect any initial properties/constructor, instead all properties are defined after the instantiation of the class. 

    The ``Builder`` interface lets the program know what fields will be expected by the ``Car`` and ``CarSchema`` class. The actual builders ``CarBuilder`` and ``CarSchemaBuilder`` handles the construction of the ``Car`` and ``CarSchema`` classes. 

    Using the same example in the bad example, what if a new car, **Semi-Truck**, was added with the new field ``CargoBox``? 

    - All the classes in this example would have to be updated to handle the new field.  
    - **The code in the driver code would remain the same without any issues**.

    The addition of this simple builder design pattern allows for construction of the products to be separated. Meaning if construction changes (new fields are added), it doesn't effect products already created (the ``Car`` and ``CarSchema`` objects created in driver code).

    This fixes the issue that broke the following SOLID principle: 

    - Single responsibility principle: new fields won't break the code since construction logic is separated.

    This pattern can be further optimized. The driver code has a lot of duplicated logic which can be cut down by creating a ``Director`` class.

### Builder Example

???+ Example "Builder Example"

    === "C#" 

        ```c# linenums="1" title="Example Code" hl_lines="130-149"
        // Builder (Interface)
        public interface Builder
        {
            public Builder AddModel(string model);
            public Builder AddEngine(string engine);
            public Builder AddSeats(int seats);
            public Builder AddColor(string color);
            public Builder AddSpoiler(string material);
        }


        // Product (Concrete Class)
        public class Car
        {
            public string Model { get; set; }
            public string Engine { get; set; }
            public int Seats { get; set; }
            public string Color { get; set; }
            public string Spoiler { get; set; }
            public override string ToString()
            {
                return string.Format("Model: {0}, Engine: {1}, Seats: {2}, Color: {3}, Spoiler: {4}", 
                    Model, Engine, Seats, Color, Spoiler);
            }


            // ... Additional car related behavior ...
        }

        // Product (Concrete Class)
        public class CarSchema
        {
            public string Model { get; set; }
            public string Engine { get; set; }
            public int Seats { get; set; }
            public string Color { get; set; }
            public string Spoiler { get; set; }
            
            public override string ToString()
            {
                return string.Format("Model: {0}, Engine: {1}, Seats: {2}, Color: {3}, Spoiler: {4}", 
                    Model, Engine, Seats, Color, Spoiler);
            }

            // ... Additional car schema related behavior ...
        }

        // Builder (Concrete Class)
        public class CarBuilder : Builder
        {
            private Car _car = new Car();

            public Builder AddColor(string color)
            {
                _car.Color = color;
                return this;
            }

            public Builder AddEngine(string engine)
            {
                _car.Engine = engine;
                return this;
            }

            public Builder AddModel(string model)
            {
                _car.Model = model;
                return this;
            }

            public Builder AddSeats(int seats)
            {
                _car.Seats = seats;
                return this;
            }

            public Builder AddSpoiler(string material)
            {
                _car.Spoiler = material;
                return this;
            }

            public Car Build()
            {
                return _car;
            }
        }

        // Builder (Concrete Class)
        public class CarSchemaBuilder : Builder
        {
            private CarSchema _carSchema = new CarSchema();

            public Builder AddColor(string color)
            {
                _carSchema.Color = color;
                return this;
            }

            public Builder AddEngine(string engine)
            {
                _carSchema.Engine = engine;
                return this;
            }

            public Builder AddModel(string model)
            {
                _carSchema.Model = model;
                return this;
            }

            public Builder AddSeats(int seats)
            {
                _carSchema.Seats = seats;
                return this;
            }

            public Builder AddSpoiler(string material)
            {
                _carSchema.Spoiler = material;
                return this;
            }

            public CarSchema Build()
            {
                return _carSchema;
            }
        }

        // Director 
        public class Director
        {
            public void BuildToyotaTacoma(Builder builder)
            {
                builder.AddModel("Tacoma")
                    .AddEngine("4 Cylinder")
                    .AddColor("Tan")
                    .AddSeats(4);
            }

            public void BuildHondaS2000(Builder builder)
            {
                builder.AddModel("S2000")
                    .AddEngine("Inline 4 Cylinder")
                    .AddColor("Yellow")
                    .AddSeats(2)
                    .AddSpoiler("Carbon Fiber");
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="7-9 11-14 16-19"
        static void Main(string[] args)
        {
            Director director = new Director();
            CarBuilder carMaker = new CarBuilder();
            CarSchemaBuilder carSchemaMaker = new CarSchemaBuilder();

            // Car builder example - Tacoma
            director.BuildToyotaTacoma(carMaker);
            Console.WriteLine(carMaker.Build());

            // Car Schema builder example - Tacoma
            director.BuildToyotaTacoma(carSchemaMaker);
            carMaker.AddColor("Green");
            Console.WriteLine(carSchemaMaker.Build());

            // Car Schema builder example - S2000
            director.BuildHondaS2000(carSchemaMaker);
            carSchemaMaker.AddEngine("Inline 4 Cylinder Turbo-charged");
            Console.WriteLine(carSchemaMaker.Build());
        }
        ```

        ```title="Output"
        Model: Tacoma, Engine: 4 Cylinder, Seats: 4, Color: Tan, Spoiler:
        Model: Tacoma, Engine: 4 Cylinder, Seats: 4, Color: Green, Spoiler:
        Model: S2000, Engine: Inline 4 Cylinder Turbo-charged, Seats: 2, Color: Yellow, Spoiler: Carbon Fiber
        ```
    
    This implementation is identical to the simple builder example except for the ``Director`` class which simplifies the construction process even more. 

    Construction is now simplified by using the methods in the ``Director`` class to create presets of ``Car`` and ``CarSchema`` objects.

## When To Use

- When the code needs to represent an object and various representations of that object where the details are slightly different between each representation.
- Especially useful when there are a lot of variations to create, but the variations share some core behaviors.

## Resources

[:material-video-outline: Builder Example](https://www.youtube.com/watch?v=MaY_MDdWkQw)

[:material-file-document-outline: Builder](https://refactoring.guru/design-patterns/builder)
