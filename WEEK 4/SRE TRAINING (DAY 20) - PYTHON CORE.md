# SRE TRAINING (DAY 20) - Python OOP and Advanced Concepts
---

## 1. Inheritance, Polymorphism, Method Overriding, and Encapsulation

### Code Example:
```python
from abc import ABC, abstractmethod

class Animal(ABC):  # Abstract class
    def __init__(self, name):
        self._name = name  # Encapsulation (protected attribute)
    
    @abstractmethod
    def speak(self):
        pass  # Enforcing method implementation
    
    def get_name(self):
        return self._name
    
    def set_name(self, new_name):
        self._name = new_name

class Dog(Animal):
    def speak(self, age=0):  # Method Overloading
        return f"Dog {self._name} is barking and {age} years old"
    
    def __str__(self):
        return f"Dog {self._name}"

class Cat(Animal):
    def speak(self):
        return f"Cat {self._name} is meowing"

def main():
    dog = Dog("Buddy")
    print(dog.speak(10))
    print(dog)
    print(f"Dog name: {dog.get_name()}")
    dog.set_name("Max")
    print(f"Updated Dog name: {dog.get_name()}")

if __name__ == "__main__":
    main()
```

### Explanation:
- **Inheritance**: Allows `Dog` and `Cat` to reuse `Animal` attributes and methods.
- **Abstraction**: `Animal` enforces method implementation in subclasses.
- **Encapsulation**: `_name` is a protected variable with controlled access.
- **Method Overloading**: `speak()` in `Dog` has an optional parameter.
- **Polymorphism**: Different classes implement `speak()` in unique ways.

---

## 2. Constructor & Destructor
A constructor (`__init__`) initializes an object, while a destructor (`__del__`) is called when the object is deleted.

### Code Example:
```python
class Demo:
    def __init__(self):
        print("Constructor called")
    
    def __del__(self):
        print("Destructor called")

obj = Demo()
del obj  # Destructor will be called here
```

### Explanation:
- **Constructor (`__init__`)**: Initializes object properties when created.
- **Destructor (`__del__`)**: Cleans up resources when an object is deleted.

---

## 3. Shallow Copy vs Deep Copy
Shallow copy creates a new object but references nested objects, whereas deep copy creates independent copies.

### Code Example:
```python
import copy

class Address:
    def __init__(self, city, country):
        self.city = city
        self.country = country
    
    def __repr__(self):
        return f"Address(city={self.city}, country={self.country})"

class Person:
    def __init__(self, name, address):
        self.name = name
        self.address = address
    
    def __repr__(self):
        return f"Person(name={self.name}, address={self.address})"

address = Address("New York", "USA")
person = Person("John", address)

shallow_person = copy.copy(person)
deep_person = copy.deepcopy(person)

person.address.city = "Boston"

print("Original:", person)
print("Shallow Copy:", shallow_person)
print("Deep Copy:", deep_person)
```

### Explanation:
- **Shallow Copy**: Duplicates top-level object but shares nested objects.
- **Deep Copy**: Recursively copies all objects, making them independent.

---

## 4. Exception Handling (Try, Except, Finally)
Exception handling prevents runtime errors from crashing the program.

### Code Example:
```python
class CustomException(Exception):
    def __init__(self, message):
        self.message = message
        super().__init__(self.message)

try:
    raise CustomException("This is a custom exception")
except CustomException as e:
    print(e)
except Exception as e:
    print("General Exception:", e)
finally:
    print("Finally block executes regardless of exception")
```

### Explanation:
- **Try-Except**: Handles errors to prevent crashes.
- **Custom Exceptions**: Define user-specific error handling.
- **Finally Block**: Runs regardless of exceptions.

---

## 5. File Handling
File handling allows reading, writing, and managing files.

### Example:
```python
import os

def create_file():
    with open("test.txt", "w") as file:
        file.write("Hello, World!")

def read_file():
    with open("test.txt", "r") as file:
        print(file.read())

def append_to_file():
    with open("test.txt", "a") as file:
        file.write(" Appended text!")

def delete_file():
    if os.path.exists("test.txt"):
        os.remove("test.txt")
    else:
        print("File does not exist")

create_file()
read_file()
append_to_file()
read_file()
delete_file()
```

### Explanation:
- **Create File**: Opens a file in write mode and adds content.
- **Read File**: Reads and displays the content of the file.
- **Append File**: Adds new content without overwriting existing data.
- **Delete File**: Removes the file if it exists.



