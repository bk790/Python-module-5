# Module 5: Advanced Object-Oriented Programming and Exception Handling

# 1. Advanced Object-Oriented Programming (OOP)

## 1.1 Objects and Mutability

In Python, objects are generally mutable, meaning their internal state (attributes) can be modified dynamically after the object has been created in memory. When a mutable object is altered, its memory address remains exactly the same.

Built-in types like lists, dictionaries, and sets are mutable. User-defined class instances are also mutable by default. We can use the built-in `id()` function to verify an object's memory address.

```python
class Student:
    def __init__(self, name, marks):
        self.name = name
        self.marks = marks

# Creating a mutable object
s1 = Student("Alice", 95)

print(f"Original Marks: {s1.marks}")
print(f"Memory Address: {id(s1)}")

# Modifying the object's state (Mutation)
s1.marks = 88

print(f"Updated Marks: {s1.marks}")
print(f"Memory Address: {id(s1)}")  # Address remains exactly the same
```

---

## 1.2 Sameness: Identity vs. Equivalence

Understanding how Python compares objects is critical to avoiding logical bugs.

- **Equivalence (`==`)**: Checks if two variables hold the same data or values.
- **Identity (`is`)**: Checks if two variables refer to the exact same physical location in memory.

### Object Reference Diagram

```
p1
p3 = p1

Point Object
x = 1, y = 3

p2

Point Object
x = 1, y = 3
```

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        # Defines deep equality logic
        return self.x == other.x and self.y == other.y

p1 = Point(1, 3)
p2 = Point(1, 3)
p3 = p1  # p3 becomes an alias for p1

print("Equality (==):", p1 == p2)   # True
print("Sameness (is):", p1 is p2)   # False
print("Sameness (is):", p1 is p3)   # True

# Mutability effect via aliasing
p3.y = 10

print(p1.y)  # 10
```

---

## 1.3 Copying Objects to Avoid Aliasing

Because aliasing can cause unintended side effects when variables are mutated, Python provides the `copy` module to duplicate objects.

### Types of Copy

- **Shallow Copy**: Creates a new object but references the original nested objects.
- **Deep Copy**: Creates a completely independent copy of all nested objects.

```python
import copy

class Rectangle:
    def __init__(self, w, h):
        self.width = w
        self.height = h

box1 = Rectangle(10, 20)

box2 = copy.copy(box1)

print(box1 is box2)                   # False
print(box1.width == box2.width)       # True
```

---

## 1.4 Pure Functions and Modifiers

When passing objects to functions, the function can either modify the existing object or generate a new one.

### Pure Functions

* Return a brand-new object.
* Do not alter input parameters.
* No side effects.

### Modifiers

* Modify the internal state of objects passed to them.

```python
class Time:
    def __init__(self, h, m):
        self.hours = h
        self.minutes = m
    
    def __str__(self):
        return f"{self.hours:02d}:{self.minutes:02d}"

# Modifier
def add_minutes_modifier(t, mins):
    t.minutes += mins

    while t.minutes >= 60:
        t.minutes -= 60
        t.hours += 1

# Pure Function
def add_minutes_pure(t, mins):
    new_m = t.minutes + mins
    new_h = t.hours + (new_m // 60)
    new_m = new_m % 60

    return Time(new_h, new_m)

# Demonstration
if __name__ == "__main__":
    # Modifier Test
    t1 = Time(9, 30)
    print(f"Original: {t1}")
    add_minutes_modifier(t1, 45)
    print(f"Modified: {t1}")

    # Pure Function Test
    t2 = Time(9, 30)
    print(f"Original: {t2}")
    t3 = add_minutes_pure(t2, 45)
    print(f"Pure Result: {t3}")
    print(f"Original after Pure: {t2}")

```

---



## 1.5 Operator Overloading

Operator overloading allows built-in mathematical operators to work seamlessly with user-defined objects.

### Program Example: Cumulative Sum of Complex Numbers

```python
class Complex:
    def __init__(self, r, i):
        self.r = r
        self.i = i

    def __add__(self, other):
        # Overloads the + operator
        return Complex(self.r + other.r,
                       self.i + other.i)

    def __str__(self):
        return f"{self.r} + {self.i}i"

N = int(input("Enter number of complex numbers: "))

total = Complex(0, 0)

for _ in range(N):
    r = int(input("Real part: "))
    i = int(input("Imaginary part: "))

    total = total + Complex(r, i)

print("Cumulative Sum:", total)
```

---

## 1.6 Polymorphism and Duck Typing

**Polymorphism** is the ability of different classes to respond to the same method call in their own specific way. This allows a single interface to represent different underlying forms (data types).

**Duck Typing** is a specific Pythonic application of polymorphism. It follows the philosophy: *"If it walks like a duck and quacks like a duck, then it must be a duck."* In practice, this means Python does not care about the actual **type** of the object; it only cares whether the object **supports the methods or attributes** being called.

```python
class Rectangle:
    def draw(self):
        print("Drawing a Rectangle")

class Circle:
    def draw(self):
        print("Drawing a Circle")

# Polymorphic function
# It doesn't check the type; it relies on 'Duck Typing' 
# by assuming any object passed will have a .draw() method.
def render_shape(shape):
    shape.draw()

# Creating a list of different objects that share the same method name
shapes = [Rectangle(), Circle()]

for s in shapes:
    # Polymorphism: The same call 's.draw()' behaves 
    # differently depending on the specific class of 's'.
    render_shape(s)

```

---



---

# 2. Exception Handling

## 2.1 The Need for Exception Handling

Exceptions are runtime errors that disrupt the normal flow of a program.

Benefits:

- Prevent abrupt termination
- Graceful error recovery
- Safe release of resources

---

## 2.2 Structure: try, except, else, finally

```
try
  |
  +----> except (if error occurs)
  |
  +----> else (if no error)
  |
finally (always executes)
```

### Program Example: Handling Multiple Exceptions

```python
try:
    a = int(input("Enter numerator: "))
    b = int(input("Enter denominator: "))

    result = a / b

except ZeroDivisionError:
    print("Error: Division by zero is undefined.")

except ValueError:
    print("Error: Please enter integer values only.")

else:
    print(f"Result is {result}")

finally:
    print("Operation finished. Cleaning up resources.")
```

---

## 2.3 Assertion and Raising Exceptions

### Assertions

An **Assertion** is a debugging aid that tests a condition. If the condition evaluates to `True`, the program continues; if `False`, it raises an `AssertionError`. Assertions are typically used to verify internal invariants and are often disabled in production code optimization.

### Raising Exceptions

**Raising Exceptions** is the mechanism used to signal that an exceptional situation (an error) has occurred during program execution. Unlike assertions, raising exceptions is a fundamental part of flow control in production code to handle invalid data or logic states.

```python
def calculate_discount(price, discount_percent):
    # Assertion: Used for internal debugging to verify state
    assert price >= 0, "Price cannot be negative"

    # Raising Exception: Used for production-level error handling
    if discount_percent < 0 or discount_percent > 100:
        raise ValueError("Discount must be between 0 and 100")

    return price - (price * (discount_percent / 100))

# Handling Errors
try:
    # This will trigger the ValueError
    final_price = calculate_discount(1000, 150)
    print(f"Final Price: {final_price}")

except ValueError as e:
    print(f"Logic Error: {e}")

except AssertionError as e:
    print(f"System Error: {e}")

```

### Expected Output

```text
Logic Error: Discount must be between 0 and 100

```

---



# 3. Practice Questions and Examples

## Q1. Develop a program to illustrate polymorphism by defining a common interface method in two different classes.

### Solution

```python
class Dog:
    def speak(self):
        print("Dog barks")

class Cat:
    def speak(self):
        print("Cat meows")

animals = [Dog(), Cat()]

for a in animals:
    a.speak()
```

---

## Q2. Outline the difference between pure functions and modifiers. Develop a program illustrating both using a class BankAccount.

### Solution

```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance

    # Modifier
    def deposit(self, amount):
        self.balance += amount

    # Pure Function
    def get_balance(self):
        return self.balance

acc = BankAccount(1000)

acc.deposit(500)

print(acc.get_balance())
```

---

## Q3. Explain the need for exception handling in Python. Develop a program illustrating try, except, else, and finally blocks.

### Solution

```python
try:
    a = int(input("Enter a number: "))
    b = int(input("Enter another number: "))

    result = a / b

except ZeroDivisionError:
    print("Division by zero is not allowed")

except ValueError:
    print("Invalid input")

else:
    print("Result:", result)

finally:
    print("Program execution completed")
```

---

## Q4. What is operator overloading? Illustrate with an example using the addition method.

### Solution

```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Vector(
            self.x + other.x,
            self.y + other.y
        )

v1 = Vector(2, 3)
v2 = Vector(4, 5)

v3 = v1 + v2

print(v3.x, v3.y)   # Outputs 6 8
```

---

## Q5. Create a Python class Point with attributes x and y. Demonstrate sameness using the is operator and show the effect of mutability when modifying one reference.

### Solution

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p1 = Point(2, 3)

p2 = p1

print("Same object:", p1 is p2)

p2.x = 10

print("p1:", p1.x, p1.y)
print("p2:", p2.x, p2.y)
```

---

## Q6. Explain the role of the finally clause with an example.

### Solution

```python
try:
    f = open("data.txt", "r")
    data = f.read()

except FileNotFoundError:
    print("File not found")

finally:
    print("Closing resources")
```
