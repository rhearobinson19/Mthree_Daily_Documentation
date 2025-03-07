# SRE TRAINING (DAY 19) - KUBERNETES & OOPS

## Kubernetes Project Summary
Today we went over the Kubernetes Project executed yesterday and understood the concepts and project structure. This project is a containerized Python application deployed using Kubernetes. It includes application source code, Docker configurations, Kubernetes deployment manifests, and supporting documentation. Additionally, we reviewed object-oriented programming (OOP) concepts to enhance our understanding of software design principles used in the project.

## Project Structure
- **`app/`**: 
  - Contains the main application (`app.py`), which serves as the core functionality.
  - `Dockerfile` is used to build the application image for containerization.
  - `requirements.txt` lists the dependencies required for the application to run.
- **`k8s/`**:
  - `base/deployment.yaml`: Defines the Kubernetes Deployment for running the application as pods.
  - `base/namespace.yaml`: Specifies the namespace where the application is deployed.
  - Additional configuration files for managing services and resources.
- **`config/`**:
  - Stores sample configuration files that may be used for setting up the application.
- **`data/`**:
  - Contains data files such as `hello.txt` and `info.txt`, which may be used by the application.
- **`docs/`**:
  - `README.md`: Provides instructions on setting up and using the application.
  - `kubernetes-cheatsheet.md`: Contains useful Kubernetes commands and references for managing the deployment.

## Key Technologies
- **Python**: The core programming language used to develop the application.
- **Docker**: Used to create a portable container image for deployment.
- **Kubernetes**: Manages containerized applications through deployment configurations.
- **YAML Configuration Files**: Define the Kubernetes deployment, services, and other necessary settings.
  
<hr style="border: 4px solid black;">

## Object-Oriented Programming (OOP) in Python

We also went over Object-Oriented Programming (OOP) in Python which is a programming paradigm based on the concept of objects. It allows developers to structure code in a way that models real-world entities, making it modular, reusable, and scalable. Below i have documented how we explored key OOP concepts using Python, with example code and explanations.



## 1. Classes and Objects
### Explanation:
A **class** is a blueprint for creating objects. Each object is an instance of a class and has attributes (data) and methods (functions).

### Code Example:
```python
class Dog:
    """A simple class representing a dog."""
    species = "Canis familiaris"  # Class attribute shared by all instances
    
    def __init__(self, name, age):
        self.name = name  # Instance attribute
        self.age = age

    def bark(self):
        return f"{self.name} says Woof!"

# Creating objects
fido = Dog("Fido", 3)
bella = Dog("Bella", 5)
print(fido.bark())  # Output: Fido says Woof!
```


---

## 2. Inheritance
### Explanation:
**Inheritance** allows one class (child) to inherit attributes and methods from another class (parent). This promotes code reuse and hierarchy.

### Code Example:
```python
class Pet:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def speak(self):
        return "Some generic pet sound"

class Cat(Pet):  # Cat inherits from Pet
    def speak(self):
        return f"{self.name} says Meow!"

whiskers = Cat("Whiskers", 4)
print(whiskers.speak())  # Output: Whiskers says Meow!
```

---

## 3. Encapsulation
### Explanation:
**Encapsulation** restricts direct access to some object attributes, allowing controlled interaction through methods.

### Code Example:
```python
class BankAccount:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self.__balance = balance  # Private attribute
    
    def deposit(self, amount):
        self.__balance += amount
    
    def get_balance(self):
        return self.__balance

account = BankAccount("John Doe", 1000)
account.deposit(500)
print(account.get_balance())  # Output: 1500
```

---

## 4. Polymorphism
### Explanation:
**Polymorphism** allows different classes to define the same method, with each class implementing its own behavior.

### Code Example:
```python
class Animal:
    def speak(self):
        raise NotImplementedError("Subclasses must implement this method")

class Dog(Animal):
    def speak(self):
        return "Bark"

class Cat(Animal):
    def speak(self):
        return "Meow"

def animal_sound(animal):
    return animal.speak()

print(animal_sound(Dog()))  # Output: Bark
print(animal_sound(Cat()))  # Output: Meow
```

---

## 5. Abstraction
### Explanation:
**Abstraction** hides complex implementation details and enforces method definitions in derived classes.

### Code Example:
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    
    def area(self):
        return 3.14159 * self.radius ** 2

circle = Circle(5)
print(circle.area())  # Output: 78.53975
```

---

## 6. Special (Dunder) Methods
### Explanation:
Special methods (also called "dunder" methods) allow custom behavior for built-in operations like `+`, `==`, and `len()`.

### Code Example:
```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)
    
    def __str__(self):
        return f"Vector({self.x}, {self.y})"

v1 = Vector(3, 4)
v2 = Vector(1, 2)
print(v1 + v2)  # Output: Vector(4, 6)
```





