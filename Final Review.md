


# CS 246 Final Exam Review Notes

## C++ Basics

  * cin >> : Read from stdin (whitespace ignored by default)
  * cout << : Output to stdout
  * getline(cin,s) : Read into s until newline (ignores whitespace)

We can read/write from/into files or strings using stream abstractions:

  * ifstream >> : Read from files
  * ofstream << : Write to files
  
  - Available through `<fstream>`
  - All streams are stack allocated: When the stack is popped, the variable is destroyed, and file is closed.

  * istringstream >> : Input string stream
  * ostringstream << : Output string stream

  - Available through `<sstream>`

### Default Arguments:

In C++, functions parameters can be given default values, and are automatically set to the default value if function call doesn't give any values.

`int factorial(int n = 0) {
    if(n == 0) return 1;
    return n*factorial(n-1);
}`

Calling `factorial()` will run `factorial(0)`

Rule: Cannot follow a default argument with a parameter that does not have a default value

### L-value References:
`int y = 10;
int &z = y;`

`z` is an l-value reference to `y`, acting like a const pointer to `y`

References differ from const pointers, as they automatically dereference

`int *p = &z;` sets `p` to the address of `y`.

Must initialize all references to l-values, and cannot create a pointer to a reference, reference to a reference, or an array of references

### pass by value vs pass by pointer vs pass by reference


`void inc(int n) {
    n = n+1;
}
int x = 5;
inc(x);
cout << x << endl; //prints 5, passing by value`

`void inc(int *n) {
    *n = *n+1;
}
int x = 5;
inc(x);
cout << x << endl; //prints 6, passing by pointer`

`void inc(int &n) {
    n = n+1;
}
int x = 5;
inc(x);
cout << x << endl; //prints 6, passing by reference`

Always try passing by reference to const for types bigger than int, more efficient as data is not being copied.

### C++ dynamic memory:


  * `new` : creates new dynamic memory on heap/calls constructor, returns pointer to item on heap.
  * `delete` : deletes dynamic memory on heap for object which new was called on, calls the destructor.

For arrays:


  * `MyClass *oArray = new MyClass[i]`: creates new array of size i on heap and calls constructor on each element, returns pointer to the array on heap.

  * `delete [] oArray`: deletes array on heap for object which new was called on, calls the destructor on each element.

### Operator/Function Overloading:

C++ allows giving meaning to C++ operators for user defined types, and also allows multiple functions with the same name.

This is how we would add two vectors up before:

`struct vec{`
`    int x;`
`    int y;`
`};`
` `
`vec add(vec v1, vec v2) {...} `
` `
`vec v1{1,0};`
`vec v2{0,1};`
` `
`vec v3 = add(v1,v2);`

We can instead do: 

`vec operator+(const vec &v1, const vec &v2) {`
` `
`    vec toReturn{v1.x+v2.x,v1.y+v2.y};`
` `   
`    return toReturn;`
`}`
` `
`vec v1{1,0};`
`vec v2{0,1};`
` `
`vec v3 = v1+v2;`

We can do this for virtually every operator in C++

### Command Line Arguments:

To read in arguments from command line, `main` function has to be:

`int main(int argc, char *argv[]) {`
` `
`}`

`argc` is the number of arguments + 1, and `argv` holds the arguments, on their respective indices. Can loop through `argv`, with following loop:
`for(int i = 1; i <= argc; ++i) {`
` `    
`    ... `
` `
`} `

Notice that we start at `i=1`, and end at `i = argc`.

##Intro to OOP with C++

### The preprocessor

The preprocessor is a program that runs before the compiler, and can change the code being compiled before the compiler can see it.

* `#include <...>` is an example of a preprocessor directive
* `g++14 -E -P file.cc` will show the preprocessor output
* `#define VAR VALUE` creates preprocessor variable, searching for `VAR` in file and replacing with `VALUE`
* `g++ -D VAR=VALUE` only compiles if `VAR` equals `VALUE` in file. This is known as __conditional compilation__
* `#if`, `#elif`, `#endif` are conditionals in preprocessor. Work exactly as expected.

### Separate Compilation

`g++14 *.cc` compiles all .cc files in folder, and creates executable with those
`g++14 a.cc b.cc` compiles only `a.cc` and `b.cc` in the folder if they exist

g++ by default compiles files into binaries and attempts to link them to produce an executable. We can tell the compiler to just compile, and not link by using the command `g++14 -c a.cc`

This will produce a `a.o` file, an __object__ file. This is a compiled binary, that just needs to be linked.

Once all binaries are compiled, just do `g++14 *.o` and all binaries in folder will be linked to form the executable.

### Include Guards

Sometimes multiple files include the same header, and we can have multiple definition problems while compiling. To avoid this, we use what are called __include guards__.

`#ifndef A_H`
`#define A_H`
`//(contents of .h file here)`
`#endif`

This will define a preprocessor variable `A_H` if it's not defined already, and include contents of .h file if `A_H` isn't already defined.

### Intro to Classes

Classes and objects are some of the most important big ideas of OOP. Classes in C++ are structures that can __potentially__ have member functions (aka methods) inside them.

An __instance__ of a class is called an __object__. A method can only be called using an __object__ of the class. Inside the method bodies, we have access to the fields of the object which the method was called on.

Methods come with a hidden parameter called __this__, which is a pointer to the object it was called on.
Example:
`struct Fruit {`
`string type`
`int weight`
`int getWeight { return this->weight; }`
`};`
` `
`Fruit myApple{"Apple", 10};`
`int myWeight = myApple->getWeight //Returns 10`

### Basics of constructors

Constructors are special methods inside classes that are responsible for initializing an object when created. Some advantages of constructors include:

* Running additional code inside the method body
* Default arguments
* Overloading
* Sanity checks

They have a special syntax, have no return type and have the same function name as the class:

`struct Fruit{`
`//same code as above`
`Fruit(string fruitType = "", int fruitWeight = 0) {`
`this->type = fruitType;`
`this->weight = fruitWeight;`
`};`

Can initialize an instance of fruit in many ways:
`Fruit myFruit{"Apple", 10};`
`Fruit myFruit{"Orange"}; //weight is 0`
`Fruit myFruit; //no name, 0 weight`
`Fruit myFruit{}; //no name, 0 weight, same as above`

#### Default Constructor

Every class comes with a default constructor, a zero parameter constructor that's provided for free. This calls the default constructor on all fields that are __objects__. The default constructor goes away as soon as you write any constructor of your own.

### Object creation steps, MIL, and implicit conversion

Initializing fields that are constant or must be initialized:

`struct Fruit{`
`//same code as above`
`const string colour; //new const parameter`
`Fruit(string fruitType = "", int fruitWeight = 0, const string fruitColour) {`
`this->type = fruitType;`
`this->weight = fruitWeight;`
`this->colour = fruitColour;`
`};`

The colour of the fruit must stay constant, but it can't be default initialized as every fruit has a different colour.
The fields must already by constructed by the time the constructor body runs.

#### Steps for object creation:

1. Space is allocated on the stack or heap.
2. Default initialization of fields that are objects
3. Constructor body runs

A __Member Initialization List (MIL)__ hijacks step 2 so we can construct the constant field:

`struct Fruit{`
`//same code as above`
`const string colour; //new const parameter`
`Fruit(string fruitType, i	nt fruitWeight, const string fruitColour): type{fruitType}, weight{fruitWeight}, colour{fruitColour} {}`
`};`

MIL syntax is only available in constructors.

* Not restricted to consts or references
* Can use same name for field & parameter without needing to use "this"
* Fields are initialized in __declaration order__
* Using MIL to initialize fields is more efficient than initialization in constructor body
* If both in-class initialization & MIL are used, MIL gets precedent

#### Implicit Conversions

`string s = "Hello";`

Left-hand side is C++ style `std::string`, while right-hand side is a C style `char *` string. An implicit conversion is being made here, from `char *` to `std::string`. To disable automatic conversions, prefix the constructor with the `explicit` keyword.

### Copy constructors and copy assignment operators

#### Copy constructor

We can initialize objects as copies of others easily in C++:

`Fruit myApple{"Apple, 10, "Red};`
`Fruit myCopy{myApple}; //exact same fields/data  as myApple`
`Fruit myOtherCopy = myApple; //also copies`

This is done by the __copy constructor__, which creates a copy from an existing object. We get this for free!

Consider this linked list node class and copying it with the default constructor:

`struct Node{`
	`int data;`
    `Node *next;`
    `Node(const Node &other): data{other.data}, next{other.next}{}`
`};`

`Node *np = new Node{1, new Node{2, new Node{3, nullptr}}};`
`Node m{&np};`

The first "next" node of m is just pointing to the second node in the original, so if original is destroyed, m has a dangling pointer. This is a __shallow copy__.

We need to do a __deep copy__ in order for m to have it's own node. Usuallyneed this when we have a __heap allocated__ object in the class:

`Node(const Node &other): data{other.data}, next{other.next ? new Node{*other.next} : nullptr}`

A copy constructor is used:

1. When an object is constructed as copy of another object
2. Pass by value
3. Pass by reference

Note: the parameter of a copy constructor is __always__ passed by reference.

#### Copy assignment operator

The copy assignment operator assigns an __existing__ object to be a copy of another:

`Fruit myApple{"Apple", 10, "Red"};`
`Fruit myOrange{"Orange", 7, "Orange"}; //copy constructor used here`
`Fruit myFruit{"", 0, ""};`
`myFruit = myOrange; //copy assignment operator used here`

The copy assignment operator must always be implemented as a method:

`struct Node {`
`...`
`Node &operator=(const Node &other) {`
`data = other.data;`
`delete this->next;`
`}`
`};`