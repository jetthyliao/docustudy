# Adapter

The **Adapter** is a design pattern that allows object with incompatible interfaces to collaborate.

## Problem

Assume there is a program that utilizes an API service which returns data in XML format (will label this as API service 1). 

Now assume the program is scaling up and an additional API service (will call it API service 2) needs to be integrated to gather more data. However, this API service returns all its data in JSON format. 

There are two bad approaches to solving this issue: 

1. Rewrite API service 1 or 2 to have a standard data format
2. Rewrite program to handle both data types

Both of these require refactoring which should be avoided. The best solution is utilizing the **adapter** design pattern. 

## Solution

The adapter pattern integrates a class that inherits from whatever object is already supported. In this example, the adapter class would inherit from the API service 1 object. 

The adapter class then takes in objects of the other type (API service 2 object) and contains override methods necessary to have it behave like the original type (API service 1 object).

Essentially, the other object type (API service 2) is being wrapped with all the necessary functionalities for it to behave as the original type (API service 1).

## Structure

- **Client:** class that contains existing business logic.
- **Client Interface:** class that represents object type that must be used to interact with client code. 
- **Service:** some useful class/object that is incompatible with client code (usually 3rd-party or legacy code)
- **Adapter:** class that *implements/inherits* the client interface AND *wraps* the service class. It receives calls from the client and translates it for the wrapper service object to understand. 

## Adapter Example

Lets say there is a program that gathers meteorology data from API services to display weather forecast for the Washington state area.

Originally, there was a single API service to gather the data which was built alongside the client program. This API returns data corresponding to Washington and is in a XML format. Lets assume there is a new API service that needs to be implemented that will fetch new data for Idaho, but this API is in JSON format.

The program consists of the following: 

1. Client code which contains function to forecast the data.
2. Washington meteorology data API (XML)
3. Idaho meteorology data API (JSON)

Below are two implementations to integrate both API into the client code

1. Non Adapter Solution
2. Adapter Solution

### Non Adapter Example

???+ Example "Non Adapter Example"

    ```c# linenums="1" title="Example Code"
    public class Forecaster
    {
        public string GetForecast(WashingtonAPI meteorologyData)
        {
            // Assume XmlData class has a function called XmlExtrapolate that returns forecast data
            string forecastWashington = meteorologyData.GetData().XmlExtrapolate();
            return "Forecast: " + forecastWashington;
        }

        public string GetForecast(IdahoAPI meteorologyData)
        { 
            // Assume XmlData class has a function called JsonExtrapolate that returns forecast data
            string forecastIdaho = meteorologyData.GetData().JsonExtrapolate();
            return "Forecast: " + forecastIdaho; 
        }
    }

    public class WashingtonAPI
    {
        // Other API Functionality
         
        public XmlData GetData()
        {
            return new XmlData();
        }
    }

    public class IdahoAPI 
    {
        // Other API Functionality
         
        public JsonData GetData()
        {
            return new JsonData();
        }
    } 
    ```

    ```c# linenums="1" title="Driver Code"
    Forecaster forecastService = new Forecaster();
    WashingtonAPI wApi = new WashingtonApi();
    IdahoAPI iApi = new IdahoAPI();

    Console.WriteLine(forecastService.GetForecast(wApi))
    Console.WriteLine(forecastService.GetForecast(iApi))
    ``` 

    ```title="Output"
    Forecast: <Washington forecast...>
    Forecast: <Idaho forecast...>
    ```  

    In this example there are the following classes: 

    - ``Forecaster`` the client code
    - ``WashingtonAPI`` API that returns Washington weather data
    - ``IdahoAPI`` API that returns Idaho weather data

    There are also two classes not shown: 

    - ``XmlData`` fake class to represent XML data
    - ``JsonData`` fake class to represent JSON data

    In this solution, method overloading was used to handle the different API services. 
    
    This solution is not ideal as it requires refactoring of the ``Forecaster`` class. This is also a bad solution due to the extremely similar code found in both method overloads. This indicates it could be rewritten into a single method. 

    This issues are addressed with the adapter design pattern. 
    
### Adapter Example

???+ Example "Adapter Example"

    ```c# linenums="1" title="Example Code"
    public class Forecaster
    {
        public string GetForecast(WashingtonAPI meteorologyData)
        {
            // Assume XmlData class has a function called XmlExtrapolate that returns forecast data
            string forecast = meteorologyData.GetData().XmlExtrapolate();
            return "Forecast: " + forecastWashington;
        }
    }

    public class WashingtonAPI
    {
        // Other API Functionality

        public XmlData GetData()
        {
            return new XmlData();
        }
    }

    public class WashingtonIdahoAdapter : WashingtonAPI
    {
        private IdahoAPI Api;

        WashingtonIdahoAdapter(IdahoAPI idahoApi)
        {
            Api = idahoApi;
        }

        public XmlData ConverToXmlData(IdahoApi api)
        {
            // Converting code...
            return convertedApi;
        }

        public XmlData GetData()
        {
            return ConvertToXmlData(Api)
        }
    }

    public class IdahoAPI
    {
        // Other API Functionality
    }

    ```

    ```c# linenums="1" title="Driver Code"
    Forecaster forecastService = new Forecaster();
    WashingtonAPI wApi = new WashingtonApi();
    IdahoAPI iApi = new IdahoAPI();
    WashingtonAPI adapter = new WashingtonIdahoAdapter(iApi);

    Console.WriteLine(forecastService.GetForecast(wApi))
    Console.WriteLine(forecastService.GetForecast(adapter))
    ``` 

    ```title="Output"
    Forecast: <Washington forecast...>
    Forecast: <Idaho forecast...>
    ```

    In this Adapter solution there are the following classes: 

    - ``Forecaster`` the client code
    - ``WashingtonAPI`` API that returns Washington weather data
    - ``IdahoAPI`` API that returns Idaho weather data
    - ``WashingtonIdahoAdapter`` adapter to convert Idaho API to XmlData

    In this solution none of the preexisting classes had to be modified to integrate the new ``IdahoAPI`` class. This is also true for any future API services that need to be added. 


