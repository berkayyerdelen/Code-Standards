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
  
public partial class Sample // non-compliant
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