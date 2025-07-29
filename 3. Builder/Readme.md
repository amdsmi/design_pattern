# 1:10:31    -  Builder.

The Builder design pattern is a creational design pattern that aims to separate the construction of a complex object from its representation. This means that the same construction process can create different representations of the object.

Think of it like building a custom computer:

Director: You, the customer, tell the builder what kind of computer you want (e.g., "a gaming PC with high-end graphics and lots of RAM").
Builder: The builder is the expert who knows how to assemble the parts. They have methods like "add CPU," "add RAM," "add GPU," etc.
Concrete Builders: Different builders might specialize in different types of computers (e.g., a "Gaming PC Builder" and an "Office PC Builder"). Each knows how to add specific parts according to the type.
Product: The final assembled computer.
The Builder pattern is particularly useful when:

An object's construction is complex and involves many optional parts or configurations.
You want to create different representations of a complex object using the same construction process.
You want to isolate the creation logic from the client code.
Key Components of the Builder Pattern:
Product: The complex object being built. It's often composed of multiple parts.
Builder (Abstract/Interface): Declares the interface for building parts of the Product object. It defines a set of methods for constructing the various components of the product.
Concrete Builder: Implements the Builder interface. It constructs and assembles the parts of the product and provides a method to retrieve the final product. Each concrete builder specializes in creating a particular representation of the product.
Director (Optional): Constructs an object using the Builder interface. It orchestrates the building process, calling the builder's methods in a specific sequence. The director is useful when you have common construction algorithms.
Builder Design Pattern in Python with Example
Let's illustrate with an example: building a custom Pizza. A pizza can have different dough, sauce, and toppings.

1. Product (Pizza):

```Python

class Pizza:
    def __init__(self):
        self.dough = None
        self.sauce = None
        self.toppings = []

    def set_dough(self, dough):
        self.dough = dough

    def set_sauce(self, sauce):
        self.sauce = sauce

    def add_topping(self, topping):
        self.toppings.append(topping)

    def __str__(self):
        return (f"Pizza with: Dough='{self.dough}', Sauce='{self.sauce}', "
                f"Toppings={', '.join(self.toppings) if self.toppings else 'None'}")
```

2. Builder (Abstract Base Class - ABC):

```Python

import abc

class PizzaBuilder(abc.ABC):
    def __init__(self):
        self.pizza = Pizza()

    @abc.abstractmethod
    def build_dough(self):
        pass

    @abc.abstractmethod
    def build_sauce(self):
        pass

    @abc.abstractmethod
    def build_toppings(self):
        pass

    def get_pizza(self):
        return self.pizza
```

3. Concrete Builders:

These will implement the PizzaBuilder and define how specific types of pizzas are built.

```Python

class MargheritaPizzaBuilder(PizzaBuilder):
    def build_dough(self):
        self.pizza.set_dough("thin crust")

    def build_sauce(self):
        self.pizza.set_sauce("tomato sauce")

    def build_toppings(self):
        self.pizza.add_topping("mozzarella cheese")
        self.pizza.add_topping("basil")

class PepperoniPizzaBuilder(PizzaBuilder):
    def build_dough(self):
        self.pizza.set_dough("hand-tossed")

    def build_sauce(self):
        self.pizza.set_sauce("spicy tomato sauce")

    def build_toppings(self):
        self.pizza.add_topping("pepperoni slices")
        self.pizza.add_topping("extra mozzarella")

class VeggiePizzaBuilder(PizzaBuilder):
    def build_dough(self):
        self.pizza.set_dough("whole wheat")

    def build_sauce(self):
        self.pizza.set_sauce("pesto sauce")

    def build_toppings(self):
        self.pizza.add_topping("bell peppers")
        self.pizza.add_topping("onions")
        self.pizza.add_topping("mushrooms")
        self.pizza.add_topping("olives")
```
4. Director (Pizzeria):

The director orchestrates the building process. It takes a builder and tells it which steps to perform to construct a full pizza.

```Python

class Pizzeria:
    def __init__(self, builder: PizzaBuilder):
        self._builder = builder

    def set_builder(self, builder: PizzaBuilder):
        self._builder = builder

    def construct_pizza(self):
        self._builder.build_dough()
        self._builder.build_sauce()
        self._builder.build_toppings()
        return self._builder.get_pizza()
```

Putting It All Together (Client Code):

```Python

# Create a Margherita Pizza
margherita_builder = MargheritaPizzaBuilder()
pizzeria = Pizzeria(margherita_builder)
margherita_pizza = pizzeria.construct_pizza()
print(margherita_pizza)

print("-" * 30)

# Create a Pepperoni Pizza
pepperoni_builder = PepperoniPizzaBuilder()
pizzeria.set_builder(pepperoni_builder) # Change the builder
pepperoni_pizza = pizzeria.construct_pizza()
print(pepperoni_pizza)

print("-" * 30)

# Create a Veggie Pizza
veggie_builder = VeggiePizzaBuilder()
pizzeria.set_builder(veggie_builder)
veggie_pizza = pizzeria.construct_pizza()
print(veggie_pizza)

print("-" * 30)

# Example of a custom pizza (without director if the construction steps are unique)
# Or, you could have a 'CustomPizzaBuilder' and a 'Director' method for it.
class CustomPizzaBuilder(PizzaBuilder):
    def build_dough(self):
        self.pizza.set_dough("thick crust")

    def build_sauce(self):
        self.pizza.set_sauce("BBQ sauce")

    def build_toppings(self):
        self.pizza.add_topping("chicken")
        self.pizza.add_topping("onions")
        self.pizza.add_topping("cheddar cheese")

custom_builder = CustomPizzaBuilder()
pizzeria.set_builder(custom_builder)
custom_pizza = pizzeria.construct_pizza()
print(custom_pizza)
```

Explanation of the Example:
Pizza (Product): This is the simple object that gets built. It has attributes for dough, sauce, and toppings.
PizzaBuilder (Abstract Builder): Defines the interface (using abc.ABC) for all pizza builders. It ensures that any concrete builder will have methods to build dough, sauce, and toppings, and a way to get the final pizza.
MargheritaPizzaBuilder, PepperoniPizzaBuilder, VeggiePizzaBuilder, CustomPizzaBuilder (Concrete Builders): Each of these classes implements the PizzaBuilder interface. They define the specific steps and ingredients for creating a particular type of pizza. Notice how each build_X method adds different components to its own self.pizza instance.
Pizzeria (Director): This class is optional but common. It takes a PizzaBuilder in its constructor (or via set_builder) and has a construct_pizza method. This method defines the order of the building steps (dough, then sauce, then toppings). The director encapsulates the common construction logic, so the client doesn't need to remember which builder methods to call in what sequence.
Benefits of the Builder Pattern:
Clear Separation of Concerns: The construction logic is separate from the product's representation.
Fine-grained Control over Construction: You can control the building process step by step.
Supports Different Representations: Using the same director and builder interface, you can create various types of complex objects (e.g., different pizza types) without changing the client code that orchestrates the building.
Improves Readability: When an object has many parameters in its constructor, using a builder can make the client code cleaner and more readable.
Reduces "Telescoping Constructor" Problem: Instead of having many constructors with increasing numbers of arguments, you use methods of the builder to set properties.
When to Use the Builder Pattern:
When the creation of a complex object needs to be independent of its parts and how they are assembled.
When the object being constructed has many optional parts or properties, and you want to avoid a "telescoping constructor" (a constructor with many overloaded versions or a single one with many optional arguments).
When you need to create different representations of the same complex object.
When the construction process itself is complex and needs to be encapsulated.
The Builder pattern provides a flexible and maintainable way to construct complex objects, making your code easier to understand, extend, and test.

# How to create chaining 

in a class add method that so something and return self like this 

```py
class HtmlBuilder:
    __root = HtmlElement()

    def __init__(self, root_name):
        self.root_name = root_name
        self.__root.name = root_name

    # not fluent
    def add_child(self, child_name, child_text):
        self.__root.elements.append(
            HtmlElement(child_name, child_text)
        )

    # fluent
    def add_child_fluent(self, child_name, child_text):
        self.__root.elements.append(
            HtmlElement(child_name, child_text)
        )
        return self

    def clear(self):
        self.__root = HtmlElement(name=self.root_name)

    def __str__(self):
        return str(self.__root)

```

1:22:51    -  Builder Facets.
1:32:10    -  Builder Inheritance.