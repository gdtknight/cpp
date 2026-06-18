# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

42 School C++ Module series (cpp00–cpp09). Each module covers progressively advanced C++ concepts, organized as independent exercises under `cppXX/exYY/` directories. All modules are registered as git submodules pointing to `github.com/gdtknight/cppXX`.

- **Standard**: Strict **C++98** compliance — no C++11+ features (`auto`, `nullptr`, `override`, range-for, lambdas, etc.)
- **Forbidden**: `printf`, `alloc()`, `free()` — use C++ idioms (`std::cout`, `new`/`delete`)
- **STL**: Allowed **only in cpp08 and cpp09**. cpp00–cpp07 must avoid STL containers/algorithms
- **OCF**: Every class from cpp02 onward must implement Orthodox Canonical Form

## Build

Each exercise has its own Makefile in `cppXX/exYY/`. There is no top-level build system.

```bash
cd cpp05/ex02 && make      # build
make re                    # clean rebuild
make dev                   # build with -g -DDEBUG (where supported)
make fclean                # remove objects + binary
```

**Compiler and flags** (consistent across all exercises):
```
CXX = c++
CXXFLAGS = -Wall -Wextra -Werror -std=c++98
```

**Two Makefile patterns** exist in this repo:

- **cpp05 style** (direct): Sources compile straight to binary — `$(CXX) $(CXXFLAGS) $(SRCS) -o $(NAME)`. No `.o` intermediates, no pattern rule.
- **cpp07+ style** (object-based): Uses `%.o: %.cpp $(HEADERS)` pattern rule and links from `$(OBJS)`. Required when `HEADERS` includes `.tpp` files.

## Code Conventions

- **42 header**: Every `.cpp` and `.hpp` file starts with the 42 header block (`yoshin@student.42gyeongsan.kr`)
- **File naming**: `ClassName.hpp` / `ClassName.cpp` in UpperCamelCase, one class per file pair
- **Private members**: underscore prefix (`_name`, `_grade`)
- **OCF order**: default ctor, parameterized ctor, copy ctor, copy assignment operator, destructor
- **Void parameters**: `ClassName(void)` style for no-argument functions/methods
- **Return in parens**: `return (value);`
- **Const-correctness**: Getters are `const`, parameters passed as `const &`
- **Include guards**: `# ifndef CLASSNAME_HPP` / `# define CLASSNAME_HPP` (space after `#`)

## Naming Conventions for Class Hierarchies

- **`A` prefix** — abstract base class: has implementation plus at least one pure virtual method (`AAnimal`, `AForm`)
- **`I` prefix** — pure abstract interface: only pure virtual methods and a virtual destructor (`ICharacter`, `IMateriaSource`)

## Key Patterns by Module

### cpp05 — Exceptions
Nested exception classes inside the class that throws them, all inheriting `std::exception` with `const char *what(void) const throw()`. Grade range constants (`HIGHEST_GRADE = 1`, `LOWEST_GRADE = 150`) as `static const int` members.

```cpp
class GradeTooHighException : public std::exception {
public:
    const char *what(void) const throw();
};
```

### cpp06 — Casts
Static-only utility classes with all constructors private (non-instantiable):

```cpp
class ScalarConverter {
private:
    ScalarConverter(void);
    ScalarConverter(ScalarConverter const &src);
    ScalarConverter &operator=(ScalarConverter const &rhs);
    ~ScalarConverter(void);
public:
    static void convert(std::string const &literal);
};
```

`Serializer` uses `uintptr_t` (from `<stdint.h>`) for pointer↔integer round-trips.

### cpp07 — Templates
Template implementations live in `.tpp` files, included at the bottom of the `.hpp`:

```cpp
// Array.hpp
# ifndef ARRAY_TPP
#  include "Array.tpp"
# endif
```

The `ARRAY_TPP` guard prevents double inclusion when `.tpp` is compiled independently. List `.tpp` files in `HEADERS` in the Makefile so recompilation triggers correctly.

### cpp08 — STL Containers (first allowed module)
`cpp08` starts at **ex01** (no ex00). `Span` stores values in an STL container and finds shortest/longest span. `MutantStack<T>` inherits `std::stack<T>` and exposes the underlying container's iterators via `this->c`.

### cpp09 — STL Mandatory
Each exercise must use a **different** STL container justified by the problem:
- `BitcoinExchange`: `std::map` for date-keyed price lookup
- `RPN`: `std::stack` for postfix evaluation
- `PmergeMe`: two distinct containers for Ford-Johnson merge-insert sort

## Module Progression

| Module | Core Topics | Key Classes |
|--------|-------------|-------------|
| cpp00 | Namespaces, classes, streams, static, const | `PhoneBook`, `Contact`, `Account` |
| cpp01 | Memory allocation, references, pointers to members, file streams | `Zombie`, `Harl` |
| cpp02 | Operator overloading, fixed-point arithmetic | `Fixed`, `Point` |
| cpp03 | Single/multiple inheritance, diamond problem | `ClapTrap` → `ScavTrap`/`FragTrap` → `DiamondTrap` |
| cpp04 | Virtual functions, abstract classes, interfaces | `Animal` → `Cat`/`Dog`; abstract `AAnimal`; `ICharacter`/`IMateriaSource` interfaces |
| cpp05 | Exceptions, nested exception classes | `Bureaucrat`, `Form`/`AForm`, three concrete forms, `Intern` (factory) |
| cpp06 | C++ casts (`static_cast`, `reinterpret_cast`, `dynamic_cast`) | `ScalarConverter`, `Serializer`; `Base`/`A`/`B`/`C` for cast identification |
| cpp07 | Function and class templates, `.tpp` pattern | `whatever.hpp`, `iter.hpp`, `Array<T>` |
| cpp08 | STL containers & algorithms, iterators | `Span`, `MutantStack<T>` |
| cpp09 | STL mandatory, algorithm efficiency | `BitcoinExchange`, `RPN`, `PmergeMe` |
