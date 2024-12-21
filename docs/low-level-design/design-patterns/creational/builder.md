# Builder

The **Builder** design patterns allow for the construction of complex objects step by step. It allows for producing different types and representation of an object using the same construction code.

## Problem

If an object has multiple properties, sometimes all the properties need not be defined or used. When this occurs, if an object is designed poorly, the constructor will ask the user to define all the fields anyways meaning they have to fill in properties with nulls creating messier code. 

For example, lets say there is a ``house`` class to represent building a house. If different auxillary features of a house exist (pool, gnomes, central A/C, etc...) these would need to be properties in a ``house`` class. Also a constructor would need to be setup to take in these different properties. 

There are a few potential options... 

1. Create multiple constructors (for different permutations of a house + features): this can cause a huge hierarchy of multiple constructors (with no explanation unless commented about what constructor designs what kind of house)
2. Subclasses: a new ``HouseGarden`` class can be made to define a house with a garden, same as above, this causes a messy hierarchy of subclasses.
3. Builder design pattern


## Solution

Below is an example of a simple builder structure

!!! tip "Builder Structure UML"
    <figure markdown>
          <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-dark) -->
          <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method-light.svg#only-light) -->
    </figure>

- **Product:** object which represents some sort of product (the properties are all defined as setter/getter methods).
- **Builder:** object that constructs the instance of a product and contains method to set all the fields.

Below is an example of a simple builder structure

!!! tip "Builder Structure UML"
    <figure markdown>
          <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-dark) -->
          <!-- ![Factory Method Diagram](../../assets/images/design-patterns/factory-method-light.svg#only-light) -->
    </figure>

- **Products:** (see simple builder above)
- **Builder:** (see simple builder above)
- **Builder Interface:** defines common product construction that is shared between all types of builder (default settings) 
- **Director:** defines the order in which to call construction steps. Basically contains presets of certain products to create.

# Builder Example

Imagine a complex object like a car. Lets say the car object has the following fields: 

- Brand (string)
- Color (string)
- Engine (string)
- Seats (int)
- Wheels (int) 

Below are three implementation for this car example.

1. Non Builder Example
2. Simple Builder Example
3. Builder Example

### Non Builder Example

???+ Example "Non Builder Example"
    ```c# linenums="1" title="Example Code" hl_lines="29-36"  
    public class AutomaticCar
    {
        private string Brand;
        private string Color;
        private string Engine;
        private int Seats;
        private int Doors;

        AutomaticCar(string brand, string color, string engine, int seats, int wheels) 
        {
            this.Brand = brand;
            this.Color = color;
            this.Engine = engine;
            this.Seats = seats;
            this.Doors = doors;
        }

        public void GetSpecs()
        {
            Console.WriteLine("Listing Specs...");
            Console.WriteLine("Brand: {0}", this.brand);
            Console.WriteLine("Color: {0}", this.color);
            Console.WriteLine("Engine: {0}", this.engine);
            Console.WriteLine("Seats: {0}", this.seats);
            Console.WriteLine("Doors: {0}", this.doors);
        }
    }

    public class MatchBox : AutomaticCar
    {
        MatchBox(string color, int doors) 
        {
            this.Color = color;
            this.Doors = doors;
        }
    }
    ```

    ```c# linenums="1" title="Driver Code" hl_lines="4"
    public void Main()
    {
        Car foopy = new Car("Subaru", "Blue", "4cyl",  5, 4);
        Car basic = new Car("", "Blue", "",  1);
         
        foopy.GetSpecs();
        basic.GetSpecs();
    }
    ```

    ```title="Output"
    Listing Specs...
    Brand: Subaru
    Color: Blue
    Engine: 4cyl
    Seats: 5
    Doors: 4

    Listing Specs...
    Brand: 
    Color: Blue
    Engine: 
    Seats: 
    Doors: 1
 
    ```

    In this implementation there is a ``AutomaticCar`` class to represent creating automatic cars. Every time a new automatic car object is created, multiple fields must be instantiated first.

    This is not a good solution because sometimes not all fields need to be instantiated. For example some cars may have no doors (Jeeps) or some cars might be custom built (no brand). This is shown in the highlighted line in the driver code. The ``basic`` car represents a match box car that has no brand, no engine, and no doors, however all the fields still need to be instantiated.

    A potential solution is creating a subclass for the matchbox car, but as a program grows it could contain many subclasses (all permutations of potential properties) and this hierarchy is messy. 

    A simple builder design alleviates this issue.

### Simple Builder Example

???+ Example "Simple Builder Example"
    ```c# linenums="1" title="Example Code"
    public class AutomaticCar
    { 
        public string Brand { get; set; }
        public string Color { get; set; }
        public string Engine { get; set; }
        public int Seats { get; set; }
        public int Wheels { get; set; }

        public void GetSpecs()
        {
            Console.WriteLine("Listing Specs...");
            Console.WriteLine("Brand: {0}", this.Brand);
            Console.WriteLine("Color: {0}", this.Color);
            Console.WriteLine("Engine: {0}", this.Engine);
            Console.WriteLine("Seats: {0}", this.Seats);
            Console.WriteLine("Wheels: {0}", this.Wheels);
        }         
    }

    public class AutomaticCarBuilder
    {
        private AutomaticCar _automaticCar;

        public AutomaticCarBuilder()
        {
            this._automaticCar = new AutomaticCar();
        }

        public AutomaticCarBuilder SetBrand(string brand) 
        {
            this._automaticCar.Brand = brand;
            return this;
        }
        
        public AutomaticCarBuilder SetColor(string color) 
        {
            this._automaticCar.Color = color;
            return this;
        } 
         
        public AutomaticCarBuilder SetEngine(string engine) 
        {
            this._automaticCar.Engine = engine;
            return this;
        } 
        
        public AutomaticCarBuilder SetSeats(int seats) 
        {
            this._automaticCar.Seats = seats;
            return this;
        } 
        
        public AutomaticCarBuilder SetWheels(int wheels) 
        {
            this._automaticCar.Wheels = wheels;
            return this;
        } 

        public AutomaticCar BuildAutomaticCar()
        {
            return _automaticCar;
        }
    } 
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main()
    {
        AutomaticCarBuilder ac = new AutomaticCarBuilder();
        ac.SetBrand("Subaru")
          .SetColor("Blue")
          .SetEngine("4cyl")
          .SetSeats(5)
          .SetWheels(4);
        AutomaticCar car = ac.BuildAutomaticCar();
        car.GetSpecs();
    }
    ```

    ```title="Output"
    Listing Specs...
    Brand: Subaru
    Color: Blue
    Engine: 4cyl
    Seats: 5
    Doors: 4 
    ```

    In this example, the ``AutomaticCar`` class now doesn't expect any initial properties, instead all the properties are defined after instantiation of the class. This is done using getter/setter methods in the ``AutomaticCarBuilder`` class. 

    This solution allows a user to instantiate the properties they need leaving the rest to be null. 
### Builder Example

???+ Example "Builder Example"
    ```c# linenums="1" title="Example Code"
    public class AutomaticCar
    {
        public string Brand { get; set; }
        public string Color { get; set; }
        public string Engine { get; set; }
        public int Seats { get; set; }
        public int Wheels { get; set; }

        public void GetSpecs()
        {
            Console.WriteLine("Listing Specs...");
            Console.WriteLine("Brand: {0}", Brand);
            Console.WriteLine("Color: {0}", Color);
            Console.WriteLine("Engine: {0}", Engine);
            Console.WriteLine("Seats: {0}", Seats);
            Console.WriteLine("Wheels: {0}", Wheels);
        }
    }

    public class ManualCar
    {
        public string Brand { get; set; }
        public string Color { get; set; }
        public string Engine { get; set; }
        public int Seats { get; set; }
        public int Wheels { get; set; }
        public string Clutch { get; set; }
        public string Transmission { get; set; }

        public void GetSpecs()
        {
            Console.WriteLine("Listing Specs...");
            Console.WriteLine("Brand: {0}", Brand);
            Console.WriteLine("Color: {0}", Color);
            Console.WriteLine("Engine: {0}", Engine);
            Console.WriteLine("Seats: {0}", Seats);
            Console.WriteLine("Wheels: {0}", Wheels);
            Console.WriteLine("Clutch: {0}", Clutch);
            Console.WriteLine("Transmission: {0}", Transmission);
        }
    }

    public class AutomaticCarBuilder : IBuilder
    {
        private AutomaticCar _automaticCar;

        public AutomaticCarBuilder()
        {
            this._automaticCar = new AutomaticCar();
        }

        public IBuilder SetBrand(string brand)
        {
            this._automaticCar.Brand = brand;
            return this;
        }

        public IBuilder SetColor(string color)
        {
            this._automaticCar.Color = color;
            return this;
        }

        public IBuilder SetEngine(string engine)
        {
            this._automaticCar.Engine = engine;
            return this;
        }

        public IBuilder SetSeats(int seats)
        {
            this._automaticCar.Seats = seats;
            return this;
        }

        public IBuilder SetWheels(int wheels)
        {
            this._automaticCar.Wheels = wheels;
            return this;
        }

        public AutomaticCar BuildAutomaticCar()
        {
            return _automaticCar;
        }
    }

    public class ManualCarBuilder : IBuilder
    {
        private ManualCar _manualCar;

        public ManualCarBuilder()
        {
            this._manualCar = new ManualCar();
        }

        public IBuilder SetBrand(string brand)
        {
            this._manualCar.Brand = brand;
            return this;
        }

        public IBuilder SetColor(string color)
        {
            this._manualCar.Color = color;
            return this;
        }

        public IBuilder SetEngine(string engine)
        {
            this._manualCar.Engine = engine;
            return this;
        }

        public IBuilder SetSeats(int seats)
        {
            this._manualCar.Seats = seats;
            return this;
        }

        public IBuilder SetWheels(int wheels)
        {
            this._manualCar.Wheels = wheels;
            return this;
        }

        public IBuilder SetTransmission(string transmission)
        {
            this._manualCar.Transmission = transmission;
            return this;
        }

        public IBuilder SetClutch(string clutch)
        {
            this._manualCar.Clutch = clutch;
            return this;
        }

        public ManualCar BuildManualCar()
        {
            return _manualCar;
        }
    }

    public interface IBuilder
    {
        public IBuilder SetEngine(string engine);
        public IBuilder SetSeats(int seats);
    }

    public class Director
    {
        public void CreateSportsCar(IBuilder builder)
        {
            builder.SetEngine("8cyl")
                   .SetSeats(2);
        }

        public void CreateHonda(IBuilder builder)
        {
            builder.SetBrand("Honda")
                   .SetEngine("4cyl")
                   .SetSeats(4);
        } 
    }
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main()
    {
        Director shop = new Director();
        AutomaticCarBuilder ac = new AutomaticCarBuilder();
        ManualCarBuilder mc = new ManualCarBuilder();

        shop.CreateSportsCar(ac);
        shop.CreateSportsCar(mc);
        AutomaticCar car1 = ac.BuildAutomaticCar();
        ManualCar car2 = mc.BuildManualCar();
        
        car2.Clutch = "XM-3492";
         
        car1.GetSpecs();
        car2.GetSpecs(); 
    }
    ```

    ```title="Output"
    Listing Specs...
    Brand: 
    Color: 
    Engine: 8cyl
    Seats: 2
    Doors:   

    Listing Specs...
    Brand: 
    Color: 
    Engine: 8cyl
    Seats: 2
    Doors:  
    Clutch: XM-3492
    Transmission:
    ```

    In this example of the complex builder two additional structures have been added, the **interface builder** and the **director**. 

    There is now also two simple build patterns (``AutomaticCar`` + ``AutomaticCarBuild``) and (``ManualCar`` + ``ManualCarBuild``)
    ### Director:  

    The director allows you to create "presets" of object builds. For example, in the ``Director`` class we have two functions ``CreateSportsCar`` and ``CreateHonda``. Both of these functions have some preset fields already instantiated. This simplifies object construction.

    ### Build Interface:

    If the build interface did not exist, within the ``Director`` class there would need to be two implementations for each function, one for a ``AutomaticCarBuilder`` object and another for the ``ManualCarBuilder`` object. 

    With the ``IBuild`` interface, the expected properties are defined, and as long as a builder is a child of the interface, it can be used with the ``Director``.

## When To Use

- When the code needs to represent an object and various representations of that object where the details are slightly different between each representation.
- Especially useful when there are a lot of variations to create, but the variations share some core behaviors.

## Resources

[:material-file-document-outline: Builder](https://refactoring.guru/design-patterns/builder)

