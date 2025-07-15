# 🧩 Adapter Pattern (Structural)

> **Generative AI Instruction Prompt:**  
> “When adapting an existing class with an incompatible interface to a new interface expected by your system, use the Adapter pattern. It enables reuse without modifying the existing class.”

---

## 1. Overview

**Intent:**  
The Adapter pattern (also known as *Wrapper*) is a structural design pattern that allows objects with incompatible interfaces to work together. It acts as a bridge between the existing (adaptee) class and the new interface (target) that the client expects.

**Analogy:**  
Like a power plug adapter: it lets you plug an American device into a European socket without changing either the socket or the device.

---

## 2. Problem It Solves

- When you need to **integrate existing or third-party classes** into your system, but their interfaces are incompatible with the ones you expect.
- You want to **reuse legacy or external code** without rewriting it.
- Your system needs to **convert data or functionality** between mismatched interfaces.

---

## 3. When to Use

✅ Use Adapter when:

- You need to make a **third-party or legacy class** compatible with your system.
- You are performing a **migration** between APIs and need temporary support for both.
- You want to implement the **Open/Closed Principle**, keeping existing classes closed for modification and open for extension.

---

## 4. When Not to Use

🚫 Avoid Adapter when:

- You **can change the original class** (prefer changing the source over wrapping).
- The interface differences are **trivial** and don’t justify creating another class.
- You’re creating **adapters everywhere** instead of solving a deeper architectural mismatch.

---

## 5. Benefits

- ✅ **Reuse existing code** without modification.
- ✅ Follows **Open/Closed Principle**: you extend functionality without changing existing code.
- ✅ Improves **interoperability** between systems.
- ✅ Adapters can be chained or composed.

---

## 6. Drawbacks

- ⚠️ Adds **complexity** by introducing another layer.
- ⚠️ Can lead to **too many small classes** if overused.
- ⚠️ May introduce **performance overhead** with deep adaptation or conversions.

---

## 7. Implementation Variants

- **Object Adapter (Composition):** Most common. Adapter holds a reference to the adaptee and translates calls.
- **Class Adapter (Inheritance):** Only works in languages with multiple inheritance (e.g., C++). Not common in Java, C#, Python.
- **Two-Way Adapter:** Rare but possible when both interfaces can adapt to each other.

---

## 8. Comparison with Factory & Abstract Factory

| Feature               | Adapter                               | Factory                               | Abstract Factory                             |
|----------------------|----------------------------------------|----------------------------------------|----------------------------------------------|
| **Category**          | Structural                            | Creational                             | Creational                                   |
| **Purpose**           | Converts interface                    | Encapsulates object creation           | Creates families of related objects          |
| **Focus**             | Reuse of existing object              | Creation of new objects                | Grouped creation of related objects          |
| **Object Lifecycle**  | Reuses an existing object             | Creates new instances per call         | Creates grouped new instances                |

---

## 9. Code Examples

### 🧪 Scenario  
We have a class `Adaptee` that provides a method `SpecificRequest()`, but the client expects a method `Request()`. We need to make them compatible.

---

### ✅ C# Example

```csharp
// Target interface expected by the client
public interface ITarget
{
    void Request();
}

// Adaptee with an incompatible method
public class Adaptee
{
    public void SpecificRequest()
    {
        Console.WriteLine("Called SpecificRequest()");
    }
}

// Adapter translates Target to Adaptee
public class Adapter : ITarget
{
    private Adaptee _adaptee;

    public Adapter(Adaptee adaptee)
    {
        _adaptee = adaptee;
    }

    public void Request()
    {
        _adaptee.SpecificRequest();
    }
}

// Usage:
var adaptee = new Adaptee();
ITarget target = new Adapter(adaptee);
target.Request();  // Output: "Called SpecificRequest()"
```

---

### ✅ Java Example

```java
// Target interface
interface Target {
    void request();
}

// Adaptee class
class Adaptee {
    public void specificRequest() {
        System.out.println("Called specificRequest()");
    }
}

// Adapter class
class Adapter implements Target {
    private Adaptee adaptee;

    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    @Override
    public void request() {
        adaptee.specificRequest();
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Adaptee adaptee = new Adaptee();
        Target target = new Adapter(adaptee);
        target.request(); // Output: "Called specificRequest()"
    }
}
```

---

### ✅ Python Example

```python
# Adaptee with an incompatible method
class Adaptee:
    def specific_request(self):
        print("Called specific_request()")

# Adapter exposes the expected interface
class Adapter:
    def __init__(self, adaptee: Adaptee):
        self.adaptee = adaptee

    def request(self):
        self.adaptee.specific_request()

# Usage
adaptee = Adaptee()
target = Adapter(adaptee)
target.request()  # Output: "Called specific_request()"
```

---

## 10. Summary

* The Adapter pattern **bridges the gap** between incompatible interfaces.
* It promotes **reuse**, **decoupling**, and **compatibility**.
* Common in integrating **legacy systems**, **third-party APIs**, and **interface upgrades**.
* Use wisely to avoid **adapter overuse** and **unnecessary indirection**.

---

## 11. References

* *Design Patterns: Elements of Reusable Object-Oriented Software* — Gamma et al.
* Refactoring Guru – [Adapter Pattern](https://refactoring.guru/design-patterns/adapter)
* Microsoft Docs – Adapter Pattern in C#
* Baeldung – [Adapter Pattern in Java](https://www.baeldung.com/java-adapter-pattern)

