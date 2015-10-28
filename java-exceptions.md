## Catch or specify requirement

- A try statement that catches the exception. The try must provide a handler for the exception, as described in Catching and Handling Exceptions.
- A method that specifies that it can throw the exception. The method must provide a throws clause that lists the exception, as described in Specifying the Exceptions Thrown by a Method.

## Three kinds of exceptions

- Checked exceptions
  - Checked exceptions are subject to the Catch or Specify Requirement. All exceptions are checked exceptions, except for those indicated by Error, RuntimeException, and their subclasses. 
  - E.g. `java.io.FileNotFoundException`.
- Unchecked exceptions
  - Errors
    - External to the program 
    - E.g. `java.io.IOError` 
  - Runtime exceptions
    - Internal to the program. These usually indicate programming bugs, such as logic errors or improper use of an API.
    - E.g. `NullPointerException`
  
## Checked vs unchecked exceptions

If a client can reasonably be expected to recover from an exception, make it a checked exception. If a client cannot do anything to recover from the exception, make it an unchecked exception.