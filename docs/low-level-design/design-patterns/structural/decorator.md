# Decorator 

The **Decorator** design pattern attaches new behavior to an object by placing the objects inside special wrapper objects that contain the behaviors. 

This design pattern is often synonymous with a **wrapper**. A **wrapper** is an object that is linked with a target object and it contains the same methods as the target and delegates all the requests it receives. The difference is the **wrapper** may alter the results either before or after it passes to the target.

## Structure

Below is an example of the decorator structure

!!! tip "Decorator (Object) Structure UML"
    <figure markdown>
    </figure>

- **Component:** this declares the common interface used by both wrappers and wrapped objects.
- **Concrete Component:** this is the class for the object being **wrapped**. It defines basic behavior that may be altered by the decorators.
- **Base Decorator:** this class acts as the default **wrapper**
    - It contains a field of the **wrapped** object
    - The **wrapped** object field should be of type **Component** so it can handle both new decorators and concrete components
    - The decorator delegates operations to the wrapped object
- **Concrete Decorator:** defines extra behaviors that can be added to the components dynamically
    - Overrides methods in the **base decorator** and execute its logic before or after calling the parent method

## Decorator Example

Imagine a notifier program that has the ability to send notifications to the following places:

- Email (default)
- Microsoft Teams
- Slack

The notifier program should be able to send notifications to a single place or to multiple places if desired. 

Below are two implementations, one utilizing decorator design pattern and one without. 

### Non Decorator Example

???+ Example "Non Decorator Example"

    === "C#"

        ```c# linenums="1" title="Example Code" hl_lines="45-59"
        public class Notifier
        {

            private string name;

            public Notifier(string name)
            {
                this.name = name;
            }

            public virtual void SendMessage()
            { 
                Console.WriteLine("Sending notification through Email to {0}", name);
            }

            public string GetUser()
            {
                return name;
            }
        }

        public class MicrosoftTeamsNotifier : Notifier
        {
            public MicrosoftTeamsNotifier(string name) : base(name)
            {
            }

            public override void SendMessage()
            {
                Console.WriteLine("Sending notification through Microsoft Teams to {0}", base.GetUser());
            }
        }
        public class SlackNotifier : Notifier
        {
            public SlackNotifier(string name) : base(name)
            {
            }

            public override void SendMessage()
            {
                Console.WriteLine("Sending notification through Slack to {0}", base.GetUser());
            }
        }

        public class MicrosoftTeamsAndSlackNotifier : Notifier
        {
            public MicrosoftTeamsAndSlackNotifier(string name) : base(name)
            {
            }

            public override void SendMessage()
            { 
                MicrosoftTeamsNotifier teamsNotifier = new MicrosoftTeamsNotifier(base.GetUser());
                SlackNotifier slackNotifier = new SlackNotifier(base.GetUser());
                base.SendMessage();
                teamsNotifier.SendMessage();
                slackNotifier.SendMessage();
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="3-5 7-9"
        static void Main(string[] args)
        {
            // Single notification (to email)
            Notifier notifier = new Notifier("Jessy");
            notifier.SendMessage();
            
            // Multiple notification (to all)
            notifier = new MicrosoftTeamsAndSlackNotifier(notifier.GetUser());
            notifier.SendMessage();
        }
        ```
        
        ```title="Output"
        Sending notification through Email to Jessy

        Sending notification through Email to Jessy
        Sending notification through Microsoft Teams to Jessy
        Sending notification through Slack to Jessy
        ```

    In this example a hierarchy architecture approach was implemented, meaning the default notifier (emails) is the main class and the other two notifiers (Microsft Teams and Slack) are subclasses of the main class. 

    This implementation works fine when signaling one notifier at a time. This can be seen in lines 3-5 in the driver code. 

    The issue with this example is when triggering multiple notifications at once. The class ``MicrosoftTeamsAndSlackNotifier`` on lines 45-59 in the example code had to be introduced to handle multiple notifications. If this example contained more notifiers, more of these multi-notifier classes would have to be created which will quickly become difficult to manage. 

    This implementation also breaks the following SOLID principles:

    - Single responsibility principle: lets say a new method is introduced to the Slack notifier called ``SaveToLogs``. This change would require refactoring the ``MicrosoftTeamsAndSlackNotifier`` which breaks the single responsibility principle. 

### Decorator Example

???+ Example "Decorator Example"

    === "C#"

        ```c# linenums="1" title="Example Code" hl_lines="30-49"
        // Component (Interface)
        public interface INotifier
        {
            public void SendMessage();
            public string GetUser();
        }

        // Concrete Component (Class) 
        public class Notifier : INotifier
        {
            private string name;

            public Notifier(string name)
            {
                this.name = name;
            }

            public string GetUser()
            {
                return name;
            }

            // Default behavior is to send alerts by email
            public void SendMessage()
            {
                Console.WriteLine("Sending notification through Email to {0}", name);
            }
        }

        // Base Decorator (Abstract Class)
        public abstract class BaseNotifierDecorator : INotifier
        {
            private INotifier wrapped;

            public BaseNotifierDecorator(INotifier wrapped)
            {
                this.wrapped = wrapped;
            }

            public virtual void SendMessage()
            {
                wrapped.SendMessage();
            }

            public virtual string GetUser()
            {
                return wrapped.GetUser();
            }
        }

        // Concrete Decorator (Class)
        public class MicrosoftTeamsDecorator : BaseNotifierDecorator
        {
            public MicrosoftTeamsDecorator(INotifier wrapped) : base(wrapped)
            {
            }

            public override void SendMessage()
            {
                base.SendMessage();
                Console.WriteLine("Sending notification through Microsoft Teams to {0}", base.GetUser());
            }
        }

        // Concrete Decorator (Class)
        public class SlackDecorator : BaseNotifierDecorator
        {
            public SlackDecorator(INotifier wrapped) : base(wrapped)
            {
            }

            public override void SendMessage()
            {
                base.SendMessage();
                Console.WriteLine("Sending notification through Slack to {0}", base.GetUser());
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="8-9"
        public static void Main(string[] args)
        {
            // Single notification (to email)
            INotifier notifier = new Notifier("Jessy");
            notifier.SendMessage();

            // Multiple notification (to all)
            notifier = new MicrosoftTeamsDecorator(notifier);
            notifier = new SlackDecorator(notifier);
            notifier.SendMessage();
        }
        ```
        
        ```title="Output"
        Sending notification through Email to Jessy

        Sending notification through Email to Jessy
        Sending notification through Microsoft Teams to Jessy
        Sending notification through Slack to Jessy
        ```
    
    In this example the **decorator** design pattern is utilized. The core of this pattern is the **BaseDecorator** class ``BaseNotifierDecorator``. This decorator is responsible for triggering the ``SendMessage`` method to its wrapped object (which should be the **Concrete Component**).

    Since all the decorators utilize the ``INotifier`` interface, decorators can be wrapped on top of each other which can be seen on lines 8 and 9 in the driver code. 

    Each of the **decorators** follow the same pattern for the ``SendMessage`` method: 
    
    1. Call the wrapped object's ``SendMessage`` 
    2. Run its custom ``SendMessage`` implementation. 

    As the decorators continued to get wrapped within each other, it triggers a chain of these methods that run in order. 

    This fixes the broken SOLID principles from the last example: 

    - Single responsibility principle: using the same example of introducing the new method ``SaveToLogs`` to the Slack notifier, only the ``SlackDecorator`` would need to be refactored to integrate these changes, nothing else will be effected.
    
## When To Use

- When the program needs to assign extra behaviors to objects at runtime without breaking code that uses those objects.  
- When its awkward to extend and object's behavior using inheritance. 

## Resources

[:material-file-document-outline: Decorator](https://refactoring.guru/design-patterns/decorator)

[:material-video-outline: Decorator](https://www.youtube.com/watch?v=v6tpISNjHf8)

