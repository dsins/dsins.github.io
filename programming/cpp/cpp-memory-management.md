# C++ Memory Management: Dynamic Allocation, Smart Pointers & Common Pitfalls

---

## Stack vs Heap - Know the Difference First

| | Stack | Heap |
|---|---|---|
| Allocation | Automatic (function scope) | Manual (`new`) or RAII |
| Deallocation | Automatic on scope exit | Manual (`delete`) or smart pointer destructor |
| Size | Limited (~1–8 MB typical) | Limited only by available RAM |
| Speed | Fast | Slower (allocation overhead) |
| Risk | Stack overflow (deep recursion) | Memory leaks, dangling pointers, double-free |

```cpp
void example() {
    int x = 10;          // stack - freed automatically when function returns
    int* p = new int(10); // heap - YOU are responsible for freeing this
    delete p;
}
```

---

## Dynamic Allocation: new / delete

### Single object

```cpp
int* p = new int(42);   // allocate and initialize
std::cout << *p;        // dereference: prints 42
delete p;               // free memory
p = nullptr;            // prevent dangling pointer - always nullify after delete
```

### Array

```cpp
int* arr = new int[10]{};  // allocate zero-initialized array of 10 ints
arr[0] = 1;
arr[9] = 9;
delete[] arr;              // MUST use delete[] for arrays, not delete
arr = nullptr;
```

> **`delete` vs `delete[]`:** Using `delete` on an array is undefined behavior - it may only free the first element and corrupt the heap. Always match: `new` → `delete`, `new[]` → `delete[]`.

### Common raw pointer mistakes

```cpp
// 1. Memory leak - allocated but never freed
void leak() {
    int* p = new int(5);
    return; // p lost, memory never freed
}

// 2. Dangling pointer - using memory after free
int* p = new int(5);
delete p;
std::cout << *p; // undefined behavior - p points to freed memory

// 3. Double free - deleting the same pointer twice
int* p = new int(5);
delete p;
delete p; // crash or heap corruption

// 4. Missing delete[] for arrays
int* arr = new int[10];
delete arr;  // wrong - use delete[] arr
```

---

## Smart Pointers (C++11+) - Prefer These Over Raw new/delete

Smart pointers wrap raw pointers and automatically call `delete` when they go out of scope. Include `<memory>`.

### unique_ptr - Exclusive ownership

One owner at a time. Cannot be copied, only moved. Memory freed when the `unique_ptr` goes out of scope.

```cpp
#include <memory>

// Preferred: use make_unique (C++14) - exception-safe, no raw new
auto p = std::make_unique<int>(42);
std::cout << *p;  // 42
// No delete needed - freed automatically when p goes out of scope

// Transferring ownership
auto p2 = std::move(p);  // p is now null, p2 owns the memory
if (!p) std::cout << "p is empty\n";
std::cout << *p2;  // 42

// With a class
struct Connection {
    Connection(const std::string& host) { /* connect */ }
    ~Connection() { /* disconnect */ }
};

auto conn = std::make_unique<Connection>("localhost");
// conn automatically closes and frees when it goes out of scope
```

### shared_ptr - Shared ownership with reference counting

Multiple owners. Memory freed only when the last `shared_ptr` to the object is destroyed (ref count reaches 0).

```cpp
auto p1 = std::make_shared<int>(100);
{
    auto p2 = p1;               // ref count = 2
    auto p3 = p1;               // ref count = 3
    std::cout << p1.use_count(); // 3
}
// p2, p3 destroyed - ref count drops to 1
std::cout << p1.use_count();    // 1
// p1 goes out of scope - ref count = 0 - memory freed
```

**Cyclic reference - the classic shared_ptr leak:**

```cpp
struct Node {
    std::shared_ptr<Node> next;  // problem: two nodes pointing to each other
};

auto a = std::make_shared<Node>();
auto b = std::make_shared<Node>();
a->next = b;  // a holds b
b->next = a;  // b holds a - circular reference
// Both ref counts stay at 1 forever - neither is ever freed = memory leak
```

Fix: break the cycle with `weak_ptr`.

### weak_ptr - Non-owning observer

Observes a `shared_ptr` without incrementing the ref count. Must be locked (converted to `shared_ptr`) before use - the lock fails if the object has already been freed.

```cpp
auto sp = std::make_shared<int>(99);
std::weak_ptr<int> wp = sp;

std::cout << sp.use_count();   // 1 - weak_ptr doesn't increment

if (auto locked = wp.lock()) { // lock() returns shared_ptr or empty if expired
    std::cout << *locked;      // 99 - safe to use
} else {
    std::cout << "object expired\n";
}

sp.reset();  // free the object
if (auto locked = wp.lock()) {
    // won't enter here
} else {
    std::cout << "object expired\n";  // this prints
}
```

**Fixing the cyclic reference with weak_ptr:**

```cpp
struct Node {
    std::shared_ptr<Node> next;  // strong ref forward
    std::weak_ptr<Node> prev;    // weak ref back - breaks the cycle
};
```

---

## Smart Pointer Comparison

| | `unique_ptr` | `shared_ptr` | `weak_ptr` |
|---|---|---|---|
| Ownership | Exclusive | Shared | None (observer) |
| Copyable | No | Yes | Yes |
| Movable | Yes | Yes | Yes |
| Ref count | No | Yes | Doesn't increment |
| Overhead | Zero | Ref count + control block | Control block only |
| Use case | Single owner, RAII resource | Shared ownership | Break cycles, optional refs |

**Rule of thumb:** Default to `unique_ptr`. Use `shared_ptr` only when ownership genuinely needs to be shared. Use `weak_ptr` to observe without owning.

---

## RAII - The Core Pattern Behind All of This

RAII (Resource Acquisition Is Initialization): acquire a resource in the constructor, release it in the destructor. Smart pointers are RAII wrappers around heap memory.

```cpp
class FileHandle {
public:
    explicit FileHandle(const std::string& path) {
        f = fopen(path.c_str(), "r");
        if (!f) throw std::runtime_error("Cannot open file");
    }
    ~FileHandle() {
        if (f) fclose(f);  // always runs - even if an exception is thrown
    }
    // Prevent copying (like unique_ptr)
    FileHandle(const FileHandle&) = delete;
    FileHandle& operator=(const FileHandle&) = delete;

    FILE* get() { return f; }
private:
    FILE* f;
};

void readFile() {
    FileHandle fh("data.txt");  // opens file
    // ... use fh.get() ...
    // file closed automatically when fh goes out of scope
}   // no explicit fclose needed, no leak even on exception
```

---

## Memory Safety Checklist

```
✓ Prefer make_unique / make_shared over raw new
✓ Never use raw delete - let smart pointers handle it
✓ Match new[] with delete[], not delete
✓ Set raw pointers to nullptr after delete
✓ Avoid shared_ptr cycles - use weak_ptr to break them
✓ Lock weak_ptr before use (check if expired)
✓ Don't move a unique_ptr and then use the original
✓ Prefer stack allocation when lifetime is scope-bound
✓ Use valgrind / AddressSanitizer to detect leaks in testing
```

---

## Notes

- `make_shared<T>()` allocates the object and the ref-count control block in a single allocation - more efficient than `shared_ptr<T>(new T())`.
- `make_unique` is available from C++14. In C++11, use `std::unique_ptr<T>(new T(...))`.
- Run with AddressSanitizer to catch leaks and invalid accesses at runtime: `g++ -fsanitize=address -g program.cpp`
- `valgrind --leak-check=full ./program` is the standard tool for leak detection on Linux.
