# Flyweight Design Pattern

## What is It?
The Flyweight design pattern is a structural design pattern which aims to use shared objects to reduce memory usage and improve performance. The concept is to reuse already existing similar kind objects by storing them and create a new object when no matching object is found.

## Why Use It?
The main reason to use the Flyweight pattern is to minimize memory usage by sharing as much data as possible with related objects. This approach is useful when dealing with a high number of objects with similar properties, where each object may carry an otherwise significant amount of data.

## When to Use It?
The Flyweight pattern can be used when:
- There are a large number of objects
- The cost of storage is high
- The majority of an object's state information can be made extrinsic
- Clients won't be aware of the shared references
- The application will not depend on object identity

## Code Example in C#
Here is a simple example of the Flyweight pattern implemented in C#: 

```csharp  
 
public interface IFlyweight
{
    void Operation(int extrinsicState);
}


public class ConcreteFlyweight : IFlyweight
{
    private string intrinsicState;

    public ConcreteFlyweight(string intrinsicState)
    {
        this.intrinsicState = intrinsicState;
    }

    public void Operation(int extrinsicState)
    {
        Console.WriteLine($"Intrinsic State: {intrinsicState}, Extrinsic State: {extrinsicState}");
    }
}



public class FlyweightFactory
{
    private Dictionary<string, IFlyweight> flyweights = new Dictionary<string, IFlyweight>();

    public IFlyweight GetFlyweight(string key)
    {
        if (!flyweights.ContainsKey(key))
        {
            flyweights[key] = new ConcreteFlyweight(key);
        }
        return flyweights[key];
    }
}




class Program
{
    static void Main(string[] args)
    {
        FlyweightFactory factory = new FlyweightFactory();

        IFlyweight flyweight1 = factory.GetFlyweight("A");
        flyweight1.Operation(1);

        IFlyweight flyweight2 = factory.GetFlyweight("B");
        flyweight2.Operation(2);

        IFlyweight flyweight3 = factory.GetFlyweight("A");
        flyweight3.Operation(3);

        Console.ReadKey();
    }
}


```


Do note that the Flyweight pattern may introduce complexity and may need to be used with care in a multithreaded environment.
