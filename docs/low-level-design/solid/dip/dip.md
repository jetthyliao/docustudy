# Dependency Inversion Principle example

Dependency inversion principle: developers should depend on abstractions, not on concretions. High level modules should not depend upon low level modules. Both should depend on abstractions. Abstractions should not depend on details, details should depend upon abstractions. 

???+ code "Invalid Example"
    === "C#"
        ```c# linenums="1" title="Invalid Example" hl_lines="17 18"
        public class SalaryCalculator
        {
            public float CalculateSalary()
            {
                // Code to calculate salary
                return 0;
            }
        }

        public class EmployeeDetails
        {
            public int HoursWorked { get; set; }
            public int HourlyRate { get; set; }

            public float GetSalary()
            {
                var salaryCalculator = new SalaryCalculator();
                return salaryCalculator.CalculateSalary();
            }
        }
        ```
    
    This breaks the dependency inversion principle since the higher-level class (``EmployeeDetails``) is dependent on the lower-level class (``SalaryCalculator``) as seen on line 17-18.

???+ code "Valid Example"
    === "C#"
        ```c# linenums="1" title="Valid Example" hl_lines="1-4"
        public interface ISalaryCalculator
        {
            float CalculateSalary();
        }

        public class SalaryCalculator : ISalaryCalculator
        {
            public float CalculateSalary()
            {
                // Code to calculate salary
                return 0;
            }
        }

        public class EmployeeDetails
        {
            private readonly ISalaryCalculator _salaryCalculator;

            public int HoursWorked { get; set; }
            public int HourlyRate { get; set; }

            public EmployeeDetails(ISalaryCalculator salaryCalculator)
            {
                _salaryCalculator = salaryCalculator;
            }

            public float GetSalary()
            {
                return _salaryCalculator.CalculateSalary();
            }
        }
        ```
    
    This adheres to the dependency inversion principle since the ``ISalaryCalculator`` interface provide an abstraction for the ``EmployeeDetails`` class to reference. 

    This implementation allows the ``SalaryCalculator`` to update it's behavior without having to modify the ``EmployeeDetails`` class. 
