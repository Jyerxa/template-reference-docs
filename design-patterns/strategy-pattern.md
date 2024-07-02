# Strategy Design Pattern (WIP - Draft)

The Strategy design pattern is a behavioral design pattern that enables selecting behavior at runtime. Strategy pattern defines a family of algorithms, encapsulates them, and makes them interchangeable. 

### Key Concepts

1. **Context**: The class that uses Strategy. The Context maintains a reference to one of the concrete strategies and delegates the algorithm's execution to this strategy.

2. **Strategy**: An interface common to all supported algorithms. The Context uses this interface to call the algorithm defined by a ConcreteStrategy.

3. **ConcreteStrategy**: Classes that implement the Strategy interface. Each ConcreteStrategy implements a specific algorithm that the Context can use.

### Benefits

- **Flexibility and Reusability**: By encapsulating algorithms, they can be easily swapped without altering the client’s code.
- **Code Maintainability**: Enhances code maintainability by isolating the implementation details of an algorithm from the code that uses it.
- **Elimination of Conditional Statements**: Reduces the use of conditional statements for selecting behaviors, leading to cleaner and more understandable code.

### Example

Consider a simple example where a text editor needs to apply different text formatting algorithms:

- **Context**: The text editor class.
- **Strategy**: An interface `TextFormatter` with a method `format(String text)`.
- **ConcreteStrategy**: Classes like `PlainTextFormatter`, `HtmlTextFormatter`, and `MarkdownTextFormatter` that implement the `TextFormatter` interface with specific formatting behaviors.

```csharp
// Strategy Interface
public interface TextFormatter
{
    void Format(string text);
}

// Concrete Strategies
public class PlainTextFormatter : TextFormatter
{
    public void Format(string text)
    {
        Console.WriteLine("Plain Text: " + text);
    }
}

public class HtmlTextFormatter : TextFormatter
{
    public void Format(string text)
    {
        Console.WriteLine("<html>" + text + "</html>");
    }
}

// Context
public class TextEditor
{
    private TextFormatter formatter;

    public void SetFormatter(TextFormatter formatter)
    {
        this.formatter = formatter;
    }

    public void Publish(string text)
    {
        if (formatter != null)
        {
            formatter.Format(text);
        }
        else
        {
            Console.WriteLine("No formatter set.");
        }
    }
}

// Usage
public class Program
{
    public static void Main(string[] args)
    {
        TextEditor editor = new TextEditor();
        
        editor.SetFormatter(new PlainTextFormatter());
        editor.Publish("Hello, World!");

        editor.SetFormatter(new HtmlTextFormatter());
        editor.Publish("Hello, World!");
    }
}
