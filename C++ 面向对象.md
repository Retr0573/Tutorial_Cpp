# C++ 面向对象

面向对象编程（OOP, Object-Oriented Programming）是一种编程范式，它通过类（Class）和对象（Object）来组织代码。C++ 作为一门强大的 OOP 语言，提供了封装（Encapsulation）、继承（Inheritance）、多态（Polymorphism）等特性，使得程序设计更加模块化、可扩展、可复用。

**类（class）**是一种抽象的数据类型，C++支持使用类(class)来定义自定义数据类型，它将数据和方法（也称为成员函数）组合在一起，这些方法可以访问和修改类中的数据（即某种处理数据的功能模块）。

所有简单来说：**类 = 数据 + 处理数据的方法**。故一个类通常包括**数据成员**和**成员函数**。

- **数据成员**可以是任何数据类型，包括内置数据类型（如整数或字符）和用户定义的类型（如其他类或结构体）。
- **成员函数**是与类关联的函数，它们可以访问和操作数据成员。

**对象（object）**是类的实例。通过类定义对象后，可以使用对象访问类的公有成员（数据成员和成员函数）。

# 1 类和对象基础

## 1.1 **定义类举例**

```cpp
// Dog类的完整定义
class Dog {              // class关键字开始类定义
private:                 // 访问修饰符（默认private）
    std::string name;    // 成员变量（属性）
    int age;
    
public:                  // 公有访问区域
    // 构造函数
    Dog(std::string n, int a) : name(n), age(a) {
        // 初始化列表语法：:name(n), age(a)
        // 比在构造函数体内赋值更高效
    }

    // 成员函数（方法）
    void bark() {        // 行为定义
        std::cout << "Woof! My name is " << name << std::endl;
    }

    // Getter方法（const保证不修改对象）
    int getAge() const { 
        return age; 
    }

    // Setter方法
    void setAge(int a) { 
        if(a > 0) age = a;  // 包含验证逻辑
    }
};  // 注意结尾的分号
```

**类定义要素概述**：

1. **类声明**：`class ClassName`开始，包含成员变量和成员函数
2. **访问修饰符**：限制对类内数据和函数的访问方式
3. **成员变量**：类中的一些数据
4. **构造函数**：用于初始化类的对象。当我们创建一个类的对象时，就会自动调用该类的构造函数
5. **成员函数**：操作类中数据的方法，注意，`const`成员函数承诺不修改对象，在定义`const`成员函数时，不能修改类的任何非静态成员变量

## 1.2 创建对象

## **1.2 构造函数与析构函数**

**构造函数**是类的一种特殊成员函数，用于在创建对象时初始化对象的状态。构造函数具有如下特性：

- **名称与类名相同**：构造函数的名称必须与类名完全一致。
- **无返回类型**：构造函数没有返回类型（包括`void`）。
- **自动调用**：当对象创建时，构造函数会自动调用。
- **可以重载**：一个类可以有多个构造函数，只要它们的参数列表不同。

**析构函数**是类的另一种特殊成员函数，用于在对象销毁时执行清理操作。析构函数具有如下特性：

- **名称是`~类名`**：析构函数的名称是在类名前加一个波浪号（`~`）。
- **无参数无返回值**：析构函数不接受任何参数，也没有返回类型。
- **自动调用**：当对象离开作用域或被显式删除时，析构函数会自动调用。
- **不可重载**：每个类只能有一个析构函数。

```cpp
class Book {
private:
    std::string title;
    int pages;
    
public:
    // 1. 默认构造函数
    Book() : title("Untitled"), pages(0) {}
    
    // 2. 参数化构造函数
    Book(const std::string& t, int p) : title(t), pages(p) {}
    
    // 3. 拷贝构造函数
    Book(const Book& other) : title(other.title), pages(other.pages) {}
    
    // 析构函数
    ~Book() {
        std::cout << "Book destroyed: " << title << std::endl;
    }
};
```

## 1.3 **成员函数**

**（1）成员函数的常见定义**

成员函数的定义通常分为两部分：类内声明和类外定义（分离接口和实现，提高代码的可读性）。

- 类内声明：在类中声明成员函数时，只需要指定函数的返回类型、函数名和参数列表。
    
    ```cpp
    class MyClass {
    public:
        void myFunction(int x);  // 成员函数声明
    };
    ```
    
- 类外定义：在类外定义成员函数时，需要使用作用域解析运算符 `::` 来指定函数属于哪个类。
    
    ```cpp
    void MyClass::myFunction(int x) {
        // 函数实现
        std::cout << "Value of x: " << x << std::endl;
    }
    ```
    

**（2）常量成员函数**

如果你希望成员函数不修改类的成员变量，可以将该成员函数声明为 `const`。常量成员函数不能修改类的任何成员变量。

```cpp
class MyClass {
public:
    void nonConstFunction() {
        // 可以修改成员变量
        value = 10;
    }

    void constFunction() const {
        // 不能修改成员变量
        // value = 10;  // 错误
        std::cout << "This is a const function." << std::endl;
    }
private:
    int value;
};
```

**（3）内联成员函数**

内联函数是一种优化技术，编译器会尝试将函数调用替换为函数体，以减少函数调用的开销。

可以在类内定义成员函数，使其自动成为内联函数。

```cpp
class MyClass {
public:
    void inlineFunction() {
        std::cout << "This is an inline function." << std::endl;
    }
};
```

或者，也可以在类外定义内联函数：

```cpp
class MyClass {
public:
    void inlineFunction();
};

inline void MyClass::inlineFunction() {
    std::cout << "This is an inline function." << std::endl;
}
```

**（4）静态成员函数**

静态成员函数属于类本身，而不是类的某个对象。静态成员函数不能访问类的非静态成员变量或非静态成员函数。

```cpp
#include <iostream>

class MyClass {
public:
    // 构造函数
    MyClass() {
        instanceCount++;  // 每创建一个对象，实例数量加1
    }

    // 析构函数
    ~MyClass() {
        instanceCount--;  // 每销毁一个对象，实例数量减1
    }

    // 静态成员函数：获取当前实例数量
    static int getInstanceCount() {
        return instanceCount;
    }

private:
    static int instanceCount;  // 静态成员变量：记录实例数量
};

// 初始化静态成员变量
int MyClass::instanceCount = 0;

int main() {
    std::cout << "Initial instance count: " << MyClass::getInstanceCount() << std::endl;

    { // 在 main 函数中，我们通过作用域 {} 控制对象的生命周期，从而观察实例数量的变化。
        MyClass obj1;  // 创建第一个对象
        std::cout << "Instance count after creating obj1: " << MyClass::getInstanceCount() << std::endl;

        MyClass obj2;  // 创建第二个对象
        std::cout << "Instance count after creating obj2: " << MyClass::getInstanceCount() << std::endl;
    }  // obj1 和 obj2 离开作用域，析构函数被调用

    std::cout << "Instance count after obj1 and obj2 are destroyed: " << MyClass::getInstanceCount() << std::endl;

    return 0;
}
```

## 1.4 隐式指针this

在C++类中，this 指针是一个隐式指针，指向调用成员函数的当前对象。它在类的**非静态成员函数**内部可用，并提供了访问当前对象的方法。

 **this 指针的常见用途**

- 解决成员变量与参数同名的问题：当构造函数或成员函数的参数名称与成员变量相同时，可以使用 this 指针来区分
- **在成员函数中返回当前对象**
- 在重载运算符中使用 this

# 2 封装

封装是将数据（属性）和行为（方法）捆绑在一起，并隐藏对象的内部实现细节，只暴露必要的接口供外部使用。通过封装，可以控制对类内部数据的访问，防止外部代码直接修改对象的内部状态。

## 2.1 访问修饰符

**关键点**

- 使用访问修饰符（`public`、`private`、`protected`）控制数据的可见性。
- 为了在类外访问私有数据，经常需要提供公共的接口（如 `getter` 和 `setter` 方法），从而访问和修改私有数据。

**访问规则示意图：**

| 访问位置 | private | protected | public |
| --- | --- | --- | --- |
| 类内部 | ✅ | ✅ | ✅ |
| 派生类内部 | ❌ | ✅ | ✅ |
| 类外部 | ❌ | ❌ | ✅ |

```cpp
#include <iostream>
#include <string>

class Person {
private:
    std::string name;
    int age;

public:
    // 构造函数
    Person(std::string n, int a) {
        setName(n); // 通过 setter 方法设置 name
        setAge(a);  // 通过 setter 方法设置 age
    }

    // Getter 方法：获取 name
    std::string getName() const {
        return name;
    }

    // Setter 方法：设置 name
    void setName(std::string n) {
        if (!n.empty()) { // 验证 name 是否为空
            name = n;
        } else {
            std::cout << "Error: Name cannot be empty!" << std::endl;
        }
    }

    // Getter 方法：获取 age
    int getAge() const {
        return age;
    }

    // Setter 方法：设置 age
    void setAge(int a) {
        if (a >= 0 && a <= 120) { // 验证 age 是否在合理范围内
            age = a;
        } else {
            std::cout << "Error: Age must be between 0 and 120!" << std::endl;
        }
    }

    // 显示信息
    void display() const {
        std::cout << "Name: " << name << ", Age: " << age << std::endl;
    }
};

int main() {
    // 创建一个 Person 对象
    Person person("Alice", 25);

    // 使用公共接口访问数据
    person.display(); // 输出: Name: Alice, Age: 25

    // 修改数据
    person.setName("Bob");
    person.setAge(30);
    person.display(); // 输出: Name: Bob, Age: 30

    // 尝试设置非法数据
    person.setName(""); // 输出: Error: Name cannot be empty!
    person.setAge(150); // 输出: Error: Age must be between 0 and 120!

    // 再次显示信息（数据未被修改）
    person.display(); // 输出: Name: Bob, Age: 30

    return 0;
}
```

**使用访问修饰符的原则**：

- 默认使用`private`保护数据
- 通过`public`方法提供受控访问
- `protected`用于继承体系的内部共享

## 2.2 友元

上面说到，在 C++ 中，**封装（Encapsulation）** 是面向对象编程的核心思想之一，它通过 private 和 protected 访问控制，使得类的成员变量和函数不会直接暴露给外部。然而，在某些特殊情况下，我们希望某个**外部函数**或**另一个类**能够访问某个类的私有（private）或保护（protected）成员。这时，我们可以使用 **友元（friend）** 机制。

友元可以分为：

- **友元函数（Friend Function）**
- **友元类（Friend Class）**
- **友元成员函数（Friend Member Function）**

### 2.2.1 友元函数

友元函数是一个**普通的非成员函数**，但它被声明为某个类的 friend，从而可以访问该类的私有成员。

如下代码中：

- 友元函数**不是**类的成员函数，但可以访问 private 或 protected 成员。
- 友元函数**不能**使用 this 指针，因为它不属于类对象。
- 友元函数声明时要在**类内部**使用 friend 关键字，而**定义时不带 friend**。这样就可以直接以调用普通函数（非类成员）的方式来访问某个类的私有成员。

```cpp
#include <iostream>
using namespace std;

class Box {
private:
    double width; // 私有成员变量

public:
    Box(double w) : width(w) {}

    // 声明友元函数
    friend void printWidth(Box b);
};

// 友元函数的定义
void printWidth(Box b) {
    // 访问 Box 的私有成员
    cout << "Width of Box: " << b.width << endl;
}

int main() {
    Box box(10.5);
    printWidth(box); // 友元函数可以访问 Box 的私有成员
    return 0;
}
```

### 2.2.2 友元类

如果一个类被声明为另一个类的友元类，那么该友元类可以访问该类的所有私有和保护成员。

如下代码中：

- **友元类**的所有成员函数都可以访问该类的私有成员。
- 友元关系是**单向的**，即 Printer 是 Box 的友元，但 Box 不能访问 Printer 的私有成员。
- **友元不能继承**：友元关系不会被子类继承。

```cpp
#include <iostream>
using namespace std;

class Box {
private:
    double width;

public:
    Box(double w) : width(w) {}

    // 友元类声明
    friend class Printer;
};

class Printer {
public:
    void print(Box b) {
        cout << "Box width: " << b.width << endl;  // 访问 Box 的私有成员
    }
};

int main() {
    Box box(15.2);
    Printer printer;
    printer.print(box); // 友元类可以访问 Box 的私有成员
    return 0;
}
```

### 2.2.3 友元成员函数

友元成员函数是指某个类的**某个成员函数**被声明为另一个类的 friend，它只对该成员函数赋予访问权限，而不是整个类。

如下代码中：

- 只有 Printer 类的 printWidth 成员函数能访问 Box 的私有成员，而 Printer 类的其他函数**不能**访问 Box 的私有成员。
- 需要**先声明 Box**，再声明 Printer，否则 friend void Printer::printWidth(Box& b); 无法解析。

```cpp
#include <iostream>
using namespace std;

class Box; // 先声明 Box 类

class Printer {
public:
    void printWidth(Box& b); // 声明友元成员函数
};

class Box {
private:
    double width;

public:
    Box(double w) : width(w) {}

    // 让 Printer::printWidth 成为友元
    friend void Printer::printWidth(Box& b);
};

// 友元成员函数的定义
void Printer::printWidth(Box& b) {
    cout << "Width of Box: " << b.width << endl;
}

int main() {
    Box box(8.5);
    Printer printer;
    printer.printWidth(box); // 访问 Box 的私有成员
    return 0;
}
```

### 2.2.4 **友元与运算符重载**

友元函数常用于**重载二元运算符**（如 +、-、<< 等），因为这些运算符通常需要访问类的私有成员。

如下，以友元重载 <<（输出运算符）为例，虽然重载了的`operator<<`函数是一个外部函数（全局函数，而不是类的成员函数），但是由于是`Vector2D`类的友元函数，所以可以访问类内私有成员。

```cpp
#include <iostream>
using namespace std;

class Vector2D {
private:
    double x, y;

public:
    Vector2D(double x = 0, double y = 0) : x(x), y(y) {}

    // 友元函数重载 <<
    friend ostream& operator<<(ostream& os, const Vector2D& v);
};

// 友元函数的定义
ostream& operator<<(ostream& os, const Vector2D& v) {
    os << "(" << v.x << ", " << v.y << ")";
    return os;
}

int main() {
    Vector2D v1(3.5, 7.2);
    cout << v1 << endl; // 输出 "(3.5, 7.2)"
    return 0;
}
```

# 3 继承

继承是面向对象编程 (OOP) 的核心概念之一，它允许你创建一个新的类 (派生类) 从已有的类 (基类) 继承属性和行为。派生类可以复用基类的代码，并可以扩展或修改基类的行为。

## 3.1 继承的基本语法

```cpp
class BaseClass {
  // 基类成员
};

class DerivedClass : access-specifier BaseClass {
  // 派生类成员
};
```

- `BaseClass`: 已存在的类，即基类。
- `DerivedClass`: 新创建的类，即派生类。
- `access-specifier`: 访问说明符，可以是 `public`、`protected` 或 `private`，用于控制基类成员在派生类中的访问权限。

**访问说明符的作用：**

| **访问说明符** | **基类中的 public 成员** | **基类中的 protected 成员** | **基类中的 private 成员** |
| --- | --- | --- | --- |
| **public** | 在派生类中为 public | 在派生类中为 protected | 在派生类中不可访问 |
| **protected** | 在派生类中为 protected | 在派生类中为 protected | 在派生类中不可访问 |
| **private** | 在派生类中为 private | 在派生类中为 private | 在派生类中不可访问 |

PS：如果不指定访问说明符，默认情况下为 `private` 继承。

一个简单的单继承例子：

```cpp
#include <iostream>

// 基类
class Animal {
public:
  void eat() {
    std::cout << "I can eat!" << std::endl;
  }

  void sleep() {
    std::cout << "I can sleep!" << std::endl;
  }
};

// 派生类
class Dog : public Animal {
public:
  void bark() {
    std::cout << "I can bark! Woof woof!" << std::endl;
  }
};

int main() {
  Dog dog;

  // 调用基类方法
  dog.eat();
  dog.sleep();

  // 调用派生类方法
  dog.bark();

  return 0;
}
// output:
// I can eat!
// I can sleep!
// I can bark! Woof woof!
```

- `Dog` 类继承了 `Animal` 类的 `eat()` 和 `sleep()` 方法。
- `Dog` 类添加了自己的 `bark()` 方法。

## 3.2 多继承

**多继承:** 一个派生类从多个基类继承。

```cpp
#include <iostream>

// 基类 1
class Mammal {
public:
  void feedMilk() {
    std::cout << "I can feed milk!" << std::endl;
  }
};

// 基类 2
class WingedAnimal {
public:
  void fly() {
    std::cout << "I can fly!" << std::endl;
  }
};

// 派生类
class Bat : public Mammal, public WingedAnimal {
public:
  void echoLocation() {
    std::cout << "I can use echolocation!" << std::endl;
  }
};

int main() {
  Bat bat;

  // 调用基类方法
  bat.feedMilk();
  bat.fly();

  // 调用派生类方法
  bat.echoLocation();

  return 0;
}
// output: 
// I can feed milk!
// I can fly!
// I can use echolocation!
```

- `Bat` 类继承了 `Mammal` 类的 `feedMilk()` 方法和 `WingedAnimal` 类的 `fly()` 方法。
- `Bat` 类添加了自己的 `echoLocation()` 方法。

# 4 多态

多态性（Polymorphism）是面向对象编程（OOP）的核心概念之一，它允许我们使用统一的接口来处理不同类型的对象。简单来说，多态性意味着“一个接口，多种实现”。

在 C++ 中，多态性主要通过以下两种方式实现：

- **编译时多态性 (静态多态性):** 通过函数重载和运算符重载实现。
- **运行时多态性 (动态多态性):** 通过虚函数和继承实现。

## 4.1 静态多态性

静态多态性是指在 **编译时** 就能确定调用哪个函数或操作符的多态性。它不依赖于运行时的对象类型，而是通过**函数重载**或**运算符重载**在编译时解析。

### 4.1.1 **函数重载 (Function Overloading)**

数重载允许在同一个类中定义多个同名函数，但这些函数的参数列表必须不同（参数类型、参数个数或参数顺序）。

示例代码：

```cpp
#include <iostream>
using namespace std;

class Math {
public:
    // 重载的 add 函数
    int add(int a, int b) {
        return a + b;
    }

    double add(double a, double b) {
        return a + b;
    }

    string add(const string& a, const string& b) {
        return a + b;
    }
};

int main() {
    Math math;
    cout << math.add(10, 20) << endl;           // 调用 int add(int, int)
    cout << math.add(3.14, 2.71) << endl;       // 调用 double add(double, double)
    cout << math.add("Hello, ", "World!") << endl; // 调用 string add(const string&, const string&)

    return 0;
}

// output:
// 30
// 5.85
// Hello, World!
```

- `Math` 类中定义了三个同名的 `add` 函数，分别用于处理 `int`、`double` 和 `string` 类型的数据。
- 在 `main()` 函数中调用 `add` 函数时，编译器会根据传入的参数类型自动选择调用哪个 `add` 函数。

### 4.1.2 运算符重载（Operator Overloading）

运算符重载允许我们为自定义类型（如类）定义运算符的行为。通过运算符重载，可以使自定义类型像内置类型一样使用运算符。

在 C++ 中，运算符重载是通过定义一个特殊函数来实现的。这个函数的名称是 `operator` 后跟要重载的运算符符号（如 `+`、`-`、`*` 等）。

语法：

```cpp
返回类型 operator运算符符号(参数) {
    // 实现运算符的行为
}
```

**示例代码：**

```cpp
#include <iostream>
using namespace std;

class Vector2D {
private:
    double x;
    double y;

public:
    // 构造函数
    Vector2D(double x = 0, double y = 0) : x(x), y(y) {}

    // 重载 + 运算符（向量加法）
    Vector2D operator+(const Vector2D& other) const {
        return Vector2D(x + other.x, y + other.y);
    }

    // 重载 - 运算符（向量减法）
    Vector2D operator-(const Vector2D& other) const {
        return Vector2D(x - other.x, y - other.y);
    }

    // 重载 * 运算符（点积）
    double operator*(const Vector2D& other) const {
        return x * other.x + y * other.y;
    }

    // 重载 << 运算符（用于输出）
    friend ostream& operator<<(ostream& os, const Vector2D& v) {
        os << "(" << v.x << ", " << v.y << ")";
        return os;
    }
};

int main() {
    Vector2D v1(3.0, 4.0);
    Vector2D v2(1.0, 2.0);

    // 向量加法
    Vector2D v3 = v1 + v2;
    cout << "v1 + v2 = " << v3 << endl;

    // 向量减法
    Vector2D v4 = v1 - v2;
    cout << "v1 - v2 = " << v4 << endl;

    // 向量点积
    double dotProduct = v1 * v2;
    cout << "v1 * v2 (点积) = " << dotProduct << endl;

    return 0;
}
// output:
// v1 + v2 = (4, 6)
// v1 - v2 = (2, 2)
// v1 * v2 (点积) = 11
```

- **`operator+`：重载 `+` 运算符，用于实现两个向量的加法。**
- **`operator-`：重载  运算符，用于实现两个向量的减法。**
- **`operator*`：重载  运算符，用于计算两个向量的点积（返回一个标量值）。**
- **`operator<<`：重载 `<<` 运算符，用于方便地输出向量。**

## 4.2 动态多态性与虚函数

动态多态性是指在程序运行时（而不是编译时）确定调用哪个函数的多态性。它允许我们使用 **基类指针或引用** 来调用派生类的函数，从而实现“一个接口，多种实现”。即动态多态性允许程序在运行时根据对象的实际类型来调用相应的函数。

这种机制通过 **虚函数** 和 **继承重写** 实现，是 C++ 中实现多态性的主要方式。

1. **虚函数（Virtual Function）与重写**：
    - 在基类中使用 `virtual` 关键字声明的函数。
    - 派生类可以重写（override）虚函数，提供自己的实现。
2. **基类指针或引用**：
    - 使用基类指针或引用指向派生类对象。
    - 通过基类指针或引用调用虚函数时，实际调用的是派生类的函数。

示例代码：

```cpp
#include <iostream>
using namespace std;

// 基类 Animal
class Animal {
public:
    // 虚函数
    virtual void makeSound() const {
        cout << "Animal makes a sound" << endl;
    }
};

// 派生类 Dog
class Dog : public Animal {
public:
    // 重写虚函数
    void makeSound() const override {
        cout << "Dog barks: Woof! Woof!" << endl;
    }
};

// 派生类 Cat
class Cat : public Animal {
public:
    // 重写虚函数
    void makeSound() const override {
        cout << "Cat meows: Meow! Meow!" << endl;
    }
};

int main() {
    // 基类指针指向派生类对象
    Animal* animal1 = new Dog();
    Animal* animal2 = new Cat();

    // 通过基类指针调用虚函数
    animal1->makeSound(); // 调用 Dog 的 makeSound()
    animal2->makeSound(); // 调用 Cat 的 makeSound()

    // 释放内存
    delete animal1;
    delete animal2;

    return 0;
}
// output:
// Dog barks: Woof! Woof!
// Cat meows: Meow! Meow!
```

1. **基类 `Animal`**：
    - 定义了一个虚函数 `makeSound()`，使用 `virtual` 关键字声明。
    - 虚函数允许派生类重写该函数。
2. **派生类 `Dog` 和 `Cat`**：
    - 分别重写了 `makeSound()` 函数，提供了自己的实现。
    - 使用 `override` 关键字明确表示重写基类的虚函数（C++11 引入）。
3. **`main` 函数**：
    - 创建了两个基类指针 `animal1` 和 `animal2`，分别指向 `Dog` 和 `Cat` 对象。
    - 通过基类指针调用 `makeSound()` 时，程序会根据指针实际指向的对象类型调用相应的函数。

# 5 抽象

**抽象（Abstraction）** 是面向对象编程（OOP）的四大特性之一（其他三个是封装、继承、多态）。抽象的核心思想是**隐藏不必要的实现细节**，仅向用户提供必要的接口，从而简化系统的使用和维护。

C++ 主要通过**抽象类（Abstract Class）** 和 **接口（Interface）** 来实现抽象。

- **抽象类** 是一种具有 **部分实现** 的基类，通常用于代码复用。
- **接口** 是一个 **全是纯虚函数的类**，用于定义行为约束，支持 **多重继承**。

## 5.1 抽象类

**抽象类**是 **至少包含一个纯虚函数（pure virtual function）** 的类。它不能直接实例化，但可以作为基类（Base Class），让派生类（Derived Class）提供具体实现。

**纯虚函数**是一个在基类中声明但不实现的函数，其语法如下：

```cpp
class AbstractClass {
public:
    virtual void show() = 0; // 纯虚函数
};
```

`=0` 使得该函数成为纯虚函数，强制派生类必须实现它，否则该派生类仍然为抽象类，无法实例化。

使用抽象类的代码示例：

```cpp
#include <iostream>
using namespace std;

// 抽象类
class Shape
{
public:
    virtual void draw() = 0; // 纯虚函数，派生类必须实现
    virtual ~Shape() {}      // 虚析构函数，确保子类析构时能正确调用析构函数
};

// 派生类：实现抽象类的纯虚函数
class Circle : public Shape
{
public:
    void draw() override
    {
        cout << "Drawing a Circle" << endl;
    }
};

class Rectangle : public Shape
{
public:
    void draw() override
    {
        cout << "Drawing a Rectangle" << endl;
    }
};

int main()
{
    Shape *s1 = new Circle();
    Shape *s2 = new Rectangle();

    s1->draw(); // Drawing a Circle
    s2->draw(); // Drawing a Rectangle

    delete s1;
    delete s2;

    return 0;
}
```

## 5.2 接口

C++ 没有像 Java 那样的 `interface` 关键字，但可以通过 **纯虚类（Pure Abstract Class）** 实现接口。即 **所有成员函数都是纯虚函数**，且 **不包含成员变量**。

使用接口的代码示例：

```cpp
#include <iostream>
using namespace std;

// 定义接口
class Drawable
{
public:
    virtual void draw() = 0;
    virtual ~Drawable() {} // 虚析构函数
};

class Movable
{
public:
    virtual void move(int x, int y) = 0;
    virtual ~Movable() {}
};

// 具体类实现多个接口
class Car : public Drawable, public Movable
{
public:
    void draw() override
    {
        cout << "Drawing a car." << endl;
    }
    void move(int x, int y) override
    {
        cout << "Moving car to position (" << x << ", " << y << ")." << endl;
    }
};

int main()
{
    Car myCar;
    myCar.draw();       // Drawing a car.
    myCar.move(10, 20); // Moving car to position (10, 20).
    return 0;
}
```

1. `Drawable` 和 `Movable` 仅定义纯虚函数，因此可以视为 **接口**。
2. `Car` 继承了多个接口，并实现了 `draw()` 和 `move()`。
3. C++ **支持多重继承**，允许一个类同时继承多个接口。

# **类（class）与结构体（struct）的区别**

对于这个问题，说一个最简单的答案就是几乎没有区别，两者的本质都是相通的，都是自定义的数据类型，前者是C++的产物，而后者是C++为了向下兼容C保留的。在实际使用中，可能唯一能感受出来的区别就是**类内成员默认private，而结构体默认public**。

当然，如果需要严肃的回答这个问题，可以这么回答：它们之间的区别主要在于默认访问权限、继承方式、成员函数默认为inline函数以及对象的默认初始化方式等方面。

- **默认访问权限不同**
类默认的成员访问权限是private，而结构体默认的成员访问权限是public。这意味着，在类中定义的成员函数和变量默认情况下只能被类内部访问，而在结构体中定义的成员函数和变量可以被其他程序单元访问。
- **继承方式不同**
类的默认继承方式是private继承，即派生类继承的成员在派生类中的访问权限是private。而结构体的默认继承方式是public继承，即派生类继承的成员在派生类中的访问权限是public。
- **成员函数默认为inline函数**
在类中声明的成员函数默认情况下是inline函数，而在结构体中声明的成员函数默认不是inline函数。inline函数的作用是将函数的定义直接嵌入到调用该函数的地方，可以减少函数调用的开销，提高程序的运行效率。
- **对象的默认初始化方式不同**
在C++中，使用类和结构体定义的对象都可以通过默认构造函数来进行默认初始化。但是，在结构体中定义的对象可以使用C风格的初始化方式进行默认初始化，即用大括号括起来的初始化列表，而在类中定义的对象不能使用这种方式。