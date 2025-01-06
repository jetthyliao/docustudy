# Adapter

The **Adapter** is a design pattern that allows object with incompatible interfaces to collaborate. This adapter bridges between two classes that would normally not be able to communicate with one another. 

Sometimes the Adapter design pattern is also known as a **wrapper** design pattern since it wraps the incompatible interface/class to make it interact with the compatible version. 

## Implementations

There are several implementations of the adapter design pattern that varies based on programming language and context.


1. Object adapter (composition based)

    - Adapter holds an instance of the **service or adaptee** and implements the **client or target interface**. 
    - More flexible as it allows a single adapter to work with multiple **services or adaptees* 
    - Widely used in language like C# or Java

2. Class adapter (inheritance based)

    - Adapter class inherits from both the **client or target interface** and the **service or adaptee**. 
    - Programming languages that allow multiple inheritance (C++) are more likely to use this technique

## Structure

Below is an example of the object adapter structure

!!! tip "Adapter (Object) Structure UML"
    <figure markdown>
    </figure>

- **Client:** class that contains existing business logic.
- **Client Interface or Target Interface:** defines the interface expected by the client. It declares the methods the client can use.
- **Service or Adaptee:** some useful class/object that is incompatible with client code (usually 3rd-party service or legacy code)
- **Adapter:** class that *implements/inherits* the client interface AND *wraps* the service class. It receives calls from the client and translates it for the wrapper service object to understand. 

## Adapter Example

Consider a scenario where there is a program that displays weather information based on the current location data. The program generates weather data in XML format and the weather service API takes in XML data and returns the corresponding weather information. 

A new weather service is now available and the code should be updated to use this new service. The issue is this new service only takes in JSON data for the location data. Additionally, the service is a third party service and the source code can not be edited. 

The adapter design pattern helps bridge the gap with communicating with this incompatible new service. 

A quick break down of this example. There are two services (legacy vs modern) that both expects two different location data types (XML vs JSON)

- Legacy Service: XML data
- Modern Service: JSON data

### Non Adapter Example

???+ Example "Non Adapter Example"

    === "C#" 

        ```c# linenums="1" title="Example Code"
        public class XmlData { }
        public class JsonData { }

        public interface ILegacyWeatherService
        {
            public void GetCurrentWeather(XmlData location);
            public void GetWeekForecast(XmlData location);
        }

        public class LegacyWeatherService : ILegacyWeatherService
        {
            public void GetCurrentWeather(XmlData location)
            {
                Console.WriteLine("Using XML Data to get current weather data...");
            }

            public void GetWeekForecast(XmlData location)
            {
                Console.WriteLine("Using XML Data to get forecast for the week...");
            }
        }

        public class ModernWeatherService
        { 
            public void GetCurrentWeather(JsonData location)
            {
                Console.WriteLine("Using JSON Data to get current weather data..."); }

            public void GetWeekForecast(JsonData location)
            {
                Console.WriteLine("Using JSON Data to get forecast for the week...");
            }

        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="1-5 11-13"
        public static JsonData ConvertXmlToJson(XmlData location)
        {
            Console.WriteLine("Converting XML data to JSON");
            return new JsonData();
        }

        static void Main(string[] args)
        { 
            XmlData geoLocation = new XmlData();

            // LegacyWeatherService service = new LegacyWeatherService();
            // service.GetCurrentWeather(geoLocation);
            // service.GetWeekForecase(geoLocation);

            ModernWeatherService service = new ModernWeatherService();
            JsonData geoLocationJSON = ConvertXmlToJson(geoLocation);
            service.GetCurrentWeather(geoLocationJSON);
            service.GetWeekForecast(geoLocationJSON);
        }
        ``` 

        ```title="Output"
        Converting XML data to JSON
        Using JSON Data to get current weather data...
        Using JSON Data to get forecast for the week...
        ```  

        In this example, the adapter design pattern was not use. Instead, a new method ``ConvertXmlToJson`` was added to handle converting the ``XmlData`` location data to the ``JsonData`` format. 

        This implementation required going into the client code and modifying all instances of the ``XmlData`` location data and converting it to the new data type. 

        This violates the following SOLID principles: 

        - Single responsibility principle: the client code had to be refactored to introduce conversion code
        - Open-closed principle: switching between ``ModernWeatherService`` and ``LegacyWeatherService`` does not work without changing client code first

### Adapter Example

???+ Example "Adapter Example"

    === "C#"

        ```c# linenums="1" title="Example Code" hl_lines="36-62"
        public class XmlData { }
        public class JsonData { }

        public interface ILegacyWeatherService
        {
            public void GetCurrentWeather(XmlData location);
            public void GetWeekForecast(XmlData location);
        }

        public class LegacyWeatherService : ILegacyWeatherService
        {
            public void GetCurrentWeather(XmlData location)
            {
                Console.WriteLine("Using XML Data to get current weather data...");
            }

            public void GetWeekForecast(XmlData location)
            {
                Console.WriteLine("Using XML Data to get forecast for the week...");
            }
        }

        public class ModernWeatherService
        { 
            public void GetCurrentWeather(JsonData location)
            {
                Console.WriteLine("Using JSON Data to get current weather data..."); }

            public void GetWeekForecast(JsonData location)
            {
                Console.WriteLine("Using JSON Data to get forecast for the week...");
            }

        }

        public class ModernWeatherServiceAdapter : ILegacyWeatherService
        {
            private ModernWeatherService modernWeatherService;

            public ModernWeatherServiceAdapter() 
            {
                modernWeatherService = new ModernWeatherService(); 
            }

            public void GetCurrentWeather(XmlData location)
            {
                JsonData jsonData = ConvertXmlToJson(location);
                modernWeatherService.GetCurrentWeather(jsonData);
            }

            public void GetWeekForecast(XmlData location)
            {
                JsonData jsonData = ConvertXmlToJson(location);
                modernWeatherService.GetWeekForecast(jsonData);
            }

            private JsonData ConvertXmlToJson(XmlData location)
            {
                Console.WriteLine("Converting XML data to JSON");
                return new JsonData();
            }
        }
        ```

        ```c# linenums="1" title="Driver Code" hl_lines="5-6"
        static void Main(string[] args)
        {
            XmlData geoLocation = new XmlData();

            // LegacyWeatherService service = new LegacyWeatherService();
            ModernWeatherServiceAdapter service = new ModernWeatherServiceAdapter();

            service.GetCurrentWeather(geoLocation);
            service.GetWeekForecast(geoLocation);
        }
        ``` 

        ```title="Output"
        Converting XML data to JSON
        Using JSON Data to get current weather data...
        Converting XML data to JSON
        Using JSON Data to get forecast for the week...
        ```

    In this example the object adapter was introduce (line 36-62) which automatically handles conversion between the two data types. The adapter also inherited from the ``ILegacyWeatherService`` interface which guarantees that the behavior between the Legacy service and the Modern service will be the same. 

    As seen on line 5 and 6 of the driver code, the two services can be used interchangeably without causing issues to the rest of the client code. 

    This addresses the two SOLID principles that were broken before: 

    - Single responsibility principle: client code now can remain the same (only need to change which service is desired).
    - Open-closed principle: the Legacy and Modern service can be used interchangeably.

## When To use

- When connecting systems or components that weren't built to work together
- When existing code or libraries don't match the current system

## Resources

[:material-file-document-outline: Adapter](https://refactoring.guru/design-patterns/adapter)

[:material-file-document-outline: Adapter](https://www.geeksforgeeks.org/adapter-pattern/)
