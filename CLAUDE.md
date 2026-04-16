# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

42 School C++ Module series (cpp00–cpp09). Each module covers progressively advanced C++ concepts, organized as independent exercises under `cppXX/exYY/` directories.

- **cpp00–cpp04**: Completed (git submodules pointing to `github.com/gdtknight/cppXX`, see `.gitmodules`)
- **cpp05–cpp09**: In progress. These directories have their own `.git/` but are not yet registered in `.gitmodules`
- **Standard**: Strict **C++98** compliance — no C++11+ features (`auto`, `nullptr`, `override`, range-for, lambdas, etc.)
- **Forbidden per module spec** (`cpp05-09.md`): `printf`, `alloc()`, `free()` — use C++ idioms (`std::cout`, `new`/`delete`)
- **STL**: Allowed **only in cpp08 and cpp09**. cpp00–cpp07 must avoid STL containers/algorithms
- **OCF**: Every class from cpp02 onward must implement Orthodox Canonical Form

## Build

Each exercise has its own Makefile in `cppXX/exYY/`. There is no top-level build system.

```bash
# Build a single exercise
cd cpp04/ex03 && make

# Rebuild
make re

# Clean
make clean    # remove object files
make fclean   # remove objects + binary
```

**Compiler and flags** (consistent across all exercises):
```
CXX = c++
CXXFLAGS = -Wall -Wextra -Werror -std=c++98
```

Some Makefiles include a `dev` target that adds `-g -DDEBUG`.

**Makefile shape** (cpp05+ template): explicit `NAME`, `SRCS`, `HEADERS` variables; targets `all`, `clean`, `fclean`, `re`, `dev`; sources listed by name (no wildcards).

## Code Conventions

- **42 header**: Every `.cpp` and `.hpp` file starts with the 42 header block (author: `yoshin@student.42gyeongsan.kr`)
- **File naming**: `ClassName.hpp` / `ClassName.cpp` in UpperCamelCase, one class per file pair
- **Private members**: underscore prefix (`_name`, `_hitPoints`)
- **Orthodox Canonical Form** (OCF): default ctor, copy ctor, copy assignment, destructor
- **Void parameters**: `ClassName(void)` style for no-argument functions
- **Return in parens**: `return (value);`
- **Comments**: Korean, Doxygen-like style with function purpose/params/return documented
- **Const-correctness**: Getters are `const`, parameters passed as `const&`

## Module Progression

| Module | Topics |
|--------|--------|
| cpp00 | Namespaces, classes, member functions, stdio streams, initialization lists, static, const |
| cpp01 | Memory allocation, pointers to members, references, file streams |
| cpp02 | Ad-hoc polymorphism, operator overloading, fixed-point numbers |
| cpp03 | Inheritance, diamond problem, virtual inheritance |
| cpp04 | Subtype polymorphism, abstract classes, interfaces |
| cpp05–09 | Exceptions, casts, templates, containers, iterators (upcoming) |

## Architecture Notes

- Each exercise is fully self-contained with its own `main.cpp`, classes, and Makefile
- No shared libraries or cross-exercise dependencies
- `cpp05-09.md` at the repo root is the authoritative spec (Korean) for upcoming modules — per-exercise requirements, forbidden items, and class structure expectations. Consult it before implementing any cpp05–cpp09 exercise
- `diamond_virtual_compare_with_sizeof.cpp` at root is a standalone educational file comparing virtual vs non-virtual inheritance sizing (compile with `-DNO_VIRTUAL` or `-DWITH_VIRTUAL`, e.g. `c++ -std=c++98 -DWITH_VIRTUAL diamond_virtual_compare_with_sizeof.cpp`)
