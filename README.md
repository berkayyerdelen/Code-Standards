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
        var service = _container.Resolve<IService>(); // non-compliant
 
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