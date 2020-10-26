# Code Standards

#### 1.Dependencies should only be resolved in the class' constructor, not in any methods or properties:

#Non-compliant
```csharp
public class Sample
{
    private readonly IContainer _container;
 
    public Sample(IContainer container)
    {
        _container = container;
    }
 
    public void DoSomething()
    {
        var service = _container.Resolve<IService>(); // leave these lands once if you're doing it!
 
        service.CallServiceMethod();
    }
}
```
#Compliant
```csharp
public class Sample
{
    private readonly IDependency  _dependency;
 
    public Sample(IDependency dependency)
    {
        _dependency = dependency;
    }
 
    public void DoSomething()
    {        
        _dependency.CallServiceMethod();
    }
}
```
#### 2.Null checks on dependencies should not be done:

#Non-compliant
```csharp
public Sample(IDependency dependency)
{
    this.dependency = dependency;
}
 
public void Bar()
{
    if (this.dependency is null)
    {                                       //Instead of checking NullReferenceException,
        throw new NullReferenceException(); //you might try to test your code correctly through unit and integration tests
                                            //Imagine doing null-checking for each dependencies LMFAO ;)
    }
 
    dependency.DoSomeStuff();
}
```
#Compliant

```csharp
public Sample(IDependency dependency)
{
    this.dependency = dependency;
}
 
public void DoSomething()
{  
    dependency.DoSomeStuff();
}
```

#### 3.The ErrorCodes constants should be used instead of their equivalent string values:

#Non-compliant
```csharp
public class Sample
{
    public void DoSomething()
    {
        throw new BusinessException("RANDOM_ERROR_CODE_0001"); //this does not make sense even for developers! 
    }
}
```
#Compliant
```csharp
public class Sample
{
    public void DoSomething()
    {
        throw new BusinessException(ErrorCodes.CheckOutError);  //works like a charm
    }
}
```
#### 4.Partials should not be created:
Tip: I do believe that being able to group code to from logical block is useful, however, I do not agree to use damn partial classes since they are making less readable code for most of the cases

#Non-compliant
```csharp
public partial class Sample 
{
    public void DoSomeThing()
    {
       
    }
}
  
public partial class Sample 
{
    public void DoUsefulThing()
    {  
       
    }
}
```

#Compliant
```csharp
public class Some : ISome
{
    public void DoSomeThing()
    {
       
    }
}
 
public class Useful : IUseful
{
    public void DoUsefulThing()
    {  
       
    }
}
```

#### 5.Nullable.HasValue should be used:

#Non-compliant
```csharp
int? value = null;
  
if (value != null)
{    
}
  
if (value == null)
{   
}
```

#Compliant
```csharp
int? value = null;

if (value.HasValue)
{   
}
  
if (!value.HasValue)
{   
}
```

#### 5.Enums should have their values explicitly defined:

#Non-compliant
```csharp
public enum Days
{
    Monday,
    Tuesday,
    Wednesday,
    Thursday
}
  
[Flags]
public enum Days
{
    Monday,
    Tuesday,
    Wednesday,
    Thursday = 4
}
```
#Compliant
```csharp

public enum Days
{
    Monday = 0,
    Tuesday = 1,
    Wednesday = 2,
    Thursday = 3
}
 
[Flags]
public enum Days
{
    Monday = 0,
    Tuesday = 1,
    Wednesday = 2,
    Thursday = 4
}
```

#### 6.Else blocks should contain executable code:

#Non-compliant
```csharp
public class Sample
{
    public void DoSomething()
    {
        var listOfMembers =  GetListOfMembers();
        if(listOfMembers.Any()){
         DoSomethingWithMembers();
        }
        else {
             throw new NullReferenceException();
        }
    }
}
```

#Compliant
```csharp
public class Sample
{
    public void DoSomething()
    {
        var listOfMembers =  GetListOfMembers();
        if(!listOfMembers.Any()){
         throw new BusinessException(ErrorCodes.MembersNullError);
        }
        else {
             DoSomethingWithMembers();             
        }
    }
}
```

#### 6.Public methods should have 1 unit test for each logical flow:

#Compliant
```csharp
 public class Sample
    {
        public int DoSomething(bool skip)
        {
            if (skip)
                return 1000;
            else
                return 5000;
        }
    }

 public class SampleTest
    {
        private Sample sample;

        public SampleTest()
        {
            sample = new Sample();
        }
        [Fact]
        public void Should_Sample_Return_5000IfSkipIsTrue()
        {
            var skip = true;
            var result = sample.DoSomething(skip);
            Assert.Equal(5000,result);
        }
        [Fact]
        public void Should_Sample_Return_1000IfSkipIsFalse()
        {
            var skip = false;
            var result = sample.DoSomething(skip);
            Assert.Equal(1000, result);
        }
    }
```
#### 6 Unit tests should be written in the arrange, act and assert format:

Tip: Unit tests should be written in 3 sections, Arrange, Act, and Assert, each denoted with a comment.

* Arrange: all mock setups should go here.
* Act: all actions such as methods calls go here.
* Assert: assert or mock verification(s) goes here
#Compliant
```csharp
  [Fact]
        public void Should_Sample_Return_5000IfSkipIsTrue()
        {
            //Arrange
            var skip = true;
            //Act
            var result = sample.DoSomething(skip);
            //Assert
            Assert.Equal(5000,result);
        }
```


#### 7.The 'this' keyword should only be used when needed:
Tip: To qualify names hidden by similar names. It eliminates naming conflicts
#Non-compliant
```csharp
public class ExChange : IExcange
{
    private string rate;
 
    public string GetRate() 
        => $"Rate: {this.rate}"; //the use of 'this' is unnecessary
    
}
```
#Compliant
```csharp
public class ExChange : IExcange
{
    private string rate;
 
    public string GetRate() 
        => $"Rate: {rate}"; // compliant - 'this' should not be used in this case
    
    public void UpdateRate(string rate) 
       => this.rate = rate; // compliant - 'this' is needed to differentiate between the local 'rate' and the class 'rate'   
}
```

#### 8.The 'this' keyword should only be used when needed:
Tip: Unit, integration and other kinds of tests should be named properly so that it is easier to understand their intent and so that it is easier to search for them.
#Non-compliant
```csharp
public class SampleTest
    {
        private Sample sample;

        public SampleTest()
        {
            sample = new Sample();
        }
        [Fact]
        public void Should_Sample_Return_True() 
        {
            var skip = true;
            var result = sample.DoSomething(skip);
            Assert.Equal(5000,result);
        }
        [Fact]
        public void Should_Sample_Return_False()
        {
            var skip = false;
            var result = sample.DoSomething(skip);
            Assert.Equal(1000, result);
        }
    }
```
#Compliant
```csharp
 public class SampleTest
    {
        private Sample sample;

        public SampleTest()
        {
            sample = new Sample();
        }
        [Fact]
        public void Should_Sample_Return_5000IfSkipIsTrue()
        {
            var skip = true;
            var result = sample.DoSomething(skip);
            Assert.Equal(5000,result);
        }
        [Fact]
        public void Should_Sample_Return_1000IfSkipIsFalse()
        {
            var skip = false;
            var result = sample.DoSomething(skip);
            Assert.Equal(1000, result);
        }
    }
```
#### 9.Naming and Capitalization Conventions:
Tip: PascalCase is used most of the time
* Private fields: use camelCase
* Method parameters: use camelCase
* Local variables: use camelCase
* A special case is made for two-letter acronyms in which both letters are capitalized (example: System.IO.Stream)


#Compliant
```csharp
  class MyClass
    {
        // camelCase for private fields
        private int myPrivateField;
        private const int myConstant=1000;
  
        // PascalCase for public and protected static fields
        public static int MyPublicStaticField      
        protected static int MyProtectedStaticField
 
        // PascalCase for properties
        public int MyProperty { get; set; }
        public HttpClient HttpClient {get; set;}
         
        // camelCase for parameters
        public MyClass(int myParameter)
        {
            // camelCase for local variables
            var myLocalVariable = myParameter;
        }
  
        // PascalCase for ALL methods
        private string MyMethod()
        {
        }
    }
```

#### 10.Solution names and project names should be equal to the primary namespace
#Non-compliant
```csharp
Solution DevFrame.Domain
Project  DevFrame.Entities
```
#Compliant
```csharp
Solution DevFrame.Domain
Project  DevFrame.Domain.Entities
```
#### 11.Use of SingleOrDefault or FirstOrDefault should be assessed carefully
Tip: The main difference is that SingleOrDefault throws if the result contains more than one match whereas FirstOrDefault does not throw in such a situation.
#Compliant
```csharp
int [] numbers = {0,1,2,4,6,8};
int number = numbers.SingleOrDefault(n=> n.Equals(1)); //its ok
int [] numbers = {0,1,1,4,6,8};
int number = numbers.FirstOrDefault(n=> n.Equals(1)); //its also ok
```

-------------------------------------------------
#### 12.Use Entity framework async implementation in async methods
public async Task<int> GetObjectsCountAsync(string categoryCode)
{
    var count = await (from o in Context.MyObjects.Where(MyObjectPredicateBuilder)
                       where o.CategoryCode
                       select t.Code).CountAsync().ConfigureAwait(false);
    return count;  
}

#### 13.Projection queries should be used whenever possible
Tip: This allows to get only the needed columns and therefore reduces database load and improves performances.
#Non-compliant
```csharp
var productsQuery = context.Products.toList();
```
If only product names are needed for the process
#Compliant
```csharp
var productNames = context.Products.select(x=>x.Name).ToList();                   
```
#### 14.AsNoTracking should be used whenever possible
Tip: his should be used to improve select performances (as this removes Entity Framework tracking cost) when the result does not need to be updated.
#Non-compliant
```csharp
var productsQuery = context.Products.toList();
```
#Compliant
```csharp
var productNames = context.Products.select(x=>x.Name).AsNoTracking().ToList();                   
```
#### 15.SaveChanges should not be called several times in the same flow
#Non-compliant
```csharp
var keyboard = new Product(){Name = "logitech", CreatedDate= DateTime.Now}
var desktop = new Product(){Name = "MSI", CreatedDate= DateTime.Now}
context.Products.Add(keyboard);
context.SaveChanges();
context.Products.Add(desktop);
context.SaveChanges();
```
#Compliant
```csharp
var keyboard = new Product(){Name = "logitech", CreatedDate= DateTime.Now}
var desktop = new Product(){Name = "MSI", CreatedDate= DateTime.Now}
context.Products.Add(keyboard);
context.Products.Add(desktop);  
context.SaveChanges();
```



