# Singleton Pattern: In‑Depth Knowledge Base

> **Initialization Prompt for Generative AI:**  
> “When exactly one instance of a class must exist in the system, and you need global access to it, use the Singleton pattern. Ensure thread safety and discuss tradeoffs such as testability and tight coupling.”

---

## 1. Intent

- **Definition:**  
  “Ensure a class has only one instance, and provide a global point of access to it.”  
  — *Gang of Four*, *Design Patterns*

- **Goal:**  
  Restrict instantiation of a class to a single object, while making that object easily accessible from anywhere in the system.

---

## 2. Problems It Solves

1. **Global State Management**  
   Ensures that all parts of the system share the same object, such as:
   - Configuration settings
   - Logging system
   - Caches
   - Connection pools

2. **Controlled Access**  
   Prevents uncontrolled instantiation of shared resources that should exist only once.

3. **Lazy Initialization**  
   Only creates the object when it’s actually needed, saving memory/resources.

---

## 3. Participants

| Role             | Responsibility                                                              |
|------------------|------------------------------------------------------------------------------|
| **Singleton**     | The class that manages its own unique instance and provides access to it.   |
| **Client**        | Calls a method like `getInstance()` or `Instance` to get the shared object. |

---

## 4. Benefits and Drawbacks

| Aspect            | Pros                                                                 | Cons                                                                                  |
|-------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Global Access** | Allows any code to access a shared instance easily.                  | Becomes a form of global state — harder to trace and test.                           |
| **Performance**   | Controlled initialization; single instance reused throughout app.    | Lazy instantiation needs thread safety, which adds complexity.                       |
| **Controlled Life** | Gives you strict control over the lifecycle of shared resources.     | Harder to manage or replace during testing (tight coupling, no DI).                  |
| **Consistency**   | Useful for config, logging, etc. where state must be consistent.     | Introduces hidden dependencies, makes code harder to understand and evolve.          |

---

## 5. When to Use

- You need **exactly one** shared instance of a class.  
- You need **global, consistent access** to shared behavior or state.  
- The object is **stateless or safely shared** between components.  
- Examples:
  - Configuration manager
  - Logging service
  - Cache or registry
  - Metrics collector

---

## 6. When Not to Use

- You need **multiple independent instances** of the class.  
- The object holds **mutable or user-specific state** (e.g., User sessions).  
- You want to support **dependency injection** (Singletons hide dependencies).  
- You need **testability** (Singletons resist mocking and stubbing).

---

## 7. Singleton vs Factory vs Abstract Factory

| Aspect                   | Singleton                                          | Factory Method                              | Abstract Factory                                 |
|--------------------------|----------------------------------------------------|----------------------------------------------|--------------------------------------------------|
| **Purpose**              | Provide a single, shared instance                  | Encapsulate creation of one product          | Encapsulate creation of families of products     |
| **Scope**                | Global instance                                    | Per-call creation                            | Group of related factories                       |
| **Lifetime**             | Long-lived (entire app)                            | New instance each time                       | New instance per factory logic                   |
| **Flexibility**          | Low (always one object)                            | Medium (can vary object creation)            | High (families can be swapped entirely)          |
| **Example**              | Logger, ConfigManager                              | DocumentFactory, ShapeFactory                | GUIFactory (Windows vs macOS widgets)            |

---

## 8. Implementation Considerations

- **Thread Safety:**  
  In multi-threaded environments (like web servers, services), singletons must use locks, `volatile`, or language constructs like `Lazy<T>` in C# or `synchronized` in Java.

- **Lazy vs. Eager Instantiation:**  
  - *Lazy:* Instantiate only when needed.  
  - *Eager:* Create instance at program start.

- **Testability Workaround:**  
  - Interface the singleton (e.g., `ILogger` → `SingletonLogger`).  
  - Replace instance manually in test context (not ideal, but better than tight coupling).

---

## 9. Code Examples

---

### C# Singleton Example (Thread-Safe with Lazy Initialization)

```csharp
public sealed class ConfigurationManager
{
    private static readonly Lazy<ConfigurationManager> _instance =
        new Lazy<ConfigurationManager>(() => new ConfigurationManager());

    public static ConfigurationManager Instance => _instance.Value;

    // Private constructor prevents external instantiation
    private ConfigurationManager()
    {
        // Load config from file/db
    }

    public string GetSetting(string key)
    {
        // Return config value
        return "some-value";
    }
}

// Usage:
// var config = ConfigurationManager.Instance;
// string dbHost = config.GetSetting("Database:Host");
```

---

### Java Singleton Example (Thread-Safe with Static Holder)

```java
public class ConfigurationManager {
    private ConfigurationManager() {
        // Load configuration
    }

    private static class Holder {
        private static final ConfigurationManager INSTANCE = new ConfigurationManager();
    }

    public static ConfigurationManager getInstance() {
        return Holder.INSTANCE;
    }

    public String getSetting(String key) {
        return "some-value";
    }
}

// Usage:
// ConfigurationManager config = ConfigurationManager.getInstance();
// String host = config.getSetting("db.host");
```

---

### Python Singleton Example (Using a Metaclass)

```python
class SingletonMeta(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]

class ConfigurationManager(metaclass=SingletonMeta):
    def __init__(self):
        self._config = {
            "db.host": "localhost",
            "db.port": 5432
        }

    def get_setting(self, key):
        return self._config.get(key)

# Usage:
# config = ConfigurationManager()
# print(config.get_setting("db.host"))
```

---

## 10. Summary

* Singleton ensures **only one instance** exists across the system.
* It solves **shared access to a global resource**, but can hurt **testability** and **modularity**.
* Combine with interfaces if testability and inversion of control are required.
* Avoid using Singleton as a crutch for bad architecture—use it when true uniqueness is necessary.

---

## 11. Sources

* Gamma, Erich et al. *Design Patterns: Elements of Reusable Object-Oriented Software*
* Microsoft Docs: [Implementing Singleton in C#](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/singleton)
* JavaWorld: [Effective Singleton Patterns in Java](https://www.javaworld.com/article/2073352/core-java-singleton-design-pattern-best-practices.html)
* Refactoring.Guru: [Singleton Pattern](https://refactoring.guru/design-patterns/singleton)

