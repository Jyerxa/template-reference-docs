# Strategy Design Pattern (WIP)

The Strategy design pattern is a behavioral design pattern that enables selecting an algorithm's behavior at runtime. This pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable. The primary goal is to enable a client to choose the algorithm from a family of related algorithms and use it independently of the client’s code.

### Key Concepts

1. **Context**: The class that uses a Strategy. The Context maintains a reference to one of the concrete strategies and delegates the algorithm's execution to this strategy.

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

```java
// Strategy Interface
public interface TextFormatter {
    void format(String text);
}

// Concrete Strategies
public class PlainTextFormatter implements TextFormatter {
    public void format(String text) {
        System.out.println("Plain Text: " + text);
    }
}

public class HtmlTextFormatter implements TextFormatter {
    public void format(String text) {
        System.out.println("<html>" + text + "</html>");
    }
}

// Context
public class TextEditor {
    private TextFormatter formatter;

    public void setFormatter(TextFormatter formatter) {
        this.formatter = formatter;
    }

    public void publish(String text) {
        if (formatter != null) {
            formatter.format(text);
        } else {
            System.out.println("No formatter set.");
        }
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        TextEditor editor = new TextEditor();
        
        editor.setFormatter(new PlainTextFormatter());
        editor.publish("Hello, World!");

        editor.setFormatter(new HtmlTextFormatter());
        editor.publish("Hello, World!");
    }
}
