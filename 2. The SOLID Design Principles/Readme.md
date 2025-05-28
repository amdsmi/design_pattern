# THE SOLID DESIGN PRINCIPLES
 
* 0:00:00    -  Introduction.
* 0:07:04    -  Overview.
## 0:07:57    -  $ Single Responsibility Principle. $

The Single Responsibility Principle (SRP) is the first of the five SOLID design principles. It states that a class should have only one reason to change. In simpler terms, each class should have a single, well-defined responsibility.

<b>```Think of it this way: if you can think of more than one reason why a class would need to be modified, it likely violates the SRP.```</b>

Why is SRP important?

Improved Maintainability: When a class has a single responsibility, changes to that responsibility are isolated to that class. This reduces the risk of unintended side effects in other parts of the system.
Reduced Complexity: Classes with focused responsibilities are generally smaller and easier to understand.
Increased Reusability: A class that does one thing well is more likely to be reusable in different contexts.
Easier Testing: When a class has a single responsibility, it's easier to write comprehensive unit tests for it.
Violation of SRP (Bad Example)

Let's imagine a Report class that handles both generating the report content and printing it.

```py

class Report:
    def __init__(self, title, content):
        self.title = title
        self.content = content

    def generate_report_content(self):
        # Logic to format and generate the report text
        return f"--- {self.title} ---\n\n{self.content}\n\nGenerated on: 2025-05-27"

    def print_report(self):
        # Logic to send the report to a printer
        print(self.generate_report_content())
        print("Report sent to printer.")

# Usage
my_report = Report("Monthly Sales", "Details about sales performance...")
my_report.print_report()
```

In this example, the Report class has two reasons to change:

Changes in report content generation logic: If we want to change how the report content is formatted (e.g., add more sections, change the date format), we'd modify generate_report_content.
Changes in printing mechanism: If we want to print to a PDF instead of a physical printer, or send it via email, we'd modify print_report.
This violates SRP because the class has two distinct responsibilities: content generation and output (printing).

Adhering to SRP (Good Example)

To adhere to SRP, we can separate these responsibilities into different classes:

```Python

class ReportContent:
    def __init__(self, title, content):
        self.title = title
        self.content = content

    def generate(self):
        # Logic to format and generate the report text
        return f"--- {self.title} ---\n\n{self.content}\n\nGenerated on: 2025-05-27"

class ReportPrinter:
    def print_to_console(self, report_text):
        print(report_text)
        print("Report printed to console.")

    def print_to_pdf(self, report_text, filename="report.pdf"):
        # In a real scenario, this would use a PDF library
        with open(filename, "w") as f:
            f.write(report_text)
        print(f"Report saved to {filename}")

# Usage
report_data = ReportContent("Monthly Sales", "Details about sales performance...")
report_text = report_data.generate()

printer = ReportPrinter()
printer.print_to_console(report_text)
printer.print_to_pdf(report_text, "monthly_sales_report.pdf")
```

Now:

The ReportContent class is solely responsible for generating the report's textual content. If the content format changes, only this class needs modification.
The ReportPrinter class is solely responsible for handling different ways of outputting (printing) the report. If we add new output methods (e.g., email, cloud storage), only this class needs modification.
This separation of concerns makes the code more modular, easier to understand, test, and maintain. Each class has one reason to change.

## 0:15:28    - $ Open-Closed Principle. $

The Open-Closed Principle (OCP) is the second of the five SOLID design principles. It states that software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

$In simpler terms:$

<b>`Open for extension: You should be able to add new functionality or behaviors to a system without changing its existing code.
Closed for modification: Once a class or module is written and tested, you should ideally not have to modify its source code to accommodate new requirements.`</b>

The primary goal of OCP is to make your code more robust, maintainable, and flexible. When you adhere to OCP, you reduce the risk of introducing bugs into existing, well-tested code every time you need to add a new feature.

How to achieve OCP
OCP is typically achieved through the use of abstractions (interfaces or abstract classes) and polymorphism.

Abstractions: Define an interface or an abstract base class that outlines the common behavior.
Implementations: Create concrete classes that implement or inherit from this abstraction, providing specific implementations of the defined behavior.
Client Code: Write client code that depends on the abstraction, not on the concrete implementations. This allows you to "plug in" new implementations without changing the client code.
Example in Python
Let's consider an example of a reporting system where we need to generate reports in different formats (e.g., PDF, CSV, JSON).

Violating OCP (Bad Example)
```py

class ReportGenerator:
    def generate_report(self, report_data, format_type):
        if format_type == "pdf":
            print("Generating PDF report...")
            # Logic for PDF generation
        elif format_type == "csv":
            print("Generating CSV report...")
            # Logic for CSV generation
        elif format_type == "json":
            print("Generating JSON report...")
            # Logic for JSON generation
        else:
            raise ValueError("Unsupported report format")

# Usage
data = {"title": "Monthly Sales", "items": [{"product": "A", "sales": 100}]}
generator = ReportGenerator()
generator.generate_report(data, "pdf")
generator.generate_report(data, "csv")
```

Why this violates OCP:
If you need to add a new report format (e.g., XML), you must modify the generate_report method in the ReportGenerator class. This means changing existing, potentially working code.
The ReportGenerator class is coupled to specific report formats.
Adhering to OCP (Good Example)
To adhere to OCP, we'll use an abstract base class and polymorphism.

```Py

from abc import ABC, abstractmethod

# 1. Abstraction (Open for Extension)
class ReportFormatter(ABC):
    @abstractmethod
    def format(self, report_data):
        pass

# 2. Implementations (New functionality doesn't change existing code)
class PdfReportFormatter(ReportFormatter):
    def format(self, report_data):
        print(f"Formatting report as PDF: {report_data}")
        # Actual PDF generation logic would go here
        return "PDF Report Content"

class CsvReportFormatter(ReportFormatter):
    def format(self, report_data):
        print(f"Formatting report as CSV: {report_data}")
        # Actual CSV generation logic
        return "CSV Report Content"

class JsonReportFormatter(ReportFormatter):
    def format(self, report_data):
        print(f"Formatting report as JSON: {report_data}")
        # Actual JSON generation logic
        return "JSON Report Content"

# 3. Client Code (Closed for Modification)
class ReportGenerator:
    def __init__(self, formatter: ReportFormatter):
        self.formatter = formatter

    def generate_report(self, report_data):
        formatted_report = self.formatter.format(report_data)
        print(f"Report generated: {formatted_report}")
        return formatted_report

# Usage
data = {"title": "Monthly Sales", "items": [{"product": "A", "sales": 100}]}

# Generate PDF report
pdf_generator = ReportGenerator(PdfReportFormatter())
pdf_generator.generate_report(data)

# Generate CSV report
csv_generator = ReportGenerator(CsvReportFormatter())
csv_generator.generate_report(data)

# Generate JSON report
json_generator = ReportGenerator(JsonReportFormatter())
json_generator.generate_report(data)

# Adding a new format (e.g., XML) - No modification to existing classes!
class XmlReportFormatter(ReportFormatter):
    def format(self, report_data):
        print(f"Formatting report as XML: {report_data}")
        # Actual XML generation logic
        return "<xml>XML Report Content</xml>"

print("\n--- Adding new XML format ---")
xml_generator = ReportGenerator(XmlReportFormatter())
xml_generator.generate_report(data)
```

Why this adheres to OCP:

Open for extension: If you want to add a new report format (e.g., XmlReportFormatter), you simply create a new class that inherits from ReportFormatter and implements the format method. You don't need to touch the ReportGenerator class or any existing formatter classes.
Closed for modification: The ReportGenerator class doesn't need to be modified when new report formats are introduced. It depends on the ReportFormatter abstraction, not on specific implementations.
Benefits of OCP:
Increased Flexibility: Easily add new features without changing existing code.
Reduced Risk: Less chance of breaking existing functionality when adding new features.
Improved Maintainability: Code is easier to understand and manage because responsibilities are clearly separated.
Better Testability: Individual components (formatters) can be tested in isolation.
By following the Open-Closed Principle, you build systems that are more adaptable to change, which is crucial in the ever-evolving world of software development.

## 0:32:58    - $ Liskov Substitution Principle. $

The Liskov Substitution Principle (LSP) is the third of the five SOLID design principles. It states that:

"Objects in a program should be replaceable with instances of their subtypes without altering the correctness of that program."

In simpler terms:

<b>`If you have a base class (or interface) A, and a derived class B that inherits from A, then wherever you use A, you should be able to substitute it with B without breaking the program's functionality.
Subtypes must be able to substitute their base types without introducing unexpected behavior or errors.
LSP essentially means that a subclass should extend the capabilities of its parent class without changing its fundamental behavior. It reinforces the "is-a" relationship, ensuring that inheritance is used correctly.`</b>

#### Violations of LSP often occur when:

A subclass breaks the parent's contract: The subclass might not fulfill all the expectations or guarantees made by the parent class.
A subclass changes the meaning of inherited methods: The overridden methods in the subclass behave in a way that is not consistent with the parent's original intent.
A subclass restricts the functionality of the parent: The subclass throws new exceptions or returns unexpected values that the client code using the parent type isn't prepared for.
Example in Python
Let's consider a classic example involving shapes.

Violating LSP (Bad Example)
Imagine we have a Rectangle class and we want to create a Square class. Since a square is a rectangle, we might be tempted to make Square inherit from Rectangle.

```Py

class Rectangle:
    def __init__(self, width, height):
        self._width = width
        self._height = height

    @property
    def width(self):
        return self._width

    @width.setter
    def width(self, value):
        self._width = value

    @property
    def height(self):
        return self._height

    @height.setter
    def height(self, value):
        self._height = value

    def get_area(self):
        return self._width * self._height

class Square(Rectangle):
    def __init__(self, side):
        super().__init__(side, side)

    @Rectangle.width.setter
    def width(self, value):
        self._width = value
        self._height = value  # This is the problematic part!

    @Rectangle.height.setter
    def height(self, value):
        self._height = value
        self._width = value  # This is the problematic part!

def set_and_test_area(rectangle: Rectangle):
    """
    A function that expects a Rectangle and sets its dimensions.
    """
    print(f"Original width: {rectangle.width}, height: {rectangle.height}")
    rectangle.width = 5
    rectangle.height = 4
    print(f"Set width to 5, height to 4.")
    print(f"Expected area: 5 * 4 = 20")
    print(f"Actual area: {rectangle.get_area()}")
    print("-" * 20)

# Usage
print("Testing with Rectangle:")
rect = Rectangle(2, 3)
set_and_test_area(rect)

print("Testing with Square:")
sq = Square(3)
set_and_test_area(sq) # This will produce unexpected results!
```

Why this violates LSP:

When set_and_test_area expects a Rectangle and we pass a Square, the behavior changes:

For Rectangle, setting width to 5 and height to 4 results in an area of 20, as expected.
For Square, when width is set to 5, height also becomes 5. Then, when height is set to 4, width also becomes 4. So, the square ends up with width=4 and height=4, yielding an area of 16, not the expected 20.
The Square class has modified the behavior of the width and height setters in a way that breaks the contract of the Rectangle class (where width and height can be set independently). Client code expecting Rectangle behavior will be surprised when a Square is substituted.

Adhering to LSP (Good Example)
To adhere to LSP, we should recognize that while a square is a rectangle conceptually, its behavior regarding setting independent width and height is different. It's better to either:

Have a common abstraction for shapes with area calculation, and separate concrete classes.
Ensure that subclasses don't violate the base class's invariants. In the case of Square, its invariant is that width == height.
Here's an approach using option 1, which often works well with LSP:

```Py

from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def get_area(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self._width = width
        self._height = height

    @property
    def width(self):
        return self._width

    @width.setter
    def width(self, value):
        self._width = value

    @property
    def height(self):
        return self._height

    @height.setter
    def height(self, value):
        self._height = value

    def get_area(self):
        return self._width * self._height

class Square(Shape):
    def __init__(self, side):
        self._side = side

    @property
    def side(self):
        return self._side

    @side.setter
    def side(self, value):
        self._side = value

    def get_area(self):
        return self._side * self._side

def print_area(shape: Shape):
    """
    A function that expects any Shape and prints its area.
    """
    print(f"The area of the shape is: {shape.get_area()}")

# Usage
rect = Rectangle(5, 4)
print_area(rect) # Output: The area of the shape is: 20

sq = Square(5)
print_area(sq) # Output: The area of the shape is: 25

# Example of LSP in action: a list of shapes
shapes = [Rectangle(2, 3), Square(4), Rectangle(6, 2)]

print("\nProcessing a list of shapes:")
for shape in shapes:
    print_area(shape)
```

Why this adheres to LSP:

Both Rectangle and Square inherit from Shape, which defines the get_area contract.
Each subclass (Rectangle and Square) implements get_area in a way that is consistent with its own internal state and doesn't surprise the client.

The print_area function can accept any Shape object, and it will always correctly calculate and print the area without needing to know the specific type of shape it's dealing with. There's no unexpected behavior when substituting a Square for a Rectangle (or vice-versa, if the context allowed).

Key takeaways for LSP:
---
Behavioral Subtyping: Focus on the behavior of the methods. A subclass's methods should not violate the expectations set by the parent class's methods.
Don't break invariants: If a base class has certain rules (invariants) about its state, subclasses should maintain those rules.
Don't introduce new exceptions: If a base class method doesn't throw a certain exception, its overriding method in a subclass shouldn't either (unless it's a more specific sub-type of an existing exception).
Don't strengthen preconditions or weaken postconditions: A subclass method should accept at least the same range of inputs as its parent and guarantee at least the same outputs.
Adhering to LSP leads to more robust, understandable, and easily extensible code, as you can rely on the contracts defined by your base types.

# 0:40:45    - $Interface Segregation Principle.$

The Interface Segregation Principle (ISP) is the fourth of the five SOLID design principles. It states that:

"Clients should not be forced to depend on interfaces they do not use."

In simpler terms:

<b>`Instead of one large, "fat" interface, it's better to have many small, specific interfaces (or abstract base classes in Python).
Clients (classes that use an interface) should only implement or depend on the methods that are relevant to them. They shouldn't be forced to implement methods they don't need or care about.
The primary goal of ISP is to reduce the negative impact of changes. If a client depends on a large interface, and a method is added or changed in that interface that the client doesn't even use, the client might still be affected or even require recompilation/retesting, leading to unnecessary coupling.`</b>

How to achieve ISP

ISP is achieved by breaking down large, monolithic interfaces into smaller, more focused ones.

Identify diverse client needs: Understand what different "clients" (classes or modules) need to do.
Create granular interfaces: Define separate interfaces for each distinct set of functionality.
Clients implement only relevant interfaces: A class then implements only the interfaces that correspond to the functionalities it actually provides or requires.
Example in Python
Let's consider an example of a "Worker" in a company, who might have different responsibilities like Work, Eat, Manage, etc.

Violating ISP (Bad Example)
Imagine a single, large Worker interface that all types of employees must implement.

```py

from abc import ABC, abstractmethod

class Worker(ABC):
    @abstractmethod
    def work(self):
        pass

    @abstractmethod
    def eat(self):
        pass

    @abstractmethod
    def sleep(self):
        pass

    @abstractmethod
    def manage(self): # Not all workers are managers!
        pass

class HumanWorker(Worker):
    def work(self):
        print("Human working hard...")

    def eat(self):
        print("Human eating lunch...")

    def sleep(self):
        print("Human sleeping...")

    def manage(self):
        # This human worker might not be a manager, but is forced to implement this method
        print("Human trying to manage, perhaps poorly...")

class RobotWorker(Worker):
    def work(self):
        print("Robot working mechanically...")

    def eat(self):
        # Robots don't eat! Forced to implement.
        raise NotImplementedError("Robots don't eat!")

    def sleep(self):
        # Robots don't sleep in the human sense! Forced to implement.
        raise NotImplementedError("Robots don't sleep!")

    def manage(self):
        print("Robot managing tasks...")

# Usage
human = HumanWorker()
human.work()
human.eat()
human.sleep()
human.manage() # This might be unexpected for a non-manager human

robot = RobotWorker()
robot.work()
# robot.eat() # This would raise an error, even though the client just wants the robot to work!
# robot.sleep() # This would raise an error
robot.manage()
```

Why this violates ISP:

RobotWorker is forced to implement eat() and sleep(): Robots don't eat or sleep in the way humans do. The NotImplementedError is a strong indicator of an ISP violation, as it means the interface is too broad for this client.
HumanWorker is forced to implement manage(): Not every human worker is a manager. They are forced to provide an implementation, even if it's a "do-nothing" or "print a message" implementation, making the class less cohesive and potentially misleading.
Clients depend on methods they don't use: Any client code that interacts with the Worker interface might accidentally call eat() or sleep() on a RobotWorker, leading to errors.
Adhering to ISP (Good Example)
To adhere to ISP, we break down the Worker interface into smaller, more specific interfaces.

```py

from abc import ABC, abstractmethod

# 1. Granular Interfaces (Open for Extension, specific to needs)
class Workable(ABC):
    @abstractmethod
    def work(self):
        pass

class Eatable(ABC):
    @abstractmethod
    def eat(self):
        pass

class Sleepable(ABC):
    @abstractmethod
    def sleep(self):
        pass

class Manageable(ABC):
    @abstractmethod
    def manage(self):
        pass

# 2. Clients implement only relevant interfaces
class HumanWorker(Workable, Eatable, Sleepable): # Not a manager
    def work(self):
        print("Human working hard...")

    def eat(self):
        print("Human eating lunch...")

    def sleep(self):
        print("Human sleeping...")

class HumanManager(Workable, Eatable, Sleepable, Manageable): # A human who also manages
    def work(self):
        print("Human manager delegating tasks...")

    def eat(self):
        print("Human manager eating lunch...")

    def sleep(self):
        print("Human manager sleeping...")

    def manage(self):
        print("Human manager managing team...")

class RobotWorker(Workable): # Only works
    def work(self):
        print("Robot working mechanically...")

class RobotManager(Workable, Manageable): # Works and manages
    def work(self):
        print("Robot manager executing tasks...")

    def manage(self):
        print("Robot manager coordinating systems...")

# Usage
print("--- Human Worker ---")
human = HumanWorker()
human.work()
human.eat()
human.sleep()
# human.manage() # This would correctly raise an AttributeError, as HumanWorker doesn't implement it

print("\n--- Human Manager ---")
human_manager = HumanManager()
human_manager.work()
human_manager.eat()
human_manager.sleep()
human_manager.manage()

print("\n--- Robot Worker ---")
robot = RobotWorker()
robot.work()
# robot.eat() # Correctly raises AttributeError
# robot.sleep() # Correctly raises AttributeError

print("\n--- Robot Manager ---")
robot_manager = RobotManager()
robot_manager.work()
robot_manager.manage()


# Client functions that depend only on the interfaces they need
def daily_work_routine(worker: Workable):
    worker.work()

def lunch_break(eater: Eatable):
    eater.eat()

print("\n--- Daily Routines ---")
daily_work_routine(human)
daily_work_routine(robot)
daily_work_routine(human_manager)
daily_work_routine(robot_manager)

lunch_break(human)
lunch_break(human_manager)
# lunch_break(robot) # Correctly raises AttributeError, as RobotWorker is not Eatable
```

Why this adheres to ISP:

No forced implementations: RobotWorker only implements Workable, and it's not forced to deal with eat() or sleep(). HumanWorker doesn't implement manage(), which is appropriate.
Specific contracts: Each interface defines a very specific contract.
Reduced coupling: Changes to one interface (e.g., adding a new method to Manageable) only affect classes that implement Manageable, not every class that was once under the umbrella of a "fat" Worker interface.
Clearer responsibilities: It's immediately clear what capabilities each class has by looking at the interfaces it implements.
Benefits of ISP:
Reduced Coupling: Classes are only coupled to the specific behaviors they need, not a broad range of unrelated behaviors.
Increased Flexibility: Easier to add new functionalities or types without affecting existing code.
Easier Maintenance: Smaller interfaces are easier to understand, implement, and maintain.
Improved Testability: You can test specific capabilities of a class in isolation without worrying about unrelated methods.
By applying the Interface Segregation Principle, you design systems with higher cohesion and lower coupling, leading to more robust and adaptable software.

* 0:50:23    -  Dependency Inversion Principle.
* 1:02:54    -  Summary.