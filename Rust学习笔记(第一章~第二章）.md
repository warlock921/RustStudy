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
      println!("{}", 1);							// 默认用法，打印 Display
      println!("{:o}", 9);						// 八进制
      println!("{:x}", 255);						// 十六进制 小写
      println!("{:X}", 255);						// 十六进制 大写
      println!("{:p}", &0);						// 指针
      println!("{:b}", 15);						// 二进制
      println!("{:e}", 10000f32);					// 科学计数（小写）
      println!("{:E}", 10000f32);					// 科学计数（大写）
      println!("{:?}", "test");					// 打印 Debug
      println!("{:#?}", ("test1", "test2"));		// 带换行和缩进的 Debug 打印
      println!("{a} {b} {b}", a="x", b="y");		// 命名参数
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

