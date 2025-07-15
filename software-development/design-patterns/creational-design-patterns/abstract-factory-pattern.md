# Abstract Factory Pattern: In‑Depth Overview

The **Abstract Factory** pattern provides an interface for creating families of related or dependent objects without specifying their concrete classes. It decouples client code from the details of object creation, ensuring that products from the same family are used together.

---

## 1. Intent

- **Definition:**  
  “Provide an interface for creating families of related or dependent objects without specifying their concrete classes.”  
  — Erich Gamma et al., *Design Patterns*

- **Primary Goal:**  
  Let clients work with abstract “factories” that produce abstract “products.” Concrete factories encapsulate the creation of specific product variants.  

---

## 2. Problems It Solves

1. **Cross‑Product Consistency**  
   When you need to ensure that objects from different product families work together (e.g., a Windows button with a Windows checkbox).

2. **Decoupling Clients from Implementations**  
   Clients depend only on abstract factories and products, so adding new families doesn’t force client changes.

3. **Configurable Product Families**  
   Selecting at runtime which concrete factory to use (e.g., theme, platform, localization).

---

## 3. Participants

1. **AbstractFactory**  
   Declares creation methods for each abstract product (e.g., `CreateButton()`, `CreateCheckbox()`).

2. **ConcreteFactory**  
   Implements the creation methods to return concrete products belonging to one family (e.g., `WindowsFactory`, `MacFactory`).

3. **AbstractProduct**  
   Declares interfaces for a type of product (e.g., `IButton`, `ICheckbox`).

4. **ConcreteProduct**  
   Defines products produced by a corresponding ConcreteFactory (e.g., `WindowsButton`, `MacButton`).

5. **Client**  
   Uses only the abstract interfaces—obtains products from an AbstractFactory and works with them polymorphically.

---

## 4. Consequences, Benefits & Drawbacks

| Aspect                | Pros                                                                                          | Cons                                                                   |
|-----------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------|
| **Consistency**       | Guarantees that products from the same family are used together.                              | Introduces many new interfaces and classes.                            |
| **Flexibility**       | Easy to switch entire product families at runtime (by swapping factories).                    | Can be overkill for small systems with only a few products.            |
| **Decoupling**        | Clients remain unaware of concrete product implementations.                                   | Boilerplate: one factory per product family, two interfaces per product. |
| **Extensibility**     | Add new product families by implementing new factories without touching client code.          | Adding new product types (new AbstractProducts) requires changes in all factories. |

---

## 5. When to Use

- You need to **abstract multiple families** of products (e.g., UI controls, database drivers, messaging protocols).
- You want to **enforce consistency** among products (e.g., matching look‑and‑feel).
- You require the ability to **switch entire product families** at runtime via configuration or environment.

---

## 6. When Not to Use

- You have only **one product family** or very few products—simple Factory Method or direct instantiation may suffice.
- The **number of product types** is likely to change frequently—adding a new product type forces modification of every factory.
- You want to avoid the **overhead of many interfaces** and classes in a small codebase.

---

## 7. Code Examples

Below is a classic example: creating UI widgets for two platforms—Windows and macOS.

---

### C# Example

```csharp
// Abstract products
public interface IButton
{
    void Render();
}

public interface ICheckbox
{
    void Render();
}

// Concrete products for Windows
public class WindowsButton : IButton
{
    public void Render() => Console.WriteLine("Rendering a Windows button");
}

public class WindowsCheckbox : ICheckbox
{
    public void Render() => Console.WriteLine("Rendering a Windows checkbox");
}

// Concrete products for macOS
public class MacButton : IButton
{
    public void Render() => Console.WriteLine("Rendering a macOS button");
}

public class MacCheckbox : ICheckbox
{
    public void Render() => Console.WriteLine("Rendering a macOS checkbox");
}

// Abstract factory
public interface IWidgetFactory
{
    IButton CreateButton();
    ICheckbox CreateCheckbox();
}

// Concrete factories
public class WindowsWidgetFactory : IWidgetFactory
{
    public IButton CreateButton() => new WindowsButton();
    public ICheckbox CreateCheckbox() => new WindowsCheckbox();
}

public class MacWidgetFactory : IWidgetFactory
{
    public IButton CreateButton() => new MacButton();
    public ICheckbox CreateCheckbox() => new MacCheckbox();
}

// Client
public class Application
{
    private readonly IButton _button;
    private readonly ICheckbox _checkbox;

    public Application(IWidgetFactory factory)
    {
        _button = factory.CreateButton();
        _checkbox = factory.CreateCheckbox();
    }

    public void RenderUI()
    {
        _button.Render();
        _checkbox.Render();
    }
}

// Usage
// var factory = new WindowsWidgetFactory();
// var app = new Application(factory);
// app.RenderUI();
```

---

### Java Example

```java
// Abstract products
public interface Button {
    void render();
}

public interface Checkbox {
    void render();
}

// Concrete products for Windows
public class WindowsButton implements Button {
    public void render() { System.out.println("Rendering a Windows button"); }
}

public class WindowsCheckbox implements Checkbox {
    public void render() { System.out.println("Rendering a Windows checkbox"); }
}

// Concrete products for macOS
public class MacButton implements Button {
    public void render() { System.out.println("Rendering a macOS button"); }
}

public class MacCheckbox implements Checkbox {
    public void render() { System.out.println("Rendering a macOS checkbox"); }
}

// Abstract factory
public interface WidgetFactory {
    Button createButton();
    Checkbox createCheckbox();
}

// Concrete factories
public class WindowsWidgetFactory implements WidgetFactory {
    public Button createButton() { return new WindowsButton(); }
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}

public class MacWidgetFactory implements WidgetFactory {
    public Button createButton() { return new MacButton(); }
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}

// Client
public class Application {
    private final Button button;
    private final Checkbox checkbox;

    public Application(WidgetFactory factory) {
        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }

    public void renderUI() {
        button.render();
        checkbox.render();
    }
}

// Usage
// WidgetFactory factory = new WindowsWidgetFactory();
// Application app = new Application(factory);
// app.renderUI();
```

---

### Python Example

```python
from abc import ABC, abstractmethod

# Abstract products
class Button(ABC):
    @abstractmethod
    def render(self) -> None:
        ...

class Checkbox(ABC):
    @abstractmethod
    def render(self) -> None:
        ...

# Concrete products for Windows
class WindowsButton(Button):
    def render(self) -> None:
        print("Rendering a Windows button")

class WindowsCheckbox(Checkbox):
    def render(self) -> None:
        print("Rendering a Windows checkbox")

# Concrete products for macOS
class MacButton(Button):
    def render(self) -> None:
        print("Rendering a macOS button")

class MacCheckbox(Checkbox):
    def render(self) -> None:
        print("Rendering a macOS checkbox")

# Abstract factory
class WidgetFactory(ABC):
    @abstractmethod
    def create_button(self) -> Button:
        ...

    @abstractmethod
    def create_checkbox(self) -> Checkbox:
        ...

# Concrete factories
class WindowsWidgetFactory(WidgetFactory):
    def create_button(self) -> Button:
        return WindowsButton()

    def create_checkbox(self) -> Checkbox:
        return WindowsCheckbox()

class MacWidgetFactory(WidgetFactory):
    def create_button(self) -> Button:
        return MacButton()

    def create_checkbox(self) -> Checkbox:
        return MacCheckbox()

# Client
class Application:
    def __init__(self, factory: WidgetFactory) -> None:
        self.button = factory.create_button()
        self.checkbox = factory.create_checkbox()

    def render_ui(self) -> None:
        self.button.render()
        self.checkbox.render()

# Usage
# factory = WindowsWidgetFactory()
# app = Application(factory)
# app.render_ui()
```

---

> **Prompt for Generative AI:**
> “When you need to produce families of related objects, use Abstract Factory. Explain how new families (e.g., a Linux factory) can be added without modifying existing code, and illustrate client code that remains unchanged.”