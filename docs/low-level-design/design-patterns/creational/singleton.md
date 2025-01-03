# Singleton

The **singleton** design pattern ensures a class has only one instance, while providing a global access point to this instance. Singleton patterns are so common sometimes something could be called a singleton without fulfilling its two functionality (so important it'll be listed again)

1. Ensure a class has just a single instance
2. Provide global access point to that instance

## Structure

Below is an example of the singleton structure

!!! tip "Singleton Structure UML"
    <figure markdown>
        <!--
        -->
    </figure>

- **Singleton Class**: the singleton class (which will fulfill the two functionalities)
    - **Access Point Method**: access point to the single instance

## Singleton Example

This example is implementing a database connection pool. To control access to the database, there should only be one connection pool that is being used. This is a perfect example of when to use the singleton design pattern as it will guarantee one connection pool is established, and a way to access this pool.

### Non Singleton Example

???+ Example "Non Singleton example"

    === "C#"

        ```c# linenums="1" title="Example Code"
        public class DatabaseConnectorPool
        {
            public DatabaseConnectorPool()
            {
                ConnectToDatabases();
            }

            public void ConnectToDatabases() // Example business logic
            { 
                // ... Code to setup multiple connections to database
            }

            public void Query() // Example business logic 
            { 
                // ... Code to pick a database connection and perform query
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="3-4 6-7"
        static void Main(string[] args)
        {
            DatabaseConnectorPool pool1 = new DatabaseConnectorPool();
            pool1.Query();

            DatabaseConnectorPool pool2 = new DatabaseConnectorPool();
            pool2.Query();
        }
        ```

        ```title="Output"
        Setting up connection pool
        Using connection pool to query
        Setting up connection pool
        Using connection pool to query
        ```

    In this example, the ``DatabaseConnectorPool`` class is created twice making two instances of the class, as seen in the driver code. 

    In a real world scenario this would mean two connection pools have been created to the database which is a waste of resources, or if the database doesn't handle concurrent reads/writes well, it could cause errors down the line.

### Singleton Example 

???+ Example "Singleton example"

    ```c# linenums="1" title="Example Code" hl_lines="6 12-19"
    public sealed class DatabaseConnectorPool // Sealed so no subclasses can be made
    {
        private static DatabaseConnectorPool _instance;

        // Hidden Constructor
        private DatabaseConnectorPool()
        { 
            ConnectToDatabases();
        }

        // Access Point Method
        public static DatabaseConnectorPool GetInstance()
        {
            if (_instance == null)
            { 
                _instance = new DatabaseConnectorPool();
            }
            return _instance;
        }

        public void ConnectToDatabases() // Example business logic
        { 
            Console.WriteLine("Setting up connection pool");
            // ... Code to setup multiple connections to database
        }

        public void Query() // Example business logic 
        { 
            Console.WriteLine("Using connection pool to query");
            // ... Code to pick a database connection and perform query
        }
    }
    ```

    ```c# linenums="1" title="Driver Code"
    static void Main(string[] args)
    {
        DatabaseConnectorPool pool1 = DatabaseConnectorPool.GetInstance();
        pool1.Query();

        DatabaseConnectorPool pool2 = DatabaseConnectorPool.GetInstance();
        pool2.Query();
    }
    ```

    ```title="Output"
    Setting up connection pool
    Using connection pool to query
    Using connection pool to query
    ```

    In this example, using the the singleton pattern, the first call of ``GetInstance`` created a new ``DatabaseConnectorPool`` instance and assigned it to the ``_instance`` variable. All subsequent calls to the ``GetInstance`` method will now reference this same instance.

    The ``DatabaseConnectorPool`` class is also set with the ``sealed`` keyword to ensure it cannot be inherited.

    In the output, "Setting up connection pool" only printed once which indicates the pool was setup only once and there is in fact only one instance.

## When To Use 

- When an object needs to have a single instance that is available to all clients.

## Resources

[:material-file-document-outline: Singleton](https://refactoring.guru/design-patterns/singleton)





