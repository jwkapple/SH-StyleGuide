# `StudioN` C++ Coding Style

> 2018-11-27 PM 01:57 <br>Create Document. <br>`C. Comments` Chapter complete.

## Introduction

### In. Preface

It's important to code by following code style instruction when proceed scaled C++ project. Because there are a potential asleep bugs and a lot of codes to manage along with being scaled up of project, but instruction makes it to ease managing and fixing project.

There are so many C++ coding convention documents. But `StudioN` also have independent C++ coding convention instruction that can be applied to every project except given another instruction. By applying `StudioN` 's instruction, we would decrease potential bugs and increase readability and uniformity.

### In. Prerequisites

`StudioN` 's C++ coding style can be applied fully when using these libraries and compilers.

#### Libraries

- [**Doxygen**](http://www.stack.nl/~dimitri/doxygen/) : Generate documentation from source code, **Doxygen** is the de facto standard tool for generating documentation from annotated C++ sources.
- [**Git**](https://git-scm.com/) : **Git** is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency. 
- [**fmt**](http://fmtlib.net/latest/index.html) : **fmt** (formerly cppformat) is an open-source formatting library. It can be used as a safe alternative to printf or as a fast alternative to C++ IOStreams.

#### Compilers

All Compilers must support `C++17` features, <br>such as `[[nodiscard]]` `[[maybe_unused]]` `fold expression` and `std::filesystem`.

* `MSVC 19.14` : https://blogs.msdn.microsoft.com/chuckw/2018/05/07/vs-2017-15-7-update/
* `Clang 7` : https://releases.llvm.org/7.0.0/tools/clang/docs/ReleaseNotes.html
* `gcc 8` : https://gcc.gnu.org/gcc-8/changes.html

If your project does not use `std::filesystem`, requisites might be loosed.

* `MSVC 19.12` : https://blogs.msdn.microsoft.com/vcblog/2017/11/15/msvc-conformance-improvements-in-visual-studio-2017-version-15-5/
* `Clang 4` : http://releases.llvm.org/4.0.1/tools/clang/ReleaseNotes.html
* `gcc 6` : https://gcc.gnu.org/gcc-6/changes.html

### In. Influenced Style guides

- Google C++ Style Guide
  https://google.github.io/styleguide/cppguide.html
- Bjarne Stroustrup's C++ Style and Technique FAQ
  http://www.stroustrup.com/bs_faq2.html
- Blink C++ Style Guide
  https://chromium.googlesource.com/chromium/src/+/master/styleguide/c++/blink-c++.md
- LLVM Coding Standards
  https://llvm.org/docs/CodingStandards.html
- C++ Core Guidelines
  https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md
- Constructors
  http://yosefk.com/c++fqa/ctors.html#fqa-10.12

## Overall

### O. C++ Standard Versions

* on `25 Nov, 2018` Use C++17 except for `pmr` and `parallel STL`.
  * I think `clang` and `gcc` does not support `parallel STL` formally, but as `<experimental/xxx>` . <br>(2018-11-25 https://en.cppreference.com/w/cpp/compiler_support)
* Avoid non-standard features which is dependent to C++ compiler.
  * If you use non-standard features to your project, do like this.
    1. Wrap non-standard features with `Macro`
    2. Construct `Macro` with C++ compiler defined preprocessor macro flag. and `#ifdef` `#else` `#endif` and `#elif`.

### O. Regard warning to error.

* `warning` would be regarded as pontential error of project.
  * warning would be occurred when type casting, not used variables, discarded return values and so on.

1. in `MSVC` with `Microsoft Visual Studio`, just do that.
2. in `clang` and `gcc`, use option `-Werror -Wall -Wextra --verbose`.

### O. Avoid using static instance (singleton).

* Be careful of using static instance type that has `Constructor` and `Desctructor`. If any other way which does not use static instance, do other way.
* If there is any variables which scope is outside of any type or any function, must be `static`. 
* When program initializes singleton, should initialize singleton classes only in program's start and program's end.
* Each `Constructor` and `Destructor` of arbitary static instance must be independent from any other static instance.
  * If could not, use `assertion` whether dependent static instance is initialized prior to.
* Static instance must be implemented using magic static singleton pattern since `C++11`.

### O. DO NOT USE `RTTI` and `C++ Exception`.

* Do not use `RTTI` (Run-Time Type Information & Inference). RTTI is C++ feature that varifies type information of arbitary type in runtime. So, RTTI is against to C++ principle, `Zero-cost overhead principle`.
  * In addition to, RTTI operator `typeid()` is not always return reference of same type instance. Even the same type, it's possible to return other instance type so easy to have potential bugs.
  * RTTI type converted string from type instance is not specified definitely in C++ specification. It's just for implementation dependent to all compilers.
  * There is other way that is able to verify type information not using RTTI, and on compile time.
* Do not use `C++ Exception` (SEH also). Present C++ Exception `try` `catch` is also against to C++ principle. But this is also controversial for 2 decades at least.
  * If any exception does not occured in `try` `catch` clause, this guarantees performance same as same statement without `try` `catch`.
  * But when throwing any exception from `try`, Stack unrolling will happened and causes huge performance down (5% decrase compared to plain statement.)
* In `Constructor` , error handling only could be handled by exception. But in our case, we must implemented error handling code prior to call `Constructor`. and `Constructor` must be succeeded or halted by assertion.
  * `Destructor` must be succeeded even error handling is handled by `try` `catch`, so have not care about that.
* Consider using `boost::outcome`, that is implementation of zero overhead exception handling. <br>https://ned14.github.io/outcome/
* Using of `STL` is exceptional to this clause. Becase `STL` fully use `try` `catch` exception handling feature, but provide many help functions and containers to project.

## Comments

### C.Commenting

> Comments are one critical part of readability and maintainability.  Everyone
> knows they should comment their code, and so should you.  When writing comments,
> write them as English prose, which means they should use proper capitalization,
> punctuation, etc.  Aim to describe what the code is trying to do and why, not
> *how* it does it at a micro level. Here are a few critical things to document:
>
> ~ LLVM Coding Standards

> **Comments are comments.  They describe the code.**
>
> Code that's being excluded from compilation is code, not comments. It will often include comments, that describe the code that isn't being compiled, for the moment. 
>
> They are two distinct concepts, and forcing the same syntax strikes me as being a mistake.
>
> ~ https://stackoverflow.com/questions/3630601/why-use-if-0-for-block-commenting-out

* Write clean and brief comments in english.
* When disables a lot of code chunk temporary, use `#ifdef false` and `#endif`. Do not use comment feature such as `/* */`.
  * Comment is only exists for describing description of code.

**Good**

``` c++
if (it->second != nullptr) 
{
    // Codes...
#ifdef false
    if (auto& additional_list = it->second->GetChildList();
        additional_list.empty() == false) 
    {
        it_list.emplace(++it);
        tree_list.emplace(&additional_list);
        it_list.emplace(additional_list.begin());
        break;
    }
#endif
}
```

~~**Bad**~~

``` c++
if (it->second) 
{
    // Codes...
    /*
    if (auto& additional_list = it->second->GetChildList(); !additional_list.empty()) {
        it_list.emplace(++it);
        tree_list.emplace(&additional_list);
        it_list.emplace(additional_list.begin());
        break;
    }	
    */
}
```

### C. Comment Formatting

* Consider using `//` rather than `/* */` syntax.
* Use `//` comment syntax for writing comments in function body.
* Use `///` comment syntax when writing `doxygen` documents.
* Use `///` comment syntax when writing comment on end of namespace scope or header file.
* Consider using `//!` for code section separation.

**Good**

``` c++
///
/// @brief 
/// Let each key value where key status is KeyInputStatus::RELEASED 
/// falling down into dead_zone and change status into KeyInputStatus::NEUTRAL 
/// along with neutral_gravity.
///
/// This methods gets delta time from Application time data, 
/// multiply it with gravity and fall it down to 0 (neutral value).
///
/// @param[in] key_info Key information to apply.
///
void ProceedGravity(opgs16::manager::_internal::BindingKeyInfo& key_info)
```

**Bad**

``` c++
/// @brief 
/// Let each key value where key status is KeyInputStatus::RELEASED 
/// falling down into dead_zone and change status into KeyInputStatus::NEUTRAL 
/// along with neutral_gravity.

or

/** 
 * @brief 
 * Let each key value where key status is KeyInputStatus::RELEASED 
 * falling down into dead_zone and change status into KeyInputStatus::NEUTRAL 
 * along with neutral_gravity.
 */
```

### C. Comments for Non-Obvious Objects

> Every non-obvious class declaration should have an accompanying comment that describes what it is for and how it should be used.
>
> ~ Class Comments from Google C++ Style Guide
>
> Classes are one fundamental part of a good object oriented design. As such, a class definition should have a comment block that explains what the class is used for and how it works. Every non-trivial class is expected to have a `doxygen` comment block.
>
> ~ Class overviews from LLVM Coding Standards

* A method or class that is non-obvious should write comment to descript that with `doxygen` style.
  * Overall description should be written in `.h` file when type or function declaration exist in `.h`.
  * in `.cc`, recommend to write techniques and tricky part.

### C. File Header

* Do not use `#pragma once` include guard. Just use `#ifndef` `#define` and `#endif`.

> Every file should contain license boilerplate. Choose the appropriate boilerplate for the license used by the project (for example, Apache 2.0, BSD, LGPL, GPL).
>
> ~ Legal Notice and Author Line from Google C++ Style Guide

* If any license being bound to project, write license statement fully or briefly at the top of file.

**Good**

``` c++
///
/// @license BSD 2-Clause License
/// Copyright (c) 2018, Jongmin Yun(Neu.), All rights reserved.
/// If you want to read full statements, read LICENSE file.
///
```

### C. Implementation Comments

* Recommend to write comments to techniques, non-obvious and tricky part of code block.
* Do alignment when comments are dirty and not aligned.

``` c++
// Divide result by two, taking into account that x
// contains the carry from the add.
for (int i = 0; i < result->size(); i++) {
  x = (x << 8) + (*result)[i];
  (*result)[i] = x >> 1;
  x &= 1;
}
```

* Avoid to comment to obvious code chunk.

``` c++
// Find the element in the vector.  <-- Bad: obvious!
auto iter = std::find(v.begin(), v.end(), element);
if (iter != v.end()) { Process(element); }
```

### C. TODO Style

* Write comment with `@TODO` and all characters must be a capital.
  * Try to using parenthesis `()` and issue number from git repository.

