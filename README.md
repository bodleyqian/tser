# Tser
A TypeScript virtual machine.

Switch Language: [中文](./README_zh.md).

#### Install and Usage
```bash
$ brew tap tser-project/tser && brew install tser;

$ tser ./input.ts;
```

## Why create Tser?
TypeScript (TS) is a greate invention, for letting us have a statically typed language development experience while reusing the JS ecosystem.
TS is essentially a pre-compiled language, executed using the JS virtual machine after compiled into JS. Due to its strong dependence on JS, it cannot get rid of some of the stubborn problems of JS, such as execution efficiency. TS itself is a statically typed language with certain data type declaration, but the type declaration is lost when escaping to JS; if we can directly execute the TS program instead of first escaping to JS and then executing, the data type declaration can Brings a great performance improvement to the program.

Let’s look at a set of performance comparison data, only comparing the performance of `fib(42)` in each virtual machine or language (It cannot be completely regarded as a performance evaluation standard. Test conditions: the same device in the same state, none of the compilation processes usded optimization).


| Language         | Virtual Machine  | Time Cost (ms)     |
| ----             | ----             | ----               |
| TypeScript       | deno             | 4150               |
| JavaScript       | v8 / node        | 3859               |
| TypeScript       | Tser             | 2035               |
| C++              | --               | 2106               |


TS technology has developed rapidly, and more and more projects in the web industry have been developed and refactored using TS. When we thinking about the development of TS technology aside, where will it end? Will it stay on a pre-compiled language? When the TS ecosystem develops more and more sound, is it necessary to rely entirely on the JS ecosystem? Will there be a real TS virtual machine (Deno is not)? If the industry has a stable and high-performance TS virtual machine, is it a good thing for the TS ecosystem, and will it push TS to a new level?


*<u>TS may never be able to replace the JS ecology, but in some areas, TS can exist independently of the JS ecology; the TS virtual machine is the cornerstone of the independent TS ecology, allowing TS to have significantly better runtime performance than JS in these areas, and Bring real business benefits to these areas.</u>*

## What can Tser do? 
Tser has relatively low compilation performance and high runtime performance, which is more suitable for scenarios such as independent background services and serverless. When the grammar support is complete, and most of the existing background services written in TS can be run, then these services can get a great performance improvement.

Tser can do very little at present, because the syntax support is very imperfect, and can only support the execution of some simple scripts, for example, it may be able to support some simple cloud function scenarios.

## Tser technology principle
The Tser's front-end relies on the syntax parser generated by Antlr, and then generates and traverses the syntax tree; the back-end relies on LLVM construction, compiling TS code into LLVM IR, and using its JIT engine to immediately execute IR.

Similar products: [assemblyscript](https://www.assemblyscript.org/)、[StaticScript](https://github.com/ovr/StaticScript).

## Tser syntax support
> Refer to the [__test](./__test/) file in the directory

### Variable
Support `var` `let` `const`, `var` is the same as `let`.

### Basic data type
| Data Type        | Bytes (in 64bit)         | Supported       |
| ----             | ----                     | ----            |
| boolean          | 1                        | ✔️               |
| number           | 4                        | ✔️ Same as int32        |
| int32            | 4                        | ✔️               |
| int64            | 8                        | ✔️               |
| float            | 4                        | ✔️               |
| double           | 8                        | ✔️               |
| string           | --                       | ✔️ Not support operation     |

### Operations
| Operator                  | Supported               |
| ----                      | ----                    |
| `+` `-` `*` `/` `%`       | ✔️                       |
| `++` `--`                 | ✔️                       | 
| `+=` `-=` `*=` `/=` `%=`  | ✔️                       |
| `<` `>` `<=` `>=`         | ✔️                       |
| `==` `===` `!=` `!==`     | ✔️ `===` and `==` are the same currently |
| `&&` `||`                 | ✔️                       |
| `!`                       | ✔️                       |
| `? :`                     | ✔️                       |
| `()`                      | ✔️                       |
| `.`                       | ✔️                       |

### Logic control statement
| statement                 | Supported                |
| ----                      | ----                    |
| `if else`                 | ✔️                       |
| `while` `do while`        | ✔️                       | 
| `for`                     | ✔️                       |
| `switch`                  | ✔️                       |
| `continue`                | ✔️                       |
| `break`                   | ✔️                       |

### Function
Support most parts of function, support nested functions, closure and function-as-parameter are temporarily not supported.

Example reference: [__test/basic/function](./__test/basic/7_function.ts)

### Class
Class supports inheritance, polymorphism, class combination, supports static properties and static methods, and does not support method overloading; Class inheritance and polymorphism are supported in a manner similar to virtual table, and polymorphism supports methods and attributes.

Example reference: [__test/class/](./__test/class/)

### Built-in objects
The current built-in object support is very low and is only used for testing.

| Built-in objects          | methods                              |
| ----                      | ----                                 |
| `console`                 | `debug` `log` `info` `warn` `error`  |
| `Date`                    | `now`                                | 

### Not supported currently
`module` `Built-in objects`、`Event Loop`、`GC` etc.

## Contribute to Tser
Tser is a huge project and it is really difficult to complete it by one person. Now Tser is still a baby. Hope it can play a role of attractor which can gather some people with real abilities for better development.

### Environmental setting
1. Install `cmake`、`make`
2. Install [LLVM](http://clang.llvm.org/get_started.html) 
3. Install [Antlr](https://www.antlr.org/download/)
4. Environment variable configuration: 
    ```bash
    # Clang
    export CC=/usr/bin/clang
    export CXX=/usr/bin/clang++

    # LLVM
    export PATH="/Users/vifird/C/compile/llvm-project/build/bin:$PATH"

    # Antlr
    export ANTLR_RUNTIME=${Path of runtime}/runtime/usr/local
    alias antlr4='java -jar /usr/local/lib/antlr-4.8-complete.jar'
    alias grun='java org.antlr.v4.gui.TestRig'
    export ANTLR_EXECUTABLE=/usr/local/lib/antlr-4.8-complete.jar
    export CLASSPATH=".:/usr/local/lib/antlr-4.8-complete.jar:$CLASSPATH"
    ```

### Build command
- `make build-debug` : Build debugging package (Recommends using Debug mode to build LLVM while debugging)
- `make build` : Build a production package
- `make build-builtin` : Build built-in objects
- `make build-parser` : Regenerate the parser
- `make grun` : Visually view the syntax tree generated by TS code

## Donate to support
<a target="_blank" href="https://github.com/lanistor/assets/blob/master/tser/support_tser_zfb_en.jpg">Donate to Tser</a>, to support the author to continue to create Tser.

