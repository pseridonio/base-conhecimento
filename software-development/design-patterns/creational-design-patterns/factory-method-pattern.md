# Factory Method Pattern: In‑Depth Knowledge Base

> **Initialization Prompt for Generative AI:**  
> “When you need to centralize and possibly defer the decision of which concrete class to instantiate, use the Factory Method pattern. Encapsulate branching logic inside a single factory method or registry, explain where the decision happens, and favor extension-friendly registries over hard‑coded switches.”

---

## 1. Intent

- **Definition:**  
  “Define an interface for creating an object, but let subclasses—or a central factory—decide which class to instantiate.”  
- **Goal:**  
  Decouple client code from concrete types by moving instantiation logic into a factory method.

---

## 2. Problems It Solves

- **Tight Coupling:** Eliminates scattered `new ConcreteClass()` calls.  
- **Scattered Conditional Logic:** Consolidates `if/else` or `switch` branches into one place.  
- **Extension without Modification:** New variants can be plugged in with minimal changes.  
- **Single Responsibility:** Business logic classes focus on behavior, not on “which class” logic.

---

## 3. Key Participants

| Role               | Responsibility                                                                                   |
|--------------------|--------------------------------------------------------------------------------------------------|
| **Product**        | Abstract interface/base that clients program against (e.g. `ILogger`).                           |
| **ConcreteProduct**| Specific classes implementing the Product interface (e.g. `ConsoleLogger`, `FileLogger`).       |
| **Creator**        | Declares the factory method (e.g. `CreateLogger`) returning a `Product`.                         |
| **ConcreteCreator**| Subclasses or static factories that implement the factory method to return a specific product.    |
| **Client**         | Calls the factory method and works solely with the `Product` interface.                          |

---

## 4. Static vs. Dynamic Factories

- **Static Factory Method:**  
  - One factory subclass per product.  
  - Client chooses the factory type (e.g. `new ConsoleLoggerFactory()`).  
  - **Decision lives in the client.**  

- **Dynamic (“Smart”) Factory Method:**  
  - A single factory class or method reads a parameter, configuration, or registry to pick the product at runtime.  
  - **Decision lives inside the factory method**—clients simply call `LoggerFactory.CreateLogger(key)`.

---

## 5. When to Use

- You need to **vary instantiation** based on runtime data (parameters, config, database).  
- You want to **decouple** business logic from creation logic.  
- You expect to **add new product types** without touching every client.  
- You wish to conform to **Single Responsibility**, separating “what to create” from “how to use it.”

---

## 6. When Not to Use

- Only one concrete implementation exists and is unlikely to change.  
- Instantiation logic is trivial and doesn’t warrant extra indirection.  
- You want to minimize class/interface proliferation for simple cases.

---

## 7. Factory Method vs. Abstract Factory

| Aspect                   | Factory Method                                 | Abstract Factory                                         |
|--------------------------|------------------------------------------------|----------------------------------------------------------|
| **Scope**                | Creates **one** product                        | Creates **families** of related products                 |
| **Implementation Unit**  | A method (possibly overridden in subclasses)   | A separate factory interface/class per family            |
| **Extension Point**      | Subclass or parameter-driven factory method    | New factory class implementing the AbstractFactory       |
| **Client Configuration** | Client picks factory or passes a key to method | Client receives/configures a factory instance            |
| **Use Cases**            | Plugin frameworks, single-object variation     | UI control kits, cross-platform families                 |

---

## 8. Dynamic (“Smart”) Factory Code Examples

The decision logic—*“which concrete product to create?”*—lives inside the factory method or registry lookup.

### C# Example

```csharp
public enum LoggerType { Console, File, Remote }

public interface ILogger
{
    void Log(string message);
}

public class ConsoleLogger : ILogger
{
    public void Log(string message) => Console.WriteLine($"Console: {message}");
}

public class FileLogger : ILogger
{
    public void Log(string message) => File.AppendAllText("log.txt", message + "\n");
}

public class RemoteLogger : ILogger
{
    private readonly string _endpoint;
    public RemoteLogger(string endpoint) => _endpoint = endpoint;
    public void Log(string message)
    {
        // Send to remote service...
    }
}

public static class LoggerFactory
{
    private static readonly Dictionary<LoggerType, Func<ILogger>> _registry =
        new Dictionary<LoggerType, Func<ILogger>>
    {
        { LoggerType.Console, () => new ConsoleLogger() },
        { LoggerType.File,    () => new FileLogger() },
        { LoggerType.Remote,  () =>
            new RemoteLogger(
                ConfigurationManager.AppSettings["RemoteLoggerUrl"]!
            )
        }
    };

    public static ILogger CreateLogger(LoggerType type)
    {
        if (!_registry.TryGetValue(type, out Func<ILogger>? creator))
            throw new ArgumentOutOfRangeException(nameof(type), $"Unsupported logger type: {type}");
        // ← Decision happens here: lookup in registry
        return creator();
    }
}
```

### Java Example

```java
public interface Logger {
    void log(String message);
}

public class ConsoleLogger implements Logger { /*…*/ }
public class FileLogger implements Logger { /*…*/ }
public class RemoteLogger implements Logger {
    public RemoteLogger(String url) { /*…*/ }
    public void log(String message) { /*…*/ }
}

public class LoggerFactory {
    private static final Map<String, Supplier<Logger>> registry = Map.of(
        "console", ConsoleLogger::new,
        "file",    FileLogger::new,
        "remote",  () -> new RemoteLogger(Config.get("remote.url"))
    );

    public static Logger createLogger(String key) {
        Supplier<Logger> supplier = registry.get(key.toLowerCase());
        if (supplier == null)
            throw new IllegalArgumentException("Unknown logger key: " + key);
        // ← Decision happens here: registry lookup
        return supplier.get();
    }
}
```

### Python Example

```python
from typing import Callable, Dict

class Logger:
    def log(self, message: str) -> None:
        raise NotImplementedError

class ConsoleLogger(Logger):
    def log(self, message: str) -> None:
        print(f"Console: {message}")

class FileLogger(Logger):
    def log(self, message: str) -> None:
        with open("log.txt", "a") as f:
            f.write(message + "\n")

class RemoteLogger(Logger):
    def __init__(self, endpoint: str) -> None:
        self.endpoint = endpoint
    def log(self, message: str) -> None:
        # send to remote endpoint...
        pass

# Registry maps keys to factory callables
_LOGGER_REGISTRY: Dict[str, Callable[[], Logger]] = {
    "console": ConsoleLogger,
    "file":    FileLogger,
    "remote":  lambda: RemoteLogger(config["REMOTE_URL"]),
}

def create_logger(kind: str) -> Logger:
    factory = _LOGGER_REGISTRY.get(kind.lower())
    if not factory:
        raise ValueError(f"Unknown logger kind: {kind}")
    # ← Decision happens here: registry lookup
    return factory()
```

---

## 9. Summary

* **Decision Point:** Always live inside the factory method or registry—the client simply calls `Factory.Create(...)`.
* **Dynamic vs. Static:** Dynamic factories centralize runtime branching; static factories illustrate pattern mechanics.
* **OCP Concern:** Replace hard‑coded switches with registries or DI containers to remain open for new variants.
* **Key Benefit:** Centralized, flexible instantiation logic that can evolve without touching business‑logic clients.

---

## 10. Sources

* Gamma, Erich et al. *Design Patterns: Elements of Reusable Object-Oriented Software*.
* Microsoft Patterns & Practices: Factory Method.
* Martin Fowler, “Inversion of Control Containers and the Dependency Injection pattern.”

