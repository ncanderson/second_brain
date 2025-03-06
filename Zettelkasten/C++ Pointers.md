202409091514
Status: #idea
Tags: #cpp #programming 

# C++ Pointers
> So a reference to a variable is like 2 particles that are quantum entangled, make a change to one and the other will change too. whereas a pointer is a signpost telling you where to look for the data?

> Ownership semantics are incredibly important in a language such as C++ so I recommend you get acquainted with how **modern** C++ conveys and enforces it. You can started by learning the correct usage of `unique_ptr` and `shared_ptr`.

## Pointers and Arguments
   * https://stackoverflow.com/a/10826907/5543374
   * https://stackoverflow.com/q/3310737/5543374
   
   for something we're just observing, pass by const reference. if we need the value(s), pass by value. Consider pushing the creation of the shared data objects down into lower classes, rather than up top in main()
   
   Basically the only reasonable parameters types are:
   - `shared_ptr<T>` - Modify and take ownership
   - `shared_ptr<const T>` - Don't modify, take ownership
   - `T&` - Modify, no ownership
   - `const T&` - Don't modify, no ownership
   - `T` - Don't modify, no ownership, Cheap to copy
   
# &
- https://www.reddit.com/r/cpp/comments/18186it/i_am_absolutely_confused_on_the_topic_of/
- https://levelup.gitconnected.com/passing-smart-pointers-in-c-479775632443
- https://stackoverflow.com/q/13852710/5543374