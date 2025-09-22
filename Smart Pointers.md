The defining characteristic of a smart pointer is that it manages a dynamically allocated resource provided by the user of the smart pointer, and ensures the dynamically allocated object is properly cleaned up at the appropriate time (usually when the smart pointer goes out of scope).

Because of this, smart pointers should never be dynamically allocated themselves (otherwise, there is the risk that the smart pointer may not be properly deallocated, which means the object it owns would not be deallocated, causing a memory leak). By always allocating smart pointers on the stack (as local variables or composition members of a class), we’re guaranteed that the smart pointer will properly go out of scope when the function or object it is contained within ends, ensuring the object the smart pointer owns is properly deallocated.

C++11 standard library ships with 4 smart pointer classes: `std::auto_ptr` (removed in C++17), `std::unique_ptr`, `std::shared_ptr`, and `std::weak_ptr`.

## std::unique_ptr
`std::unique_ptr` is the C++11 replacement for `std::auto_ptr`. It should be used to manage any dynamically allocated object that is not shared by multiple objects. That is, `std::unique_ptr` should completely own the object it manages, not share that ownership with other classes. `std::unique_ptr` lives in the `<memory>` header.

Because `std::unique_ptr` is designed with move semantics in mind, copy initialization and copy assignment are disabled. If you want to transfer the contents managed by `std::unique_ptr`, you must use move semantics.

## std::shared_ptr
Unlike `std::unique_ptr`, which is designed to singly own and manage a resource, `std::shared_ptr` is meant to solve the case where you need multiple smart pointers co-owning a resource.

This means that it is fine to have multiple `std::shared_ptr` pointing to the same resource. Internally, `std::shared_ptr` keeps track of how many `std::shared_ptr` are sharing the resource. As long as at least one `std::shared_ptr` is pointing to the resource, the resource will not be deallocated, even if individual `std::shared_ptr` are destroyed. As soon as the last `std::shared_ptr` managing the resource goes out of scope (or is reassigned to point at something else), the resource will be deallocated.

Like `std::unique_ptr`, `std::shared_ptr` lives in the `<memory> `header.

Always make a copy of an existing `std::shared_ptr` if you need more than one `std::shared_ptr` pointing to the same resource.

## Shared pointers can be created from unique pointers
A `std::unique_ptr` can be converted into a `std::shared_ptr` via a special `std::shared_ptr` constructor that accepts a `std::unique_ptr` r-value. The contents of the `std::unique_ptr` will be moved to the `std::shared_ptr`.

However, `std::shared_ptr` can not be safely converted to a `std::unique_ptr`. This means that if you’re creating a function that is going to return a smart pointer, you’re better off returning a `std::unique_ptr` and assigning it to a `std::shared_ptr` if and when that’s appropriate.

## Circular References
A **Circular reference** (also called a **cyclical reference** or a **cycle**) is a series of references where each object references the next, and the last object references back to the first, causing a referential loop. The references do not need to be actual C++ references -- they can be pointers, unique IDs, or any other means of identifying specific objects.

With three pointers, you’d get the same thing when A points at B, B points at C, and C points at A. The practical effect of having shared pointers form a cycle is that each object ends up keeping the next object alive -- with the last object keeping the first object alive. Thus, no objects in the series can be deallocated because they all think some other object still needs it!

## std::weak_ptr
`std::weak_ptr` was designed to solve the “cyclical ownership” problem described above. A `std::weak_ptr` is an observer -- it can observe and access the same object as a `std::shared_ptr` (or other `std::weak_ptrs`) but it is not considered an owner. Remember, when a `std::shared_ptr` goes out of scope, it only considers whether other `std::shared_ptr` are co-owning the object. The `std::weak_ptr` does not count!

One downside of `std::weak_ptr` is that `std::weak_ptr` are not directly usable (they have no operator->). To use a `std::weak_ptr`, you must first convert it into a `std::shared_ptr`. Then you can use the `std::shared_ptr`. To convert a `std::weak_ptr` into a `std::shared_ptr`, you can use the lock() member function.

Because `std::weak_ptr` won’t keep an owned resource alive, it’s possible for a `std::weak_ptr` to be left pointing to a resource that has been deallocated by a `std::shared_ptr`. The easiest way to test whether a `std::weak_ptr` is valid is to use the `expired()` member function, which returns `true` if the `std::weak_ptr` is pointing to an invalid object, and `false` otherwise.