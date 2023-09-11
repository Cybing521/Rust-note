# Rust

## 猜数游戏

```rust
use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("猜数!");

    let secret_number=rand::thread_rng().gen_range(1..101);
    println!("神秘数字是{}",secret_number);

    loop{
        println!("猜测一个数");

        let mut guess=String::new();   
        io::stdin().read_line(&mut guess).expect("无法读取行");

        let guess: u32 =match guess.trim().parse(){
            Ok(num) =>num,
            Err(_)=>continue,
        };

        println!("你猜测的数是:{}",guess);

        match guess.cmp(&secret_number){
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal=>{
                println!("you win");
                break;
            }
        }
    }
}

```

## 2 cargo

### 2.1 使用cargo创建项目

```rust
cargo new hello_cargo
cd hello_cargo
```

### 2.2 构建和运行货运项目

```
cargo build
cargo run
```

### 2.3 更新cargo以获得新版本

cargo update



## 3 变量

### 3.1 变量与可变性

- 声明变量使用let关键字
- 默认情况下，变量是不可变的（immutable）
- 声明变量时，在变量前面加上mut，就可以使变量可变

### 3.2 变量与常量

常量在绑定值以后是不可变的

- 不可以使用mut
- 声明常量使用const关键字
- 可以在任何作用域内进行声明
- 只可以绑定到常量表达式，无法绑定到函数的调用结果或只能在运行时才能计算出的值

命名规范：使用全大写字母

### 3.3 shadowing （隐藏）

- 可以使用相同的名字声明新的变量，新的变量就会shadow（隐藏）之前声明的同名变量
- shadow和把变量标记为mut是不一样的
  - 使用let声明的同名新变量，它的类型可以与之前不同

### 3.4 标量类型

- 一个标量类型代表一个单个的值
- Rust有四个主要的标量类型：
  - 整数类型
    - 整数溢出
  - 浮点类型
  - 布尔类型：true和false
  - 字符类型：被用来描述语言中最基础的单个字符
- 数值操作：加减乘除

### 3.5 复合类型（tuple）

- 获取tuple中的元素值

  可以使用模式匹配来解构一个tuple来获取元素的值

  ```rust
  fn main(){
      let tup:(i32,f64,u8) =(500,6.5,1);
      let (x,y,z)=tup;
  }
  ```

  

- 访问tuple的元素值

  ```
  println("{},{},{}",tup.0,tup.1,tup.2);
  ```

### 3.6 复合类型（数组）

- 数组也可以将多个值放在一个类型中

- 数组中每个元素的类型必须相同

- 数组的长度也是固定的

  ```rust
  fn main(){
  	let a= [1,2,3,4];
  }
  ```

- 数组的形式表示：[类型;长度] 

  ```
  let a:[i32;5]=[1,2,3,4,5];
  ```

- 另外一种

  ```rust
  let a=[3;5]; == let a =[3,3,3,3,3]; 
  ```

- 访问数组的元素

  - 数组是Stack上分配的单个块的内存
  - 可以使用索引来访问数组的元素
  - 如果访问的索要超过了数组的范围，那么：
    - 编译会通过
    - 运行会报错（runtime时会panic）

### 3.7 函数

- 声明函数使用fn关键字
- 依照惯例，针对函数和变量名，使用snake case命名规范
  - 所有的字母都是小写的，单词之间使用下划线分开

#### 函数的参数

- parameters,arguments
- 在函数签名里，必须声明每个参数的类型

#### 函数体中的语句与表达式

#### 函数的返回值

### 3.8 控制流

#### if 表达式

- if表达式允许您根据条件来执行不同的代码分支
  - 这个条件必须是bool类型
- if表达式中，与条件相关联的代码块叫做分支（arm）
- 可选的，在后面可以加上一个else表达式
- 使用else if 处理多重条件
  - 但如果使用了多于一个else if ,那么最好使用match来重构代码

### 3.9 循环

- rust提供了3中循环：loop ,while 和 for

  ```
  fn main(){
  	let a=[10,20,30,40,50];
  	for element in a.iter(){
  		println!("{}",element);
  	}
  }
  ```

  由于for循环的安全、间接性，所以它在rust里用的最多

- range

  - 标准库提供
  - 指定一个开始数字和一个结束数字，range可以生成它们之间的数字，不含结束
  - rev方法可以反转Range

  ```
  fn main(){
  	for number in (1..4).rev(){
  		println!("{}!",number);
  	}
  	println!("LIFTOFF!");
  }
  ```


## 4 所有权

### 4.1 什么是所有权

- rust的核心特征就是所有权
- rust采用方式
  - 内存是通过一个所有权系统来管理的，其中包含一组编译器在编译时检查的规则
  - 当程序运行时，所有权特效不会减慢程序的运行速度

### 4.2 变量作用域

scope就是程序中一个项目的有效范围

### 4.3 String类型

- String比那些基础标量数据类型更复杂

- **字符串字面值**：程序里手写的哪些字符串值

- rust还有第二种字符串类型：**String**

  - 在heap上分配，能够存储在编译时未知数量的文本

- 创建String类型的值

  - 使用from函数

  - ```rust
    let s =String::from("hello");
    ```

  - 这类字符串是可以被修改的

- 内存与分配

  - Rust采用了不同的方式：对于某个值来说，当拥有它的变量走出作用范围时，内存会立即自动的交还给操作系统
  - drop函数

- 变量与数据交互的方式：移动 （Move）

  - 浅拷贝（shallow copy）
  - 深拷贝（deep copy）
  - 移动（Move） 使原指针失效
  - 隐含的一个设计原则：Rust不会自动创建数据的深拷贝

- 变量与数据交互的方式：克隆（clone）

  - ```rust
    fn main(){
    	let s1=String::from("hello");
    	let s2=s1.clone();
    	println!("{},{}",s1,s2);
    }
    ```

- Stack上的数据：复制

  - copy trait,可以用于像整数这样完全存放在stack上面的类型
  - 一些拥有copy trait 的类型
    - 任何简单标量的组合类型都可以使copy的
    - 任何需要分配内存或某种资源的都不是Copy的
    - 一些拥有copy trait的类型:
      - 所有的整数类型，例如u32
      - bool
      - char
      - 所有的浮点类型，例如f64
      - Tuple（元组），如果其所有的字段都是Copy的 
        - （i32,i32）是
        - （i32,String）不是

### 4.4 所有权与函数

- 如何让函数使用某个值，但不获得其所有权

  ```rust
  fn main(){
  	let s1=String::from("hello");
  	let (s2,s1)=calculate_length(s1);
  	
  	println!("The length of {} is {}",s2,len );
  }
  
  fn calculate_length(s:String) =>(String,usize){
  	let length =s.len();
  	(s,length)
  }
  ```

- 引用和接用

  - 参数的类型是%String而不是String

  - &符号就表示引用：允许你引用某些值而不取得其所有权

    ```
    fn main(){
    	let s1 =String::from ("hello");
    	let len =calculate_length(&s1);
    	
    	println!("The length of {} is {}",s1,len);
    }
    
    fn calculate_length(s:&String) =>usize{
    	s.len()
    }
    ```

- 可变引用

  - 可变引用有一个重要let 限制：在特定作用域内，对某一块数据，只能有一个可变引用

  - ```rust
    fn main(){
    	let mut s=String::from("hello");
    	{
    		let s1=&mut s;
    	}
    	
    	let s2=&mut s;
    }
    ```

  - 不可以同时拥有一个可变引用和一个不变的应用

  - 多个不变的应用是可以的

    ```rust
    fn main(){
    	let mut s= String::from("hello");
    	let r1=&s;
    	let r2=&s;
    	let s1=&mut s;	//这句话是不行的
    	
    	println!("{} {} {}",r1,r2,s1);
    }
    ```

    

- 悬空引用 Dangling Rederences

  - 一个指针引用了内存中的某个地址，而这块内存可能已经释放并分配给其他人使用了

  - 在Rust里，编译器可保证应用永远都不是悬空引用的：

    ```rust
    //错误例子
    fn main(){
    	let r= dangle();
    }
    
    fn dangle() => & String{
    	let s =String::from("hello");
    	&s
    }
    ```

    

### 4.5 引用的规则

- ​	在任何给定的时刻，只能满足下列条件之一：
  - 一个可变的引用
  - 任意数量不可变的引用
- 引用必须一直有效下去

### 4.6 切片

- ​	Rust的另外一种不持有所有权的数据类型：切片（Slice）

- ```rust
  fn main(){
  	let mut s =String::from("hello world");
  	let wordIndex= first_world(&s);
  	
  	s.clear();	//不可变不能转为可变
  	println("{}",worldIndex);
  }
  
  fn first_world(s:&String) =>&str{
  	let bytes =s.as_bytes();
  	
  	for (i ,& item) in bytes.iter().enumerate(){
  		if iterm ==b' '{
  			return &s[..i];
  		}
  	}
  	&s[..]
  }
  ```

- 将字符串切片作为参数传递

## 5 Struct

### 5.1 定义 struct

- 使用struct关键字，并为整个struct命名

- 在花括号内，为所有字段（Field）定义名称和类型

  - 例如

    ```rust
    struct User{
    	username :String,
    	email:String,
    	sign_in_count:u64,
    	active:bool,
    }
    ```

    

### 5.2 取得struct里面得某个值

- 使用点标记法：

- ```rust
  let mut user1=User{
  	email:String::from("someone"),
  	username:String::from("cyb"),
  	active:true;
  	sign_in_count:1,
  }
  
  user1.email =String::from("21301062@bjtu.edu.cn");
  ```

### 5.3 struct跟新语法

```rust
let user2=User{
	email:String::from("xxx"),
	username:String::from("dsfd"),
	..user1
};
```

### 5.4 Tuple struct

### 5.5 Unit-Like Struct

- 可以定义没有任何字段得struct，叫做Unit-Like struct
- 适用于需要在某个类型上实现某个trait，但是在里面又没有想要存储得数据

### 5.6 struct数据得所有权

- ```
  struct User{
  	username:String,
  	email:String,
  	sign_in_count:u64,
  	active:bool,
  }
  ```

- struct里也可以存放引用，但这需要使用生命周期

### 5.7 struct例子

- std::fmt::Display

- std::fmt::Debug

- #[derive(Debug)]

- {:?}

- {:#?}

- ```rust
  #[derive(Debug)]
  struct Rectangle{
      width:u32,
      height:u32,
  }
  
  fn main(){
      let rect=Rectangle{
          width:30,
          height:20,     
      };
  
      println!("{}",area(&rect));
      println!("{:#?}",rect);
  
  }
  
  fn area(rect:&Rectangle) ->u32{
      rect.width*rect.height
  }
  ```

### 5.8 struct的方法

- 方法和函数类似：fn关键字、名称、参数、返回值
- 方法和函数不同之处
  - 方法是在struct的上下文中定义
  - 第一个参数是self，表示方法被调用的struct实例

- 在impl块里定义方法

- impl可以使用多次

- 方法的第一个参数可以是&self，也可以获得其所有权或可变借用。和其他参数一样

  ```rust
  impl Rectangle {
      fn area(&self)->u32{
          self.width*self.height
      }
  }
  ```

### 5.9 关联函数

- 可以在impl块里定义不把self作为第一个参数的函数，它们叫关联函数
- 关联函数通常用于构造器
- ：：符号
  - 关联函数
  - 模块创建的命名空间

## 6 枚举

### 6.1 定义枚举

```rust
enum Message{
	Quit,
    Move{x:i32,y:i32},
    Write(String),
    ChangeColor(i32,i32,i32),
}

fn main(){
    let q=Message::Quit;
    let m=Message::Move{x:12,y:12};
    let w=Message::Write(String::from("hello"));
    let c=Message::ChangeColor(0,255,255);
}
```

### 6,2 Option枚举

- 在Prelude(预导入模块)中
  - Option<T>
  - Some(T)
  - None
- 描述了：某个值可能存在或不存在的情况
- Rust没有Null
  - null的问题在于：当你尝试像使用非Null值那样使用Null值得时候，就会引起某种错误
  - Null的概念还是有用的：因某种原因而变为无效或缺失的值

### 6.3 match

- 允许一个值与一系列模式进行匹配，并执行匹配的模式对应的代码
- 绑定值的模式
  - 匹配的分支可以绑定到被匹配对象的不分枝
- match匹配必须穷举所有的可能
  - _通配符:替代其他没列出的值

### 6.4 if let

- 处理只关心一种匹配而忽略其他匹配的情况

- ```rust
  fn main(){
  	let v =Some(0u8),
      match v{
          Some(3)=>println!("three"),
          _=>println!("others"),
      }
      
      if let Some(3)=v{
          println!("three");
      }else{
          println!("others");
      }
  }
  ```

  

## 7. Package,Crate,Module

### 7.1 模块系统

- Package(包) :Cargo的特性
- Crate(单员包)：一个模块树
- Module(模块)、use：让你控制代码的组织、作用域、私有路径
- Path（路径）：为struct、function或module等项命名的方式

### 7.2 Package和Crate

- Crate的类型：
  - binary
  - library
- Crate Root：
  - 是源代码文件
  - Rust编译器从这里开始
- 一个Package：
  - 只包含一个Cargo.toml
  - 只能包含0-1个library crate
  - 可以包含任意数量的binary crate
  - 但必须至少包含一个crate （library 或 binary）

### 7.3 定义module来控制作用域和私有性

- Module：
  - 在一个crate内，将代码进行分组
  - 增加可读性，易于复用
  - 控制项目（item）的私有性。public、private
- 建立module：
  - mod关键字
  - 可嵌套

### 7.4 路径

- ​	路径的两种形式
  - 绝对路径：从crate root 开始，使用crate名或字面值crate
  - 相对路径：从当前模块开始，使用self，super或当前模块的标识符
- 路径至少由一个标识符组成，标识符之间使用::
- pub 关键字
- super关键字
- pub enum
  - enum是公共的
  - enum的变体也都是公共的

#### 7.5 use关键字

- ​	可以使用use关键字将路径导入到作用域中
  - 仍遵循私有性规则

### 7.6 通配符 *

- 使用*可以把路径所有的公共条目都引入到作用域
- 应用场景
  - 测试-将多有被测试代码引入到tests模块
  - 有时被用于预导入（prelude）模块

## 8 常用的集合

### 8.1 vector

#### 使用vector存储多个值

- Vec<T>,叫做vector
  - 由标准库提供
  - 可存储多个值
  - 只能存储相同类型的数据
  - 值在内存中连续存放

#### 创建vector

```rust
fn main(){
	let v:Vec<i32> =Vec::new();
}
```

- 使用初始值创建Vec<T>,使用vec!宏

```rust
fn main(){
	let v=vec![1,2,3];
}
```

#### 更新Vector

```rust
fn main(){
	let mut v=Vec::new();
    v.push(1);
}
```

#### 删除Vector

- 与任何其他struct一样，当Vector离开作用域后
  - 它就被清理了
  - 它所有的元素也被清理掉了

#### 索引vs get处理访问越界

- 索引：panic
- get：返回None

#### 所有权和借用规则

- 不能再同一作用域内同时拥有可变和不可变引用

#### 遍历Vector里的值

```rust
fn main(){
    let mut v=vec![100,32,57];
    for i in &mut v{
        *i+=50;
    }

    for i in v{
        println!("{}",i);
    }

}
```

### 8.2 vector例子

#### 使用enum来存储多种数据类型

- Enum的变体可以附加不同类型的数据
- Enum的变体定义再同一个enum类型下

```rust
enum SpreadsheetCell{
    Int (i32),
    Float(f64),
    Text(String),
}

fn main(){
    let row =vec![
        SpreadsheetCell::Int(3),
        SpreadsheetCell::Text::(String::from("blue")),
        SpreadsheetCell::Fload(10.12),
    ];
}
```

### 8.3 String

#### Rust开发者经常被字符串困扰的原因

- Rust倾向于暴露可能的错误
- 字符串数据结构复杂
- UTF-8

#### 创建一个新的字符串

- String::new()函数

- 使用初始值来创建

  ```rust
  let data="hello"l
  let s =data.to_string();
  ```

#### 更新String

- push_str()方法：

  ```rust
  fn main(){
      let x=String::from("foo");
      let s1=String::from("bar");
      x.push_str(&s1);
      
      println!("{}",s1);
  }
  ```

- push方法：把单个字符添加

  ```rust
  s.push('l');
  ```

- +:连接字符串

  ```rust
  fn main(){
      let s1=String::from("hello ");
      let s2=String::from("world");
      
      let s3=s1 + &s2;
      
      //s1的使用权转让给了s3,s2是引用
  }
  ```

  

#### 对String按索引的形式进行访问

- 按索引语法访问String的某部分，会报错

  ```rust
  fn main(){
  	let s=String::from("hello");
      println!("{}",s[1]);//报错
  }
  ```

#### 切割String

- 可以使用[]和一个范围来创建字符串的切片
  - 必须谨慎使用
  - 如果切割时跨越了字符边界，程序就会panic

### 8.4 HashMap<K,V>

- 键值对的形式存储数据，一个键（key）对应一个值（value）
- Hash函数：决定如何在内存中存放K和V
- 使用场景：通过K来寻找数据，而不是通过索引

#### 创建HashMap

```rust
use std::collections::HashMap;

fn main(){
    
    //let mut scores:HashMap<String,i32>=HashMap::new()
    let mut scores=HashMap::new();
    scores.insert(String::from("a"),String::from("b"));
}
```

同构的，一个HashMap中：

—所有的K必须是同一种类型

#### 另一种创建HashMap的方法：collect方法

```rust
use std::collections::HashMap;

fn main(){
    let teams=vec![String::from("blue"),String::from("yellow")];
    let intial_scores=vec![10,50];
    let scores:HashMap<_,_>=teams.iter().zip(intial_scores.iter()).collect();
}
```

#### HashMap和所有权

- 对于实现了Copy trait的类型，值会被复制到HashMap中
- 对于拥有所有权的值，值会被制动，所有权会转移给HashMap
- 如果将值得引用插入到HashMap,值本身不会移动
  - 在HashMap有效得期间，被引用得值必须保持有效

#### 访问HashMap中的值

- get方法
  - 参数：K
  - 返回：Option<&V>

```rust
use std::collections::HashMap;

fn main(){
   let mut scores=HashMap::new();

   scores.insert(String::from("blue"), 10);
   scores.insert(String::from("red"), 100);

   let x =String::from("blue");
   let score=scores.get(&x) ;

   match score{
        Some(s)=>println!("{}",s),
        None=>println!("team not exist"),
   }
}
```

#### 遍历HashMap中的值

```rust
use std::collections::HashMap;

fn main(){
   let mut scores=HashMap::new();

   scores.insert(String::from("blue"), 10);
   scores.insert(String::from("red"), 100);

  for (k,v) in &scores{
    println!("{} : {}",k,v);
  }

}
```

#### 更新HashMap<K,V>

- HashMap大小可变

- 每个K同时对应一个V

- 更新HashMap中的数据：

  - K已经存在，对应一个V

    - 替换现有的V
    - 保留现有的V，忽略新的V

    ```rust
    scores.entry(String::from("yellow")).or_insert(50);
    ```

    entry方法：检查指定的K是否对应一个V

    ​	—参数为K

    ​	—返回enum Entry:代表值是否存在

    ```rust
    use std::collections::HashMap;
    
    fn main(){
       let text="hello world wonderful world";
    
       let mut map=HashMap::new();
       for word in text.split_whitespace(){
          let count=map.entry(word).or_insert(0);
          *count+=1;
       }
    
       println!("{:#?}",map);
    }
    ```

    ​			

    - 合并现有的V和新的V

  - K不存在

#### Hash函数

- 默认情况下，HashMap使用加密功能强大的Hash函数，可以抵抗拒绝服务（DoS）攻击。
  - 不是可用的最快的Hash算法
  - 但具有更好安全性
- 可以指定不同的hasher来切换到另一个函数
  - hasher是实现BuildHasher trait的类型

## 9 Rust错误处理

### 9.1 Rust错误处理概述

- Rust的可靠性：错误处理
  - 大部分情况下：在编译时提示错误，并处理
- 错误的分类：
  - 可恢复
    - 例如文件未找到，可再次尝试
  - 不可恢复
    - bug,例如访问的索引超出范围

#### 不可恢复的错误与Panic

- 当panic!宏执行：
  - 你的程序会打印一个错误信息
  - 展开（unwind）、清理调用栈（stack）
  - 退出程序

#### 为应对panic,展开或中止（abort）调用栈

```rust
[profile.release]
panic='abort'
```

#### 使用panic!产生的回溯信息

- panic!可能出现在：
  - 我们写的代码中
  - 我们所依赖的代码中
- 可通过调用panic！的函数的回溯信息来定位引起问题的代码
- 通过设置环境变量RUST_BACKTRACE可得到回溯信息
- 为了获取带有调试信息的回溯，必须启用调试符号（不带 --release）

### 9.2 Result枚举

```rust
enum Result<T,E>{
	OK(T),
	Err(E),
}
```

- T：操作成功情况下，Ok变体里返回的数据的类型
- E：操作失败情况下，Err变体里返回的错误的类型

```rust
use std::fs::File;

fn main(){
  let f =File::open("hello.txt");
}
```

#### 处理Result的一种方法：match表达式

- 和Option枚举一样，Result及其变体也是有prelude带入作用域

#### unwrap

- unwrap:match表达式的一个快捷方式：
  - 如果Result结果是Ok，返回Ok里面的值
  - 如果Reslut结果是Err,调用panic!宏

```rust
let f =File::open("hello.txt").unwrap();
```

#### expect

- expect:和unwrap相似，但可指定错误信息

```rust
let f=File::open("hello.txt").expect("无法打开文件");
```

### 9.3 Result 中可恢复的错误

#### 传播错误

- 在函数出处理错误
- 将错误返回给调用者

#### ？运算符

- 传播错误的一种快捷方式
- 如果Result是Ok:Ok中的值就是表达式的结果
- 如果Result是Err:Err就是整个函数的返回值，就像使用了return

#### ? 与from函数

- Trait std::convert::From上的from函数
  - 用于错误之间的转换
- 被？所应用的错误，会隐式的被from函数处理
- 当？调用from函数时：
  - 它所接收的错误类型会被转化为当前函数返回类型所定义的错误类型

### 9.4 何时使用panic!

#### 总体原则

- 在定义一个可能失败的函数时，优先使用panic!

#### 编写实例、原型代码、测试

- 可以使用panic!
  - 演示某些概念：unwrap
  - 原型代码：unwrap、expect
  - 测试：unwrap、expect

#### 有时你比编译器掌握更多的信息

- 你可以确定Result就是Ok:unwrap

  ```rust
  use std::net::IpAddr;
  
  fn main(){
    let home:IpAddr ="127.0.0.1".parse().unwrap();
  }
  ```

#### 错误处理的指导性建议

- 当代码最终可能处于损坏状态时，最好使用panic!
- 损坏状态：某些假设、保证、约定或不可变性被打破
  - 例如非法的值、矛盾的值或空缺的值被传入代码

## 10 泛型，Trait，生命周期

### 10.1 提取函数消除重复的代码

```rust
use std::result;

fn largest(list:&[i32])->i32{
  let mut largest=list[0];
  for &item in list{
    if item >largest{
      largest=item;
    }
  }
  largest
}

fn main(){
  let number=vec![1,2,34,5,6];
  let result=largest(&number);

  println!("the largest number is {}",result);
}
```

### 10.2 泛型

- 泛型：提高代码复用能力

  - 处理重复代码的问题

- 泛型是具体类型或其他属性的抽象代替：

  - 你编写的代码不是最终的代码，而是一种模板

  #### Struct定义中的泛型

  ```rust
  struct Point<T>{
    x:T,
    y:T,
  }
  
  
  fn main(){
    let integer=Point{x:5,y:5};
  }
  ```

  #### Enum定义中的泛型

- 可以让枚举的变体持有泛型数据类型

  - 例如Option<T>,Result<T,E>

  #### 方法中定义的泛型

  ```rust
  impl<T> Point<T>{
      fn x(&self) ->&T{
      	&self.x
      }
  }
  
  impl Point<i32>{
      fn x(&self) ->&i32{
          &self.x
      }
  }
  ```

  #### 泛型代码的性能

- 使用泛型的代码和使用具体类型的代码运行速度是一样的

- 单态化

  - 在编译时将泛型替换为具体类型的过程

### 10.3 Trait

- trait告诉Rust编译器：
  - 某种类型具有哪些并且可以与其他类型共享的功能 
- Trait:抽象的定义共享行为
- Trait bounds(约束)：泛型类型参数指定为实现了特定行为的类型
- Trait 与其他语言的接口类似，但有些区别

#### 实现trait的约束

- 这个类型或这个trait是在本地crate里定义的
- 无法为外部类型来实现外部的trait（一致性）

#### 默认实现

- 注意：无法从方法的重写实现里面调用默认的实现

#### Trait作为参数

- impl Trait语法：适用于简单情况

```rust
pub fn notify1(item1:impl Summary,item2:impl Summary){
    println!("Breaking news!{}",item1.summarize());
}
```

- Trait bound语法：可用于复杂情况

```rust
pub fn notify<T:Summary>(item1:T,item2:T){
    println!("Breaking news!{}",item1.summarize());
}
```

- 使用+指定多个Trait bound

```rust
pub fn notify<T:Summary +Display,U:Clone +Debug>(a:T,b:U)->String{
    format!("Breaking news! {}",a.summarize())
}
```



- Trait bound 使用where子句
  - 在方法签名后指定where子句

```rust
pub fn notify2<T,U>(a:T,b:U)->String
where
	T:Summary+Display,
	U:Clone+Debug,
{
    format!("Breaking news!{}",a.summarize())
}
```

#### 实现Trait作为返回类型

impl Trait只能返回确定的同一种类型，返回可能不同类型的代码会报错

### 10.4 生命周期

- Rust的每个引用都有自己的生命周期
- 生命周期：引用保持有效的作用域
- 大多数情况：生命周期是隐式的、可被推断的
- 当引用的生命周期可能以不同的方式互相关联时：手动标注生命周期

#### 借用检查器

#### 函数中的泛型生命周期

```rust
fn longest<'a>(x:&'a str,y:&'a str)->&'a str{
    if x.len()>y.len(){
        x
    }else{
        y
    }
}
```

#### 生命周期标注-语法

- 生命周期参数名：
  - 以 ‘ 开头
  - 通常全小写且非常短
  - 很多人使用’ a
- 生命周期标注的未知
  - 在引用的&符号后
  - 使用空格将标注和引用类型分开

#### 生命周期标注-例子

```rust
&i32 //一个引用
&‘a i32 //带有显式生命周期的引用
&’a mut i32 //带有显式生命周期的可变引用
```

- 单个生命周期标注本身没有意义
- 泛型生命周期参数声明在：函数名和参数列表之间的<>里
- 生命周期'a的实际生命周期是：x和y两个生命周期中较小的那个

#### Struct定义中 的生命周期标注

- Struct里可包括：
  - 自持有的类型
  - 引用：需要在每个引用上添加生命周期标注

```rust
struct ImportantExcept<'a>{
  part: &'a str,
}

fn main(){
  let novel=String::from("bu zhi dao xie sm, wsw");
  let first_sentence=novel.split('.')
    .next()
    .expect("could not found a ','");

  let i =ImportantExcept{
    part:first_sentence
  };

}
```

#### 生命周期的省略

- 在Rust引用分析中所编入的模式称为**生命周期省略规则**
- 生命周期省略规则不会提供完整的推断

#### 输入、输出生命周期

- 生命周期在：
  - 函数/方法的参数：输入生命周期
  - 函数/方法的返回值：输出生命周期

#### 生命周期省略的三个规则

- 规则1：每个引用类型的参数都有自己的生命周期
- 规则2：如果只有1个输入生命周期参数，那么该生命周期就被赋给所有的输出生命周期参数
- 规则3：如果有多个输入生命周期参数，但其中一个是&self或&mjut self，那么self的生命周期就会被赋给所有的输出生命周期参数

#### 方法定义中的生命周期标注

- 在struct上使用生命周期实现方法，语法和泛型参数的语法一样
- 在哪声明和使用生命周期参数，依赖于：
  - 生命周期参数是否和字段、方法的参数或返回值有关
- struct字段的生命周期名：
  - 在impl 后声明
  - 在struct名后使用
  - 这些生命周期是struct类型的一部分
- impl块内的方法签名中：
  - 引用必须绑定与struct字段引用的生命周期，或者引用是独立的也可以
  - 生命周期省略规则经常使得方法中的生命周期标注不是必须的

```rust

impl<'a> ImportantExcept<'a>{
  fn level(&self) ->i32{
    3
  }
}
```

#### 静态生命周期

- static是一个特殊的生命周期：整个程序的持续时间

## 11 编写和运行测试

### 11.1 测试

- 测试
  - 函数
  - 测试非测试代码的功能是否和预期一致
- 测试函数体执行的3个操作：
  - 准备数据/状态
  - 运行被测试的代码
  - 断言（Assert）的结果

#### 解剖测试函数

- 测试函数需要使用test属性进行标注
  - Attribute就是一段Rust代码的元数据
  - 在函数上加#[test],可把函数变成测试函数

#### 运行测试

- 使用cargo test命令运行所有测试函数
  - Rust会构建一个Test Runner可执行文件
    - 它会运行标注了test的函数，并报告运行是否成功

#### 测试失败

- 测试函数panic就表示失败

- 每个测试运行在一个新线程

- 当主线程看到某个测试线程挂掉了，那个测试标记为失败了

  ```rust
  #[test]
  fn another(){
      panic!("Make this test fail");
  }
  ```

### 11.2 断言

#### 使用assert!宏检查测试结果

- 来自标准库，用来确定某个状态是否为true
  - true:测试通过
  - false:调用panic!，测试失败

#### 使用assert_eq!和assert_ne！测试相等性

- 都来自标准库
- 判断两个参数是否相等或不等
- 实际上，它们使用的就是==和！=运算符

```rust
#[test]
fn it_add_two(){
    assert_eq!(4,add_two(2));
}

#[test]
fn it_add_two2(){
    assert_ne!(5,add_two(2));
}
```

#### 添加自定义错误信息

- 可以向assert!、assert_eq!、assert_ne!添加可选的自定义消息

#### 验证错误处理的情况

- 测试除了验证代码的返回值是否正确，还需验证代码是否会如预期的处理了发生错误的情况
- 可验证代码在特定情况下是否发生了panic
- should_panic属性（attribute）:
  - 函数panic:测试通过
  - 函数没有panic:测试失败

```rust
#[test]
#[should_panic]
fn greater_than_100(){
    Guess::new(200);
}
```

#### 让should_panic更精确

- 为should_panic属性添加一个可选的expected参数：
  - 将检查失败消息中是否包含所指定的文字

```rust
#[test]
#[should_panic(expected="xxx")]
fn greater_than_100(){
    Guess::new(200);
}
```

#### 在测试中使用Result<T,E>

- 无需panic,可使用Result<T,E>作为返回类型编写测试
  - 返回ok:测试通过
  - 返回Err:测试失败

```rust
#[test]
fn it_works() ->Result<(),String>{
    if 2+2 ==4{
        Ok(())
    }else{
        Err(String::from("xxx"))
    }
}
```

#### 控制测试如何运行

- 改变cargo test的行为：添加命令行参数
- 默认行为：
  - 并行运行
  - 所有测试
  - 捕获（不显示）所有输出，使读取与测试结果相关的输出更容易

- 命令行参数：
  - 针对cargo test的参数：紧跟cargo test后
  - 针对测试可执行程序：放在 -- 之后
- cargo test --help
- cargo test -- -- help

### 11.3 控制测试运行

#### 并行测试

##### --test-threads 参数

- 传递给二进制文件
- 不想以并行方式运行测试，或想对线程数进行细粒度控制
- 可以使用 --test-threads参数，后面跟着现场的数量

```rust
cargo test -- --test-threads=1
```

##### 默认函数输出

- 默认，如测试通过，Rust的test库会捕获所有打印到标准输出的内容
- 例如，如果被测试代码中用到了println!:
  - 如果测试通过：不会再终端看到println!打印的内容
  - 如果测试失败：会看到打印的内容和失败信息
- **如果想在成功的测试中看到打印的内容： --show-poutput**

#### 按名称运行测试的子集

- 选择运行的测试：将测试的名称（一个或多个）作为cargo test的参数
- 运行单个测试：指定测试名
- 运行多个测试：指定测试名的一部分

#### 忽略某些测试

```rust
#[test]
#[ignore]
fn expensive_test(){
    assert_eq!(5,1+4);
}
```



- 运行被忽略的测试：
  - cargo test -- --ignored

### 11.4 测试的组织

#### 测试的分类

- Rust对测试的分类
  - 单员测试
  - 集成测试

#### 单元测试

- ```rust
  #[cfg(test)]标注
  ```

  - 只有运行cargo test才编译和运行代码
  - 运行cargo build则不会

- 集成测试再不同的目录，它不需要#[cfg(test)] 标注

- cfg:configuration(配置)

  - 告诉Rust下面的条目只有在指定的配置选项下才被包含
  - 配置选项test:由Rust提供，用来编译和运行测试
    - 只有才会编译代码，包括模块中的helper函数和 #[test]标注的函数

#### 集成测试

- 在Rust里，集成测试完全位于被测试库的外部
- 目的：是测试被测试库的多个部分是否能正确的一起工作
- 集成测试的覆盖率很重要

#### 集成测试中的子模块

- tests目录下每个文件被编译成独自得crate文件
