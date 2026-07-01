# 🚀 Master Object-Oriented Programming (OOP) in C++

Object-Oriented Programming (OOP) is a programming paradigm based on the concept of **"objects"**, which can contain data (in the form of fields or attributes) and code (in the form of procedures or methods).

This guide provides a comprehensive overview of OOP concepts in C++, complete with code examples, detailed explanations, and tricky interview-style questions.

---

## 🏗️ 1. Classes and Objects

* **Objects**: Entities in the real world that have state (attributes) and behavior (methods). For example, a `Car` is an object with a state (`color`, `speed`) and behavior (`accelerate()`, `brake()`).
* **Classes**: The blueprint, or template, for creating these objects. A class defines what an object of its type will consist of.

```cpp
#include <iostream>
using namespace std;

// Class definition
class Car {
public:
    string brand;
    int speed;

    void drive() {
        cout << brand << " is driving at " << speed << " km/h." << endl;
    }
};

int main() {
    // Object creation
    Car myCar; 
    myCar.brand = "Toyota";
    myCar.speed = 120;
    
    myCar.drive(); // Output: Toyota is driving at 120 km/h.
    return 0;
}
```

---

## 🔐 2. Access Specifiers

Access specifiers define how the members (attributes and methods) of a class can be accessed.

1. **`public`**: Members can be accessed from *anywhere* outside the class.
2. **`private`**: Members can only be accessed from *within* the class itself. (By default, all members in a C++ `class` are private).
3. **`protected`**: Members can be accessed within the class and by its *subclasses* (derived classes).

```cpp
class Person {
private:
    int ssn; // Cannot be accessed outside

protected:
    int familyWealth; // Accessible in this class and derived classes

public:
    string name; // Accessible everywhere
    
    void setSSN(int newSSN) {
        ssn = newSSN; // Private members can be accessed via public methods
    }
};
```

---

## 💊 3. Encapsulation

Encapsulation is the wrapping of data (variables) and member functions (methods) into a single unit (the class). It also implies **data hiding**—restricting direct access to some of an object's components, usually using the `private` access specifier, and exposing a safe `public` interface (getters and setters).

```cpp
class BankAccount {
private:
    double balance; // Hidden data

public:
    BankAccount() { balance = 0.0; }

    void deposit(double amount) {
        if (amount > 0) balance += amount;
    }

    double getBalance() {
        return balance; // Safe read-only access
    }
};
```

---

## 🛠️ 4. Constructors

A constructor is a special member function that is automatically invoked when an object is created. 
* It has the **exact same name** as the class.
* It **does not have a return type** (not even `void`).
* It is called **only once** per object creation.

### Types of Constructors

1. **Default Constructor**: Takes no arguments.
2. **Parameterized (Custom) Constructor**: Takes arguments to initialize an object with specific values.
3. **Copy Constructor**: Initializes an object using another object of the same class.

### Constructor Overloading
Having multiple constructors in a class with different parameter lists.

```cpp
class Student {
public:
    string name;
    int age;

    // 1. Default Constructor
    Student() {
        name = "Unknown";
        age = 0;
    }

    // 2. Parameterized Constructor (Overloaded)
    Student(string n, int a) {
        name = n;
        age = a;
    }

    // 3. Copy Constructor
    Student(const Student &other) {
        name = other.name;
        age = other.age;
    }
};

int main() {
    Student s1;                  // Calls Default
    Student s2("Alice", 20);     // Calls Parameterized
    Student s3 = s2;             // Calls Copy constructor
}
```

---

## 🪞 5. Shallow Copy vs. Deep Copy

To understand this, we must first understand memory:
* **Stack Memory**: Managed automatically. Variables declared locally inside functions reside here.
* **Dynamic (Heap) Memory**: Managed manually using `new` and `delete`.

**Shallow Copy**: Copies all member values as-is. If a member is a pointer, it copies the *memory address*, meaning both the original and copied object point to the same dynamically allocated memory. Modifying one affects the other!

**Deep Copy**: Copies the actual values, and explicitly allocates *new* dynamic memory for pointers, copying the data over.

```cpp
class ShallowDeep {
public:
    int* data;

    // Constructor allocates dynamic memory
    ShallowDeep(int val) {
        data = new int(val);
    }

    // Custom Deep Copy Constructor
    ShallowDeep(const ShallowDeep &source) {
        // We allocate brand NEW memory, and copy the value, not the pointer
        data = new int(*(source.data));
    }

    // Destructor to free memory
    ~ShallowDeep() {
        delete data;
    }
};

int main() {
    ShallowDeep obj1(10);
    
    // Deep copy is invoked. obj2 gets its OWN memory block holding '10'.
    ShallowDeep obj2 = obj1; 
    
    *(obj1.data) = 50; 
    
    // Output: obj1=50, obj2=10 (Because they point to different memory blocks)
    cout << "obj1: " << *(obj1.data) << ", obj2: " << *(obj2.data) << endl;
}
```

---

## 🗑️ 6. Destructors

The exact opposite of a constructor. It is invoked automatically when an object goes out of scope or is explicitly deleted. 
* Named with a tilde `~` followed by the class name.
* Used to release resources (like dynamically allocated memory using `delete`, closing files, etc.).
* No return type, no arguments.

---

## 🧬 7. Inheritance

Inheritance allows a `derived` (child) class to inherit properties and behaviors from a `base` (parent) class. It promotes **code reusability**.

### Syntax and Calling Parent Constructors
```cpp
class Animal {
public:
    int age;
    // Parent Parameterized Constructor
    Animal(int a) { age = a; cout << "Animal created." << endl; }
};

// class Derived : access_mode Base
class Dog : public Animal {
public:
    string breed;
    
    // Calling the parent's parameterized constructor in the initialization list
    Dog(int a, string b) : Animal(a) { 
        breed = b;
        cout << "Dog created." << endl;
    }
};
```

### Modes of Inheritance
* `public`: Public members of base become public in derived. Protected become protected.
* `protected`: Public and protected members of base become protected in derived.
* `private`: Public and protected members of base become private in derived.

### Types of Inheritance
1. **Single**: A -> B
2. **Multilevel**: A -> B -> C
3. **Multiple**: A, B -> C (`class C : public A, public B`)
4. **Hierarchical**: A -> B, A -> C
5. **Hybrid**: Combination of multiple and hierarchical.

---

## 🎭 8. Polymorphism

Polymorphism means "many forms". It's the ability of objects to behave differently depending on the context.

### 8.1 Compile-Time (Early Binding)
Resolved during compilation.
* **Function Overloading**: Multiple functions with the same name but different parameters.
* **Constructor Overloading**: (Already covered).
* **Operator Overloading**: Giving special meaning to operators (e.g., `+` to add two custom `Complex` number objects).

### 8.2 Run-Time (Late Binding)
Resolved during program execution using **Virtual Functions**.
* **Function Overriding**: When a derived class provides a specific implementation of a function that is already provided by its base class.

```cpp
class Base {
public:
    // Virtual function tells compiler to perform late binding
    virtual void show() {
        cout << "Base class show function" << endl;
    }
};

class Derived : public Base {
public:
    void show() override { // Overrides the base class function
        cout << "Derived class show function" << endl;
    }
};

int main() {
    Base* bPtr;
    Derived dObj;
    bPtr = &dObj;
    
    bPtr->show(); // Output: Derived class show function (Because of 'virtual')
}
```

---

## 👻 9. Abstraction

Abstraction means hiding unnecessary implementation details and showing only the essential features of the object.

### Abstract Classes & Pure Virtual Functions
An **Abstract Class** is a class that contains at least one **Pure Virtual Function**.
* Used to define an *interface* for derived classes.
* Cannot be instantiated (you cannot create objects of an abstract class).
* Any class deriving from an abstract class *must* implement the pure virtual function, otherwise, it becomes abstract too.

```cpp
class Shape { // Abstract Class
public:
    // Pure Virtual Function
    virtual void draw() = 0; 
};

class Circle : public Shape {
public:
    void draw() override {
        cout << "Drawing a Circle" << endl;
    }
};
```

---

## 🤝 10. Friend Classes and Functions

A `friend` function or class is granted special access to the `private` and `protected` members of the class in which it is declared.

```cpp
class Vault {
private:
    int secretCode;
public:
    Vault() { secretCode = 1234; }
    
    // Declaring a friend class
    friend class Hacker; 
};

class Hacker {
public:
    void hack(Vault v) {
        // Can access private member directly
        cout << "The secret code is: " << v.secretCode << endl;
    }
};
```

---

## 🌟 11. Extra Essential Concepts (Not in TXT but vital)

### `this` Pointer
A pointer accessible only within non-static member functions of a class. It points to the object for which the member function is called.
```cpp
class Test {
    int x;
public:
    void setX(int x) {
        this->x = x; // Differentiates class member 'x' from parameter 'x'
    }
};
```

### Static Members
Belong to the *class itself* rather than any specific object. Shared across all instances.
```cpp
class Counter {
public:
    static int count; // Declaration
    Counter() { count++; }
};
int Counter::count = 0; // Definition outside class
```

---

## 🧠 12. Tricky Interview Questions & Deep Dives

### Q1: Why do we need a `virtual` Destructor?
> **The Problem**: If you delete an object of a derived class through a pointer to a base class, and the base class has a *non-virtual* destructor, only the base class destructor will be called. This leads to **memory leaks** because the derived class resources are never freed.

```cpp
class Base {
public:
    Base() { cout << "Base Constructor\n"; }
    // TRICKY: If this is not virtual, Derived's destructor is skipped!
    virtual ~Base() { cout << "Base Destructor\n"; } 
};

class Derived : public Base {
public:
    Derived() { cout << "Derived Constructor\n"; }
    ~Derived() { cout << "Derived Destructor\n"; }
};

int main() {
    Base* ptr = new Derived();
    delete ptr; 
    // Output WITH virtual ~Base(): 
    // Base Constructor -> Derived Constructor -> Derived Destructor -> Base Destructor
}
```

### Q2: What is the "Diamond Problem" in Multiple Inheritance?
> **The Problem**: When Class `B` and `C` inherit from `A`, and Class `D` inherits from both `B` and `C`. Class `D` now has *two* copies of `A`'s members, leading to ambiguity.
> **The Solution**: Use **Virtual Inheritance**.

```cpp
class Animal { public: int age; };

// Virtual inheritance solves the diamond problem
class Tiger : virtual public Animal {}; 
class Lion : virtual public Animal {};

class Liger : public Tiger, public Lion {}; 
// Liger now has only ONE copy of 'age'.
```

### Q3: Can a Constructor be `virtual`?
> **Answer**: **No.** A virtual call is a mechanism to get work done given partial information (you know the interface, not the exact object type). To create an object, you need complete information. At the time a constructor is called, the virtual table (V-Table) used to resolve virtual functions is not fully constructed for the derived class yet. 

### Q4: Should you call virtual functions from inside a Constructor or Destructor?
> **Answer**: **No, avoid it.** 
> When a base class constructor executes, the object is technically still of the base type (the derived part hasn't been constructed yet). Therefore, any virtual function call resolves to the *base class* implementation, not the derived class implementation. This often leads to confusing and unexpected behavior.
