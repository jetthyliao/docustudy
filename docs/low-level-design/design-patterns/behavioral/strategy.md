# Strategy 

## Overview

The **Strategy** design pattern enables defining a group of algorithms, placing them into separate classes with individual & unique implementations, but making their objects (the class themselves) interchangeable.  

### Structure

- **Context:**  This is what the client will mainly interact with. The ``Context`` maintains a reference to one of the ``Concrete Strategy``. It uses the ``Concrete Strategy`` reference to relay instructions from client to functions within the ``Concrete Strategy``. 
- **Strategy Interface:** common interface for all ``Concrete Strategies``. Declares methods the ``Context`` will use to execute the strategy.  
- **Concrete Strategy:** class with actual implementation of the methods declared in ``Strategy Interface``. Each ``Concrete Strategy`` may have their own unique implementation for the same method. 

## Strategy Example

### Problem

Imagine building a e-commerce platform. The platform handles electronic transactions by communicating with payment providers (Stripe, PayPal, etc). Some of the key functions of this platform are:

- checking status of provider (health check)
- authenticating user 
- checking if currency is supported
- checking if region is serviceable
- processing payment

The initial app only supported Stripe as the main payment provider. As the project continued, PayPal was introduced as another provider. 

!!! note "Try It Out!"
    Try to integrate the strategy pattern to create this platform! You can take a look at the ``Non Strategy Example`` to get started.


### Non Strategy Example

???+ Example "Non Strategy Example"

    === "Python"

        ```python linenums="1" title="Example Code"
        # import stripe_api
        # import paypal_api

        class PaymentProcessor:
            def __init__(self, provider: str):
                self.provider = provider

            def authenticate_user(self, username: str):
                if self.provider == "stripe":
                    print(f"Stripe API - authenticating {username}")
                elif self.provider == "paypal":
                    print(f"PayPal API - authenticating {username}")
                
                return True # Assume authentication is successful for simplicity

            def health_check(self):
                if self.provider == "stripe":
                    print("Stripe API - health check")
                elif self.provider == "paypal":
                    print("PayPal API - health check")

                return True # Assume providers are both healthy 

            def is_currency_supported(self, currency: str):
                if self.provider == "stripe":
                    print("Stripe API - checking currency support")
                    return currency in ["USD", "EUR", "GBP"]
                elif self.provider == "paypal":
                    print("PayPal API - checking currency support")
                    return currency in ["USD", "EUR"]

            def is_region_serviceable(self, region: str):
                if self.provider == "stripe":
                    print("Stripe API - checking region serviceability")
                    return region in ["US", "EU", "APAC"]
                elif self.provider == "paypal":
                    print("PayPal API - checking region serviceability")
                    return region in ["US", "EU"]

            def process_payment(
                self, amount: float, currency: str, region: str, sender: str, receiver="merchant"
            ):
                if self.provider == "stripe":
                    print("Stripe API - processing payment")
                elif self.provider == "paypal":
                    print("PayPal API - processing payment")
        ```

        ```python title="Client Code"
        pp = PaymentProcessor(provider="stripe")

        # Use Stripe as payment provider
        pp.health_check()
        pp.authenticate_user(username="alice")
        pp.is_currency_supported(currency="USD")
        pp.is_region_serviceable(region="US")
        pp.process_payment(69.69, "USD", "US", "alice")

        # Use PayPal as payment provider
        pp = PaymentProcessor(provider="paypal")
        pp.health_check()
        pp.authenticate_user(username="bob")
        pp.is_currency_supported(currency="EUR")
        pp.is_region_serviceable(region="EU")
        pp.process_payment(49.99, "EUR", "EU", "bob")
        ```

        ```title="Output"
        Stripe API - health check
        Stripe API - authenticating alice
        Stripe API - checking currency support
        Stripe API - checking region serviceability
        Stripe API - processing payment
        PayPal API - health check
        PayPal API - authenticating bob
        PayPal API - checking currency support
        PayPal API - checking region serviceability
        PayPal API - processing payment
        ```

        This implementation handles two providers (Stripe & PayPal). The ``PaymentProcessor`` is the main class the client interacts with. The class knows what provider API to use based on what the client sets as the main ``provider`` in the constructor. 

        Below are issues with this implementation:

        1. Breaks Open/Closed principle, when adding support for a new provider, this class will need to be refactored.
        2. Breaks Single Responsibility principle, the class is responsible for handling Stripe and PayPal functionality.

### Strategy Example

???+ Example "Strategy Example"

    === "Python"
        ```python title="Example Code"
        # Context
        class PaymentProcessorContext:
            def set_strategy(self, strategy):
                self.strategy = strategy

            def execute_status_check(self, username: str, currency: str, region: str):
                self.strategy.health_check()
                self.strategy.authenticate_user(username)
                self.strategy.is_currency_supported(currency)
                self.strategy.is_region_serviceable(region)

            def execute_checkout(
                self, amount: float, currency: str, region: str, sender: str, receiver="merchant"
            ):
                self.strategy.process_payment(amount, currency, region, sender, receiver)


        # Strategy Interface
        class PaymentProcessorStrategy:
            def authenticate_user(self, username: str):
                pass

            def health_check(self):
                pass

            def is_currency_supported(self, currency: str):
                pass

            def is_region_serviceable(self, region: str):
                pass

            def process_payment(self):
                pass


        # Concrete Strategy
        class StripePaymentProcessor(PaymentProcessorStrategy):
            def authenticate_user(self, username: str):
                print(f"Stripe API - authenticating {username}")
                return True  # Assume authentication is successful for simplicity

            def health_check(self):
                print("Stripe API - health check")
                return True  # Assume providers are both healthy

            def is_currency_supported(self, currency: str):
                print("Stripe API - checking currency support")
                return currency in ["USD", "EUR", "GBP"]

            def is_region_serviceable(self, region: str):
                print("Stripe API - checking region serviceability")
                return region in ["US", "EU", "APAC"]

            def process_payment(
                self, amount: float, currency: str, region: str, sender: str, receiver="merchant"
            ):
                print("Stripe API - processing payment")


        # Concrete Strategy
        class PayPalPaymentProcessor(PaymentProcessorStrategy):
            def authenticate_user(self, username: str):
                print(f"PayPal API - authenticating {username}")
                return True  # Assume authentication is successful for simplicity

            def health_check(self):
                print("PayPal API - health check")
                return True  # Assume providers are both healthy

            def is_currency_supported(self, currency: str):
                print("PayPal API - checking currency support")
                return currency in ["USD", "EUR"]

            def is_region_serviceable(self, region: str):
                print("PayPal API - checking region serviceability")
                return region in ["US", "EU"]

            def process_payment(
                self, amount: float, currency: str, region: str, sender: str, receiver="merchant"
            ):
                print("PayPal API - processing payment")

        ```

        ```python title="Client Code"
        context = PaymentProcessorContext()

        # Use Stripe as the payment provider
        context.set_strategy(StripePaymentProcessor())
        context.execute_status_check(username="alice", currency="USD", region="US")
        context.execute_checkout(amount=69.69, currency="USD", region="US", sender="alice")

        # Switch to PayPal as the payment provider
        context.set_strategy(PayPalPaymentProcessor())
        context.execute_status_check(username="bob", currency="EUR", region="EU")
        context.execute_checkout(amount=49.99, currency="EUR", region="EU", sender="bob")
        ```

        ```title="Output"
        Stripe API - health check
        Stripe API - authenticating alice
        Stripe API - checking currency support
        Stripe API - checking region serviceability
        Stripe API - processing payment
        PayPal API - health check
        PayPal API - authenticating bob
        PayPal API - checking currency support
        PayPal API - checking region serviceability
        PayPal API - processing payment
        ```

        The ``PaymentProcessorStrategy`` acts as a guideline for the various functions each strategy must implement for the ``PaymentProcessorContext`` context. 

        With this pattern, a client can easily swap out different strategies with the ``set_strategy`` function in the ``PaymentProcessorContext`` class. 

        The client also doesn't need to know about the specific implementation of a Stripe vs PayPal processor. All they need to know is what functions are available to them (``authenticate_user``, ``health_check``, etc). In fact, they also don't need to know about these since the ``PaymentProcessorContext`` provides a function `` execute_status_check`` which will call all the "status" related functions. 

        This pattern makes the code much more flexible as new providers can be implemented as new **concrete strategies**. 

## Analysis

### SOLID principles

- Single responsibility principle: since the strategy pattern separates strategies into individual classes, the only reason a class needs to change is if implementation for that strategy needs to be updated.
- Open-Closed principle: the **context** works as long as a valid **concrete strategy** is given. This means we can extend functionality by creating new **concrete strategies** and the **context** should still behave properly.
- Dependency inversion principle: the **context** depends on the **concrete interface** rather than explicit **concrete strategies**.

### When to use?

- Use when you have different variants of an algorithm. (e.g. algorithms to interact with database, but we have 3 variety of databases)
- Scenarios where you have conditional bloat 
    ```python
    if algo == "bubble sort":
        # do bubble
    elif algo == "merge sort": 
        # do merge
    elif algo = "random":
        # do random
    ```
- When you want to encapsulate complex code. Can hide complex implementation in a strategy and the user just needs to know about the simple functions exposed by a strategy/context
- When you need to dynamically swap between behaviors. (e.g. aggressive vs defensive mode in a video game. These behaviors can be strategies that can be swapped dynamically)

### Pros & Cons

Pros: 

- Can easily swap algorithms/strategies at run time
- Isolate implementation details
- Supports open/closed

Cons: 

- Client must know difference between strategies to know which one to use (deep knowledge of behavior might be lost due to encapsulation)
- Can introduce complexity. Not worth implementing if you know options won't expand (just implement if-else)
