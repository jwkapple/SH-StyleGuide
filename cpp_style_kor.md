# StudioN C++ Coding Style

## Introduction

### In. Preface



### In. Prerequisites

이 코딩 스타일은 다음 라이브러리 혹은 관리 프레임워크를 쓸 때 완전히 적용된다.

* [**Doxygen**](http://www.stack.nl/~dimitri/doxygen/) : Generate documentation from source code, **Doxygen** is the de facto standard tool for generating documentation from annotated C++ sources.
* [**Git**](https://git-scm.com/) : **Git** is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency. 
* [**fmt**](http://fmtlib.net/latest/index.html) : **fmt** (formerly cppformat) is an open-source formatting library. It can be used as a safe alternative to printf or as a fast alternative to C++ IOStreams.

### In. Log

* 2018-05-21 Under construction.

### In. Influenced Style guides

* Google C++ Style Guide
  https://google.github.io/styleguide/cppguide.html
* Bjarne Stroustrup's C++ Style and Technique FAQ
  http://www.stroustrup.com/bs_faq2.html
* Blink C++ Style Guide
  https://chromium.googlesource.com/chromium/src/+/master/styleguide/c++/blink-c++.md
* LLVM Coding Standards
  https://llvm.org/docs/CodingStandards.html
* C++ Core Guidelines
  https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md
* Constructors
  http://yosefk.com/c++fqa/ctors.html#fqa-10.12



## Overall

### O. C++ Standard Versions

* **`2018-05-10` 기준, C++17 을 사용한다.**
* **표준에 기재된 기능 외의 각 컴파일러마다 지원하고 있는 비표준 기능은 사용하지 않는다.**
  * `module`, `_s` method 등 역시 사용할 수 없다.

### O. 경고를 에러같이 취급하라

* **컴파일 도중에 발생하는 경고는 해당 코드에 대해서 잠재적인 에러 요소를 가지고 있다는 증거이다.**
  * 값 변환을 제대로 하지 않았을 때, 등등에서 경고가 발생할 수 있다.
* **되도록 표준 컴파일러를 채용해서 모든 경고를 출력할 수 있도록 하라.**

### O. Static Instance 을 사용하지 마라

> Static constructors and destructors (e.g. global variables whose types have a constructor or destructor) should not be added to the code base, and should be removed wherever possible. Besides well known problems where the order of initialization is undefined between globals in different source files, the entire concept of static constructors is at odds with the common use case of LLVM as a library linked into a larger application.
>
> ~ LLVM Coding Standards

### O. RTTI 및 Exception 을 사용하지 마라.

* Querying the type of an object at run-time frequently means a design problem. Needing to know the type of an object at runtime is often an indication that the design of your class hierarchy is flawed.

  Undisciplined use of RTTI makes code hard to maintain. It can lead to type-based decision trees or switch statements scattered throughout the code, all of which must be examined when making further changes.



## Comments

### C. Commenting

> Comments are one critical part of readability and maintainability.  Everyone
> knows they should comment their code, and so should you.  When writing comments,
> write them as English prose, which means they should use proper capitalization,
> punctuation, etc.  Aim to describe what the code is trying to do and why, not
> *how* it does it at a micro level. Here are a few critical things to document:
>
> ~ LLVM Coding Standards

* **영어로, 간결하게, 코드의 전반적인 행동을 써라.**
* **디버깅용으로 상당량의 코드를 주석 처리 할 경우에는, `#if 0` 와 `#endif` 을 써서 코드를 무효화한다.**
  * 어디까지나 주석은 코드에 대한 설명을 위해서만 존재한다.

> **Comments are comments.  They describe the code.**
>
> Code that's being excluded from compilation is code, not comments. It will often include comments, that describe the code that isn't being compiled, for the moment. 
>
> They are two distinct concepts, and forcing the same syntax strikes me as being a mistake.
>
> ~ https://stackoverflow.com/questions/3630601/why-use-if-0-for-block-commenting-out

**Good**

``` c++
if (it->second) {
    // Codes...
#if 0
    if (auto& additional_list = it->second->GetChildList(); !additional_list.empty()) {
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
if (it->second) {
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

* `//` 혹은 `/* */` 신택스 중 둘 중 하나를 써도 상관은 없으나, **가급적이면 `//` 을 쓴다.**
  * **함수 바디 안에서 주석을 작성할 때는 `//` 을 쓴다. 이 경우 양 끝에 주석을 한 줄씩 추가할 필요는 없다.**
  * *doxygen* 의 문서를 작성할 경우, `///` 을 쓴다. 이 때 양 끝에 한 줄씩 주석을 더 쓴다.
  * 네임스페이스의 끝, 헤더 파일 코멘트의 경우 `///` 을 쓴다.

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

###### 예외

주석을 작성할 때, 다음과 같은 예외가 있다.

1. C 코드를 쓸 때는 `/* */` 의 C 주석 스타일을 쓴다.
2. C 스타일의 주석만 인식하는 툴을 사용할 때는 C 스타일의 주석 스타일을 쓴다.

### C. Comments for Non-obvious objects

> Every non-obvious class declaration should have an accompanying comment that describes what it is for and how it should be used.
>
> ~ Class Comments from Google C++ Style Guide
>
> Classes are one fundamental part of a good object oriented design. As such, a class definition should have a comment block that explains what the class is used for and how it works. Every non-trivial class is expected to have a `doxygen` comment block.
>
> ~ Class overviews from LLVM Coding Standards

* **Non-obvious 한 Class 및 Method 는 해당 객체의 역할, 동작 방법에 대해서 `doxygen` 으로 설명해야 한다**
  * *method* 의 경우에는 해당 함수의 전반적인 동작에 대해서 설명한다. `.h` 파일의 선언 부분에서 전반적인 설명을 하며 구현에서 까다로운 부분, 혹은 테크닉을 쓴 경우에는 `.cc` 에서 별도로 설명한다.

### C. File Header

* **`.h` `.cc` 불문하고 모든 파일들은 머리 부분에 이 파일의 기본적인 목적을 써라.**
* **해당 파일을 변경했을 때는 변경 사유 등을 날짜와 같이 써라.**
* **프로젝트에 적용되고 있는 라이선스가 있을 경우에는, 원문 혹은 요약문을 맨 위에 붙여라**

> Every file should contain license boilerplate. Choose the appropriate boilerplate for the license used by the project (for example, Apache 2.0, BSD, LGPL, GPL).
>
> ~ Legal Notice and Author Line from Google C++ Style Guide

**Good**

``` c++
///
/// @license BSD 2-Clause License
///
/// Copyright (c) 2018, Jongmin Yun(Neu.), All rights reserved.
/// If you want to read full statements, read LICENSE file.
///
/// @file Manager/input.cc
///
/// @brief
/// blah blah something...
///
/// @author Jongmin Yun
///
/// @log
/// 2018-03-03 Refactoring.
/// 2018-05-20 Get rid of singleton pattern and rebuild it to namespace.
///
/// @todo Replace own input data style to json.
///
```

* **만약 `.h` 파일이 여러가지 추상화를 가지고 있으면, 해당 파일의 전반적인 내용에 대해서 1, 2 줄로 쓴다.**
  * 구체적인 문서는 해당 추상화 클래스 및 기능의 앞 부분에서 코멘트를 쓴다.

### C. Implementation Comments

* **트릭키하거나 복잡한 코드 블록은 해당 코드 블록 앞에 동작을 설명하는 주석을 가져야 한다.**

**Good**

``` c++
// Divide result by two, taking into account that x
// contains the carry from the add.
for (int i = 0; i < result->size(); i++) {
  x = (x << 8) + (*result)[i];
  (*result)[i] = x >> 1;
  x &= 1;
}
```

* **명확하지 않은 행동을 나타내는 코드 줄에는 위에 주석을 달아서 설명을 하는 것을 추천한다.**

**Good**

``` c++
// If we have enough memory, mmap the data portion too.
mmap_budget = max<int64>(0, mmap_budget - index_->length());
if (mmap_budget >= data_size_ && !MmapData(mmap_chunk_bytes, mlock))
  return;  // Error already logged.
```

* **만약 여러 개의 주석 줄을 끝에 달아야 한다면, 주석 정렬을 사용할 수 있다.**
* **명확한 구문에는 일일히 주석을 달지 않는다.**

~~**Bad**~~

``` c++
// Find the element in the vector.  <-- Bad: obvious!
auto iter = std::find(v.begin(), v.end(), element);
if (iter != v.end()) {
  Process(element);
}
```

### C. TODO style

* **`doxygen` 으로 todo 을 쓰되, 소괄호 `()` 안에 issue 번호를 기입한다.**



## Formatting

### F. Line Length

> *Why Not Both?*
> ~ https://softwareengineering.stackexchange.com/questions/184037/descriptive-naming-vs-80-character-lines
>
> http://katafrakt.me/2017/09/16/80-characters-line-length-limit/
>
> The optimal line length for your body text is considered to be 50-60 characters per line, including spaces (“Typographie”, E. Ruder). Other sources suggest that up to 75 characters is acceptable.
>
> ~ https://baymard.com/blog/line-length-readability
>
> https://wrongsideofmemphis.wordpress.com/2013/03/25/80-chars-per-line-is-great/
>
> The longer answer is that there must be some limit to the width of the code in order to reasonably allow developers to have multiple files side-by-side in windows on a modest display. If you are going to pick a width limit, it is somewhat arbitrary but you might as well pick something standard. Going with 90 columns (for example) instead of 80 columns wouldn’t add any significant value and would be detrimental to printing out code. Also many other projects have standardized on 80 columns, so some people have already configured their editors for it (vs something else, like 90 columns).
>
> ~ LLVM Coding Standards

* **코드의 모든 줄의 텍스트 수는 80 컬럼 안에서 작성되어야 한다.**
  * 코드는 여러 곳에서 보여지고 쓰여질 수 있기 때문에 최저 한계를 고려해야만 한다.

###### 예외

* 주석은 URL 등이 포함될 경우에는 해당 줄에 한해 80 글자 이상을 허용한다.
* A raw-string literal may have content that exceeds 80 characters. Except for test code, such literals should appear near the top of a file.
* `#include` 구문은 80 글자 이상을 허용한다.
* Include guard 역시 80 글자 이상을 허용한다.

### F. Must use `spaces` intead of tab

* **Indent 는 오직 Space 만 사용하며, 2 space 을 사용한다.**
  * `tab` 키를 누를 때, 2 space 을 띄우게끔 설정하는 것도 좋다.
  * 80 characters 의 제약을 맞추기 위해 4 space 대신 2 space 을 사용한다.
* 코드 수정 도중에 스타일을 따르지 않는 코드를 수정할 때에는 전체 소스 파일을 반영하지 않도록 해야하며 오직 수정하고자 하는 부분의 최소 단위만 수정한다.
  * it makes for incredible diffs that are absolutely worthless.


### F. Fundtion Declaration and Definition

* **Return type 은 함수 시그니쳐의 같은 줄에 온다.**
* **인자 (parameter) 역시 함수 시그니쳐의 같은 줄에 오도록 한다. 만약 그러지 못할 경우에는 다음 줄에 오게 한다.**
  * 길어서 정렬 조차 못할 경우에는, 모든 인자를 4 indent 을 사용해서 정렬하게 할 수 있다.

**Good**

``` c++
ReturnType ClassName::FunctionName(Type par1, Type par2) {
  DoSomething();
}
```

``` c++
ReturnType ClassName::LongLongFunctionName(Type par1, Type par2,
                                           Type par3) {
  DoSomething();
}
```

``` c++
ReturnType ClassName::SoMuchLongLongFunctionName(
    Type par1,
    Type par2,
    Type par3) {  // 4 line indent
  DoSomething();  // 2 line indent
}
```

*Google C++ Styleguide* 을 사용해서 다음을 준수한다.

* 인자 이름은 정확한 역할 및 의도를 가져야 한다.
* 함수 정의에서 쓰이지 않는 인자는 **정의에서 생략될 수 있다**.
* 반환 타입과 함수 이름을 한 줄에 같이 쓸 수 없다면, 이 마저도 줄을 띄워 쓸 수 있다.
* `()` 괄호의 여는 괄호는 항상 함수 이름 옆에 와야하며, 띄어쓰기를 금한다.
* `{}` 블록 괄호의 여는 괄호는 항상 함수 선언 시그니쳐의 마지막 줄의 끝에 와야한다. (K&R 을 선호한다)
* 인자 리스트와 `{}` 블록 괄호 사이에는 띄어쓰기가 와야한다.
* 기본 들여쓰기는 **2 spaces** 이다.
* 인자들을 여러 줄로 정렬할 때는 **4 spaces** 로 정렬한다.
* 매크로, 속성 등은 함수 시그니쳐의 맨 앞에 와야 한다.

**Good**

``` c++
MUST_USE_RESULT __CDECL bool IsOk();
```

### F. Lambda Expression



### F. 함수 호출 (Function Call)

* **자리가 충분한 경우 한 줄에 쓰고, 그렇지 않은 경우 괄호 안의 인자들을 줄바꿈한다. **
* **인자들이 모두 한 줄에 들어갈 수 없다면, 여러 줄로 나누어 쓰되 이어지는 줄은 첫번째 인자와 같은 열에 오도록 한다.**
* **모든 인자들마다 줄 바꿈하여 한 줄에 하나씩 쓸 때는, 해당 함수로부터 4 space 들여쓰기 해서 쓴다.**
  * 혹은 세로 줄을 낭비하는 것을 줄이기 위해 여러 인자를 한 줄에 집어넣는 것을 추천한다. 

**Good**

``` c++
EFlag ret_val = DoSomething(argument1, argument2, argument3);
EFlag ret_val = DoSomething(argument1, argument2,
                            argument3);

EFlag ret_val = DoSomething(argument1,
                            argument2,
                            argument3,
                            argument4);
```

``` c++
if (...) {
  if (...) {
    // 4 indents
  	DoSomething(
        argument1, argument2,
        argument3, argument4);
  }
}
```

* **만일 함수의 인자에 표현식을 사용할 때 가독성이 떨어진다면, 해당 표현식의 결과를 가지는 변수를 만들어 인자로 넘긴다.**

``` c++
int my_heuristic = scores[x] * y + bases[x];
EDoFlag result = DoSomething(my_heuristic, x, y, z);
```

### F. 중괄호 초기화 리스트 (Braced Initializer List)

* **함수 호출과 동일한 방식으로 쓴다.**

### F. 조건 분기문 (Conditional branch)

* **조건 표현식은 `()` 양 옆 사이에 띄어쓰기가 없어야 한다.**
* **`else` `else if` 구문을 적을 때는 then `}` 중괄호 바로 뒤에 띄어쓰기 후, 적는다.**

``` c++
if (condition) {
  ...
} else if (...) {
  ...
} else {
  ...
}
```

* **짧은 조건 분기문은 가독성을 높이기 위해 한 줄로 붙여 쓸 수 있다. `else` 가 올 경우에는 두 줄로 써야하며, 만약 분기문 중 하나가 길어질 경우에는 원래 조건문 포맷을 따라야 한다.**

**Good**

``` c++
if (x == EEnum::Foo) return new Foo();

if (x == EEnum::Foo) 
  return new Foo();
else 
  return new Nop();

if (x == EEnum::Foo) {
  return new Foo();
} else {
  ...
  ...
}
```

~~**Bad**~~

``` c++
if (x == EEnum::Foo) return new Foo(); 
else return new Nop();
if (x == EEnum::Foo) return new Foo(); else return new Nop();
if (x == EEnum::Foo) return new Foo();
else {
  ...
  ...
}
```

* **`if` 구문에서 중괄호를 쓸 때는 모든 경우에 항상 같이 중괄호를 쓰거나 말아야 한다.**

**Good**

``` c++
if (complex_condition) {
  DoSomething();
} else {
  DoOtherThing();
}
```

~~**Bad**~~

``` c++
if (complex_condition) {
  DoSomething();
} else
  DoOtherThing();
```

* **에러 출력 혹은 즉각 return 구문이 있는 절을 if 로 분기를 나눠서 쓰고자 할 때는, `then` 구문에 에러 혹은 immediate return 을 쓸 수 있도록 한다.**

``` c++
float GetKeyValue(const std::string& key) {
  NEU_ASSERT(m_initiated == EInitiated::Initiated, debug::err_input_not_initiated);

  if (IsKeyExist(key) == EKeyExist::NotExist) {
    PUSH_LOG_ERRO("fuck");
		return 0.f;
	}

  return m_key_inputs[key].value;
}
```

### F. 루프문 (Loops)

* **한 줄만 실행하는 루프문에서는 `{}` 중괄호는 쓰지 않아도 된다.**
* **빈 루프 구문은 `{}` 빈 중괄호 구문 혹은 `continue` 을 사용해야 한다. 그렇지 않으면 해당 구문이 무엇을 하는가 파악 할 수가 없다.**

``` c++
while (condition) {} // Repeat test until it retuens false.
while (condition) continue;

for (int i = 0; i < gk_some_number; ++i) {}
for (int i = 0; i < gk_some_number; ++i) continue;
```

### F. 스위치문 (Switch)

* **`switch` 구문의 `case ` 블록은 중괄호를 쓸 수도 있고 쓰지 않을 수도 있다. 만약에 중괄호를 쓴다면 여는 괄호 `{` 는 `case` 블록의 바로 뒤에 와야 한다.**
* **의도적인 *Fall through* 을 사용하는 case 는 마지막 줄에 속성 `[[fallthrough]]`[link](http://en.cppreference.com/w/cpp/language/attributes) 을 붙인다.**

``` c++
switch (x) {
  case 41:  // space 2 indent
    ...     // space 4 indent
    [[fallthrough]]
  case 43:
   	...
    break;
  default: assert(false);
}
```

* **열거형 타입을 `case` 로 하는 `switch` 문이 아닐 경우, `switch` 문은 항상 `default` 케이스를 구현해야 한다. **
* **일반 동작에서 `default` 케이스가 실행이 되지 않을 것이라고 설계한 경우에는 즉각적인 에러를 띄우도록 해야한다.**

``` c++
switch (x) {
  ...
  default: assert(false);
}
```

### F. Pointer and Reference Expressions

* **`.` 혹은 `->` 연산자 뒤, 앞에 공백이 있어서는 안된다.**
* **`*` 혹은 `&` 을 변수 이름쪽에 붙일 지, 타입에 붙일 지는 어떻게 하든 상관은 없으나 프로젝트에 걸쳐서  한 가지 방법을 유지해야 한다.**
  * 다만, `char * c ` 와 같이 공백을 양 옆에 두는 것은 금지한다.

``` c++
char* c;
const std::string& str;
```

### F. 불리언 표현식 (Boolean Expression)

* **만약 불리언 표현식이 기존 80 글자보다 크다면,  줄을 나눠서 써라.**
  **나눠서 쓰는 경우에는 4 space indent 을 사용한다.**

``` c++
if (this_one_thing > this_other_thing &&
    a_third_thing == a_fourth_thing &&
    yet_another && last_one) {
  ...
}
```

### F. 변수 및 배열 초기화 (Variable and Array Initialization)

* **uniform initialization `{}` 을 가급적 사용한다.**
  * *Uniform initialization* 은 Narrowing Conversion 을 컴파일 타임에서 막기 때문에 사전에 에러를 방지한다.
* **생성자에 std::initializer_list 가 인자로 들어갈 경우에는 `{}` 중괄호 초기화 대신에 `()` 을 사용한다.**

``` c++
std::vector<int> v(100, 1);  // A vector containing 100 items: All 1s.
std::vector<int> v{100, 1};  // A vector containing 2 items: 100 and 1.
```

### F. 네임스페이스 (Namespace)

* **네임스페이스의 내용들은 들여쓰기를 하지 않는다.**
* **네임스페이스를 끝낼 때, `}` 닫는 중괄호 뒤에 해당 네임스페이스의 전체 계층 구조를 코멘트로 남긴다.**

``` c++
namespace nm_a {
namespace nm_b {
namespace nm_c {
	...
} // ::nm_a::nm_b::nm_c
} // ::nm_a::nm_b
} // ::nm_a
```

* **C++17 에서 추가된 중첩 네임스페이스 기능은 파일 당 하나의 네임스페이스의 내용을 구현할 때만 사용한다.**

``` c++
namespace nm_a::nm_b::nm_c {
  ...
} // ::nm_a::nm_b::nm_c
```

## Header file

### H. File type specifier

선언을 담는 헤더 파일은 `.h` 의 확장자를 가진다.
구현체를 담는 파일은 `.cc` 의 확장자를 가진다.

### H. Define(Include) Guard

모든 헤더 파일들은 *해석 단위* 에서 여러 번 재 선언되는 것을 막고, 컴파일 크기를 줄이기 위해 한 번만 포함될 수 있도록 해야한다.

*  **#ifndef, #define, #endif 의 Define guard 을 쓴다.**

*Define Guard* 을 설정할 때, `#ifndef` `#define` `#endif` 을 사용해서 설정한다.
*Guard* 의 이름은 해당 프로젝트의 이름 + 해당 프로젝트로부터의 경로 이름이 된다. 다만 `Include` 혹은 `Source` 와 같은 대분류 디렉터리는 쓰지 않는다.

**Good**

``` c++
// Path is ${ProjectDir}/Include/Module/owner.h
#ifndef PROJECT_NAME_INCLUDE_MODULE_OWNER_H
#define PROJECT_NAME_INCLUDE_MODULE_OWNER_H

#endif // PROJECT_NAME_MODULE_OWNER_H
```

~~**Bad**~~

``` c++
// Path is ${ProjectDir}/Include/Module/owner.h
#pragma once
```

`#pragma once` 는 C++ 표준에는 포함되어 있지 않으나, 현재 여러 주요한 컴파일러에서는 지원하고 있는 *Define guard* 이다. 그러나 이것을 쓸 경우, 다른 위치에 존재하는 동일한 이름의 파일이 읽혀지지 않는다. 따라서 만일을 위해 해당 전처리 지시자는 사용을 하지 않는다.

###### 예외

프로젝트에 정식으로 포함되지 않을, 임시 파일에는 `#pragma once` 을 쓸 수 있다. 다만 실제 프로젝트에 반영할 때는 위의 사항을 지켜야 한다.

### H. Header Include Order

*  **All of a project's header files should be listed as descendants of the project's source directory without use of UNIX directory shortcuts . (the current directory) or .. (the parent directory).**
   *  파일 디렉터리 구조가 변경되었을 때도 일일히 헤더 파일의 인클루드 경로를 변경하지 않아도 된다.

##### A. 예시

다음과 같이 헤더 파일은 `Include` 폴더에 있고, 구현 파일은 `Source` 폴더에 있다고 하자.

``` text
Test
└Include
 └Module
  └test.h
└Source
 └Module
  └main.cc
```

**Good** in main.cc

``` c++
#include <Module\test.h>
```

~~**Bad**~~ in main.cc

``` c++
#include "../../Include/Module/test.h"
```

##### B. How to?

`gcc` 혹은 `Makefile` 을 쓰고 있다면 다음을 참고한다.

> **Q / A** : How to include C++ header file which is not in the same directory as the current source file without using '..'?
> https://stackoverflow.com/questions/30289915/how-to-include-c-header-file-which-is-not-in-the-same-directory-as-the-current

혹은 Visual Studio 을 쓰고 있다면, 해당 프로젝트의 속성의 *Include Directories* 에서 현재 프로젝트의 헤더 파일 디렉터리를 추가한다.

``` visual basic
$(ProjectDir)Include
```

* **구현 파일 (`.cc` 파일) 에서 헤더 파일을 포함할 때는 다음 순서대로 헤더 파일을 가져온다.**

1. 구현에 쓰일 헤더 선언 파일
2. 공백
3. C 표준 헤더 파일
4. C++ 표준 헤더 파일
5. 공백
6. Third-party 라이브러리 헤더 파일
7. 현재 프로젝트에서 가져올 부수적인 헤더 파일

**Good**

``` c++
// Include guard
#include <base/logging.h>

#include <cstdio>
#include <ctime>
#include <algorithm>
#include <utility>

#include <GL/glew.h>
#include <glm/glm.hpp>
// ::base::module::owner
#include <base/module/owner.h>
```

~~**Bad**~~

``` c++
// Include guard
#include "base/logging.h"

#include <chrono>
#include <cstdio>

#include "base/module/owner.h"
#include <GL/glew.h>
```

> 참고
> https://stackoverflow.com/questions/2762568/c-c-include-file-order-best-practices

*  **C++ 에서의 C 표준 파일은 C++ 표준 헤더로도 바꿀 수 있으나, 일관성을 유지하라**

어떤 파일에서는 `stdio.h` 을 인클루드 하고, 어떤 다른 파일에서는 `cstdio` 을 인클루드하는 등의 행동은 하지마라.

* **위의 인클루드 순서를 유지하면서, 알파벳 이름 순으로 인클루드 하라.**
* **You should include all the headers that define the symbols you rely upon, except in the unusual case of forward declaration. If you rely on symbols from bar.h, don't count on the fact that you included foo.h which (currently) includes bar.h: include bar.h yourself, unless foo.h explicitly demonstrates its intent to provide you the symbols of bar.h**
* **하나의 해석 단위의 최종 파일은 한 개의 `.cc` 로 끝나야 하며, 해석 단위 중간에 `.cc` 파일이 포함되서는 안된다.**
  * 즉, `#include` 를 할 때 `.cc` 파일의 인클루드는 하지 않는다.
* **인클루드는 오직 `.h` 파일 또는 `헤더 파일` 만 한다.**

###### 예외

만약에 라이브러리 혹은 전처리 지시자로 시스템 의존 코드 혹은 플래그를 구성해야 할 때는 위의 순서가 끝나고 난 뒤의 맨 뒤에 추가한다. 이 경우에도 기본적인 인클루드 순서는 유지한다.

``` c++
#include <foo\public\fooserver.h>

#include <base\port.h>

#ifdef _DEBUG
#include <initializer_list>
#include <base\logger.h>
#endif // _DEBUG
```

### H. Forward Declaration

> ddf
>
> ~ [Should one use forward declarations instead of includes wherever possible?](https://stackoverflow.com/questions/9906402/should-one-use-forward-declarations-instead-of-includes-wherever-possible)



## Naming

### N. File Names

* **파일 이름은 모두 소문자 및 `_` 만을 사용해야 한다.**
* 각 단어마다 `_` 을 사용해서 띄어쓰는 것을 추천한다.
* 테스트용 파일 혹은 특별한 용도로 작성된 파일의 경우에는 본 이름 뒤에 `_test` 등의 용도를 덧붙인다.

**Good**

``` c++
my_useful_class.cc
myusefulclass.cc
myusefulclass_test.cc
```

* **파일 이름을 작성할 때는 이름을 구체적으로 작성한다.**

**Good**

``` c++
http_server_logs.h
```

~~**Bad**~~

``` c++
logs.h
```

* **파일 안에 하나의 클래스 추상화만 있을 때는 해당 클래스의 이름을 따와서 파일 이름을 작성하는 것을 추천한다.**
* **만약, 해당 파일이 있는 폴더의 이름으로 파일의 쓰임새를 확실히 알 수 있으면, **
  **간결하게 작성한다.**
  * 이 경우 파일의 이름을 구체적으로 쓰면 읽기 매우 번거롭다.

``` c++
// Include
// L Manager
//   L Internal
//     L input.h
//     L font.h
//   L input.h
//   L font.h
//   L object.h
```

### N. Type Names

* **정의된 모든 타입 이름들은 영어 명사로 끝내야 하며, 대문자로 시작한다**.
  * 타입 이름은 `struct` `class` `union` `enum class` `enum` 등이 있다.
  * `::std` 에서 제공된 타입 이름은 이 규칙을 무시한다.
* **Using 혹은 Typedef 을 사용한 Aliasing 역시 대문자로 시작해야 한다.**
  * `::std` 을 사용한 타입 별칭에도 해당 규칙을 적용한다.

**Good**

``` c++
class CTable { ... };
class CTester { ... };
struct CProperty { ... };
struct DTableElementInfo { ... };

typedef std::map<const char*, UrlTableProperties*> PerpertiesMap;
using PropertiesMap = std::map<const char*, UrlTableProperties*>;

enum class EUrlTableErrors { ... }; 
```

* **만일의 경우, 해당 프로젝트를 사용하는 제 3 자가 만들어낼 임의 타입과 구분을 지어야 한다면, `C` `E` `D` `M` 등과 같은 prefix 을 타입 이름의 앞에 넣을 수 있다.**
  * `C` : 일반 Class. 정보 외에 동작을 넣을 수 있다.
  * `D` : POD 혹은 연산 없이 자료 정보만을 가지고 있는 타입.
  * `E` : 열거형 타입. `enum` `enum class` 둘 다 쓰일 수 있다.
  * `I` : Interface 타입.
  * `U` : Union 타입.
* **prefix 을 기입 시, 대문자로 기입한다.**

### N. Variable Names

* **모든 변수의 이름 및 멤버 변수의 이름은 `_` 을 쓰거나 명사로 끝나고, 소문자로만 쓴다.**
  * 이름 맨 앞에 `_` 은 올 수 없다.
* **연산을 가지는 `C` 구조체의 멤버 변수의 경우, 각 함수의 로컬 변수와 구분하기 위해 `m_` 을 해당 변수의 앞에 기입한다.**
* **연산을 가지지 않는 `D` 구조체의 멤버 변수의 경우, `m_` 을 붙이지 않는다.**
* **구조체의 논리적 타입을 불문하고, `static` 변수의 경우에는 항상 `s_` 을 붙인다.**
  * `this->` 을 사용하는 것은 타자양이 많을 뿐만 아니라, 너무 번거롭다.
  * C++ 에서는 이런 방식을 사용하지만, 다른 언어에서는 다른 방식을 사용하라.

**Good**

``` c++
class DDatabaseItem {
public:
  // Getter Setter.
private:
  std::string name;
  uint32_t value;
};

class CDatabase {
public:
  // ...
private:
  using InformationMap = std::unordered_map<std::string, DDatabaseItem*>;
  InformationMap m_info_map;
  static bool s_is_flaged;
};
```

* **최소 namespace 의 범위를 가지는 Global variable 은 `g_` 을 사용한다.**
* **`const` `constexpr` 의 상수성을 가지는 변수에는 `k_` 을 사용한다. 만약 다른 *prefix* 와 같이 써야 한다면, 두번째 문자로 쓴다**.
  * 만일 `static` 과 `const` `constexpr` 상수성이 같이 올 경우에는 `k_` 만을 덧붙인다.

**Good**

``` c++
namespace ns {
static constexpr bool gk_flag = false;
static const bool 
static uint32_t g_value = 30;
} // ns

class Class final {
private:
  const int32_t   mk_sign_value = 0b00001111; 
  static uint32_t ms_static_variable = 0xFF;
  Class* m_ptr = nullptr;
}
```

> 
>
> ~ [Naming convention for class member variables](https://www.reddit.com/r/cpp/comments/4fxvqi/naming_convention_for_class_member_variables/)

### N. Function Names

* **보통 함수의 이름은 대문자로 시작하는 Camel case 로 작성한다.**

``` c++
AddTableEntry();
DeleteUrl();
OpenFile(k_file_path);
```

* **멤버 함수의 Getter / Setter 은 `Get` `Set` 전부 붙여서 쓴다.**
  * 현재 스타일에서는 타입 이름이 대문자 Camelcase 을 따르기 때문에, 혼동을 주지 않기 위해서 붙인다.

``` c++
class Information final {
public:
  std::string GetName() const noexcept { return m_name; }
  void SetName(const std::string& name) { m_name = name; }
  void SetName(const char* name) { m_name = name; }
private:
  std::string m_name = "";
}
```

### N. Namespace Names

* **네임스페이스는 소문자이거나 `_` 을 써야 한다.**
* **가장 최상위 네임스페이스는 항상 프로젝트의 이름이어야 한다.**
* **네임스페이스의 이름 역시 축약되지 않고 명확해야 한다.**
* **`std` 네임스페이스를 범위로 하는 사용자 정의 타입 및 중첩 네임스페이스를 만들지 말라.**

### N. Enumerator Names

* **열거형 요소들의 이름은 대문자를 시작으로 하는 Camel case 로 작성한다.**
  * 이전까지는 대문자 및 `_` 만으로 이름을 작성했지만, 매크로의 이름 표기법과 충돌이 되고 매크로가 우선 순위를 가지기 때문에 충돌이 일어날 수 있었다.

**Good**

``` c++
enum class EUrlTableErrors {
  Ok = 0,
  ErrorOutOfMemory,
  ErrorMalformedInput
};
```

~~**Bad**~~

``` c++
enum class EUrlTableErrorsNotProper {
  OK = 0,
  ERROR_OUT_OF_MEMORY,
  ERROR_MALFORMED_INPUT
};
```

### N. Macro Names

* **되도록이면 매크로를 정의하지 마라.**
  * `constexpr ` 혹은 `inline` 의 함수를 정의해서 쓰는 것을 선호하라.
* **만약에 정의를 하지 않으면 안되는 상황일 때는, 대문자 및 `_` 만으로 이루어진 이름으로 작성한다.**

``` c++
#define ROUND(x) ...
#define PI_ROUNED 3.0
```



## Other C++ Features

### T. 고정 사이즈 정수형 타입 (`cstdint`)

> 64-bit data models
> https://en.wikipedia.org/wiki/64-bit_computing
>
> | Data model      | short (integer) | int  | long (integer) | long long | pointers,  size_t | Sample operating systems                                     |
> | --------------- | --------------- | ---- | -------------- | --------- | ----------------- | ------------------------------------------------------------ |
> | LLP64,  IL32P64 | 16              | 32   | 32             | 64        | 64                | [Microsoft Windows](https://en.wikipedia.org/wiki/Microsoft_Windows) (x86-64 and IA-64) using [Visual C++](https://en.wikipedia.org/wiki/Visual_C%2B%2B); and [MinGW](https://en.wikipedia.org/wiki/MinGW) |
> | LP64,  I32LP64  | 16              | 32   | 64             | 64        | 64                | Most [Unix](https://en.wikipedia.org/wiki/Unix) and [Unix-like](https://en.wikipedia.org/wiki/Unix-like) systems, e.g., [Solaris](https://en.wikipedia.org/wiki/Solaris_(operating_system)), [Linux](https://en.wikipedia.org/wiki/Linux), [BSD](https://en.wikipedia.org/wiki/BSD), [macOS](https://en.wikipedia.org/wiki/MacOS). [Windows](https://en.wikipedia.org/wiki/Windows) when using [Cygwin](https://en.wikipedia.org/wiki/Cygwin); [z/OS](https://en.wikipedia.org/wiki/Z/OS) |
> | ILP64           | 16              | 64   | 64             | 64        | 64                | [HAL Computer Systems](https://en.wikipedia.org/wiki/HAL_Computer_Systems) port of Solaris to the [SPARC64](https://en.wikipedia.org/wiki/HAL_SPARC64) |
> | SILP64          | 64              | 64   | 64             | 64        | 64                | *Classic* [UNICOS](https://en.wikipedia.org/wiki/UNICOS)[[41\]](https://en.wikipedia.org/wiki/64-bit_computing#cite_note-SILP64-41) (versus UNICOS/mp, etc.) |
>
> Fixed width integer types (since C++11)
> http://en.cppreference.com/w/cpp/types/integer
>
> Should I use long long or int64_t for portable code?
> https://stackoverflow.com/questions/12468281/should-i-use-long-long-or-int64-t-for-portable-code
>
> Does a `long` ban make sense?
> https://softwareengineering.stackexchange.com/questions/317670/does-a-long-ban-make-sense

* C++ 표준에서는 short, int, long 등과 같은 자료형의 크기를 명시적으로 정의하지 않고 있다. 하지만 **int 는 대개 32bit로 구현되며, long long 은 64bit 로 구현된다.**
  * 따라서, 왠만한 경우에서는 `bool` `int` 만을 주 타입으로 쓴다. 
  * **하지만, `stdint` 와 같은 파일을 사용해 고정 사이즈 변수를 쓸 수 있고, 어느 플랫폼에서도 고정 비트의 변수 값을 사용하고 싶을 때는 `int32_t` `uint32_t` `int64_t` 등을 쓰도록 해야한다.** 
    다만 플랫폼 및 컴파일러에 따라 제공하는 최대 변수 타입이 달라질 수도 있다.
  * `for` `while` 에서 탐색을 위한 임시 변수를 사용할 때는 고정형 타입을 쓰지 않는다.
* `long` 타입의 경우에는 x64 모델 시스템에서의 윈도우에서의 사이즈, 그 외 유닉스 및 리눅스 계열의 사이즈 값이 다르기 때문에 사용을 금한다.

### T. `size_t`

* **인덱스 값 저장 혹은 인덱스 참조를 위한 일부 계산식을 제외하고는 `size_t` 를 일반 변수형과 같이 섞어 사용하지 않는다.**

### T. 레퍼런스 인자

* **레퍼런스로 전달되는 모든 인자는 `const` 로 수식되어야 한다.**
* **함수가 변수를 변경할 필요가 있는 경우에는 포인터를 사용해야 한다.**

``` c++
void Foo(const std::string& in, string* out);
```

### T. Rvalue References

> Rvalue references and move semantics
> https://isocpp.org/wiki/faq/cpp11-language#rval

* **Forwarding Reference (Perfect Forwarding), 이동 연산자 및 이동 생성자를 정의할 때에만 사용하라**
  * Perfect forwarding 은 std::forward<>() 혹은 std::move() 을 이용해서 시도해볼 수 있다.

### T. Friends

* **합당한 선에서 `friend` 클래스와 함수들을 사용한다. **

> friend는 코드를 읽는 이가 클래스의 private 멤버의 사용을 다른 파일에서 확인할 필요가 없도록 하기 위해서 일반적으로 같은 파일에서 정의된다. friend의 일반적인 용례는 Foo 클래스의 내부 상태를 외부에 노출시키지 않고 제대로 구성하기 위해 FooBuilder 클래스가 Foo 클래스의 friend가 되는 것이다. 유닛테스트 클래스를 테스트 대상 클래스의 friend로 만드는 것이 때때로 유용하다.
>
> friend는 클래스의 캡슐화의 경계를 확장하지만 깨뜨리지는 않는다. 이것은 다른 클래스 하나를 위해 멤버를 public으로 두는 것보다 낫다. 그러나 대부분의 클래스는 public 멤버들을 통해서만 다른 클래스들과 상호작용하여야 한다.
>
> ~  Google C++ Style guide (한글) [link](http://jongwook.kim/google-styleguide/trunk/cppguide.xml)

### T. Exceptions

* **C++ 예외를 사용하지 않는다.**

> 더 일반적으로, 예외는 코드를 보며 프로그램의 흐름을 파악하기 어렵게 하고 함수들이 어디서 제대로 리턴할 지 기대하기 어렵게 만들며 이것은 유지 보수와 디버깅을 어렵게 한다. 이러한 불편은 어디서 어떻게 예외를 사용할지 규칙을 정하는 것으로 줄일 수 있지만 개발자가 그것을 알고 이해해야 한다는 비용도 크다. 
>
> 예외를 사용하는 것은 만들어진 각 바이너리(binary)에 데이터를 추가하고 컴파일 시간과 주소 공간의 부담을 증가시킨다. 
>
> 더 일반적으로, 예외는 코드를 보며 프로그램의 흐름을 파악하기 어렵게 하고 함수들이 어디서 제대로 리턴할 지 기대하기 어렵게 만들며 이것은 유지 보수와 디버깅을 어렵게 한다. 이러한 불편은 어디서 어떻게 예외를 사용할지 규칙을 정하는 것으로 줄일 수 있지만 개발자가 그것을 알고 이해해야 한다는 비용도 크다. 
>
> ~  Google C++ Style guide (한글) [link](http://jongwook.kim/google-styleguide/trunk/cppguide.xml)

* **따라서, STL 컨테이너 대다수 `std::vector` `std::list` `std::map` 등 은 사용할 수 없다.**

  * exception 을 사용하지 않는 STL 방언을 사용한다. 
  * EASTL 을 사용하는 것을 추천한다.

  *예외*는 현대 C++ 의 기본 철칙인 

### T. `noexcept`

> noexcept — what for?
> https://akrzemi1.wordpress.com/2014/04/24/noexcept-what-for/
>
> Should I use noexcept for getters always?
> https://stackoverflow.com/questions/21330807/should-i-use-noexcept-for-getters-always

* 이 가이드에서는 예외를 사용하지 않기 때문에 **거의 `noexcept` 을 사용하는 것을 권장한다.** 
  * 예외를 사용한다면, 간단하고 예외를 던지지 않는 것을 보장할 때만 `noexcept` 을 사용한다.
  * **`throw()` 을 사용하지 않는다. (deprecated since C++17~)**
* `move` 시맨틱을 활용할 경우에는 **무조건 `noexcept` 을 보장해야 한다. (후술)**
  * 왜냐면, `std::vector<T>::push_back()` 와 같은 경우에는 **예외 안정성을 보장해야 하는데 이동 함수가 예외를 보장하지 못할 경우에는 복사가 되기 ** 때문이다.
  * 객체 타입이 예외 안전성을 보장하는지 볼려면 `std::move_if_noexcept` 을 사용해서 볼 수 있다.
* **Special function** 에서, **암시적 기본 생성자, 복사 생성자, 대입 복사 연산자, 이동 생성자, 대입 이동 연산자, 소멸자는 기본 `noexcept` 이다**.

일반 예외가 던져질 때는 *Stack unwinding* 을 통해 스택을 훑어나가는 데, `noexcept` 을 사용해서 std::terminate() 을 호출하는 대신 *unwinding* 코드를 생성하지 않도록 할 수도 있다.

`noexcept(expression)` 는 표현식을 컴파일 타임 체크를 해서, 해당 함수의 바디가 결과적으로 예외를 던지지 않는다면 성능 향상의 효과를 기대할 수 있다. 만약 표현식이 예외를 던진다면, 해당 함수는 *잠재적인 예외 함수*가 된다. 

또한 *Move 생성자*를 `noexcept` 로 설정 시, `std::vector<T>::resize()` 에서 동적 배열을 리사이징 할 때 값을 복사하지 않고 그대로 이동시키게 할 수 있는 잠재적인 성능 향상을 기대해볼 수 있다.

* *no-fail* 과 *no-throw* 는 엄연히 다른 개념이다.
  * **`noexcept` 는 함수의 행동이 절대로 실패하지 않음을 보장할 때만 써야한다.**

#### Example

``` c++
// From https://akrzemi1.wordpress.com/2014/04/24/noexcept-what-for/
int Tool::operator=(Tool&& rhs)
{
  if (ResourceHandle tmp = getSentinelResource()) {
    dispose(this->resourceHandle);
    this->resourceHandle = rhs.resourceHandle;
    rhs.resourceHandle = tmp;
    return EXIT_SUCCESS;
  }
  else {
    return EXIT_FAILURE;
  }
}
```

위 코드에서는 어느 코드도 예외를 던지지 않을 뿐더러, 내부에 호출되는 함수 역시 예외를 던지지 않는다. 하지만 이 코드가 `noexcept` 로 선언되어야 하냐면 그것은 또 아니다. 왜냐면 **이동 연산이 실패**할 수도 있기 때문이다. `push_back()` 의 함수를 사용할 때 해당 이동 연산이 `noexcept` 로 처리되어 복사 대신 이동을 할 때, 조건 분기에서 실패를 하게 된다면 미정의 행동을 일으킬 가능성이 높다. 

#### operator `noexcept`

``` c++
noexcept(expression);
```

* **noexcept 연산자**는 컴파일 타임에 해당 표현식이 예외를 던지지 않는지 확인해서 true/false 을 반환한다.
* *noexcept specifier* 와는 엄연히 다르다. *specifier* 는 런타임에 예외 판정을 한다.

### T. RTTI

- **`typeid` `dynamic_cast` 등의 RTTI 의 사용을 금지한다.**
- **유닛 테스트에서는 자유롭게 사용해도 된다.**

> 실행시간(run-time)에 객체의 타입을 자주 질의하는 것은 설계에 문제가 있다는 뜻이다. 실행시간에 객체의 타입을 알아야 하는 것은 클래스 계층 설계가 가진 결함의 징후인 경우가 종종 있다.
>
> RTTI 의 무분별한 사용은 코드를 유지보수하기 어렵게 만든다. 그리고 타입 기반의 의사 결정 트리나 코드에 흩어져있는 switch 문을 만든다. 이런 모든 것들은 나중에 코드를 변경할 때 반드시 검사되어야 한다. 
>
> ~  Google C++ Style guide (한글) [link](http://jongwook.kim/google-styleguide/trunk/cppguide.xml)

### T. Use `static_cast<>()`

* **`static_cast<>()` 와 같은 정적 형 변환만을 사용한다.**
* **C 언어 형 변환, RTTI 형 변환 및 `const_cast<>()` 역시 사용하지 않는다.**
* **포인터 타입을 정수 및 다른 포인터 타입과 안전하지 않게 서로 형 변환하고자 할 때는 `reinterpret_cast<>()` 을 사용한다. 이 경우 의도를 가지고 주석 등으로 설명하는 것을 선호하라.**

### T. Macro preprocessor

* **합당한 이유가 있을 때만 매크로를 써라.**

C 의 유산인 매크로는 해석 범위에 대해 전역 범위를 가진다. 매크로를 사용 할 때 에러를 파악하기 힘들며 매크로 지점에서 에러가 발생했을 때는 매크로가 어떤 행동을 하는지 알아야 하는 수고가 있다. 또한 리팩터링 및 정적 분석 툴을 사용하는데 있어서도 어려움이 동반된다. 

매크로를 정 쓰고자 한다면, 최신 C++ 의 기능을 사용해서 기능을 구현할 수 있는지 확인하고 없을 때 매크로를 쓸 수 있다.

* **매크로를 사용해서 `inline` 화를 꾀하고자 할 때는 `inline` 함수를 사용한다.**
* **상수를 이용하고 싶을 때는 `const` 혹은 `constexpr` 변수를 사용한다.**
* **긴 이름을 줄이려고 한다면, *참조*를 사용한다.**
* **그 외 등등...**

또한 다음 규칙을 준수한다.

* **프로젝트 전체에 범용으로 쓰지 않을 예정이면 `.h` 헤더 파일에 매크로를 선언하지 않는다.**
  * 프로젝트 전체에 쓸 때는 `.h` 파일에 매크로를 작성할 수 있다.
* 일부 소스 코드에서만 사용하는 매크로를 쓸 경우, 
  매크로를 사용하기 직전에 `#define` 을 쓰고, 쓰고 난 직후 `#undef` 을 써서 매크로를 해제시킨다.
* 가급적이면 `##` concatenation 전처리 지시자를 사용하지 않는다.
* 부득이하게 헤더에서 매크로를 선언해야 할 경우, *prefix* 로 프로젝트 고유 식별자를 사용한다.

``` c++
/// Helper/assert.h

#ifndef NDEBUG

#define NEU_ASSERT(__MAExpr__, __MAMessage__) \
  ::opgs16::debug::__EnhancedAssert(#__MAExpr__, __MAExpr__, __FILE__, __LINE__, __MAMessage__)

#define NEU_NOT_IMPLEMENTED_ASSERT() \
  ::opgs16::debug::__NotImplementedAssert(__FILE__, __LINE__)

#else

#define NEU_ASSERT(__MAExpr__, __MAMessage__) (void(0));

#define NEU_NOT_IMPLEMENTED_ASSERT() (void(0));

#endif
```

### T. `sizeof`

* **`sizeof(Type)` 대신에 `sizeof(variable_name)` 을 사용한다.**

**Good**

``` c++
Struct data;
memset(&data, 0, sizeof(data));
```

~~**Bad**~~

``` c++
memset(&data, 0, sizeof(Struct));
```

* **만약 특정 변수에 종속되지 않은 sizeof 을 사용할 때는 `sizeof(Type)` 을 써도 된다.**

**Example**

``` c++
if (raw_size < sizeof(int)) {
  LOG(ERROR) << "compressed record not big enough for count: " << raw_size;
  return false;
}
```

### T. Template Metaprogramming

> Template metaprogramming
> https://google.github.io/styleguide/cppguide.html#Template_metaprogramming

* **너무 복잡한 템플릿 메타 프로그래밍은 자제한다.**

### T. Strong boolean enum

> Boolean enums: improved clarity or just overkill? [closed]
> https://codereview.stackexchange.com/questions/11300/boolean-enums-improved-clarity-or-just-overkill
>
> ...the enum is a very nice solution here. And in a way I disagree with Johannes, even for single-use the enum improves readability and discoverability of the API, and writing it is a negligible effort...

* **어떤 행동의 발생 여부를 저장하는 변수, 또는 함수의 반환형은 `bool` 이 아닌, bool 을 바탕 타입으로 가지는 `enum class` 타입이어야 한다.**
  * 가독성을 높이고, 유지 관리를 보다 더 수월하게 할 수 있다.
  * `enum class` 는 기존의 `enum` 과는 다르게 범위를 가지며 암시적으로 바탕 타입으로 변환이 될 수 없다.
  * `bool` 만으로 모든 플래그를 관리하려면 생각 외로 머리를 많이 굴려야 한다.

boolean 열거형 플래그는 다음처럼 작성한다.

```c++
///
/// @enum EInitiated
/// @brief
/// This enum class is for checking and preventing duplicated 
/// ::opgs16::manager::(namespace)::Initiate() call from somewhere.
///
enum class EInitiated : bool {
  NotInitiated = false,
  Initiated = true
};
```

그리고 다음처럼 쓸 수 있다.

```c++
void Initiate() {
  // Integrity check.
  NEU_ASSERT(m_initiated == EInitiated::NotInitiated, debug::err_setting_duplicated_init);
  m_initiated = EInitiated::Initiated;
  ...
}
```

* **그 외에 `bool` 타입은 어떤 사용자 정의 타입의 연산이 매우 명확할 때에만 인자 값으로 사용할 수 있다.**
  * 어떤 함수의 인자가 `isSensitive` `is...` 와 같은 이름으로 `bool` 값을 받는다고 해도 사용자 측에서는 해당 함수의 인자 값이 무엇을 의미하는지 API 문서를 찾아보지 않고서는 전혀 알 수 없다.

```c++
myObject.Enable(true);
```

* `bool` 와 바탕 타입이 `bool` 인 `enum class` 사이의 성능 차이는 없으며, `-O2` 에서 출력되는 어셈블리 역시 동일하다.
  * -std=c++0x 에서, ARM gcc 6.3.0, clang 4.0.0, x86-64 gcc 4.8.3 에서 검증 완료
  * https://godbolt.org/g/eAAiBh

```assembly
OnBool():
        movb    $1, (anonymous namespace)::is_on(%rip)
        ret
OnEBool():
        movb    $1, (anonymous namespace)::is_eon(%rip)
        ret
.LC0:
        .string "plain bool is off"
.LC1:
        .string "plain bool is on"
CheckBool():
        cmpb    $0, (anonymous namespace)::is_on(%rip)
        je      .L6
        movl    $.LC1, %edi
        jmp     puts
.L6:
        movl    $.LC0, %edi
        jmp     puts
.LC2:
        .string "plain enum bool is off"
.LC3:
        .string "plain enum bool is on"
CheckEBool():
        cmpb    $0, (anonymous namespace)::is_eon(%rip)
        je      .L9
        movl    $.LC3, %edi
        jmp     puts
```

### T. Assertion

* **적극 사용한다. 하지만 단언문 (assert) 은 릴리즈 모드에서는 무효화되기 때문에 검증 표현식에 프로그램의 행동을 바꾸는 구문은 쓰지 말아야 한다.**

**Good**

``` c++
assert(1 == 1)
```

~~**Bad**~~

``` c++
assert(value == i++)
```

* **기본으로 제공되고 있는 assert(expression) 은 메시지를 출력할 수 없기 때문에 메시지 출력이 가능한 이식성 있는 assert 을 만들어서 쓴다.**

## Et cetera

### E. 64-bit Portability

* **해당 프로젝트의 코드는 반드시 64-bit 와 32-bit 호환성을 만족시켜야 한다.**
  * 출력, 비교 및 구조체 정렬에 대해서 조심히 다뤄야 한다.

### E. Boost

### E. EASTL

### E. Do not use singleton

* **싱글턴은 사용하지도 않고, 쳐다보지도 않는다.**
  * C++ 는 C# 이 아니다!
  * **싱글턴은 게으른 초기화 (lazy initialization) 을 사용하고 있으며 제어할 수 없다.**
  * C++11 이후에는 지역 범위 정적 객체에 대한 참조 반환이 스레드에 안전하게 변경이 되었지만, **싱글턴 클래스 자체의 스레드 안전 여부와는 별개의 사항이다.** C++11 이전에는 [double-checked locking](https://en.wikipedia.org/wiki/Double-checked_locking) 과 같은 특수한 패턴을 사용했지만 매우 번거롭고 뮤텍스를 사용하기 때문에 성능을 떨어뜨린다.

~~**Bad**~~

``` c++
// Fxxking do not use singleton!!!!
class MFontManager final {
public:
	/*! Return single static instance. This must be called in initiation time once. */
	static MFontManager& Instance() {
		static MFontManager instance{};
		return instance;
	}
}
```

* **관리 객체에 대해서는 정적 클래스를 사용한 싱글턴 대신, 네임스페이스를 사용한다.**
  * 네임스페이스로 바깥에 보일 함수만을 만들고, 나머지는 `.cc` 파일에 구현한다.
  * 관리할 데이터는 `.cc` 파일에서 **무명 네임스페이스**를 사용해서 변수를 선언하고, 정적 링킹을 한다.
  * 임의 시점에서 초기화가 될 지 모르는 일반 정적 클래스, 혹은 싱글턴과는 다르게 스코프가 어플리케이션의 시작부터 끝까지로 보장이 된다.
* **처음부터 끝까지 특정 메모리 영역을 사용하기 때문에, 필수 데이터만 선언한다.**
* **Initiate() 함수가 필요할 경우, 반드시 단언문 (assert) 을 사용해서 한 번만 초기화할 수 있도록 해야한다.**
  * 런타임에 인스턴스 개수를 확인 할 수 밖에 없는 단점이 존재한다.

``` c++
/// header file
/// @namespace opgs16::manager::input
///
namespace opgs16::manager::input {
///
/// @brief
/// 
void Initiate(GLFWwindow* window_context);
///
/// @brief 
/// 
float GetKeyValue(const std::string& key);
 
} /// ::opgs16::manager::input
```

``` c++
/// source file
///
/// This namespace stores variables or 
/// constexpr variables to be used by functions.
///
namespace {
using opgs16::manager::_internal::BindingKeyInfo;
using key_map = std::unordered_map<std::string, BindingKeyInfo>;
constexpr std::string_view input_path{ "_Setting/input.meta"sv };

// Window handle pointer
GLFWwindow* m_window;
key_map m_key_inputs;
} /// unnamed namespace

namespace opgs16::manager::input {
void Initiate(GLFWwindow* window_context) {
  NEU_ASSERT(m_initiated == EInitiated::NotInitiated, debug::err_input_duplicated_init);
  m_initiated = EInitiated::Initiated;
  m_window = window_context;
  ReadInputFile(input_path.data());
}
} /// ::opgs16::manager::input
```

* **여기서 제시한 방법이 완전한 해답은 아님을 명심한다.**
  * 더 좋은 방법이 있으면 그걸 써도 된다. (+ 알려주세요...)

### E. Error checking

* **에러 체킹은 발생 즉시 처리해야만 한다. 따라서 `.IsError()` `.ErrorCheck()` 와 같은 함수로 에러 발생 이후에 뒤늦게 체킹하는 것은 금지한다.**
* 개발은 항상 디버그 모드에서 하며, 실행 중 에러로 인해서 앞으로의 처리에 이상이 올 경우에는 주저없이 **assert 등을 사용해서 동작을 멈춘다.**

