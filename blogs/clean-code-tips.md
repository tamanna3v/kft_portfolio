# Tips for Writing Cleaner, More Readable Code

Writing clean code offers better performance and faster development. Sometimes "quick and dirty" code may save time during development but there is a strong likelihood it will cost you time later fixing the bugs. Writing clean code is a matter of habit which is easier to maintain and easier to spot bugs in a long run. Here some tips have been described on how we can write more readable and cleaner code and some new features we can adopt.

## 1. Keep your functions/methods simple

Avoid writing long functions. The simpler your class is, your code is more readable and cleaner. If any function is required to write a hundred lines of business logic, break it down into smaller functions and name it appropriately describing the steps best way.

```csharp
Product PurchaseProduct(int id)
{
    var product = GetProduct(id);
    UpdateInventory(product);
    SendPurchaseNotification();
    return null;
}

Product UpdateInventory(Product product)
{
    if (product.Quantity > 0)
    {
        product.Quantity--;
        return product;
    }
}
```

## 2. Pay attention to readability and meaningful method/property names

**Use variable names that are easy to remember**

Bad practice:

```csharp
var yyyymmdstr = DateTime.Now.ToString("YYYY/MM/DD");
```

Good practice:

```csharp
var currentDate = DateTime.Now.ToString("YYYY/MM/DD");
```

**Use searchable names**

Bad practice:

```csharp
// In the future we will not remember what 86400000 means.
clearBacklog(backlog, 86400000);
```

Good practice:

```csharp
// Declare constants with a searchable name
var MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000; //86400000;
clearBacklog(backlog, MILLISECONDS_PER_DAY);
```

**Use camel case notation for local variables and arguments**

Bad practice:

```csharp
int RandomInteger;
string FirstName;
```

Good practice:

```csharp
int randomInteger;
string firstName;
```

**Add comments only when the situation demands.** For example, when a particular method is too complex and requires an in-depth explanation. Maintaining comments will become a task as well, so use comments wisely.

## 3. Don't repeat yourself

This error is very common because we unconsciously repeat names or words. This bad practice in the long run makes you have a very dirty code since there will be many things that will be repeated. Let's get down to practice.

Bad practice:

```csharp
Employee employee = new()
{
    employeeName = "ABC",
    employeeCode = "EMP0001",
    employeeMobileNo = "018xxxxxxxx"
};

void UpdateMobileNo(Employee employee, string number)
{
    employee.employeeMobileNo = number;
}
```

Good practice:

```csharp
Employee employee = new()
{
    name = "ABC",
    code = "EMP0001",
    mobileNo = "018xxxxxxxx"
};

void UpdateMobileNo(Employee employee, string number)
{
    employee.MobileNo = number;
}
```

## 4. Follow the 'early return' principle

Return early is the way of writing functions or methods so that the invalid cases validate first and return as early as possible. The expected positive result is returned at the end of the function after some required validations. It works as a guard of the functions for invalid cases.

```csharp
Product PurchaseProduct(int id)
{
    var product = GetProduct(id);
    if (product.Quantity > 0)
    {
        product.Quantity--;
        return product;
    }
    SendOutOfStockNotification(product);
    return null;
}
```

## 5. Simpler ways of using the if/else statement

We often write a validation case that has to return a `bool` value from a function:

```csharp
bool IsInStock(Product product)
{
    if (product.Quantity > 0)
    {
        return true;
    }
    else
    {
        return false;
    }
}
```

Although this approach is mostly fine, we have to ask ourselves if we even need an if-statement in the first place. Since we already have a logical expression inside the if-statement, we can simplify the method by simply returning the value of that logical expression:

```csharp
bool IsInStock(Product product)
{
    return product.Quantity > 0;
}
```

For achieving the same result we can do it in a more simplistic way:

```csharp
bool IsInStock(Product product) => product.Quantity > 0;
```

Besides, we often need to validate whether a value/object is null or not. We usually do it the following way:

```csharp
var employee = GetEmployee();
if (employee == null)
{
    return new Employee();
}
return employee;
```

You can do this in a more simplistic way by using the null-coalescing operator:

```csharp
var employee = GetEmployee() ?? new Employee();
return employee;
```

Another scenario:

```csharp
if (Employee != null && Employee.Name == null)
{
    Console.WriteLine("No Employee name provided");
}
```

Use the null propagation operator instead:

```csharp
Console.WriteLine(Employee?.Name ?? "No Employee name provided");
```

## 6. Simplify the switch statement

We generally write a switch statement in this way. There is no issue with it, but C# 8.0 introduces a more simplistic way of writing a switch statement which is more clean and readable.

```csharp
public static int DayId(this DateTime date)
{
    var dn = date.ToString("dddd");
    switch (dn)
    {
        case "Saturday":
            return 1;
        case "Sunday":
            return 2;
        case "Monday":
            return 3;
        case "Tuesday":
            return 4;
        case "Wednesday":
            return 5;
        case "Thursday":
            return 6;
        case "Friday":
            return 7;
    }
    return 0;
}
```

C# 8.0 simplifies the switch statement this way:

```csharp
public static int DayId(this DateTime date)
{
    var dn = date.ToString("dddd");
    return dn switch
    {
        "Saturday" => 1,
        "Sunday" => 2,
        "Monday" => 3,
        "Tuesday" => 4,
        "Wednesday" => 5,
        "Thursday" => 6,
        "Friday" => 7,
        _ => 0
    };
}
```

**Pattern matching expression**

```csharp
public enum Hand
{
    Rock, Paper, Scissors
}

public class RockPaperScissors
{
    public Option<Hand> Round(Hand first, Hand second)
        => (first, second) switch
        {
            (Hand.Rock, Hand.Paper) => "Paper Wins!",
            (Hand.Rock, Hand.Scissors) => "Rock Wins!",
            (Hand.Paper, Hand.Rock) => "Paper Wins!",
            (Hand.Paper, Hand.Scissors) => "Scissors Wins!",
            (Hand.Scissors, Hand.Rock) => "Rock Wins!",
            (Hand.Scissors, Hand.Paper) => "Scissors Wins!",
            (_, _) => "Tie!" // to be interpreted as a tie
        };
}
```

## 7. Minimize exceptions

- Throwing exceptions as normal behavior is bad practice. While adding `try/catch` is cheap, actually throwing an exception is expensive. Therefore, include logic in the method to detect and handle conditions that would cause an exception. For example, checking if something can be parsed by calling `int.Parse()` and catching an exception should be avoided. Instead, we can use `int.TryParse()` which returns `false` if parsing is impossible.
- `throw ex` causes the loss of the stack trace data. The stack trace of the exception gets rewritten to the line of code where we explicitly rethrow it. This means we lose all of the valuable information about what caused the exception in the first place, which can make debugging very hard. Just use `throw`. Besides, use try/catch only if you need to catch the exception and handle it as well.

Bad practice:

```csharp
try
{
    // Do something..
}
catch (Exception ex)
{
    throw ex;
}
```

Good practice:

```csharp
try
{
    // Do something..
}
catch (Exception ex)
{
    throw;
}
```

## 8. Use FirstOrDefault() instead of First()

`FirstOrDefault()` works the same as `First()` but there is a significant advantage over `First()`. `First()` returns the first element from a sequence, but if there is no record in the collection which matches the input criteria then it throws an exception immediately, whereas `FirstOrDefault()` can handle null values and it does not throw an exception.

The same scenario goes for `Single` vs `SingleOrDefault`, and `Last()` vs `LastOrDefault()`.

## 9. Use the "using" statement

The `using` keyword in C# plays a vital role in improving performance in garbage collection.

The using statement ensures that `Dispose()` is called even if an exception occurs when you are creating objects and calling methods, properties, and so on. `Dispose()` is a method that is present in the `IDisposable` interface that helps to implement custom garbage collection. In other words, if you are doing some database operation (Insert, Update, Delete) but somehow an exception occurs, then here the using statement closes the connection automatically. There's no need to call the connection's `Close()` method explicitly.

```csharp
public async Task<IEnumerable<Company>> GetCompanies()
{
    var query = "SELECT * FROM Companies";
    using (var connection = _context.CreateConnection())
    {
        var companies = await connection.QueryAsync<Company>(query);
        return companies.ToList();
    }
}
```

The using declaration, introduced in C# 8.0, doesn't require braces:

```csharp
public async Task<IEnumerable<Company>> GetCompanies()
{
    var query = "SELECT * FROM Companies";
    using var connection = _context.CreateConnection();
    var companies = await connection.QueryAsync<Company>(query);
    return companies.ToList();
}
```

## 10. Default interface methods

Before C# 8.0, interfaces only contained the declaration of the members (methods, properties, events, and indexers), but from C# 8.0 it is allowed to add members as well as their implementation to the interface. Now you are allowed to add a method with its implementation to the interface without breaking the existing implementation of the interface. Such a method is known as a default interface method (also known as a virtual extension method).

The main benefit of the default method is that it allows us to add new functionality to the interfaces of our libraries and ensure backward compatibility with code written for older versions of those interfaces. For a better understanding, please have a look at the below example.

```csharp
using System;

namespace Csharp8Features
{
    interface IDefaultInterfaceMethod
    {
        public void DefaultMethod()
        {
            Console.WriteLine("I am a default method in the interface!");
        }
    }

    class AnyClass : IDefaultInterfaceMethod
    {
    }

    class Program
    {
        static void Main(string[] args)
        {
            IDefaultInterfaceMethod anyClass = new AnyClass();
            anyClass.DefaultMethod();
            Console.ReadKey();
        }
    }
}
```
