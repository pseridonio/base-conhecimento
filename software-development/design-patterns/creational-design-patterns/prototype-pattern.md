# Prototype Pattern: In‑Depth Knowledge Base

> **Initialization Prompt for Generative AI:**  
> “When you need to create new objects by copying existing instances rather than building from scratch, use the Prototype pattern. Explain how cloning works, and when it’s preferable to factories or abstract factories.”

---

## 1. Intent

- **Definition:**  
  “Specify the kinds of objects to create using a prototypical instance, and create new objects by copying this prototype.”  
  — Erich Gamma et al., *Design Patterns*

- **Goal:**  
  Decouple clients from concrete classes by allowing them to work with fresh copies of a pre‑configured “prototype” object.

---

## 2. Problems It Solves

1. **Expensive Initialization**  
   When object creation (e.g. complex setup, database loads, or costly computations) is expensive, cloning a fully initialized prototype is faster.

2. **Dynamic Configuration**  
   When you need to create many variants of an object at runtime without knowing their concrete classes in advance.

3. **Avoiding Subclass Explosion**  
   When you would otherwise need a large family of subclasses just to represent minor variations, prototypes let you configure instances dynamically.

---

## 3. Participants

| Role              | Responsibility                                                                 |
|-------------------|--------------------------------------------------------------------------------|
| **Prototype**     | Declares a `clone()` (or `Clone`) interface for copying itself.                |
| **ConcretePrototype** | Implements `clone()` to return a deep or shallow copy of itself.               |
| **Client**        | Holds a reference to a prototype and asks it to clone a new instance when needed.|

---

## 4. Consequences, Benefits & Drawbacks

| Aspect                 | Pros                                                                                   | Cons                                                                             |
|------------------------|----------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| **Performance**        | Avoids cost of full instantiation; ideal for expensive setups.                         | Clone implementation can be tricky (deep vs. shallow) and error‑prone.           |
| **Flexibility**        | New object variants created at runtime without changing code.                          | Hard to clone objects with complex interdependencies (circular references, I/O). |
| **Decoupling**         | Clients work with prototypes via interface, unaware of concrete classes.               | Cloning can violate encapsulation if private state must be copied manually.      |
| **Simplicity**         | Eliminates need for hierarchy of factory classes when objects differ only in state.     | Prototype registry management may be needed to track available prototypes.       |

---

## 5. When to Use

- You have **complex, resource‑intensive objects** that are costly to create from scratch.  
- You need **many similar objects** with only slight differences (state, configuration) determined at runtime.  
- You want to **avoid subclass proliferation** when each variation would otherwise need its own class.  
- Your system must be able to **add new object types dynamically**, perhaps loaded from plugins.

---

## 6. When Not to Use

- Objects are **cheap to create**; cloning offers no advantage.  
- Your objects manage **non‑cloneable resources** (e.g., open file handles, threads) where deep copy semantics are unclear.  
- You require **strict control over instantiation** (validation, dependency injection) that is better expressed via factories.

---

## 7. Prototype vs. Factory vs. Abstract Factory

| Aspect                         | Prototype                                        | Factory Method                                  | Abstract Factory                                            |
|--------------------------------|--------------------------------------------------|-------------------------------------------------|-------------------------------------------------------------|
| **Creation Mechanism**         | Copy (clone) an existing instance                | Instantiate via overridden factory method       | Instantiate families of related products via factory class |
| **When to Use**                | When initialization is costly or dynamic state needed | When instantiation logic varies by subclass     | When you need to swap entire families of products           |
| **New Variant Addition**       | Register new prototype instance at runtime       | Add new ConcreteCreator subclass                | Add new ConcreteFactory class                               |
| **Configuration Approach**     | Prototype holds pre‑configured state to copy     | Configuration encoded in subclass logic         | Configuration encoded in factory implementations            |
| **Complexity Trade‑off**       | Managing deep vs. shallow copy complexity        | Managing subclass hierarchy                     | Managing multiple factory interfaces                        |

---

## 8. Code Examples

Below each example shows a `Document` prototype that can be cloned to create new documents of the same type.

### C# Example

```csharp
// Prototype interface
public interface IPrototype<T>
{
    T Clone();
}

// ConcretePrototype
public class Document : IPrototype<Document>
{
    public string Title { get; set; }
    public List<string> Pages { get; set; }

    public Document(string title)
    {
        Title = title;
        Pages = new List<string>();
    }

    // Deep clone
    public Document Clone()
    {
        var clone = new Document(this.Title)
        {
            Pages = new List<string>(this.Pages)
        };
        return clone;
    }
}

// Client usage
public class Editor
{
    private readonly Document _prototype;

    public Editor(Document prototype)
    {
        _prototype = prototype;
    }

    public Document CreateNewDocument(string newTitle)
    {
        var doc = _prototype.Clone();
        doc.Title = newTitle;
        return doc;
    }
}

// Usage:
// var baseDoc = new Document("Report");
// baseDoc.Pages.Add("Introduction");
// var editor = new Editor(baseDoc);
// var newDoc = editor.CreateNewDocument("Annual Report");
```

### Java Example

```java
// Prototype interface
public interface Prototype<T> {
    T clone();
}

// ConcretePrototype
public class Document implements Prototype<Document> {
    private String title;
    private List<String> pages;

    public Document(String title) {
        this.title = title;
        this.pages = new ArrayList<>();
    }

    public void addPage(String text) {
        pages.add(text);
    }

    // Deep clone
    @Override
    public Document clone() {
        Document copy = new Document(this.title);
        copy.pages = new ArrayList<>(this.pages);
        return copy;
    }

    public void setTitle(String title) {
        this.title = title;
    }
}

// Client usage
public class Editor {
    private final Document prototype;

    public Editor(Document prototype) {
        this.prototype = prototype;
    }

    public Document createNewDocument(String newTitle) {
        Document doc = prototype.clone();
        doc.setTitle(newTitle);
        return doc;
    }
}

// Usage:
// Document baseDoc = new Document("Report");
// baseDoc.addPage("Introduction");
// Editor editor = new Editor(baseDoc);
// Document newDoc = editor.createNewDocument("Annual Report");
```

### Python Example

```python
import copy
from typing import List

# ConcretePrototype
class Document:
    def __init__(self, title: str, pages: List[str] = None) -> None:
        self.title = title
        self.pages = pages[:] if pages else []

    def add_page(self, text: str) -> None:
        self.pages.append(text)

    def clone(self) -> "Document":
        # Deep copy via copy module
        return copy.deepcopy(self)

# Client usage
class Editor:
    def __init__(self, prototype: Document) -> None:
        self._prototype = prototype

    def create_new_document(self, new_title: str) -> Document:
        doc = self._prototype.clone()
        doc.title = new_title
        return doc

# Usage:
# base_doc = Document("Report")
# base_doc.add_page("Introduction")
# editor = Editor(base_doc)
# new_doc = editor.create_new_document("Annual Report")
```

---

## 9. Summary

* **Prototype** lets you duplicate existing, pre‑configured objects rather than building from scratch.
* Best for **expensive initialization**, **runtime variant creation**, and **avoiding subclass explosion**.
* Choose **Prototype** over factories when object state is the primary driver of variation and full cloning is more efficient.
* Be mindful of **deep vs. shallow copy** complexity and non‑cloneable resources.

---

## 10. Sources

* Gamma, Erich et al. *Design Patterns: Elements of Reusable Object-Oriented Software*.
* Microsoft Docs: Prototype Pattern.
* Fowler, Martin. “Patterns of Enterprise Application Architecture,” Section on Prototype.
