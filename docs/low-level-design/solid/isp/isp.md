# Interface Segregation Principle Example

Interface segregation principle: make fine grained interfaces that are client-specific. Clients should not be forced to implement interfaces they do not use. 

???+ code "Invalid Example"
    === "C#"
        ```c# linenums="1" title="Invalid Example" hl_lines="15-18 20-23 39-42" 
        public interface Clock
        {
            void SetAlarm(DateTime time);
            float ReadThermometer();
            void TuneInToRandomRadio();
        }

        public class AncientClock : Clock
        {
            public void SetAlarm(DateTime time)
            {
                // Code to set alarm
            }

            public float ReadThermometer()
            {
                throw new InvalidOperationException("Ancient clocks do not have thermometers");
            }

            public void TuneInToRandomRadio()
            {
                throw new InvalidOperationException("Ancient clocks do not have radios");
            }
        }

        public class ModernClock : Clock
        {
            public void SetAlarm(DateTime time)
            {
                // Code to set alarm
            }

            public float ReadThermometer()
            {
                // Code to read thermometer and return temp
                return 0;
            }

            public void TuneInToRandomRadio()
            {
                throw new InvalidOperationException("Modern clocks do not have radios");
            }
        }
        ```
    This breaks the interface segregation example because the functions ``ReadThermometer`` and ``TuneInToRadio`` in the ``AncientClock`` class needed to be defined even though an ancient clock does not have those features. 

    This same example can be seen in the ``TuneInToRadio`` function in the ``ModernClock`` class as well. 
    

???+ code "Valid Example"
    === "C#"
        ```c# linenums="1" title="Valid Example" hl_lines="1-4 6-9 11-14"
        public interface Thermometer
        {
            float ReadThermometer();
        }

        public interface Alarm
        {
            void SetAlarm(DateTime time);
        }

        public interface RadioPlayer
        {
            void TuneInToRandomRadio();
        }

        public class AncientClock : Alarm
        {
            public void SetAlarm(DateTime time)
            {
                // Code to set alartm
            }
        }

        public class ModernClock : Alarm, Thermometer
        {
            public void SetAlarm(DateTime time)
            {
                // Code to set alarm
            }

            public float ReadThermometer()
            {
                // Code to read thermometer and return temp
                return 0;
            }
        }
        ```
    This example fixes the issues in the invalid interface segregation example. It does this by separating the functions in the ``Clock`` interface nto separate interfaces. 

    Now when a derived class like ``AncientClock`` or ``ModernClock`` needs to inherit these functions they can inherit from the respective interfaces.
    

