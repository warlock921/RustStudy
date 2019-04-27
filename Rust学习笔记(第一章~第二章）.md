# Rust 学习笔记

## 第一章

### Rust 如何做好版本更新及开篇

* **Rust** 是一门系统编程语言，它的三大特点：**运行快、防止段错误、保证线程安全** 

* **Rust** 的编译器使用语义化版本号可以分为：
  * 主版本号：做了不兼容的**API**修改
  * 次版本号：做了向下兼容的功能性新增
  * 修订号：做了向下兼容的问题修正

* 为了兼顾更新速度以及稳定性，**Rust** 使用了多渠道发布的策略：
  * nightly 版本
    * 每天在主版本自动创建的版本，功能最多，更新最快，但某些功能存在问题的可能性大
  * beta 版本
    * 此版本每隔一段时间，将一些nightly版本中验证过的功能开放给用户使用
  * stable 版本
    * 此为正式版本，每隔6个星期发布一次，最稳定、可靠的版本
  * **RFC** -> **Nightly** -> **Beta** -> **Stable**
    * **Rust** 语言每个相对复杂一点的新功能，都要经历以上步骤才算稳定可用

* 更新 rustup 之前，国内用户设置好以下环境变量再使用：

  ```bash
  export RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static
  ```

  ```bash
  export RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup
  ```

  

* 更新 cargo 工具链还需要添加配置：

  * 在  /home/.cargo 目录下创建一个名为config的文本文件，内容如下：

    ```bash
    [source.crates-io]
    registry = "https//github.com/rust-lang/crates.io-index"
    replace-with = 'ustc'
    [source.ustc]
    registry = "git://mirrors.ustc.edu.cn/crates.io-index"
    ```

    

* **Rust** 更新

  ```bash
  // 更新 rustup 到最新
  rustup self update
  // 更新 rustup 编译器到最新的 nightly 版本
  rustup update nightly
  // 安装RLS
  rustup component add rls --toolchain nightly
  rustup component add rust-analysis --toolchain nightly
  rustup component add rust-src --toolchain nightly
  // 查看 rustc 的版本
  rustc -V
  ```

  

* **Hello World**

  ```rust
  // hello_world.rs
  fn main() {
      let s = "hello world!";
      println!("{}", s);
  }
  // fn 是rust定义函数的关键字
  // main函数是可执行程序的入口点，无参数，无返回值
  // let 关键字用于定义变量
  // println!() 这是rust的标准输出宏，注意println后面的感叹号，它代表这是一个宏，而不是一个函数。
  // Rust中的宏与C/C++中的宏是完全不一样的东西
  // 每条语句都需要用;结束
  ```

  ```bash
  //编译 hello_world.rs 文件
  rustc hello_world.rs
  //执行 hello_world
  ./hello_word
  ```

  

* **Prelude**

  * **Rust** 的代码从逻辑上是分 crate 和 mod 管理的，crate - 项目，crate 内部则由  mod 管理

  * **Rust** 有一个极简标准库，叫作 std，除了极少数嵌入式系统无法使用标准库，绝大部分情况下，都需要使用标准库里的东西

  * 默认情况下，用户不需要导入标准库，编译器会自动引入，系统会在每个 crate 开头自动插入一句话：

    ```rust
    use std::prelude::*
    ```

    

* **Format** 格式详细说明：

  ```rust
  fn main() {
      println!("{}", 1);// 默认用法，打印 Display
      println!("{:o}", 9);// 八进制
      println!("{:x}", 255);// 十六进制 小写
      println!("{:X}", 255);// 十六进制 大写
      println!("{:p}", &0);// 指针
      println!("{:b}", 15);// 二进制
      println!("{:e}", 10000f32);// 科学计数（小写）
      println!("{:E}", 10000f32);// 科学计数（大写）
      println!("{:?}", "test");// 打印 Debug
      println!("{:#?}", ("test1", "test2"));// 带换行和缩进的 Debug 打印
      println!("{a} {b} {b}", a="x", b="y");// 命名参数
  }
  ```

  Rust中还有一系列的宏，都是用的同样的格式控制规则，如 format! write! writeln!等

  Rust标准库中设计这些宏来做标准输出，主要是为了更好地做错误检查。
  
  

------



## 第二章

### 变量声明

* 常用声明语法

  ```rust
  let variable : i32 = 100;
  // variable 是变量名，变量类型一定跟在冒号 : 的后面
  // 此方法声明的变量是只读的，如果重新给变量赋值，则无法编译通过，会报错
  ```

  ```rust
  let mut x : i32 = 5;
  // 使用mut关键字声明变量，则表明该变量可写
  // 还可以这么做：
  let (mut a, mut b) = (1, 2);
  ```

  *Rust中， 每个变量必须被合理初始化之后才能被使用。使用未初始化的变量会使编译器报错(利用 unsafe 做 hack 除外)*

  *Rust里的合法标识符（包括变量名、函数名、trait 名等）必须由数字、字母、下划线组成，且不能以数字开头，未来rust可能允许其他 Unicode 字符做标识符*

  <u>*可以使用 r#self ，让用户可以以关键字作为普通标识符，这只是为了应付某些特殊情况，平常最好不要用这种方法*</u>

  

### 变量遮蔽

* **Rust** 允许在同一个代码块中声明同样名字的变量。如果这样做，同名的变量会将前面声明的变量遮蔽。

  ```rust
  let x = "hello";		//先声明一个字符串类型的变量x
  let x = 5;				//此处再次声明一个x，这条语句对x的重新绑定（重新赋值）
  //两次声明的变量名相同，x的值则是第二次声明的值，这就是变量遮蔽
  ```

  

* 如果一个变量是不可写的，则可以通过变量遮蔽创建一个新的、可变的同名变量。

  ```rust
  fn main() {
      let v = Vec::new();
      let mut v = v;
      v.push(1);
      println!("{:?}", v);
  }
  // 这个过程是符合”内存“安全的。
  ```

  

### 类型推导

* **Rust** 的类型推导功能比较强大，它不仅可以从变量声明的当前语句中获取信息进行推导，而且还能通过上下文信息进行推导

  ```rust
  fn main() {
      // 没有明确标出变量的类型，但是通过字面量的后缀，
      // 编译器知道 elem 的类型为 u8
      let elem = 5u8;
      
      // 创建一个动态数组，数组内包含的是什么元素类型可以不写
      let mut vec = Vec::new();
      vec.push(elem);
      // 到后面调用了 push 函数， 通过 elem 变量的类型，
      // 编译器可以推导出 vec 的实际类型是 Vec<u8>
      println!("{:?}", vec);
  }
  ```

* 自动类型推导和“动态类型系统”是两码事。**Rust** 依然是静态类型的。一个变量的类型必须在编译阶段确定，且无法更改，只是某些时候不需要在源码中显式写出来而已。这只是编译器给我们提供的一个辅助工具。
* **Rust** 只允许“局部变量/全局变量”实现类型推导，而函数签名等场景下是不允许的。

### 类型别名

* 可以用 type 关键字给同一个类型起个别名（ type alias ）

  ```rust
  type Age = u32;
  fn grow(age: Age, year: u32) -> Age {
      age + year;
  }
  fn main() {
      let x: Age = 20;
      println!("20 years later: {}", grow(x, 20));
  }
  ```

* 类型别名还可以用在泛型场景

  ```rust
  type Double<T> = (T, Vec<T>);
  //未来使用的时候就可以：Double<i32>，等同于（i32, Vec<i32>），可以简化代码
  ```

### 静态变量

* **Rust** 中可以用 static 关键字声明静态变量。

  ```rust
  static GLOBAL: i32 = 0;
  ```

  * 与 let 语句一样，static 语句同样也是一个模式匹配。
  * 与 let 语句不同的是，用 static 声明的变量的生命周期是整个程序，从启动到退出。
  * 它占用的内存空间也不会在执行过程中回收。
  * 这是 **Rust** 中唯一的声明全局变量的方式。

* **Rust** 中对于全局变量是有限制的：

  * 全局变量必须在声明的时候马上初始化；

  * 全局变量的初始化必须是编译期可确定的常量，不能包括执行期才能确定的表达式、语句和函数调用；

  * 带有 mut 修饰的全局变量，在使用的时候必须使用 unsafe 关键字。

    ```rust
    static mut G2: i32 = 4;
    unsafe {
        G2 = 5;
        println!("{}", G2);
    }
    //全局变量的内存不是分配在当前函数栈上，函数退出的时候，
    //并不会销毁全局变量占用的内存空间，程序退出才会回收
    ```

    ```rust
    //这样是允许的
    static array: [i32; 3] = [1,2,3];
    //这样是不允许的
    static vec: Vec<i32> = { let mut v = Vec::new(); v.push(1); v};
    ```

    *如果用户需要使用比较复杂的全局变量初始化，推荐使用 lazy_static 库*

### 常量

```rust
const GLOBAL: i32 = 0;
//这是常量，而不是变量
//常量一定不允许使用 mut 关键字修饰这个变量绑定，这是语法错误
```

* const 常量  与 static 变量最大的区别在于：编译器并不一定会给 const 常量分配内存空间，在编译过程和，它很可能会被内联优化
* 用户千万不要用 hack 的方式，通过 unsafe 的方式去修改常量的值，这么做是没有意义的 

### 基本数据类型

#### bool 类型

* 布尔类型（ bool ) 代表的是“ 是 ” 和 “ 否 ” 的二值逻辑。它有两个值：true 和 false

```rust
fn logical_op(x: i32, y: i32) {
    let z : bool = x < y;
    println!("{}", z);
}
bool类型表达式可以用在 if / while 等表达式，作为条件表达式
if a >= b {
    ...
} else {
    ...
}
```

#### char 类型

* 字符类型由 char 表示，它可以描述任何一个符合 unicode 标准的字符值。

  ```rust
  let love = 'σ'; //可以直接嵌入任何 unicode 字符
  
  let love = '★';
  println!("{}", love);
  
  let c1 = '\n';
  println!("{}", c1);
  
  let c2 = '\x7f';
  println!("{}", c2);
  
  let c3 = '\u{7FFF}';
  println!("{}", c3);
  ```

  *char 类型设计的目的是描述任意一个 unicode，占据的内存空间不是1个字节，而是4个字节*

* 对于 ASCII字符其实只需占用一个字节的空间，因此 **Rust** 提供了单字节字符字面量来表示 ASCII 字符。

  ```rust
  let x :u8 = 1;
  println!("{}", x);
  
  let y :u8 = b'A';
  println!("{}", y);
  
  let _s :&[u8;5] = b"hello";
  
  let _r :&[u8;14] = br#"hello \n world"#;
  //使用一个字母 b 在字符或者字符串前面，代表这个字面量存储在 u8 类型数组中，占用空间比 char 型数组小
  ```

  

#### 整数类型

| 整数类型 | 有符号 | 无符号 |
| :----------: | :----: | :----: |
|    8 bits    |   i8   |   u8   |
|   16 bits    |  i16   |  u16   |
|   32 bits    |  i32   |  u32   |
|   64 bits    |  i64   |  u64   |
|   128 bits   |  i128  |  u128  |
| Pointer size | isize  | usize  |

*所谓有符号/无符号，指的是如何理解内存空间中的bit表达的含义*

1. 如果一个变量是有符号类型，那么它的最高位的那一个 bit 就是 ”符号位“ ，表示该数为正值还是负值
2. 如果一个变量是无符号类型，那么它的最高位和其他位一样，表示该数的大小
3. 特别注意的是 isize 和 usize 类型，它们占据的空间是不定的，与指针占据的空间一致，与所在的平台相关

_在所有的数字字面量中，可以在任意地方添加任意的下划线，以方便阅读：_

```rust
let var1 = 0x_1234_ABCD; //使用下划线分割数字，不影响语义，但是极大地提升了阅读体验
```

**在 Rust 中， 我们可以为任何一个类型添加方法，整形也不例外。**

```rust
let x : i32 = 9;
println!("9 power 3 = {}", x.pow(3));
println!("9 power 3 = {}", 9_i32.pow(3));
//这是非常方便的设计
```

_另外，对于整数类型，如果 **Rust** 无法判断变量的具体类型，则自动默认为 i32 类型_

#### 整数溢出

直接上例子：

```rust
fn arithmetic(m: i8, n: i8) {
    //加法运算，有溢出风险
    println!("{}", m + n);
}
fn main() {
    let m: i8 = 120;
    let n: i8 = 120;
    arithmetic(m, n);
}
// i8 为有符号类型，取值范围：-128 ~ 127 ，以上结果会产生溢出
//执行程序后，结果为：
//thread 'main' panicked at 'attempt to add with overflow', src/main.rs:3:20
//程序执行时发生了 panic
//可以使用： rustc -O 文件名.rs 编译，执行时没有错误，但是采用了自动截断的策略
//以上程序是 debug 版本，项目中不要这么做，会埋下炸弹
```

如果在某些场景下，用户确定需要更精细地自主控制整数溢出的行为，可以调用标准库中的：

```rust
checked_*
saturating_*
warpping_*
```

```rust
fn main() {
    let i = 100_i8;
    println!("checked {:?}", i.checked_add(i));
    println!("saturating {:?}", i.saturating_add(i));
    println!("warpping {:?}", i.wrapping_add(i));
}
//结果：
// checked None  --  checked_* 系列函数返回的类型是 Option<_>，当出现溢出的时候，返回值是None
// saturating 127  --  saturating_* 系列函数返回类型是整数，如果溢出，则给出该类型可表示范围的“最大/最小”值
// warpping -56 -- warpping_* 系列函数则是直接抛弃已经溢出的最高位，将剩下的部分返回
// 在对安全性要求非常高的情况下，强烈建议尽量使用这几个方法替代默认的算术运算符做数学运算，这样表意更清晰

```

标准库中还提供了一个叫作：std::num::Wrapping<T>的类型。

```rust
use std::num::Wrapping;

fn main() {
    let big = Wrapping(std::u32::MAX);
    let sum = big + Wrapping(2_u32);
    println!("{}", sum.0);
}
// 它重载了算术运算符，可以被当成普通整数使用，凡是被它包裹起来的整数，任何时候出现溢出都是截断
// 上述代码，不论使用什么编译选项，都不会触发 panic 
```

