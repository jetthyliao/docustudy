# Factory Method

## Overview

The **Factory Method** design pattern defines an interface for creating objects, but allows subclasses to alter the type of object that is created.

### Structure

- **Creator:** class that contains the *factory method* and additional optional operations. The role of the ``Creator`` class is not solely product creation, it has other operations that are related to the product as well. 

    - **Factory Method:** method that creates the subclasses, the return type should be the Product interface. This can be set to ``abstract`` so subclasses implement their own versions of the method, or it can return some sort of default.

- **Concrete Creator:** these are the subclass objects that overrides the ``factory method`` to have its own object construction. The object it constructs is a concrete product.
- **Product:** interface that declares all the common properties between objects that can be produced by the ``Creator``
- **Concrete Product:** specific implementations of product interface

## Factory Method Example

### Problem

Imagine a new feature to send notifications. It initially supports Microsoft Teams, but is later extended to support SMS notifications.

This program has two classes to handle notifications: 

- ``TeamsNotification``
- ``SMSNotification``

It also has a class ``Sender`` to construct ``Notifications`` and trigger sending messages on them.

!!! note "Try It Out!"
    Try to integrate the factory method to create this feature! You can take a look at the ``Non Factory Example`` to get started.


### Non Factory Method Example

???+ Example "Non Factory Method Example"

    === "Python"

        ```python linenums="1" title="Example Code" 
        # Product Interface Class
        class Notification:
            def send(self): ...


        # Concrete Product Subclass
        class TeamsNotification(Notification):
            def send(self):
                print("TEAMS NOTIFICATION: sup")


        # Concrete Product Subclass
        class SMSNotification(Notification):
            def send(self):
                print("SMS NOTIFICATION: yo")


        class Sender:
            def create_notification(self, sender_type: str):
                if sender_type == "teams":
                    return TeamsNotification()
                elif sender_type == "sms":
                    return SMSNotification()
                else:
                    raise ValueError(f"Sender type {sender_type} not supported")

            def notify(self, sender_type):
                notification: Notification = self.create_notification(sender_type)
                notification.send()
        ```

        ```python title="Client Code"
        sender = Sender()
        sender.notify("teams")
        sender.notify("sms")
        ```

        ```title="Output"
        TEAMS NOTIFICATION: sup
        SMS NOTIFICATION: yo
        ```

        This implementation handles the two notifications ``TeamsNotification`` and ``SMSNotification`` with the ``Sender`` class. The ``Sender`` class has a function called ``create_notification`` which takes in a string to indicate the type of notification the sender should send. 

        The issue with this implementation is if a new notification type were to be added, the ``create_notification`` function would need to be refactored to handle that. This breaks multiple SOLID principles.

### Factory Method Example

???+ Example "Factory Method Example"

    === "Python"

        ```python title="Example Code"
        # Product Class
        class Notification:
            # Abstract
            def send(self): ...


        # Concrete Product Subclass
        class TeamsNotification(Notification):
            def send(self):
                print("TEAMS NOTIFICATION: sup")


        # Concrete Product Subclass
        class SMSNotification(Notification):
            def send(self):
                print("SMS NOTIFICATION: yo")


        # Creator Class
        class Sender:
            # Abstract Factory Method
            def create_notification(self): ...  # Abstract or can return some default

            def notify(self):
                notification: Notification = self.create_notification()
                notification.send()


        # Concrete Creator Subclass
        class TeamsSender(Sender):
            def create_notification(self):
                return TeamsNotification()


        # Concrete Creator Subclass
        class SMSSender(Sender):
            def create_notification(self):
                return SMSNotification()
        ```

        ```python title="Driver Code"
        sender_type = "teams"
        sender: Sender = Sender()

        if sender_type == "teams":
            sender = TeamsSender()
        elif sender_type == "sms":
            sender = SMSSender()
        else:
            raise ValueError(f"Sender type {sender_type} not supported")

        sender.notify()
        ```

        ```title="Output"
        TEAMS NOTIFICATION: sup
        ```

        The ``Sender`` class acts as a guideline for the various **concrete creator subclasses** (``TeamsSender`` and ``SMSSender``). All subclasses must implement the **abstract factory method** ``create_notification``, each implementation creating their respective ``Notification`` product (``TeamsNotification`` or ``SMSNotification``). 

        A user can safely assume the **abstract factory method** in ``Sender`` is implemented in any ``Sender`` subclasses. This enables them to extend the code and handle multiple subclasses, but never having to touch the central parent ``Sender`` class.

        !!! note "python specific implementation notes"

            In traditionally strongly-typed OOP languages, the **creator parent class** is an *abstract class* and the **product parent class** is an *interface*

            In python, we can duck type everything so theres multiple ways we can implement them: 
                - python protocol 
                - ABC library: indicate its an abstract class
                - regular duck typing 

## Analysis

### SOLID principles

- Single responsibility principle: the pattern utilizes the *concrete create subclasses* to extend supporting additional *product subclasses*. This means the primary *concrete class* does not need to be refactored when extending. 
- Open-Closed principle: (same as single responsibility principle) no need to edit primary *concrete class* when extending.
- Liskov substitution principle: because client depends on abstractions (concrete class and product interface), the subclasses can be replaced without breaking the workflow.
- Dependency inversion: actual creation of *products* are in the *concrete subclass* and depends on the abstraction set by the *abstract factory method*.  

### When to use?

- When you don't know beforehand the exact *Products* or types of the objects your code should work with. 
- When you want to provide users a way to extend its internal components

### Pros & Cons

Pros: 

- Decouples creation and usage (clients only interact with abstractions in parent classes)
- Supports Open/Closed principle (new products doesn't require changing existing code)
- Flexible and extensible
- Centralize object creation (all in factory method)
- Encourages single responsibility (each product subclass handles its own behavior)

Cons:

- Class explosion (need new *creator subclass* and *product subclass* for each new product)
- Increased complexity

## Resources

[:material-video-outline: Factory Method Example](https://www.youtube.com/watch?v=EdFq_JIThqM)

[:material-file-document-outline: Factory Method](https://refactoring.guru/design-patterns/factory-method)

