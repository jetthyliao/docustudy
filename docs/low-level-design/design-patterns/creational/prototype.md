# Prototype

The **prototype** design patterns allow for copying existing objects without making the code dependent on the classes. 

## Structure

Below is an example of the structure of a prototype design pattern. 

!!! tip "Prototype Structure UML"
    <figure markdown> 
    </figure>

- **Prototype:** interface or abstract class that declares cloning method 
- **Concrete Prototype:** class that implements cloning method. This is a normal class, but contains function that handles cloning of itself.
- **Subclass Prototype:** all children of the concrete also implement cloning. This can be expanded to multiple children. 

!!! tip "Prototype Structure + Prototype Registry UML"
    <figure markdown> 
    </figure>

- **Prototype:** (see prototype structure above)
- **Concrete Prototype:** (see prototype structure above)
- **Subclass Prototype:** (see prototype structure above)
- **Prototype Registry:** A cache that stores frequently used prototypes. Contains method to retrieve from cache, the retrieval method returns a clone copy.

## Prototype Example

Imagine a program that creates two animals: 

1. Dogs
2. Turtles

This program should be able to create ``Dogs`` and ``Turtles`` and also deep copy them.

Below are three implementations of this program: 

1. Non Prototype Example 
2. Prototype Example
3. Prototype + Registry Example

### Non Prototype Example

???+ Example "Non Prototype Example"

    === "C#" 

        ```c# linenums="1" title="Example Code"
        public class Dog 
        {
            private string name;
            private string sex;
            private string breed;

            public Dog(string name, string sex, string breed) 
            {
                this.name = name;
                this.sex = sex;
                this.breed = breed;
            }

            private Dog(Dog dog)
            {
                this.name = dog.name;
                this.sex = dog.sex;
                this.breed = dog.breed;
            }

            public Dog Clone()
            {
                return new Dog(this);
            }
        }

        public class Turtle
        {
            private string name;
            private string sex;
            private bool isNinja;

            public Turtle(string name, string sex, bool isNinja)
            {
                this.name = name;
                this.sex = sex;
                this.isNinja = isNinja;
            }

            private Turtle(Turtle turtle)
            {
                this.name = turtle.name;
                this.sex = turtle.sex;
                this.isNinja = turtle.isNinja;
            }

            public Turtle Clone()
            {
                return new Turtle(this);
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="3-4 6-7"
        static void Main(string[] args)
        { 
            Dog dog = new Dog("Alfred", "Male", "Husky");
            Turtle turtle = new Turtle("Donatello", "Male", true);

            Dog dogClone = dog.Clone();
            Turtle turtleClone = turtle.Clone();

            Console.WriteLine(dogClone);
            Console.WriteLine(turtleClone);
        }
        ```

        ```title="Output"
        Dog
        Turtle
        ```
    
    In this example a ``Dog`` and ``Turtle`` object is created on lines 3 and 4 of the driver code. 

    In lines 6 and 7 of the driver code a clone of each of the objects are created. 

    This is a valid implementation that creates clones. The issue is the type of the object must be known before the clones can be created. For example on line 6, it must be known that the object is an instance of ``Dog`` before the ``Clone`` method can be called. 

    This implementation contains a tight coupling between the object type and the cloning process. 

### Prototype Example

???+ Example "Prototype Example"

    === "C#" 

        ```c# linenums="1" title="Example Code" hl_lines="1-20"
        // Prototype (Abstract Class)
        public abstract class Animal
        {
            public string name;
            private string sex;

            public Animal(string name, string sex)
            { 
                this.name = name;
                this.sex = sex;
            }

            protected Animal(Animal animal)
            {
                this.name = animal.name;
                this.sex = animal.sex;
            }

            public abstract Animal Clone();
        }

        // Prototype (Subclass)
        public class Dog : Animal
        {
            private string breed;

            public Dog(string name, string sex, string breed) : base(name, sex)
            {
                this.breed = breed;
            }

            private Dog(Dog dog) : base(dog)
            {
                this.breed = dog.breed;
            }

            public override Dog Clone()
            {
                return new Dog(this);
            }
        }

        // Prototype (Subclass)
        public class Turtle : Animal
        {
            private bool isNinja;

            public Turtle(string name, string sex, bool isNinja) : base(name, sex)
            {
                this.isNinja = isNinja;
            }

            private Turtle(Turtle turtle) : base(turtle)
            {
                this.isNinja = turtle.isNinja;
            }

            public override Turtle Clone()
            {
                return new Turtle(this);
            }
        }
        ```

        ```c# linenums="1" title="Driver Code"
        static void Main(string[] args)
        {
            // Create list of animals
            List<Animal> animalList = new List<Animal>();
            animalList.Add(new Dog("Alfred", "Male", "Husky"));
            animalList.Add(new Turtle("Donatello", "Male", true));

            // Create copy of each animal in animalList
            List<Animal> copyList = new List<Animal>();
            foreach (Animal animal in animalList)
            {
                copyList.Add(animal.Clone());
            }

            // Print copied animals
            copyList.ForEach(animal => { Console.WriteLine(animal); });
        }
        ```

        ```title="Output"
        Dog
        Turtle
        ```

        In this implementation the prototype abstract class ``Animal`` declares a ``Clone`` method which will handle cloning for the two concrete prototypes ``Dog`` and ``Turtle``.

        On line 12 of the driver code, all the different ``Animal`` objects are cloned irrespectively of their actual type (``Dog`` vs ``Turtle``). 

        This implementation decouples the coupling between object types and the cloning process thanks to the prototype abstract class. 

### Prototype + Registry Example

???+ Example "Prototype + Registry Example"

    === "C#" 

        ```c# linenums="1" title="Example Code" hl_lines="1-20 64-81"
        // Prototype (Abstract Class)
        public abstract class Animal
        {
            public string name;
            private string sex;

            public Animal(string name, string sex)
            { 
                this.name = name;
                this.sex = sex;
            }

            protected Animal(Animal animal)
            {
                this.name = animal.name;
                this.sex = animal.sex;
            }

            public abstract Animal Clone();
        }

        // Prototype (Subclass)
        public class Dog : Animal
        {
            private string breed;

            public Dog(string name, string sex, string breed) : base(name, sex)
            {
                this.breed = breed;
            }

            private Dog(Dog dog) : base(dog)
            {
                this.breed = dog.breed;
            }

            public override Dog Clone()
            {
                return new Dog(this);
            }
        }

        // Prototype (Subclass)
        public class Turtle : Animal
        {
            private bool isNinja;

            public Turtle(string name, string sex, bool isNinja) : base(name, sex)
            {
                this.isNinja = isNinja;
            }

            private Turtle(Turtle turtle) : base(turtle)
            {
                this.isNinja = turtle.isNinja;
            }

            public override Turtle Clone()
            {
                return new Turtle(this);
            }
        }

        // Prototype Registry (Class)
        public class AnimalCache
        {
            private Dictionary<string, Animal> cache = new Dictionary<string, Animal>();

            public Animal Get(string key)
            {
                return cache[key].Clone();
            }

            public void Put(List<Animal> animals)
            {
                animals.ForEach(animal =>
                {
                    cache.Add(animal.name, animal.Clone());
                });
            }
        }
        ```

        ```c# linenums="1" title="Driver Code"
        static void Main(string[] args)
        {
            // Create list of animals
            List<Animal> animalList = new List<Animal>();
            animalList.Add(new Dog("Alfred", "Male", "Husky"));
            animalList.Add(new Turtle("Donatello", "Male", true));

            // Add animalList to registry 
            AnimalCache registry = new AnimalCache();
            registry.Put(animalList);

            // Pull animals out of registry (these are copied animals)
            Console.WriteLine(registry.Get("Alfred"));
            Console.WriteLine(registry.Get("Donatello"));
        }
        ```

    This implementation is identical to the previous example except for the addition of the ``AnimalCache`` prototype registry class. 

    This class store frequently deep copied objects into a cache. Whenever a copy needs to be pulled, the ``AnimalCache`` will automatically create a clone and return it for the user.

## When To Use 

- When code shouldn't depend on the concrete classes of objects needed to be copied.
- Note: prototype implementation with a deep copy behaves very closely to an abstract/factory method. If the goal of the code is to create completely new copies (instances and all) of an object, an abstract factory might be better.

## Resources

[:material-file-document-outline: Prototype Method Example](https://www.dotnettricks.com/learn/designpatterns/prototype-design-pattern-dotnet)

[:material-file-document-outline: Prototype](https://refactoring.guru/design-patterns/factory-method)

