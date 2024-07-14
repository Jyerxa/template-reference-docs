# Mediator Pattern Summary

## Description:
The Mediator pattern is a behavioral design pattern that promotes loose coupling by keeping objects from referring to each other explicitly. It accomplishes this by introducing a mediator object that handles communication between different components (colleagues). This centralizes complex communication logic and controls interactions, improving maintainability and flexibility.

## Usage:
Use the Mediator pattern when:
- You want to decouple components to make the system more modular.
- You need to centralize control logic in a single place.
- Objects interact in complex ways, making the system difficult to understand and maintain.

## How to Implement:
### Define the Mediator Interface:
Create an interface to declare the method used for communication between colleagues.

### Implement the Concrete Mediator:
Implement the mediator interface, encapsulating the communication between colleague objects.

### Create Colleague Classes:
Define the classes that will interact via the mediator. Each colleague holds a reference to the mediator.

### Colleague Interaction via Mediator:
Colleague objects communicate through the mediator, which directs the communication to appropriate colleagues.

## Code Example in C#:

```csharp
public class ComponentA : IComponent
{
    public string Name { get; set; } 

    private readonly IMediator _mediator;

    public ComponentA(IMediator mediator, string name)
    {
        _mediator = mediator;
        Name = name;
    }

    public void Send(string message)
    {
        Console.WriteLine($"ComponentA ({Name}) sends: {message}");
        _mediator.Send(Name, message);
    }

    public void Receive(string sender, string message)
    {
        Console.WriteLine($"ComponentA ({Name}) received from {sender}: {message}");
    }
}

public class ComponentB : IComponent
{
    public string Name { get; set; }

    private readonly IMediator _mediator;

    public ComponentB(IMediator mediator, string name)
    {
        _mediator = mediator;
        Name = name;
    }

    public void Send(string message)
    {
        Console.WriteLine($"ComponentB ({Name}) sends: {message}");
        _mediator.Send(Name, message);
    }

    public void Receive(string sender, string message)
    {
        Console.WriteLine($"ComponentB ({Name}) received from {sender}: {message}");
    }
}

public interface IComponent
{
    string Name { get; set; }
    
    void Send(string message);
    void Receive(string sender, string message);
}

public interface IMediator
{
    void Register(IComponent component);
    void Send(string sender, string message);
}

public class MyMediator : IMediator
{
    private List<IComponent> _components;

    public MyMediator()
    {
        _components = new List<IComponent>();
    }
    
    public void Register(IComponent component)
    {
        _components.Add(component);
    }

    public void Send(string sender, string message)
    {
        foreach (IComponent component in _components.Where(component => component.Name != sender))
        {
            component.Receive(sender, message);
        }
    }
}
```

## Code Example usage:
```csharp
IMediator mediator = new MyMediator();
IComponent componentA = new ComponentA(mediator, "A");
IComponent componentB = new ComponentB(mediator, "B");

mediator.Register(componentA);
mediator.Register(componentB);

componentA.Send("Hello B!");


````

