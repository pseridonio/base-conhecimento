# Builder Pattern: In‑Depth Overview

The **Builder** pattern separates the construction of a complex object from its representation, allowing the same construction process to create different representations. It is ideal when you need to assemble an object step by step and the final object has many optional parts or complex configuration.

---

## 1. Intent

- **Definition:**  
  “Separate the construction of a complex object from its representation so that the same construction process can create different representations.”  
  — Erich Gamma et al., *Design Patterns*

- **Primary Goal:**  
  Encapsulate the steps required to build a complex object and expose a fluent, readable interface for clients.

---

## 2. Problems It Solves

1. **Telescoping Constructors**  
   When a class has many constructor parameters—especially optional ones—calling new Class(a,b,c,d,…) leads to confusing code.

2. **Clarity of Object Creation**  
   Clients avoid having to know which parameters are required vs. optional, or in which order to supply them.

3. **Immutability & Thread Safety**  
   You can construct immutable objects whose fields are set only once by the builder.

4. **Multiple Representations**  
   The same build sequence can produce different variants (e.g., JSON vs. XML document builders).

---

## 3. Participants

1. **Builder (interface/abstract class)**  
   Declares methods for creating different parts of the product. Methods typically return `this` for chaining.

2. **ConcreteBuilder**  
   Implements the Builder interface, maintaining the internal representation, and provides a `Build()` (or `GetResult()`) method to retrieve the final product.

3. **Director (optional)**  
   Knows the order in which to call builder methods to assemble the product. Clients can call the builder themselves in custom order, often making the Director unnecessary in simple cases.

4. **Product**  
   The complex object under construction with many parts or configuration options.

---

## 4. Structure & Sequence

1. Client instantiates a ConcreteBuilder.  
2. (Optional) Client passes the builder to a Director, or calls builder methods directly in desired sequence.  
3. Builder methods configure individual parts.  
4. Client calls `Build()` to obtain the fully constructed Product.

```text
Client → Builder ← Director?
           ↓
      ConcreteBuilder → Product
```

---

## 5. Consequences, Benefits & Drawbacks

| Aspect                     | Pros                                                                                 | Cons                                                          |
| -------------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------------- |
| **Readability**            | Fluent API makes object creation clear and self‑documenting.                         | More classes/interfaces introduced.                           |
| **Immutability**           | Product can be made immutable because all setup happens in builder.                  | Boilerplate builder code for each product.                    |
| **Flexibility**            | Different configurations easily expressed via different builder calls or subclasses. | Overkill for simple objects with few parameters.              |
| **Separation of Concerns** | Builder encapsulates construction logic; Product contains only data/behavior.        | Director adds indirection if used; clients may bypass it.     |
| **Reusability**            | Same builder can be reused to create multiple products with slight variations.       | If product evolves, both builder and product must be updated. |

---

## 6. When to Use

* The target object has **many optional parameters** or **complex setup** steps.
* You want to **avoid long constructors** with numerous arguments.
* You need to build **immutable** objects in a stepwise fashion.
* You want to **support multiple representations** of the same construction process.

---

## 7. When Not to Use

* The object is **simple** and has only a few parameters—standard constructor is sufficient.
* Introducing extra builder classes would **add unnecessary complexity**.
* You don’t require **fluent chaining** or **stepwise construction**.

---

## 8. Code Examples

### C# Example

```csharp
// Product
public class NutritionFacts
{
    public int Servings { get; }
    public int Calories { get; }
    public int Fat { get; }
    public int Sodium { get; }
    public int Carbohydrate { get; }

    private NutritionFacts(int servings, int calories, int fat, int sodium, int carbohydrate)
    {
        Servings = servings;
        Calories = calories;
        Fat = fat;
        Sodium = sodium;
        Carbohydrate = carbohydrate;
    }

    // Builder
    public class Builder
    {
        private int _servings;
        private int _calories;
        private int _fat = 0;
        private int _sodium = 0;
        private int _carbohydrate = 0;

        public Builder(int servings, int calories)
        {
            _servings = servings;
            _calories = calories;
        }

        public Builder SetFat(int fat)
        {
            _fat = fat;
            return this;
        }

        public Builder SetSodium(int sodium)
        {
            _sodium = sodium;
            return this;
        }

        public Builder SetCarbohydrate(int carbohydrate)
        {
            _carbohydrate = carbohydrate;
            return this;
        }

        public NutritionFacts Build()
        {
            return new NutritionFacts(_servings, _calories, _fat, _sodium, _carbohydrate);
        }
    }
}

// Usage
// NutritionFacts facts = new NutritionFacts.Builder(1, 200)
//     .SetFat(10)
//     .SetSodium(35)
//     .Build();  

```

### Java Example

```java
// Product
public class NutritionFacts {
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    private NutritionFacts(Builder builder) {
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    // Builder
    public static class Builder {
        // Required
        private final int servings;
        private final int calories;
        // Optional
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servings, int calories) {
            this.servings = servings;
            this.calories = calories;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }
}

// Usage
// NutritionFacts facts = new NutritionFacts.Builder(2, 250)
//     .fat(5)
//     .sodium(20)
//     .build();
```

### Python Example

```python
class NutritionFacts:
    def __init__(
        self,
        servings: int,
        calories: int,
        fat: int,
        sodium: int,
        carbohydrate: int
    ) -> None:
        self.servings = servings
        self.calories = calories
        self.fat = fat
        self.sodium = sodium
        self.carbohydrate = carbohydrate

    class Builder:
        def __init__(self, servings: int, calories: int) -> None:
            self._servings = servings
            self._calories = calories
            self._fat = 0
            self._sodium = 0
            self._carbohydrate = 0

        def set_fat(self, fat: int) -> "NutritionFacts.Builder":
            self._fat = fat
            return self

        def set_sodium(self, sodium: int) -> "NutritionFacts.Builder":
            self._sodium = sodium
            return self

        def set_carbohydrate(self, carbohydrate: int) -> "NutritionFacts.Builder":
            self._carbohydrate = carbohydrate
            return self

        def build(self) -> "NutritionFacts":
            return NutritionFacts(
                self._servings,
                self._calories,
                self._fat,
                self._sodium,
                self._carbohydrate
            )

# Usage
# builder = NutritionFacts.Builder(1, 180)
# facts = builder.set_fat(8).set_sodium(30).build()
```

---

> **Prompt for Generative AI:**
> “When constructing a complex object with many optional parameters, use the Builder pattern. Explain which steps are required vs. optional, and show how the fluent API avoids telescoping constructors.”
