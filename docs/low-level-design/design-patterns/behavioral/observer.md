# Observer

## Overview

The **Observer** design pattern is a publisher/subscriber pattern. It allows for a group of subscriber objects to *subscribe* to a publish object. When an event occurs on the publish object, it will notify the corresponding subscribers. 

### Structure 

- **Publisher:** Main object that issues "events" to subscriber objects when a change or event happens. The publisher method also has  ``subscribe`` and ``unsubscribe`` functions to assign subscribers to the publisher.  
- **Subscriber Interface:** Blueprint or interface of what the subscriber should look like. Defines the ``update`` function which all concrete subscribers must have. 
- **Concrete Subscriber:** Actual object that subscribes to a publisher. It contains the implementation of ``update`` and performs business logic when a publish notifies it. 

## Observer Example 

### Problem

Imagine designing the software for a smart home hub. The hub is the general brain for connecting to various smart devices in a house.  

For now, these are the following smart devices available: 

- HVAC control system
- Lighting control system 
- Thermostat
- Dashboard

The smart home hub contains all the sensors that gathers environmental metrics in the home (temperature & ambient light levels). The smart home hub should relay changes in these metrics to the corresponding smart devices. 

!!! note "Try It Out!"
    Try to integrate the observer pattern to create this platform! You can take a look at the ``Non Observer Example`` to get started.
  
### Non Observer Example

???+ Example "Non Observer Example"

    === "Python"
        ```python linenums="1" title="Example Code"
        class HVACControl:
            def turn_on(self):
                print("HVAC system turned on.")


        class LightingControl:
            def turn_on(self):
                print("Lighting system turned on.")


        class Dashboard:
            def display(self):
                print("Dashboard displayed.")


        class Thermostat:
            def show_temperature(self, temperature: float):
                print(f"Currently {temperature} degrees.")
        ```

        ```python title="Client Code"
        class SmartHomeSensorHub:
            def __init__(self):
                # Connect all smart devices to hub
                self.hvac_control = HVACControl()
                self.lighting_control = LightingControl()
                self.dashboard = Dashboard()
                self.thermostat = Thermostat()

            def on_temperature_change(self, temperature: float):
                print("--- Temperature change detected ---")
                if temperature > 55:
                    self.hvac_control.turn_on()
                elif temperature < 75:
                    self.hvac_control.turn_on()

                self.thermostat.show_temperature(temperature)
                self.dashboard.display()

            def on_light_change(self):
                print("--- Light change detected ---")
                self.dashboard.display()
                self.lighting_control.turn_on()

        hub = SmartHomeSensorHub()

        # Mock - sensor detect light change
        hub.on_light_change()

        # Mock - sensor detect temperature change
        hub.on_temperature_change(60)
        ```

        ```title="Output"
        --- Light change detected ---
        HVAC system turned on.
        Lighting system turned on.
        Dashboard displayed.
        Currently None degrees.
        --- Temperature change detected ---
        HVAC system turned on.
        Lighting system turned on.
        Dashboard displayed.
        Currently 60 degrees.
        ```

        There are a few issues with this implementation. To best illustrate these issues, here are some user stories that highlight the flaws of this implementation. 

        1. What if a new smart device is added? 

            This would require refactoring the ``SmartHomeSensorHub``. Breaking the open-closed principle 

        2. What if a device is temporarily disabled?

            This would require refactoring ``SmartHomeSensorHub`` to stop notify to the device. Again, breaking the open-closed principle.

        This implementation also breaks the single responsibility principle since the ``SmartHomeSensorHub`` needs to know about every smart device.  

### Observer Example

???+ Example "Observer Example"

    === "Python"
        ```python title="Example Code"
        # Event class to encapsulate event data
        class Event:
            def __init__(self, data=None):
                self.data = data


        # Subscriber Interface
        class SmartDeviceListener:
            def update(self, event: Event):
                pass


        # Publisher
        class SmartDeviceManager:
            def __init__(self):
                self.subscribers = []

            def subscribe(self, subscriber: SmartDeviceListener):
                self.subscribers.append(subscriber)

            def unsubscribe(self, subscriber: SmartDeviceListener):
                self.subscribers.remove(subscriber)

            def update(self, event: Event):
                for subscriber in self.subscribers:
                    subscriber.update(event)


        # Concrete Subscriber
        class HVACControlListener(SmartDeviceListener):
            def update(self, event: Event):
                print("HVAC system turned on.")


        # Concrete Subscriber
        class LightingControlListener(SmartDeviceListener):
            def update(self, event: Event):
                print("Lighting system turned on.")


        # Concrete Subscriber
        class DashboardListener(SmartDeviceListener):
            def update(self, event: Event):
                print("Dashboard displayed.")


        # Concrete Subscriber
        class ThermostatListener(SmartDeviceListener):
            def update(self, event: Event):
                print(f"Currently {event.data} degrees.")
        ```

        ```python title="Client Code"
        class SmartHomeSensorHub:
            def __init__(self):
                # Connect all smart devices to hub
                self.device_manager = SmartDeviceManager()
                self.device_manager.subscribe(HVACControlListener())
                self.device_manager.subscribe(LightingControlListener())
                self.device_manager.subscribe(DashboardListener())
                self.device_manager.subscribe(ThermostatListener())

            def on_temperature_change(self, temperature: float):
                print("--- Temperature change detected ---")
                self.device_manager.update(Event(temperature))

            def on_light_change(self):
                print("--- Light change detected ---")
                self.device_manager.update(Event())

        hub = SmartHomeSensorHub()

        # Mock - sensor detect light change
        hub.on_light_change()

        # Mock - sensor detect temperature change
        hub.on_temperature_change(60)
        ```

        ```title="Output"
        --- Light change detected ---
        HVAC system turned on.
        Lighting system turned on.
        Dashboard displayed.
        Currently None degrees.
        --- Temperature change detected ---
        HVAC system turned on.
        Lighting system turned on.
        Dashboard displayed.
        Currently 60 degrees.
        ```

        In this implementation we have a publisher ``SmartDeviceManager`` and the subscribers ``SmartDeviceListeners``.

        Using the same user story as the non-observer example, the issues should now be resolved with the observer pattern. 

        1. What if a new smart device is added? 

            To add a new smart device, just create a new concrete subscriber and use the ``subscribe`` function in ``SmartDeviceManager`` to appropriately subscribe it. 

        2. What if a device is temporarily disabled?

            Use the ``unsubscribe`` to remove it from the subscription list in ``SmartDeviceManager``. 
        
        This implementation neatly decouples responsibilities and allows smart devices to be extensible. 

## Analysis

### SOLID principle

- Single responsibility principle: Each **concrete subscriber** has all of its business logic in its own class. The **publisher** doesn't need to know about each **concrete subscriber**, just that the **concrete subscribers** has some function (like ``update``) that it can trigger. 
- Open-Closed principle: thanks to the ``subscribe`` function in the **publisher** object, new **concrete subscribers** can be made and attached to the **publisher** without ever touching any existing code. 
- Dependency inversion principle: **publisher** depends on abstract **subscriber interface**

### When to use?

- A one-to-many dependency (many subscribers need to interact with a single publisher). 
- Number of dependents if dynamic (can easily subscribe/unsubscribe to a publisher)

### Pros & Cons

Pros:

- Makes code adhere to open-closed principle
- Allows for establishing relationship at runtime (subscribe/unsubscribe at runtime)

Cons: 

- Harder to debug, can be difficult to trace flow of data in a system with many subscribers
- Order of notification, there is no guarantee order of which observers are notified
- Memory leaks (lapsed listener). If a subscriber isn't detached when it's no longer needed, the publisher will keep a reference to it. This prevents garbage collector from cleaning it up.
