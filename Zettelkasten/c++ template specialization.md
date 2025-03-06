202404180856
Status: #idea
Tags: #programming 

# c++ template specialization
Template specialization means taking a template and making it more specific.
These are base templates:
```c++
// Class template
template<class T> class X { /*...*/ };         // a

// Function template with two overloads
template<class T> void f( T );                 // b
template<class T> void f( int, T, double );    // c
```

```c++
template<> void f<int>( int );
```

# References

