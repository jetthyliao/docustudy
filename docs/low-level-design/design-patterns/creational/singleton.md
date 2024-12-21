# Singleton

The **singleton** design pattern ensures a class has only one instance, while providing a global access point to this instance. Singleton patterns are so common sometimes something could be called a singleton without fulfilling its two functionality (so important it'll be listed again)

1. Ensure a class has just a single instance
2. Provide global access point to that instance

## Problem

The problem of not using singletons in a program is as followed: 

- If multiple instances are created to represent a file or database, there can be conflict on what reads/writes or the most current.
- If there is not a single global access point, it is most likely that code to check for the single instance is copied in multiple places in the program.

## Solution

Singletons address both of the issues as stated above, it does so by: 

- Ensure single instance: the default constructor is set to **private** preventing other objects from using the ``new`` operator in the singleton class.
- Creates a static creation method that acts as a constructor. Either the default constructor will be called to create the first instance, or it will return a static cached version of the instance.

# Singleton Example

Lets say there is a program that needs to connect to a database. A single instance of the database should be created as a single connection is desired.

The singleton design pattern will ensure that there is a single connection, and it will give a single entry point to access this instance. 

### Non Singleton Example

???+ Example "Non Singleton example"
    ```c# linenums="1" title="Example Code"
    public class Database
    {
        private PostgresExample db;
        public Database()
        {
            this.db = new PostgresExample{
                ServerName = "Example1",
                ConnectionURL = "postgresql://example"
            };
        }

        public string getServerName()
        {
            return db.ServerName;
        }
    }

    public class PostgresExample
    {
        public string ServerName { get; set; }
        public string ConnectionURL { get; set; }
    }
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main() 
    {
        Database test1 = new Database();
        Console.WriteLine(test1.getServerName());

        Database test2 = new Database();
        Console.WriteLine(test2.getServerName());
    }
    ```

    ```title="Output"
    Example1
    Example1
    ```

    In this example, the ``Database`` class is created twice making two unique instances of the database object. 

    In a real world scenario this would mean two connections have been created to the database which is a waste of resources.

### Singleton Example 

???+ Example "Singleton example"
    ```c# linenums="1" title="Example Code"
    public sealed class Database // sealed so no subclasses can be made
    {
        private static readonly Database Instance = new Database();

        private PostgresExample db;

        private Database() 
        {
            this.db = new PostgresExample{ 
                ServerName = "Example1", 
                ConnectionURL = "postgressql://example"
            };
        }

        public static Database GetDatabase()
        {
            return instance;
        }
    }    

    public class PostgresExample
    {
        public string ServerName { get; set; }
        public string ConnectionURL { get; set; }
    } 
    ```

    ```c# linenums="1" title="Driver Code"
    public void Main() 
    {
        Database test1 = Database.GetDatabase();
        Database test2 = Database.GetDatabase();

        Console.WriteLine((test1 == test2));
    }
    ```

    ```title="Output"
    true
    ```

    In this example, using the the singleton pattern, the first call of ``GetDatabase`` will create a new database instance and assign it to the instance variable. Every other call will return the cached instance property. 

    The ``Database`` class is also set with the ``sealed`` keyword to ensure it cannot be inherited.

## When To Use 

- When an object needs to have a single instance that is available to all clients.

## Resources

[:material-file-document-outline: Singleton](https://refactoring.guru/design-patterns/singleton)





