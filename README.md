# Advanced Systems Programming 

## Table of Contents

- [About](#about)
- [Getting Started](#getting-started)
- [Reference Counting](#reference-counting)
- [Tasks](#tasks)

    - [Task 1](#task-1)
        - [Review](#review)
        - ['my_string' Class Breakdown](#my_string-class-breakdown)
        - [Output](#output)

    - [Task 2](#task-2)
        - [Review](#review-1)
        - ['my_string' Class with Reference Counter Implementation Breakdown](#my_string-class-with-reference-counter-implementation-breakdown)
        - [Output](#output-1)


    - [Task 3](#task-3)
        - [Review](#review-2)
        - ['my_string' Class with Reference Counter at 0 Breakdown](#my_string-class-with-reference-counter-print-at-0-breakdown)
        - [Output](#output-2)

    - [Task 4](#task-4)
        - [Review](#review-3)
        - ['ref_counter' Temp Class Breakdown](#ref_counter-temp-class-breakdown)
        - [Output](#output-3)




## About

This worksheet is focused on various aspects of C++ programming, including dynamic class creation, the use of pointers in C++ and basic template usage.

## Getting Started

To run any of the Tasks in this worksheet, use the following commands:

~~~ruby
$ cd Task<task number>
$ clang++ my_string.cpp main.cpp -o main; ./main
~~~

## Reference Counting
The worksheet introduces the concept of reference counting, a method for automatic memory management. Reference counting keeps a counter with each object, indicating the number of references that exist to the object. When a pointer is copied, the count is incremented, and when a pointer goes out of scope or is reset, the count is decremented. When the count reaches zero, the object is deleted, as it is no longer in use.

## Tasks

### Task 1
#### Review
-Task 1 involves implementing a simplified version of C's string class. The class is able to handle strings of any length using dynamic memory allocation, and assignment and copy operators should not make a copy of the string, allowing them to share the same backing memory.

#### 'my_string' Class Breakdown
Default Constructor
~~~ruby
my_string::my_string() : data(nullptr) {}
~~~
This line of code is used to initialise the string with a nullptr. Initializing 'data' as a nullptr serves the purpose of avoiding the pointer from having an unpredictable or random memory address if the pointer is not immediately assigned a memory address, which could cause issues such as crashes or unidentified behaviors.

This way when it is assigned an actual memory address (dynamically allocated using 'new' or by copying another string) to 'data', it can check whether it's still a nullptr to determine if it should deallocate the memory to 'data'.
________________________________________________________________________________________________

Constructor Overload
~~~ruby
my_string::my_string(const char *message) {

    data = new char[strlen(message) + 1];    
    strcpy(data, message);
}
~~~
This block of code is used to dynamically allocate memory to 'data' and copy the string 'message' into it. The length of the string is evaluated using the 'strlen' fucntion, and adding 1 to it accounts for the null terminator character at the end of the string.
________________________________________________________________________________________________

Copy Constructor
~~~ruby
my_string::my_string(const my_string &other) {

    data = other.data;
}
~~~
This block of code is used to create a new object from the existing one, and share the 'data' pointer with the source object (Shallow copy).  
________________________________________________________________________________________________
Copy Assignment Operator
~~~ruby
my_string &my_string::operator=(const my_string &s) {

    if (this != &s) {

        data = s.data;
    }
    return *this;
}
~~~
This block of code is responsible for assigning one object to another. We first check for self-assignment, and then share the 'data' pointer with 'this' object (referencing the current object being managed), which is a shallow copy.
________________________________________________________________________________________________
getChar function
~~~ruby
char my_string::getChar(const int i) const {

    if (i < 0 || i >= strlen(data)) {

        throw std::out_of_range("Index out of range");
    }
    return data[i];
}
~~~
This block of code is a function responsible for returning a character from 'data' using a desired index. It would first check if the index desired is out of range, and it would throw an exception if need be.

Output:
~~~ruby
Hello world
Hello world
Hello world
e <-use of getChar function
Hello world
Hello world
HEllo world
~~~

Output with exception:
~~~ruby
Hello world
Hello world
Hello world
terminate called after throwing an instance of 'std::out_of_range'
  what():  Index out of range
Aborted (core dumped)
~~~
________________________________________________________________________________________________
setChar function
~~~ruby
void my_string::setChar(const int i, const char &c) {

    if (i < 0 || i >= strlen(data)) {

        throw std::out_of_range("Index out of range");
    }
    data[i] = c;
}
~~~
This block of code is a function responsible for setting a character in 'data' to the value of 'c' in a desired index. It would first check if the index desired is out of range, and it would throw an exception if need be.

Output:
~~~ruby
Hello world
Hello world
Hello world
e 
Hello world
Hello world
HEllo world <-use of setChar function
~~~

Output with exception:
~~~ruby
Hello world
Hello world
Hello world
e
Hello world
Hello world
terminate called after throwing an instance of 'std::out_of_range'
  what():  Index out of range
Aborted (core dumped)
~~~
________________________________________________________________________________________________
print function
~~~ruby
void my_string::print() const {

    std::cout << data << std::endl;
}
~~~
This block of code is responsible for printing 'data'.

Use of print in main():
~~~ruby
int main() {

    my_string s("Hello world");
    s.print(); <----
    {
        my_string t = s;
        s.print(); <----
        t.print(); <----
        std::cout << s.getChar(1) << std::endl;
        s.print(); <----
        t.print(); <----
    }
    s.setChar(1, 'E');
    s.print(); <----
}
~~~
________________________________________________________________________________________________

### Output

Output:
~~~ruby
Hello world
Hello world
Hello world
e
Hello world
Hello world
HEllo world
~~~
________________________________________________________________________________________________
### Task 2
#### Review
-Task 2 extends the string implementation to support automatic reference counting. The interface for my_string remains the same, but the destructor now frees allocated memory if the reference count is 0. Output now includes the reference count.

#### 'my_string' Class with Reference Counter Implementation Breakdown

Default Constructor
~~~ruby
my_string::my_string() : data(nullptr), refcount(new int(1)) {}
~~~
This line of code is similar to the Default Constructor in Task1, with an addition to initialising a 'refcount' that is dynamically allocated memory to, as well as setting it with the value of '1'. This indicates that one reference to the object exists after its' creation. 
________________________________________________________________________________________________

Constructor Overload 
~~~ruby
my_string::my_string(const char *message) {

    data = new char[strlen(message) + 1];
    refcount = new int(1);
    strcpy(data, message);
}
~~~
This block of code is similar to the Constructor Overload in Task1, with an addition to initialising a 'refcount' that is dynamically allocated memory to, as well as setting it with the value of '1'. This indicates that one reference to the object exists after it has been created.
________________________________________________________________________________________________

Copy Constructor
~~~ruby
my_string::my_string(const my_string &other) {

    data = other.data;
    refcount = other.refcount;
    (*refcount)++;
}
~~~
This block of code is used to create a new object from the existing one, sharing the 'data' pointer with the source object (Shallow copy), and doing the same for 'refcount', as well as incrementing 'refcount' to indicate an additional reference. 
________________________________________________________________________________________________

Copy Assignment Operator
~~~ruby
my_string &my_string::operator=(const my_string &s) {

    if (this != &s) {

        if (--(*refcount) == 0) {

            delete[] data;
            delete refcount;
        }
        data = s.data;
        refcount = s.refcount;
        (*refcount)++;
    }
    return *this;
}
~~~
This block of code is responsible for assigning one object to another. We first check for self-assignment, and if not, then check if the decrement of 'refcount' is 0, which deletes 'data' and 'refcount' of the object, then shares the 'data' pointer with 'this' object (referencing the current object being managed), which is a shallow copy. Refcount is then incremented. 
________________________________________________________________________________________________

Destructor
~~~ruby
my_string::~my_string() {

    if (--(*refcount) == 0) {

        delete[] data;
        delete refcount;
    }
}
~~~
This block of code is responsible for freeing up memory when an object is destroyed. It checks if the 'refcount' of the object is 0, and if it is 0, it would delete both the 'data' and the 'refcount' of the object. This ultimately helps in preventing memory leaks by deallocating recources when they are no longer in use.
________________________________________________________________________________________________

### Output
~~~ruby
Hello world [1]
Hello world [2]
Hello world [2]
e
Hello world [2]
Hello world [2]
HEllo world [1]
~~~
________________________________________________________________________________________________

### Task 3
#### Review
-Task 3 demonstrates the case of a reference count of 0.

#### 'my_string' Class with Reference Counter print at 0 Breakdown

Destructor
~~~ruby
my_string::~my_string() {

    if (--(*refcount) == 0) {

        std::cout << "Refcount:" << "[" << *refcount << "]" << std::endl;   
        delete[] data;
        delete refcount;
    }
}
~~~
This block of code is similar to the Destructor in 'Task2'. An std::cout statement is used to print out the value of the reference counter to an object when it reaches 0.
________________________________________________________________________________________________
### Output

~~~ruby
Hello world [1]
Hello world [2]
Hello world [2]
e
Hello world [2]
Hello world [2]
HEllo world [1]
Refcount:[0]
~~~

________________________________________________________________________________________________

### Task 4
#### Review
-Task 4 focuses on creating a template class that handles reference counting. This template class will be used to rework the string class implementation and other classes that need reference counting. The output for the previous tasks will not be affected by these changes.

The 'my_string.cpp' of 'Task4' is similar to the one in 'Task1' with some additional member functions, and 'reference_counter.hpp' has the same implementation as 'my_string.cpp' with additional member functions.


Output Operator
~~~ruby
std::ostream &operator<<(std::ostream &os, const my_string &obj) {

    os << obj.data;
    return os;
}
~~~
This block of code is used to allow the printing of 'my_string' objects into a stream, and allowing for the chaining of multiple output operations together, by overloading the '<<' operator. 
________________________________________________________________________________________________

#### 'ref_counter' Temp Class Breakdown
Member Access Operator
~~~ruby
T *operator->() { return data; }
~~~
This line of code is used to allow the accessing of memebers of the managed object through the 'ref_counter' object by overloading the '->' operator.
________________________________________________________________________________________________

Dereference Operator
~~~ruby
T *operator*() { return *data; }
~~~
This line of code is used to dereference the data-type of the object being managed by overloading the '*' operator.
________________________________________________________________________________________________

### Test Cases

Task4 has been tested with a variety of data types to prove it's functionality.

~~~ruby
int main() {

    // Create a reference counter 's' holding a new 'my_string' object
    ref_counter<my_string> s(new my_string("Hello world")); //Use of Default Constructor
    ref_counter<my_string> u = ref_counter<my_string>(new my_string("Hello world")); //Use of Copy Assignment Operator

    s.print();
    {
        // Create a reference counter 't' and initialize it with 's'
        ref_counter<my_string> t = s;
        s.print();
        t.print();
        std::cout << s->getChar(1) << std::endl; 
        s.print();
        t.print();
    }
    s->setChar(1, 'E'); 
    s.print();

    u->setChar(1, 'E'); // Example use of operator overload '->' in 'ref_counter' template class to access 'setChar' fucntion from 'my_string' class
    u.print();

    // TEST CASE <int>
    ref_counter<int> int_(new int(4)); // Example use of the costructor overload
    ref_counter<int> int2(int_); // Example use of the copy constructor
    int_.print();

    // TEST CASE <double>
    ref_counter<double> double_(new double(5.1));
    ref_counter<double> double2_(double_);
    double_.print();

    // TEST CASE <bool>
    ref_counter<bool> bool_(new bool(false));
    ref_counter<bool> bool_2(bool_);
    bool_.print();
};
~~~
________________________________________________________________________________________________

### Output

~~~ruby
Hello world [1]
Hello world [2]
Hello world [2]
e
Hello world [2]
Hello world [2]
HEllo world [1]
Hello world [1]
4 [2]
5.1 [2]
0 [2]
~~~
________________________________________________________________________________________________


