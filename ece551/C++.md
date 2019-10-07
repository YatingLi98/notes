##C++

###14. Transition to C++
####OOP
**Object-oriented-programming** (OOP): we think about our programs in terms of objects, which have a state in them, and we ask the objects to perform actions. We can make objects of different but related classes and store them in the same variables or arrays.
- Classes: default _access control_ of its members. Classes may have _member functions or methods_ declared inside the classes.
- Objects: instances of classes.
```c++
struct myStruct {
  int a;
  int b;
};

class myClass {
  int c;
  int d;
};

int main(void) {
  myStruct x;
  myClass y;
  return 0;
}
```

Three levels of access (visibility) of members:
- **public**: can be accessed by any piece of code.
- **private**: can only be accessed by the code within that class.
- **protected**:

**Note**: In struct, the default access is public. While in class, the default access is private.

**Encapsulation**: Classes encapsulation-combine into a single logical unit-the data and methods act on them.
**_this_**: a pointer to the object the method is inside of. For a method inside of class _type T_, _this_ is the same as declared as _ T * const this_.

**Const**: When we specify that the implicit _this_ argument is a _const_ pointer, only methods declared with _const_ can be invoked.
```c++
class Point {
private:
  int x;
  int y;
public:
  void setX(int new_x) {
    x = new_x;
  }
  int getX() const {
    return x;
  }
}

void someMethod(const Point * p) {
  int x = p->getX(); //legal
  p->setX(42); //illegal because setX is not const
}
```
**Plain Old Data**(POD):
- All POD types have direct C analogs-they are just sequences of fields in memory that you could copy around freely. POD types require no special setup or destruction.
- If you declare a function inside of a class or struct, that won't make it non-POD(unless it is virtual functions). Since the code for these functions is just placed with the rest of the code in memory and just takes the extra _this_ parameter to tell which object it currently operating on.
- _access restriction_ can make a type non-POD

**Static Members**: it is shared by all instances of the classes.
```c++
class BankAccount {
private:
  static unsigned long nextAccountNumber;
  unsigned long accountNumber;
public:
  void initAccount() {
    accountNumber = nextAccountNumber;
    nextAccountNumber++;
  }
};

unsigned long BankAccount::nextAccountNumber = 0;
```
We declare the _static unsigned long nextAccountNumber_ to be shared by all instances. However, we need this line of code in the global scope to actually create it. The _::_ is the _scope resolution_ operator, which allows us to specify a name inside of another named scope, such as a class. This statement executes **before** _main_ begins execution.

**Static Methods**: cannot access non-static members of the class because they do not have a _this_ pointer passed to them.

```c++
class BankAccount {
public:
  typedef double money_t;
private:
  class Transaction {
  public:
    money_t amount;
    timeval when;
  };
  money_t balance;
  Transaction * transactions;
};
```
Methods of an inner class do not have the direct access to non-static members ot the outer classes. Since it does not have a pointer to the outer-class instance that created them. We can let the inner class reference the outer class by having a field in the inner class that is a pointer to the outer class type and initializing it to point at the appropriate outer-class instance.

####References
**reference**: `int & x = y;` declares a reference _x_, which has type "int reference" and feferences _y_. _y_ is  conceptually to be "another name for y's box".
The differences between references and pointers:
- once a reference is initialized, it cannot be changed to refer to a different box. Thus, any tyoe that has a member whose type is a reference is not a POD type;
- references are automatically dereferenced, whereas pointers must be explicitly dereferenced;
- a reference must be initialized when it is declared;
- we cannot have _NULL_ references;
- we cannot have a reference to a reference, and we cannot have a pointer to a reference. However, we can have a reference to a pointer;
- we cannot perform "reference arithmetic".

Translation reference-based code to a pointer-based:
- Declaration: _T & x_ $\Rightarrow$ _T * const x_;
- Initialization: add a _&_ before the initializing expression. For a parameter, the initialization happens when we pass the parameter value into the function. Instead of passing a copy of the value, we pass an arrow pointing to it;
- Uses: Any other time, use the reference $\Leftrightarrow$ implicitly dereference the pointer.

**Note**:
- when one reference is initialized from another, it can leads to "cancel out": _& * x_ $\Leftrightarrow$ _x_;
- a call to a function that returns a (non-const) reference may be used as the left side of an assignment statement;
- a _const_ reference may be initialized from something that is not an lvalue: _const int & x = 3_. It is only legal if the reference is a constant;
- we can define operators that operates on two references but not on two pointers.

```c++
class Point {
private:
  int x;
public:
  int & getX() {
    return x;
  }
};
//...
myPoint.getX() = 3;

//Equivalent
class Point {
private:
  int x;
public:
  int * const getX() {
    return &x;
  }
};
//...
*(myPoint.getX()) = 3;
```
####Namespaces
In C, if a fucntion is not declared _static_, its name must be unique in the entire program. If it is _static_, it may not be used on any other compilation unit.
C++ introduves a way to create named scopes, called _namespaces_, which can be used from anywhere in the program.
```C++
namespace dataAnalysis {
  class DataPoint { ... };
  class DataSet { ... };
  DataSet combineDataSets(DataSet * array, int n);
}
```
Ways to reference a name declared inside of a namespace:
- _scope resolution operator **::**_ :EX, use the _vector_ class in the c++ standard library, which is in the _std_ namespace $\Rightarrow$ _std::vector_;
- _using_ keyword: it instructs the compiler to  ring the names from the requested namespace into the current scope.
  + If we wrote _using namespace std_, we could just write _vector_ to refer it;
  + Or, _using std::vector_;
  + open multiple namespaces into the same scope.

####Function Overloading
- Multiple functions of the same name are legal if they have different parameter types(**not return types or parameter names**) in c++, but not in c.
- The same with member functions.
- The compiler must claso be able to determine an unambiguous best choice of which overloaded function you want from the parameters you pass in.

#####Name Mangling
The c++ compiler adjusts the function names to encode the parameter type information, as well as what class and namespace the funciton resides inside of-so that each name is unique.
While c does not mangle names, c++ compiler must be explicitly informed of fucntions that were compiled with a c compiler by declaring them **extern "C"** if you mix c and c++ code. **Note**: _main_ is treated specially.
```C++
extern "C" {
  void somefunction(int x);
  int anotherFunction(const char * str);
}
```

####Operator Overloading
```c++
class Point {
private:
  int x;
  int y;
public:
  //we pass a const reference rather than the value
  //to avoid copying the entire object as we pass the parameter
  Point operator+(const Point & rhs) const {
    Point ans;
    ans.x = x + rhs.x;
    ans.y = y + rhs.y;
    return ans;
  }
};
```
For matrix:
```c++
Matrix operator+(const Matrix & rhs) {
  // ...
}

Matrix & operator+=(const Matrix & rhs) {
  //...
  return *this;
}
```
In the case of modify the object they are inside of, they return a reference to that object. Thus, _a = b +=c_ is legal.

####Other Aspects
