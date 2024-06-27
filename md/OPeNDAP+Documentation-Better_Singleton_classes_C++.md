NB: This was ripped from Google Bard and edited. jhrg 12/27/23

The Meyers Singleton pattern is a popular way to implement the singleton
design pattern in C++ using a static member variable declared within a
function. It leverages the properties of static functions and objects to
guarantee only one instance of the class is ever created.

## Overview

This pattern, developed by Scott Meyers (although G'bard didn't say
so...) uses the basic properties of static methods and static variables
within functions/methods. The singleton object initialization should
happen in the object's default constructor, which is shielded from use
by anything other than the singleton class. That's the one thing that
makes this pattern somewhat tricky as constructors should not throw
exceptions.

## Example

    class Singleton {
    private:
      // Private constructor to prevent direct instantiation
      Singleton() {}

    public:
      // Delete the copy constructor and assignment operator to prevent copying
      Singleton(const Singleton&) = delete;
      Singleton& operator=(const Singleton&) = delete;

      // Static member function that returns the singleton instance
      static Singleton& getInstance() {
        // Create a local static object the first time the function is called
        static Singleton instance;
        return instance;
      }

      // Other member functions of the Singleton class...
    };

    int main() {
      // Access the singleton instance through the getInstance() function
      Singleton& instance1 = Singleton::getInstance();
      Singleton& instance2 = Singleton::getInstance();

      // Verify that both instances are the same object
      if (&instance1 == &instance2) {
        std::cout << "Both instances refer to the same Singleton object!" << std::endl;
      }

      // Use the Singleton instance...

      return 0;
    }

## Explanation

The Singleton class has a private constructor to prevent direct
instantiation. The getInstance() function is static and declared within
the Singleton class. Inside getInstance(), a static member object
instance is declared. This object is only created the first time the
function is called. Subsequent calls to getInstance() simply return the
existing instance object. The copy constructor and assignment operator
are deleted to prevent copying the singleton object.

### Benefits of the Meyers Singleton

Thread-safe: Initialization is guaranteed to happen only once, even in multithreaded environments.
Lazy initialization: The singleton object is only created when it is first needed.
Simple and concise: The implementation is relatively easy to understand and maintain.

### Drawbacks of the Meyers Singleton

Overuse: Singletons can lead to tight coupling and reduced testability. Use them sparingly and only when truly necessary.
No explicit destruction: The singleton object will be destroyed only when the program exits. This can be problematic if resources need to be explicitly released earlier.

### Alternatives to the Meyers Singleton

Static local variables: This approach can be used within a single file to create a thread-safe singleton.
Resource acquisition is initialization (RAII): This technique can be used to manage resources associated with the singleton and ensure proper cleanup.
Dependency injection: This approach can improve testability and decouple the singleton from its dependent classes.

## Thread safety

The Meyers Singleton pattern is thread-safe in C++11 and later, even
without explicit synchronization mechanisms like mutexes or locks.

The thread safety of the Meyers Singleton pattern relies on
language-level guarantees introduced in C++11. For older C++ versions
(pre-C++11), additional synchronization mechanisms would be needed for
thread safety. Even though the initialization is thread-safe, it's
important to note that any methods of the singleton class itself still
need to be thread-safe if accessed concurrently by multiple threads.

### How it achieves thread safety

Static Local Variable:

The getInstance() function uses a static local variable to hold the
singleton instance. Static local variables are guaranteed to be
initialized only once, even in multithreaded environments. This
initialization happens in a thread-safe manner due to language-level
guarantees in C++11 and beyond.

Magic Statics:

This feature of C++11 and later ensures that static variables with block
scope (like those within functions) are initialized in a thread-safe
way. The compiler and runtime collaborate to handle potential race
conditions during initialization, ensuring that only one thread
initializes the variable at a time.

No Explicit Locking Needed:

Because of these language-level guarantees, the Meyers Singleton pattern
doesn't require any explicit locking mechanisms (like mutexes or locks)
to ensure thread safety. This makes it a relatively simple and efficient
way to implement a thread-safe singleton.