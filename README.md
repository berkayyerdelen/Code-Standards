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
    {
        throw new NullReferenceException(); //Instead of checking NullReferenceException, you might try to test your code correctly throug unit and integration tests
                                            //Imagine doing null-checking for each dependencies lmfao ;)
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