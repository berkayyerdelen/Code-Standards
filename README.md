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


#### 7 The 'this' keyword should only be used when needed:
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