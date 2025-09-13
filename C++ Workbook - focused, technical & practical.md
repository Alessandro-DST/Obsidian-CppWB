## How to use this workbook

Work chapter-by-chapter. For each chapter: read the linked sources, implement the exercises, write unit tests, run static analyzers and sanitizers, refactor. Do **not** copy answers; iterate until code is clean, small, and testable.

---

# Chapter 1 - Project layout & code organization

**Goal:** produce small libraries with clear interfaces, minimal headers, and predictable build targets.

Reading (core): cppreference for language/library details. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))  
Reading (best practices & rules): C++ Core Guidelines (cheatsheet). ([isocpp.github.io](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines?utm_source=chatgpt.com "C++ Core Guidelines - GitHub Pages"))  
Reading (CMake layout): CMake best practices (see official CMake docs and project examples — search "CMake target-based" on your machine).

**Checklist / rules**

- One logical class/module → one header (`include/`) + one implementation file (`src/`). Public API in headers, implementation in `.cpp`. ([isocpp.github.io](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines?utm_source=chatgpt.com "C++ Core Guidelines - GitHub Pages"))
    
- Minimise includes in headers; prefer forward declarations to reduce compile-time coupling. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))
    
- Use `namespace` per module; keep internal helpers in unnamed namespace or `detail` namespace. ([isocpp.github.io](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines?utm_source=chatgpt.com "C++ Core Guidelines - GitHub Pages"))
    
- CMake: build your library as `target_link_libraries(my_lib PUBLIC|PRIVATE|INTERFACE)` and create a small test executable linking the library.
    

**Exercises**

1. Create a small library `mylib` with `include/mylib/calculator.hpp` and `src/calculator.cpp` exposing `class Calculator` (add, sub, mul, div). Provide a CMake target `mylib` and a small `app` target that uses it. No `using namespace std;` in headers.
    

---

# Chapter 2 - Modern C++ core idioms (RAII, smart pointers, move semantics)

**Goal:** eliminate raw ownership, implement RAII, use move semantics to manage resources.

Reading: Effective Modern C++ (covers move semantics, `auto`, `unique_ptr`, `shared_ptr`). ([Amazon](https://www.amazon.com/Effective-Modern-Specific-Ways-Improve/dp/1491903996?utm_source=chatgpt.com "Effective Modern C++: 42 Specific Ways to Improve Your ..."))  
Reference: cppreference memory management and move semantics. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))

**Checklist / rules**

- Prefer value semantics where possible; use `std::unique_ptr` for unique ownership. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))
    
- Implement copy/move correctly (or `= default`/`= delete`) when resource management is needed. ([Amazon](https://www.amazon.com/Effective-Modern-Specific-Ways-Improve/dp/1491903996?utm_source=chatgpt.com "Effective Modern C++: 42 Specific Ways to Improve Your ..."))
    
- Use RAII for file handles, sockets, locks (destructor releases). ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))
    

**Exercises**

1. Implement an RAII wrapper `class FileGuard` that opens a file in constructor and closes in destructor. Add move semantics and delete copy operations.
    
2. Create a class `ResourcePool` that stores expensive objects in `std::vector<std::unique_ptr<T>>` and supports moving items between pools without copying the underlying T.
    

---

# Chapter 3 - Types, interfaces, and header hygiene

**Goal:** design stable interfaces, reduce compile-time dependencies.

Reading: C++ Core Guidelines sections on interfaces and headers. ([isocpp.github.io](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines?utm_source=chatgpt.com "C++ Core Guidelines - GitHub Pages"))  
Reference: cppreference for forward declarations and include rules. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))

**Checklist / rules**

- Export only what clients need. Keep headers minimal. Use Pimpl only when you must hide heavy dependencies. ([isocpp.github.io](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines?utm_source=chatgpt.com "C++ Core Guidelines - GitHub Pages"))
    
- Use `constexpr`, `noexcept`, and `[[nodiscard]]` where appropriate to document intent. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))
    

**Exercises**

1. Refactor `Calculator` so the header exposes only pure public API types and uses forward declarations for internal helper classes. Add `[[nodiscard]]` to functions returning computed results.
    

---

# Chapter 4 - Templates, generic programming, and SFINAE → Concepts

**Goal:** write robust generic code, migrate classic SFINAE to concepts when useful.

Reading: cppreference templates & concepts. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))  
Reading: clang-tidy modernize checks (automatic transformations). ([Clang](https://clang.llvm.org/extra/clang-tidy/checks/list.html?utm_source=chatgpt.com "Clang-Tidy Checks"))

**Checklist / rules**

- Prefer templates for generic algorithms; constrain them (C++20 `requires` / concepts) to improve diagnostics. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))
    

**Exercises**

1. Implement a templated `sum_range` that accepts any range-like container. Add a concept (or `requires`) that constrains the template to sequences of arithmetic types. Add unit tests covering vectors and arrays.
    

---

# Chapter 5 - Design & architecture (modularity, dependency injection, patterns)

**Goal:** structure code for testability and maintainability; separate concerns.

Reading: GoF Design Patterns for architecture ideas (apply selectively). ([KDAB](https://www.kdab.com/clang-tidy-part-1-modernize-source-code-using-c11c14/?utm_source=chatgpt.com "Clang-Tidy, part 1: Modernize your source code using C ..."))  
Reading: C++ Core Guidelines design recommendations. ([isocpp.github.io](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines?utm_source=chatgpt.com "C++ Core Guidelines - GitHub Pages"))

**Checklist / rules**

- Keep functions small (< 50 lines). Push complexity into well-tested classes.
    
- Favor dependency injection (constructor injection) to enable mocking in tests.
    
- Avoid global state. Prefer pass-by-const-ref for large objects.
    

**Exercises**

1. Implement a `DataFetcher` interface and two implementations (`FileFetcher`, `HttpFetcher` stub). Write code that depends only on the `DataFetcher` interface and inject implementations at runtime.
    
2. Apply a strategy pattern to select parsing strategy for different file formats.
    

---

# Chapter 6 - Testing strategy (unit, integration, property-based)

**Goal:** write fast deterministic unit tests, integrate with CI, know when to mock.

Reading: GoogleTest user guide. ([Google GitHub](https://google.github.io/googletest/?utm_source=chatgpt.com "GoogleTest User's Guide"))  
Reading: Catch2 guides (lightweight alternative). ([DEV Community](https://dev.to/batunpc/c-unit-testing-with-catch2-20af?utm_source=chatgpt.com "C++ unit testing with Catch2 🧪👨‍"))

**Checklist / rules**

- Unit tests: test public API behaviour; small, deterministic, isolated. Use fixtures for shared setup. ([Google GitHub](https://google.github.io/googletest/?utm_source=chatgpt.com "GoogleTest User's Guide"))
    
- Integration tests: slow, exercise real subsystems (I/O, DB). Keep them separate.
    
- Use mocks/stubs for boundaries. GoogleMock integrates with GoogleTest. ([Google GitHub](https://google.github.io/googletest/?utm_source=chatgpt.com "GoogleTest User's Guide"))
    
- Run sanitizers and static analysis in CI for UB and leaks. ([GitHub](https://github.com/google/sanitizers?utm_source=chatgpt.com "google/sanitizers: AddressSanitizer, ThreadSanitizer, ..."))
    

**Testing exercises**

1. Add unit tests for `Calculator` using GoogleTest. Cover normal cases and edge cases (division by zero — expect exception). ([Google GitHub](https://google.github.io/googletest/?utm_source=chatgpt.com "GoogleTest User's Guide"))
    
2. For `FileGuard` implement tests that simulate file-not-found (open failure). Use fixtures to create temp files.
    
3. Write integration test that uses `ResourcePool` and performs operations across modules.
    

---

# Chapter 7 - Tooling: linters, sanitizers, formatters, and CI

**Goal:** catch bugs early and enforce style automatically.

Reading: clang-tidy checks and modernize suggestions. ([Clang](https://clang.llvm.org/extra/clang-tidy/checks/list.html?utm_source=chatgpt.com "Clang-Tidy Checks"))  
Reading: Google sanitizers (ASan/TSan/UBSan). ([GitHub](https://github.com/google/sanitizers?utm_source=chatgpt.com "google/sanitizers: AddressSanitizer, ThreadSanitizer, ..."))

**Checklist / rules**

- Run `clang-tidy` with `cppcoreguidelines-*` checks and `modernize-*` rules. ([Clang](https://clang.llvm.org/extra/clang-tidy/checks/list.html?utm_source=chatgpt.com "Clang-Tidy Checks"))
    
- Build tests with `-fsanitize=address,undefined,leak` locally; run ThreadSanitizer for concurrent code. ([GitHub](https://github.com/google/sanitizers?utm_source=chatgpt.com "google/sanitizers: AddressSanitizer, ThreadSanitizer, ..."))
    
- Use `clang-format` to enforce style in CI.
    

**Exercises**

1. Integrate `clang-tidy` in your CMake build for the `mylib` target and fix the top 10 warnings. ([Clang](https://clang.llvm.org/extra/clang-tidy/checks/list.html?utm_source=chatgpt.com "Clang-Tidy Checks"))
    
2. Build and run tests under AddressSanitizer and UBSan; fix any reported issues. ([GitHub](https://github.com/google/sanitizers?utm_source=chatgpt.com "google/sanitizers: AddressSanitizer, ThreadSanitizer, ..."))
    

---

# Chapter 8 - Performance & profiling basics

**Goal:** learn to measure before optimizing, use profiler and algorithmic improvements.

Reading: cppreference algorithms and complexity notes. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))

**Checklist**

- Measure with `perf`, `valgrind` (massif), or compiler profiling tools.
    
- Prefer algorithmic improvements over micro-optimizations. Use move semantics, reserve containers, avoid repeated allocations.
    

**Exercises**

1. Implement two versions of a function that concatenates many strings: naive `operator+` in loop vs. `std::string::reserve` + append. Measure and compare.
    

---

# Chapter 9 - Suggested schedule & resources

**12-week plan (intense)**

- Weeks 1–2: Chapter 1 & 2 (project layout, RAII, CMake). Use LearnCpp and cppreference. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))
    
- Weeks 3–4: Chapter 3 & 4 (interfaces, templates, concepts). Use C++ Core Guidelines and clang-tidy. ([isocpp.github.io](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines?utm_source=chatgpt.com "C++ Core Guidelines - GitHub Pages"))
    
- Weeks 5–6: Chapter 5 (design patterns) + refactor your project. ([KDAB](https://www.kdab.com/clang-tidy-part-1-modernize-source-code-using-c11c14/?utm_source=chatgpt.com "Clang-Tidy, part 1: Modernize your source code using C ..."))
    
- Weeks 7–8: Testing fundamentals (GoogleTest / Catch2), CI, sanitizers. ([Google GitHub](https://google.github.io/googletest/?utm_source=chatgpt.com "GoogleTest User's Guide"))
    
- Weeks 9–10: Profiling & advanced templates. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))
    
- Weeks 11–12: Do a capstone: pick a small real project (file server, simulator component). Apply all tooling and write tests.
    

Primary references collected: cppreference, C++ Core Guidelines, Effective Modern C++, C++ Primer, GoogleTest, Catch2, clang-tidy, sanitizers. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))

---

# Appendix - Quick links (copyable)

- cppreference: en.cppreference.com. ([Cppreference](https://en.cppreference.com/w/cpp.html?utm_source=chatgpt.com "C++ reference - cppreference.com"))
    
- C++ Core Guidelines: isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines. ([isocpp.github.io](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines?utm_source=chatgpt.com "C++ Core Guidelines - GitHub Pages"))
    
- Effective Modern C++: Scott Meyers (book). ([Amazon](https://www.amazon.com/Effective-Modern-Specific-Ways-Improve/dp/1491903996?utm_source=chatgpt.com "Effective Modern C++: 42 Specific Ways to Improve Your ..."))
    
- GoogleTest: google.github.io/googletest. ([Google GitHub](https://google.github.io/googletest/?utm_source=chatgpt.com "GoogleTest User's Guide"))
    
- Catch2: catchorg/Catch2 docs and tutorials. ([DEV Community](https://dev.to/batunpc/c-unit-testing-with-catch2-20af?utm_source=chatgpt.com "C++ unit testing with Catch2 🧪👨‍"))
    
- clang-tidy checks: clang.llvm.org/extra/clang-tidy/checks. ([Clang](https://clang.llvm.org/extra/clang-tidy/checks/list.html?utm_source=chatgpt.com "Clang-Tidy Checks"))
    
- Sanitizers: google/sanitizers (GitHub). ([GitHub](https://github.com/google/sanitizers?utm_source=chatgpt.com "google/sanitizers: AddressSanitizer, ThreadSanitizer, ..."))
