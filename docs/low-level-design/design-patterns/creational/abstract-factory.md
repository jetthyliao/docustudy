# Abstract Factory

## Overview

The **Abstract Factory** design pattern allows for the production of *families* of related objects without specifying their concrete classes. 

### Structure

Below is an example of the abstract factory structure

!!! tip "Abstract Factory Structure UML"
    <figure markdown>
        <!--
        ![Factory Method Diagram](../../assets/images/design-patterns/factory-method.svg#only-dark)
        ![Factory Method Diagram](../../assets/images/design-patterns/factory-method-light.svg#only-light)
        -->
    </figure>

- **Abstract Products:** abstract class which represents the distinct types of a product.
- **Concrete Products:** various implementations of the abstract products.
- **Abstract Factory:** interface which declares the method used to create the various products.
- **Concrete Factory:** implementation of the creation method. Each of these concrete factory corresponds to a **set** (or family) of concrete products.

## Abstract Factory Example

Imagine building a generic tool to spin up various cloud services. This tool handles the following cloud service providers: 

- AWS
- Azure

The services the tool spins up are: 

- Databases (AmazonRDS for AWS and AzureDB for Azure)
- Virtual Machines (AmazonEC2 for AWS and AzureVM for Azure)

This is a good example to integrate the **abstract factory** design patterns since there are two sets/family (AWS/Azure) of products (Database/VMs)

!!! note "Try It Out!"
    Try to create an abstract factory class to handle this feature! You can take a look at the ``Non Abstract Factory Example`` to get started.

### Non Abstract Factory Example

???+ Example "Non Abstract Factory Example"

    === "Python"

        ```python linenums="1" title="Example Code" hl_lines="55-71"
        # Product Abstract Class
        class Database:
            def create_tables():
                pass

            def get_data():
                pass


        # Concrete Product Subclass
        class AmazonRDS(Database):
            def create_tables(self):
                print("[AWS]: create tables")

            def get_data(self):
                print("[AWS]: getting data")


        # Concrete Product Subclass
        class AzureDB(Database):
            def create_tables(self):
                print("[Azure]: create tables")

            def get_data(self):
                print("[Azure]: getting data")


        # Product Abstract Class
        class VirtualMachine:
            def start_instance():
                pass

            def stop_instance():
                pass


        # Concrete Product Subclass
        class AmazonEC2(VirtualMachine):
            def start_instance(self):
                print("[AWS]: starting ec2 instance")

            def stop_instance(self):
                print("[AWS]: stopping ec2 instance")


        # Concrete Product Subclass
        class AzureVM(VirtualMachine):
            def start_instance(self):
                print("[Azure]: starting AVM instance")

            def stop_instance(self):
                print("[Azure]: stopping AVM instance")


        # Factory pattern
        class CloudFactory:
            def create_database(self, cloud_service: str) -> Database:
                if cloud_service == "Azure":
                    return AzureDB()
                elif cloud_service == "Amazon":
                    return AmazonRDS()
                else:
                    ValueError(f"Cloud service {cloud_service} not supported")

            def create_virtual_machine(self, cloud_service: str) -> VirtualMachine:
                if cloud_service == "Azure":
                    return AzureVM()
                elif cloud_service == "Amazon":
                    return AmazonEC2()
                else:
                    ValueError(f"Cloud service {cloud_service} not supported")
        ```

        ```python title="Client Code"
        cloud = CloudFactory2()
        cloud_service="Azure"
        db = cloud.create_database(cloud_service)
        vm = cloud.create_virtual_machine(cloud_service)
        db.create_tables()
        vm.start_instance()
        
        cloud_service="Amazon"
        db = cloud.create_database(cloud_service)
        vm = cloud.create_virtual_machine(cloud_service)
        db.create_tables()
        vm.start_instance()
        ```

        ```title="Output"
        [AWS]: create tables
        [AWS]: starting ec2 instance
        [Azure]: create tables
        [Azure]: starting AVM instance
        ```

        This implementation uses a design pattern similar to the factory pattern (not to be confused with the [**factory method**](factory-method.md) pattern).

        The ``CloudFactory`` class uses the factory pattern to construct the two services ``Database`` and ``VirtualMachine`` with the ``create_database`` and ``create_virtual_machine`` function. Both these functions need the parameter ``cloud_service`` to determine what type of database or virtual machine needs to be created (AWS or Azure).

        This becomes an issue when additional cloud providers are extended to this program. Both the ``create_database`` and ``create_virtual_machine`` functions would need to be refactored to handle additional cloud providers. This breaks the SOLID principle.

### Abstract Factory Example

???+ Example "Abstract Factory Example"

    === "C#"

        ```python linenums="1" title="Example Code" 
        # Product Abstract Class
        class Database:
            def create_tables():
                pass

            def get_data():
                pass


        # Concrete Product Subclass
        class AmazonRDS(Database):
            def create_tables(self):
                print("[AWS]: create tables")

            def get_data(self):
                print("[AWS]: getting data")


        # Concrete Product Subclass
        class AzureDB(Database):
            def create_tables(self):
                print("[Azure]: create tables")

            def get_data(self):
                print("[Azure]: getting data")


        # Product Abstract Class
        class VirtualMachine:
            def start_instance():
                pass

            def stop_instance():
                pass


        # Concrete Product Subclass
        class AmazonEC2(VirtualMachine):
            def start_instance(self):
                print("[AWS]: starting ec2 instance")

            def stop_instance(self):
                print("[AWS]: stopping ec2 instance")


        # Concrete Product Subclass
        class AzureVM(VirtualMachine):
            def start_instance(self):
                print("[Azure]: starting AVM instance")

            def stop_instance(self):
                print("[Azure]: stopping AVM instance")


        # Abstract Factory Class
        class CloudFactory:
            def create_database() -> Database:
                pass

            def create_virtual_machine() -> VirtualMachine:
                pass


        # Concrete Factory Subclass
        class AmazonCloudFactory(CloudFactory):
            def create_database(self):
                return AmazonRDS()

            def create_virtual_machine(self):
                return AmazonEC2()


        # Concrete Factory Subclass
        class AzureCloudFactory(CloudFactory):
            def create_database(self):
                return AzureDB()

            def create_virtual_machine(self):
                return AzureVM()
        ``` 

        ```python title="Client Code"
        # Amazon cloud
        cloud = AmazonCloudFactory()

        db = cloud.create_database()
        vm = cloud.create_virtual_machine()

        db.create_tables()
        vm.start_instance()

        # Azure cloud
        cloud = AzureCloudFactory()

        db = cloud.create_database()
        vm = cloud.create_virtual_machine()

        db.create_tables()
        vm.start_instance()
        ```

        ```title="Output"
        [AWS]: create tables
        [AWS]: starting ec2 instance
        [Azure]: create tables
        [Azure]: starting AVM instance
        ```

        This example introduces the **abstract factory class** ``CloudFactory** which acts as the blueprint on building the two concrete factories ``AmazonCloudFactory`` and ``AzureCloudFactory``. The **abstract factory class** ensures that both subclass factories implement all the functions to spin up the two services (database and virtual machine).

        In the driver code, any ``CloudFactory`` object can be used. Since all ``CloudFactory`` classes are subclasses of the **abstract factory class**, they should be swappable.

## Analysis

### SOLID principles

- Single responsibility principle: new family of products can be added without having to refactor any existing functions
- Open-Closed principle: family of products can be swapped without breaking code

### When to use?

- Usually, most people will start with a [**factory method**](factory-method.md)
- Use when code needs to work with various families of related product, but you don't want it to depend on the concrete classes of those products (might be unknown beforehand or simply want to allow for further extensibility)
- When you have a set of factory methods, abstract factory might help reorganize all of your factories

## Resources

[:material-video-outline: Abstract Factory Example](https://www.youtube.com/watch?v=QNpwWkdFvgQ)

[:material-file-document-outline: Abstract Factory](https://refactoring.guru/design-patterns/abstract-factory)

