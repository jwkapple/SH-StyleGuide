# `StudioN` C++ Coding Style

> 2018-05-21 Under construction.
>
> 2018-11-27 AM 12:06 갱신. 
> `In. Preface` 본문 추가
> `O. C++ Standard Versions` 
> `O. 경고를 에러같이 취급할 것` 
> `O. Static Instance (Singleton) 의 사용을 자제할 것.` 
> `O. RTTI 및 Exception 을 쓰지 말 것.` 본문 갱신.
>
> 2018-11-27 PM 01:26 갱신.
> `문서 전체` 갱신. 더 이상 적용되지 않는 항목 삭제.
> `T. Operator overloading` 항목만 추가. 본문 작성을 해야함.

## Introduction

### In. Preface

규모가 있는 C++ 프로젝트를 진행하고자 할 때 정해진 규정을 따라서 코딩하는 것이 매우 중요합니다. 왜냐하면 프로젝트가 스케일업이 됨에 따라 관리해야 할 코드의 양도 많고 통일성을 유지해서 코드를 숙지할 수 있기 때문입니다. 그렇게 함으로써 추후에 발생하거나 발생된 버그, 성능 등을 보다 더 빨리 파악할 수 있게 됩니다.

`StudioN` 에서도 다른 라이브러리 등에 영향을 받지 않은 독자적인 C++ 프로젝트를 진행할 때나 혹은 다른 라이브러리에서 컨벤션 및 규칙이 엄격히 규정되어 있지 않았을 때에 사용할 수 있는 C++ 코딩 규칙을 만들고자 합니다. 그리고 C++ 코딩 규칙을 향후 그리고 지금 진행하고 있는 프로젝트에 적용을 해서 가독성과 통일성, 및 잠재적인 버그의 발견을 줄이고자 합니다.

### In. Prerequisites

이 코딩 스타일은 다음 라이브러리 혹은 관리 프레임워크를 쓸 때 완전히 적용됩니다.

* [**Doxygen**](http://www.stack.nl/~dimitri/doxygen/) : Generate documentation from source code, **Doxygen** is the de facto standard tool for generating documentation from annotated C++ sources.
* [**Git**](https://git-scm.com/) : **Git** is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency. 
* [**fmt**](http://fmtlib.net/latest/index.html) : **fmt** (formerly cppformat) is an open-source formatting library. It can be used as a safe alternative to printf or as a fast alternative to C++ IOStreams.

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

* **`2018-11-25` 기준, C++17 을 사용한다.**
  * 다만 `pmr` 그리고 `parallel STL` 은 사용하지 않도록 한다. 
    왜냐하면 `clang` 과 `gcc` 는 병렬 STL 의 기능들을 `<experimental/xxx>` 로 분류하고 있으며 아직까지 정식으로 편입되지 않은 상황이기 때문이다.
    (2018-11-25 확인 https://en.cppreference.com/w/cpp/compiler_support)
* **표준에 기재된 기능 외의 각 컴파일러마다 지원하고 있는 비표준 기능은 되도록 사용하지 않습니다.**
  * `module`, `_s` method 등 역시 **그냥은** 사용할 수 없습니다.
  * 만약에 굳이 사용하고자 하는 경우라면 다음과 같이 사용하자.
    1. 비표준 기능을 매크로로 감싼다.
    2. 매크로로 감싸는 동시에 각 C++ 컴파일러에 대한 전처리 매크로 플래그를 사용해 분기를 시킨다.

### O. 경고를 에러같이 취급할 것.

* **컴파일 도중에 발생하는 경고는 해당 코드에 대해서 잠재적인 에러 요소를 가지고 있다는 증거이다.**
  * 값 변환을 제대로 하지 않았을 때, 등등에서 경고가 발생할 수 있다.
* **되도록 표준 컴파일러를 채용해서 모든 경고를 출력할 수 있도록 하라.**
  * 만약 `MSVC` 와 `Visual Studio` 을 사용해서 개발을 할 경우에는 해당 프로젝트의 C++ 로그 메시지 출력을 가장 verbose 하게 출력할 수 있도록 설정하라.
  * `gcc` `clang` 의 경우 `-Wall` `-Werror` `-Wextra` 의 옵션을 붙여서 모든 경고에 대해 에러로 치환할 수 있다. 그리고 `--verbose` 을 사용해서 출력 로그를 자세하게 표시할 수 있다.

### O. Static Instance (Singleton) 의 사용을 자제할 것.

* `Constructor` 와 `Desctructor` 을 가지는 정적 인스턴스를 사용할 때는 항상 주의하며, 다른 방법이 있으면 다른 방법을 택하라.
* 정적 인스턴스의 참조나 포인터를 가지는 변수를 클래스 밖이나 함수 밖에서 설정할 때는 반드시 헤더 파일에서 `static` 을 붙여 변수를 설정하도록 한다.
* 정적 인스턴스들을 초기화 할 때는 프로그램의 시작과 끝에서 정적 인스턴스들끼리 한꺼번에 초기화를 행할 수 있도록 한다.
* 정적 인스턴스들의 `Constructor` 와 `Destructor` 안에서는 다른 정적 인스턴스들에 종속성이 없는 코드를 설정한다.
  * 부득이 한 경우에는 `assertion` 등을 사용해서 해당 정적 인스턴스가 초기화가 되었는가를 확인하는 코드를 맨 앞에 넣어 검증한다.
* 정적 인스턴스는 마이어스 싱글턴 혹은 `C++11` 에서 보증이 된 매직 스태틱 패턴을 사용해서 구현해야 한다.

### O. RTTI 및 Exception 을 쓰지 말 것.

* **`RTTI` 을 금지한다.** RTTI (Run-Time Type Information) 은 런타임에 타입 정보를 조회하는 C++ 의 기능이다. 하지만 RTTI 은 C++ 의 기본 원칙인 `Zero Overhead Principle` 에 전면적으로 위배된다. 즉 런타임 코스트가 상당량 든다.
  * 뿐만 아니라 `typeid()` 을 사용해서 가져오는 `RTTI` 정보의 참조는 같은 타입일지라도 다를 수가 있다.
  * 또한 C++ 에서는 해당 `RTTI` 로 가져오는 타입 정보에 대해 문자열 구문에 대해 구체적인 정보 없이 컴파일러 구현에 의존할 수 있도록 했다. 따라서 각종 컴파일러에서 프로젝트를 구동시키고자 할 경우에는 `RTTI` 의 사용이 금기시 된다.
  * `RTTI` 을 사용하지 않고도 컴파일 타임에 타입의 정보를 추측할 수 있는 기법이 존재한다.
* **`Exception` 을 쓰는 것을 금지한다.** 왜냐하면 현재 C++ 에 구현된 `Exception` 역시 `RTTI` 와 마찬가지로 C++ 의 성능 법칙에 위배되기 때문이다.
  * 일반 코드에서 예외를 사용해도 에러가 일어나지 않고 원하는 로직이 실행이 된다고 하면 이는 `try` `catch` 을 사용해 예외를 사용하지 않은 구문과 동일한 성능을 보장한다.
  * 하지만 `try` `catch` 을 사용해 예외를 사용해서 에러 핸들링을 시도하면, `Stack unrolling` 이라고 하는 예외에 대해서 해당 예외를 처리할 수 있는 `catch` 구문이 있는가를 스택을 뒤집어가면서 확인하고자 한다. 이로 인해 상당량의 성능 저하가 일어나버린다.
  * 또한 `try` `catch` 를 사용해 에러 핸들링을 한다고 한들, 해당 에러가 복구 가능한 에러인지 복구 불가능한 에러인지를 파악하기 힘들다. 예외 안정성 역시 보장할 수 없다.
    * **모든 `Exception` 은 `Error` 라고 한정지을 수 없다..**
  * `Constructor` 에서의 에러 핸들링은 `assertion` 으로만 한정한다. 만약 예외 사항이 일어날 수 있다면 `Constructor` 가 있는 타입을 생성하기 전에 검증 코드를 직접 거치도록 한다.
    * `Destructor` 의 경우에는 **반드시 성공** 해야하기 때문에 검증 코드를 따로 만들지 않아도 된다.
* 에러 핸들링에 관해서 추천할 만한 라이브러리는 `boost::outcome` 이 있다. (실 사용은 아직 X)
  https://ned14.github.io/outcome/
* 기존 `STL` 의 경우에는 위 사항을 예외로 한다. 왜냐하면 `STL` 은 `try` `catch` 와 같은 예외 핸들링을 적극적으로 쓰기 때문이다. 혹여나 싶으면 옵션으로 익셉션을 사용하지 않도록 해서 `STL` 을 쓰게 할 수도 있다.

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
* **디버깅용으로 상당량의 코드를 주석 처리 할 경우에는, `#ifdef false` 와 `#endif` 을 써서 코드를 무효화한다.**
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
  * 코드를 구간 별로 분리할 경우 `//!` 계열의 주석을 써도 좋다.

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

* **Non-obvious 한 Class 및 Method 는 해당 객체의 역할, 동작 방법에 대해서 `doxygen` 으로 설명해야 한다**
  * *method* 의 경우에는 해당 함수의 전반적인 동작에 대해서 설명한다. `.h` 파일의 선언 부분에서 전반적인 설명을 하며 구현에서 까다로운 부분, 혹은 테크닉을 쓴 경우에는 `.cc` 에서 별도로 설명한다.

### C. File Header

* Include Guard 을 쓸 때 `#pragma once` 을 사용하지 않는다.

* **프로젝트에 적용되고 있는 라이선스가 있을 경우에는, 원문 혹은 요약문을 맨 위에 붙인다.**

> Every file should contain license boilerplate. Choose the appropriate boilerplate for the license used by the project (for example, Apache 2.0, BSD, LGPL, GPL).
>
> ~ Legal Notice and Author Line from Google C++ Style Guide

**Good**

``` c++
///
/// @license BSD 2-Clause License
/// Copyright (c) 2018, Jongmin Yun(Neu.), All rights reserved.
/// If you want to read full statements, read LICENSE file.
///
```

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

* **코드의 모든 줄의 텍스트 수는 최대 `120` 컬럼 안에서 작성되어야 한다.**
  * 코드는 여러 곳에서 보여지고 쓰여질 수 있기 때문에 최대 한계를 고려해야만 한다.
  * 이전에는 80 컬럼이었지만 변수의 이름을 통한 코드 읽기 및 보다 더 정확한 구문 지시 등을 고려 시 120 컬럼이 가장 적당하다고 생각함.
  * 현재 거의 모든 개발 환경은 해상도가 `1920x1080` 이기 때문에 최대 120 컬럼을 지원한다고 생각함.  
* **코드의 모든 줄의 텍스트 수는 되도록 `100` 컬럼 안에서 작성하는 것이 바람직하다.**

* 주석에 URL 등이 포함될 경우에는 해당 URL 이 들어간 줄에 한해 120 글자 이상을 허용한다.

### F. Must use `spaces` intead of tab

* **Indent 는 오직 Space 만 사용하며, 2 space 을 사용한다.**
  * `tab` 키를 누를 때, 2 space 을 띄우게끔 설정하는 것도 좋다.
  * 컬럼의 제약을 맞추기 위해 ~~4 space~~ 대신 2 space 을 사용한다.
* 코드 수정 도중에 스타일을 따르지 않는 코드를 수정할 때에는 전체 소스 파일을 반영하지 않도록 해야하며 오직 수정하고자 하는 부분의 최소 단위만 수정한다.
  * it makes for incredible diffs that are absolutely worthless.


### F. Function Declaration and Definition

* **Return type 은 함수 시그니쳐의 같은 줄에 오게 함이 바람직하다.**
  * 만약 속성을 포함한 Return type 이 길 경우에는, 함수 이름과 인자를 적는 줄의 윗줄에 적는다.
* Return type 을 반환한 경우에는 `[[nodiscard]]` 을 **반드시 적어야 한다.**
  * `[[nodiscard]]` 는 C++17 에서 추가된 속성으로 함수에서 반환된 객체를 반드시 받아서 처리해야 한다고 컴파일러에게 알린다. 만약 `[[nodiscard]]` 가 적용된 함수에서 반환된 객체를 바깥에서 처리하지 않을 경우에는 컴파일 에러가 발생한다.
    * 이를 통해 잠재적인 버그가 일어날 수 있는 가능성을 줄여준다.
* **인자 (parameter) 리스트는 함수 시그니쳐의 같은 줄에 오도록 한다. **
  **만약 그러지 못할 경우에는 다음 줄에 오게 한다.**
  * 길어서 정렬 조차 못할 경우에는, 모든 인자를 4 indent 을 사용해서 정렬하게 할 수 있다.

**Good**

``` c++
ReturnType ClassName::FunctionName(Type par1, Type par2)
{
  DoSomething();
}
```

``` c++
ReturnType ClassName::LongLongFunctionName(Type par1, Type par2, 
                                           Type par3)
{
  DoSomething();
}
```

``` c++
ReturnType ClassName::SoMuchLongLongFunctionName(
    Type par1,
    Type par2,
    Type par3)    // 4 line indent
{
  DoSomething();  // 2 line indent
}
```

* 인자 이름은 정확한 역할 및 의도를 가져야 한다.
* 함수 정의에서 쓰이지 않는 인자는 **정의에서 생략될 수 있다**.
* `()` 괄호의 여는 괄호는 항상 함수 이름 옆에 와야하며, 띄어쓰기를 금한다.
* 소스 파일의 해석 단위 안에서만 쓰이는 함수를 선언하거나 정의할 경우에는 **반드시 무명 네임스페이스 안**에서 정의한다.
* 함수로 전달되는 모든 인자들은 값이거나 레퍼런스여야 한다. `*` 의 사용은 권장하지 않는다. 이는 인자로 넘겨준 레퍼런스가 변경이 될 때에도 변함없다.
  - 불필요한 null check 을 없애기 위해서.
  - 레퍼런스로 전달되는 인자 타입은 `const` 로 수식하는 것을 권장한다.
* 매크로를 사용해서 `_MIN_` `_MINOUT_` `_MOUT_` 과 같이 인자의 용도를 설정해 두는 것도 바람직할 것이다.

``` c++
void Foo(_MIN_ const std::string& in, _MINOUT_ std::string& out);
```

### F. Lambda Expression

> [Closure (computer programming)](https://en.wikipedia.org/wiki/Closure_(computer_programming))

* `Lambda` 는 클로져를 생성할 수 있는 `C++11` 부터 추가된 문법 기능 중 하나이다.
* 재귀를 목적으로 한 `Lambda` 함수의 구현은 금지한다. (`Lambda` 는 시그니쳐가 정확하지 않기 때문에 재귀가 불가능하지만, 여러 가지 편법을 통해 재귀를 구현할 수 있도록 할 수는 있다.)
* `Lambda` 가 자기 완결적임이 확실한 경우에는 캡쳐를 `this` 혹은 `&` 로 쓸 수 있다. 하지만 자기 완결적이지 않다면 **반드시 `=` 을 써서 값을 복사하도록 하거나, 아니면 `C++14` 에서 추가된 다음 구문을 사용해 캡쳐를 한다.**

``` c++
int x = 4;
auto y = [&r = x, x = x + 1]() -> int
{
    r += 2;
    return x * x;
}(); // updates ::x to 6 and initializes y to 25.
```

* 코드 구조를 리팩터링할 때 혹은 코드 구문의 캡슐화를 실시할 때, 캡슐화된 코드 구조를 바깥에서 접근하게 하고 싶게 하지 않다면 `Lambda` 을 써서 함수 본문에 설치하는 것도 좋은 방법이다.
  * 다만 이 경우에는 `static` 을 반드시 써야한다.

### F. 함수 호출 (Function Call)

* **자리가 충분한 경우 한 줄에 쓰고, 그렇지 않은 경우 괄호 안의 인자들을 줄바꿈한다. **
* **인자들이 모두 한 줄에 들어갈 수 없다면, 여러 줄로 나누어 쓰되 이어지는 줄은 첫번째 인자와 같은 열에 오도록 하는 것을 추천한다.**
* **인자들이 모두 한 줄에 들어갈 수 없다면, 여러 줄로 나누어 쓰되 이어지는 줄은 해당 표현식에서 4 space 들여쓰기 해서 올 수 있다.**
* **모든 인자들마다 줄 바꿈하여 한 줄에 하나씩 쓸 때는, 해당 함수로부터 4 space 들여쓰기 해서 쓴다.**
  * 혹은 세로 줄을 낭비하는 것을 줄이기 위해 여러 인자를 한 줄에 집어넣는 것을 추천한다. 

**Good**

``` c++
EFlag retVal = DoSomething(argument1, argument2, argument3);
EFlag retVal = DoSomething(argument1, argument2,
                            argument3);
EFlag retVal = DoSomething(
    argument1,
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
int myHeuristic = scores[x] * y + bases[x];
EDoFlag result  = DoSomething(myHeuristic, x, y, z);
```

### F. 중괄호 방식

* **`{}` 블록 괄호의 여는 괄호는 항상 함수 정의 시그니쳐, 클래스 정의, 조건 분기문의 다음 줄 시작 지점에 와야한다.**
  * 만약 구문이 짧아서 한 줄로 해당 구문의 정의 및 코드 작성을 마칠 수 있다고 하면 `{}` 을 포함해서 한 줄에 옮긴다.

``` c++
class FClass final
{
    // LIKE THIS
};
```

``` c++
if (expression == false) { assert(false); }
```

### F. 중괄호 초기화 리스트 (Braced Initializer List)

* **함수 호출과 동일한 방식으로 쓴다.**

### F. 조건 분기문 (Conditional branch)

* 조건 표현식은 무조건 `== true` 혹은 `== false` 또는 `== Enum` `== nullptr` 로 끝내야 한다. 일반 타입에서 `bool` 타입으로 자동으로 변환되어 참비여부를 계산하는 것을 막아 가독성을 증가시키고 버그를 막을 수 있기 때문이다.

``` c++
// Do like this.
if (mPtrInstance == nullptr) { return; }
// Do not this.
if (!mPtrInstance) { return; }
```

* **조건 표현식은 `()` 양 옆 사이에 띄어쓰기가 없어야 한다.**
* **`else` `else if` 구문을 적을 때는 다음과 같은 형식을 따른다.**

``` c++
if (condition == true) 
{
  // ...
} 
else if (...) 
{
  // ...
} 
else 
{
  // ...
}
```

* **짧은 조건 분기문은 가독성을 높이기 위해 한 줄로 붙여 쓸 수 있다. `else` 가 올 경우에는 두 줄로 써야하며, 만약 분기문 중 하나가 길어질 경우에는 원래 조건문 포맷을 따라야 한다.**
  * 모든 `then` 구문과 `else` 구문은 무조건 `{}` 을 써서 해당 구문이 `then` 이거나 `else` 임을 나타내야 한다.

**Good**

``` c++
if (x == EEnum::Foo) { return new Foo(); } // 1
if (x == EEnum::Foo) { return new Foo(); } // 2
else                 { return new Nop(); }
if (x == EEnum::Foo) { return new Foo(); } // 3
else 
{
  // ...
}
```

~~**Bad**~~

``` c++
if (x == EEnum::Foo) return new Foo(); 
else                 return new Nop();
if (x == EEnum::Foo) { return new Foo(); } else { return new Nop(); }
if (x == EEnum::Foo) return new Foo();
else {
  ...
}
```

* **에러 출력 혹은 즉각 return 구문이 있는 절을 if 로 분기를 나눠서 쓰고자 할 때는, `then` 구문에 에러 혹은 처리 후 리턴 (early return) 을 쓸 수 있도록 한다.**

``` c++
[[nodiscard]] float GetKeyValue(const std::string& key) 
{
  NEU_ASSERT(m_initiated == EInitiated::Initiated, debug::err_input_not_initiated);

  if (IsKeyExist(key) == EKeyExist::NotExist) 
  {
    PUSH_LOG_ERRO("fuck");
    return 0.f;
  }

  return m_key_inputs[key].value;
}
```

### F. 루프문 (Loops)

* 루프문도 `{}` 의 사용을 강제한다.
* **빈 루프 구문은 `{}` 빈 중괄호 구문 혹은 `continue` 을 사용해야 한다.**
  * `;` 혹은 `{}` 을 사용하게 되면 해당 구문이 어떤 용도로 쓰이는 지 파악이 어려울 뿐더러 가독성이 저하된다.

``` c++
while (condition) {} // Repeat test until it retuens false.
while (condition) { continue; }

for (int i = 0; i < gk_some_number; ++i) {}
for (int i = 0; i < gk_some_number; ++i) { continue; }
```

### F. 스위치문 (Switch)

* **`switch` 구문의 `case ` 블록은 중괄호를 쓸 수도 있고 쓰지 않을 수도 있다. 만약에 중괄호를 쓴다면 여는 괄호 `{` 는 `case` 블록의 밑 줄에 둔다**
* **의도적인 *Fall through* 을 사용하는 case 는 마지막 줄에 속성 `[[fallthrough]]`[link](http://en.cppreference.com/w/cpp/language/attributes) 을 붙인다.**

``` c++
switch (x) {
  case 41:  // space 2 indent
    ...     // space 4 indent
    [[fallthrough]]
  case 43:
   	...
    break;
  default: assert(false); break;
}
```

* **열거형 타입을 `case` 로 하는 `switch` 문이 아닐 경우, `switch` 문은 항상 `default` 케이스를 구현해야 한다. **
  * 일반 동작에서 `default` 케이스가 실행이 안된다고 설계한 경우에는 `default` 에서 바로 에러를 띄우도록 해야한다.

``` c++
switch (x) {
  ...
  default: assert(false);
}
```

### F. Pointer and Reference Expressions

* **`.` 혹은 `->` 연산자 뒤, 앞에 공백이 있어서는 안된다.**
* **`*` 혹은 `&` 는 `type`의 바로 뒤에 붙인다. **

``` c++
char* c;
const std::string& str;
```

### F. 불리언 표현식 (Boolean Expression)

* 만약 불리언 표현식의 전체 길이가 허용 글자 수보다 길다면  줄을 나눠서 써라.
  나눠서 쓰는 경우에는 4 space indent 을 사용한다.
  * 불리언 표현식의 각 조건문을 나눌 때는 아래의 예시처럼 `&&` 및 `||` 을 각 구문의 앞에 오도록 한다.

``` c++
if (this_one_thing > this_other_thing 
    && a_third_thing == a_fourth_thing 
    && yet_another == true
    && last_one == false) 
{
  // ...
}
```

### F. 변수 및 배열 초기화 (Variable and Array Initialization)

* **uniform initialization `{}` 을 가급적 사용한다.**
  * *Uniform initialization* 은 Narrowing Conversion 을 컴파일 타임에서 막기 때문에 사전에 에러를 방지한다.
* **생성자에 std::initializer_list 가 인자로 들어갈 경우에는 `{}` 중괄호 초기화 대신에 `()` 을 사용한다.**
* 구조체 타입의 멤버 변수, `static` 을 포함한 모든 변수는 기본값으로 초기화 해야한다.

``` c++
std::vector<int> v(100, 1);  // A vector containing 100 items: All 1s.
std::vector<int> v{100, 1};  // A vector containing 2 items: 100 and 1.
```

### F. 네임스페이스 (Namespace)

* **네임스페이스 안의 내용들은 들여쓰기를 하지 않는다.**
* **네임스페이스를 끝낼 때, `}` 닫는 중괄호 뒤에 해당 네임스페이스의 계층 구조를 주석으로 남긴다.**

* **C++17 에서 추가된 중첩 네임스페이스 기능은 파일 당 하나의 네임스페이스의 내용을 구현할 때만 사용한다.**

``` c++
namespace nm_a::nm_b::nm_c 
{
  ...
} /// ::nm_a::nm_b::nm_c namespace
```

* 무명 네임스페이스는 `.cc` 소스 파일의 맨 윗단에서만 정리해서 사용한다.

## Header file

### H. File type specifier

|           | 확장자 |
| :-------- | :----- |
| 헤더 파일 | `.h`   |
| 소스 파일 | `.cc`  |

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

프로젝트에 정식으로 포함되지 않을 임시 파일에는 `#pragma once` 을 쓸 수 있다. 다만 실제 프로젝트에 반영할 때는 위의 사항을 지켜야 한다.

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
2. C 표준 헤더 파일
3. C++ 표준 헤더 파일
4. Third-party 라이브러리 헤더 파일
5. 현재 프로젝트에서 가져올 부수적인 헤더 파일

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

*  **C++ 에서의 C 표준 파일은 C++ 표준 헤더로도 바꿀 수 있으나, 일관성을 유지할 것.**

어떤 파일에서는 `stdio.h` 을 인클루드 하고, 어떤 다른 파일에서는 `cstdio` 을 포함하는 행동은 하지말 것.

* **위의 인클루드 순서를 유지하면서, 알파벳 이름 순으로 인클루드 하라.**
* `#include` 시에 `.cc` 파일의 인클루드는 하지 않는다.
  * **헤더 파일만 include 한다.**

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

* 헤더 파일과 소스 파일이 있을 때, 소스 파일에서 다른 헤더 파일에 존재하는 타입의 정보를 사용해 쓰지만 헤더 파일에서는 그렇지 않고 참조 혹은 포인터의 형태로만 취급한다고 하면 **헤더 파일에서만 전방 선언**을 사용한다.
* 만약 함수의 전방 선언이 필요할 경우에는 `.cc` 소스 파일의 맨 윗단에서 선언을 한다.

## Naming

### N. File Names

* 파일 이름은 첫 글자가 대문자인 **Camel case** 을 사용한다.

``` c++
MyUsefulClass.cc   // Good
my_useful_class.cc // Bad
```

* **파일 이름을 작성할 때는 이름을 구체적으로 작성한다.**

``` c++
HttpServerLogs.h // Good
logs.h           // Bad
```

* **어떤 파일 안에 하나의 타입 정의만 있을 때는 해당 타입 이름으로 파일 이름을 작성하는 것을 추천한다.**

### N. Type Names

* **정의된 모든 타입 이름들은 영어 명사로 끝내야 하며, 대문자로 시작한다**.
  * 타입 이름은 `struct` `class` `union` `enum class` `enum` 등이 있다.
  * `::std` 에서 제공된 타입 이름은 이 규칙을 무시한다.
* **Using 혹은 Typedef 을 사용한 Aliasing 역시 대문자로 시작해야 한다.**
  * `::std` 을 사용한 타입 별칭에도 해당 규칙을 적용한다.

**Good**

``` c++
class  CTable { ... };
class  CTester { ... };
struct CProperty { ... };
struct DTableElementInfo { ... };

typedef std::map<const char*, UrlTableProperties*> TPerpertiesMap;
using TPropertiesMap = std::map<const char*, UrlTableProperties*>;

enum class EUrlTableErrors { ... }; 
```

* **만일의 경우, 해당 프로젝트를 사용하는 제 3 자가 만들어낼 임의 타입과 구분을 지어야 한다면, `C` `E` `D` `M` 등과 같은 prefix 을 타입 이름의 앞에 넣을 수 있다.**
  * `C, F` : 일반 Class. 정보 외에 동작을 넣을 수 있다.
  * `D` : POD 혹은 연산 없이 자료 정보만을 가지고 있는 타입.
  * `E` : 열거형 타입. `enum` `enum class` 둘 다 쓰일 수 있다.
  * `I` : Interface 타입.
  * `U` : Union 타입.
  * `T` : 별칭 타입 혹은 템플릿 타입
  * `R` : 리플렉션 구현체 타입
  * `M` : 싱글턴 (static instance) 타입
* **prefix 을 기입 시, 대문자로 기입한다.**

### N. Variable Names

* **모든 변수의 이름은 `m` `s` `k` `g`로 시작하는 카멜 케이스 혹은 일반 카멜 케이스를 따른다.**
* 모든 구조체 타입의 멤버 변수의 경우, 각 함수의 로컬 변수와 구분하기 위해 `m` 을 해당 변수의 앞에 기입한다.
* `static` 변수의 경우에는 항상 `s` 을 붙인다.
* `constexpr` (C++17) `consteval` (C++20) `static const` 으로 지정된 경우에는 항상 맨 앞에 `k` 을 붙인다.
  * 만일 `static` 과 `const` `constexpr` 상수성이 같이 올 경우에는 `k` 만을 덧붙인다.
* 최소 namespace 의 범위를 가지는 Global variable 은 `g` 을 사용한다.
* `this->` 의 사용을 지향한다. 
  컬럼 허용량이 120자로 늘었기 때문에 `this->` 을 사용하는 것이 가독성에는 오히려 더 도움을 준다.

**Good**

``` c++
class DDatabaseItem final
{
public:
  // Getter Setter.
private:
  std::string mName  = {};
  uint32_t    mValue = 0;
};

class CDatabase final
{
public:
  // ...
private:
  using TInformationMap    = std::unordered_map<std::string, DDatabaseItem*>;
  static bool sIsFlaged    = false;
  
  TInformationMap mInfoMap = {};
};
```

### N. Function Names

* **보통 함수의 이름은 대문자로 시작하는 Camel case 로 작성한다.**

``` c++
AddTableEntry();
DeleteUrl();
OpenFile(kFilePath);
```

* **멤버 함수의 Getter / Setter 은 `Get` `Set` 전부 붙여서 쓴다.**
  * 현재 스타일에서는 타입 이름이 대문자 Camelcase 을 따르기 때문에, 혼동을 주지 않기 위해서 붙인다.

``` c++
class Information final 
{
public:
  [[nodiscard]] std::string GetName() const noexcept { return this->mName; }
  void SetName(const std::string& name) noexcept { this->mName = name; }
  void SetName(const char* name) noexcept { this->mName = name; }
private:
  std::string mName = "";
}
```

* `private` 혹은 `protected` 인 멤버 함수의 이름은 `p` 을 `prefix` 로 붙인다.
* `friend` 된 클래스 혹은 함수에서 호출할 가능성이 있는 함수의 이름은 `f` 을 붙인다.
  * 만약 `private` 이고 `friend` 타입에서 호출할 가능성이 있는 함수의 prefix 는 `pf` 가 된다.

### N. Namespace Names

* 네임스페이스 이름은 10 글자 이내의 소문자로만 구성한다.
* 가장 최상위 네임스페이스는 항상 프로젝트의 이름 혹은 프로젝트의 축약어일 것.
* `std` 네임스페이스를 범위로 하는 사용자 정의 타입 및 중첩 네임스페이스를 만들지 말 것.

### N. Enumerator Names

* 열거형 요소들의 이름은 대문자를 시작으로 하는 Camel case 로 작성한다.

**Good**

``` c++
enum class EUrlTableErrors 
{
  Ok = 0,
  ErrorOutOfMemory,
  ErrorMalformedInput
};
```

~~**Bad**~~

``` c++
enum class EUrlTableErrorsNotProper 
{
  OK = 0,
  ERROR_OUT_OF_MEMORY,
  ERROR_MALFORMED_INPUT
};
```

### N. Macro Names

* **대문자 및 `_` 만으로 이루어진 이름으로 작성한다.**
  * 매크로임을 확실하게 하기 위해 `M_` 과 같은 prefix 을 쓰는 것도 권장함.

``` c++
#define M_ROUND(x) ...
#define M_PI_ROUNED 3.0
```

## Other C++ Features

### T. 고정 사이즈 정수형 타입 (`cstdint`) && `size_t`

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
* 고정형 타입에 별칭을 적용해 사용할 것.

* 인덱스 값 저장 혹은 인덱스 참조를 위한 일부 계산식을 제외하고는 **`size_t` 를 일반 변수형과 같이 섞어 사용하지 않는다.**
  * 왠만해서는 사용하지 않으려고 하자.

### T. Operator overloading

> Under construction...

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
  if (ResourceHandle tmp = getSentinelResource(); tmp != nullptr) 
  {
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
  * 코드 읽는 양이 늘어날 수 있다.
* **유저에게 명확하게 bool 플래그를 나타내고 싶을 경우에는 의도적으로 bool 플래그를 쓸 수 있다.**

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

### T. Assertion

* **적극 사용한다. 하지만 단언문 (assert) 은 릴리즈 모드에서는 무효화되기 때문에 검증 표현식에서 프로그램의 행동을 바꾸는 구문은 쓰지 말아야 한다.**

**Good**

``` c++
assert(1 == 1)
```

~~**Bad**~~

``` c++
assert(value == i++)
```

* **기본으로 제공되고 있는 assert(expression) 은 메시지를 출력할 수 없기 때문에 메시지 출력이 가능한 이식성 있는 assert 을 만들어서 쓴다.**

### T. 문자열 타입

* `char` `std::string` 이외의 다른 타입을 사용하지 않는다.
* 만약 특정 라이브러리에서 다른 문자열 타입을 원할 경우에는 그 상황일 때만 사용한다.
* `char16_t` `char32_t` 및 `wchar_t` 을 사용하지 않는다.
* 왠만해서는 `std::string` 을 쓴다.

## Et cetera

### E. 64-bit Portability

* C++ 프로젝트의 코드는 64-bit 의 호환성을 기준으로 개발되어야 할 것.

### E. Error checking

* **에러 체킹은 발생 즉시 처리해야만 한다. **
  **따라서 `.IsError()` `.ErrorCheck()` 이 에러 발생 이후에 임의 시점에서 뒤늦게 핸들링을 하는 것은 금지한다.**
* 개발은 항상 디버그 모드에서 하며, 실행 중 에러로 인해서 앞으로의 처리에 이상이 올 경우에는 주저없이 **assert 등을 사용해서 동작을 멈춘다.**

