# Rust-笔记

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

### 2.4 cargo check

```rust
cargo check
```

cargo check 可以快速的检查一下代码是否编译通过。因此该命令速度非常快，能节省大量的编译时间

## 3 变量

### 3.1 变量与可变性

- 声明变量使用let关键字
- 默认情况下，变量是不可变的（immutable）
- 声明变量时，在变量前面加上mut，就可以使变量可变
- [Rust语言规范]([命名规范 - Rust语言圣经(Rust Course)](https://course.rs/practice/naming.html))

#### 变量绑定

```rust
let a ="hello world"
```

#### 使用下划线开头忽略未使用的变量

你希望告诉Rust不要警告未使用的变量，为此可以用下划线作为变量名的开头：

```rust
fn main(){
    let _x =5;
    let y=10;
}
```

![image-20230913095324735](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230913095324735.png)

#### 变量解构

let 表达式不仅仅用于变量的绑定，还能进行复杂变量的解构：从一个相对复杂的变量中，匹配出该变量的一部分内容

```rust
fn main(){
    let (a,mut b):(bool,bool)=(true,false);
    println!("a={:?},b={:?}",a,b);
    
    b=true;
    assert_eq!(a,b);
}
```

##### 解构Option

**Option**枚举，用来解决Rust变量是否有值的问题

```rust
enum Option<T>{
    Some(T),
    None,
}
```

一个变量要么有值：Some(T),要么为空：None

```
因为Option,Some,None都包含在prelude中，因此你可以直接通过名称来使用它们，而无需以Option::Some这种形式去使用
```

##### 匹配Option<T>

```
fn plus_one(x:Option<i32>)->Option<i32>{
	match x{
		None=>None,
		Some(i)=>Some(i+1),
	}
}

let five=Some(5);
let six=plus_one(five);
let none=plus_one(None);
```

##### 模式适用场景

###### 模式

- 字面值
- 解构的数组、枚举、结构体或者元组
- 变量
- 通配符
- 占位符

###### 所有可能用到模式的地方

- match分支

```rust
match VALUE{
	pattern =>expression,
	pattern =>expression,
	pattern =>expression,
}
```

- if let分支

```rust
if let Pattern =Some_value{
    
}
```

- while let 条件循环

一个与if let类似的结构是while let 条件循环，它允许只要模式匹配就一直进行while循环。下面展示了一个使用while let的例子：

```rust
let mut stack=Vec::new();

stack.push(1);
stack.push(2);
stack.push(3);

while let Some(top)=stack.pop(){
    println!("{}",top);
}
```

###### 全模式列表

- 匹配字面值

```rust
let x =1;
match x{
	1=>println!("one"),
	2=>println!("two"),
	_=>println!("other"),
}
```

- 匹配命名变量

变量遮蔽的问题

- 单分支多模式

在 **match**表达式中，可以使用|语法匹配多个模式，它代表或的意思

- 通过序列... =匹配值的范围

```rust
let x= 5;
match x{
	1..=5 =>println!("one through five"),
	_=>println!("others")
}
```

#### 解构式赋值

在赋值语句的左式中使用元组、切片和结构体模式

```rust
struct Struct{
    e:i32
}

fn main(){
    let (a,b,c,d,e);
    (a,b)=(1,2);
    [c,..,d,..]=[1,2,3,4,5];
    Struct {e,..}=Struct{e:5};
    
    assert_eq!([1,2,1,4,5],[a,b,c,d,e]);
}
```

使用+=的赋值语句还不支持解构式赋值

### 3.2 变量与常量

常量在绑定值以后是不可变的

- 不可以使用mut
- 声明常量使用const关键字
- 可以在任何作用域内进行声明
- 只可以绑定到常量表达式，无法绑定到函数的调用结果或只能在运行时才能计算出的值

命名规范：使用全大写字母

```rust
const MAX_POINTS:u32 =100_000;
```



### 3.3 shadowing （隐藏）

- 可以使用相同的名字声明新的变量，新的变量就会shadow（隐藏）之前声明的同名变量
- shadow和把变量标记为mut是不一样的
  - 使用let声明的同名新变量，它的类型可以与之前不同

```rust
fn var_shadow(){
    let x=5;
    let x=x+1;
    {
        let x=x*2;
        println!("the value of x is {x}");
    }

    println!("the value of x is {x}");
}
```

![image-20230913101758456](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230913101758456.png)

### 3.4 标量类型

- 一个标量类型代表一个单个的值
- Rust有四个主要的标量类型：
  - 数值类型
    - 整数类型
      - 有符号整数（i8,i16,i32,i64,isize）
      - 无符号整数（u8,u16,u32,u64,usize）
  
    - 浮点类型
      - 浮点数（f32,f64）
      - 默认浮点类型是f64
  
    - 有理数
    - 复数
  
  - 布尔类型：true和false
  - 字符类型：被用来描述语言中最基础的单个字符，存储为4个子节
  - 单员类型：即（），其唯一的值也是（）
  
- 数值操作：加减乘除

#### 类型推导与标注

rust编译器可以根据变量的值和上下文中的使用方式来自动推导出变量的类型

```rust
let guess="42".parse().expect("not a number!");
```

#### 整型溢出

- debug模式编译时，Rust会检查整型溢出，编译时panic
- --release 参数进行release模式构建时，Rust不检测溢出。相反，当检测到整型溢出时，Rust会按照补码循环溢出

```rust
fn main(){
    let a :u8 =255;
    let b=a.wrapping_add(20);
    println!("{}",b)//19
}
```

#### 浮点数陷阱

```rust
fn main() {
    let abc: (f32, f32, f32) = (0.1, 0.2, 0.3);
    let xyz: (f64, f64, f64) = (0.1, 0.2, 0.3);

    println!("abc (f32)");
    println!("   0.1 + 0.2: {:x}", (abc.0 + abc.1).to_bits());
    println!("         0.3: {:x}", (abc.2).to_bits());
    println!();

    println!("xyz (f64)");
    println!("   0.1 + 0.2: {:x}", (xyz.0 + xyz.1).to_bits());
    println!("         0.3: {:x}", (xyz.2).to_bits());
    println!();

    assert!(abc.0 + abc.1 == abc.2);
    assert!(xyz.0 + xyz.1 == xyz.2);
}
```

![image-20230913150304682](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230913150304682.png)

#### NaN

- 数学上未定义的结果
- 所有跟NaN交互的操作，都会返回一个NaN,而且NaN不能用来比较

```rust
fn main(){
	let x =(-42.0_f32).sqrt();
	assert_eq!(x,x);
}//代码会崩溃
```

#### 位运算

![image-20230913150856910](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230913150856910.png)

```rust
fn main() {
    // 二进制为00000010
    let a:i32 = 2;
    // 二进制为00000011
    let b:i32 = 3;

    println!("(a & b) value is {}", a & b);

    println!("(a | b) value is {}", a | b);

    println!("(a ^ b) value is {}", a ^ b);

    println!("(!b) value is {} ", !b);

    println!("(a << b) value is {}", a << b);

    println!("(a >> b) value is {}", a >> b);

    let mut a = a;
    // 注意这些计算符除了!之外都可以加上=进行赋值 (因为!=要用来判断不等于)
    a <<= b;
    println!("(a << b) value is {}", a);
}
```

#### 序列（Range）

```rust
for i in 1..=5{
	println!("{}",i);
}
```

```rust
for i in 'a'..='z'{
	println!("{}",i);
}
```

#### 有理数和复数



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

```rust
fn add_with_extra(x:i32,y:i32) ->i32{
	let x= x+1;	//语句
	let y=y+5;	//语句
	x+y			//表达式
}
```



#### 函数的返回值

##### 无返回值（）

- 函数没有返回值，那么返回一个（）
- 通过；结尾的表达式返回一个（）

##### 永不返回的发散函数！

- 当用！作函数返回类型的时候，表示该函数永不返回

```rust
fn dead_end()->!{
	panic("!!!");
}
```



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

### 3.10 模式匹配


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

##### String与&str的转换

- &str类型生成String类型

```rust
String::from("hello,world");
hello,world".to_string();
```

- String类型转换成&str类型

```rust
fn main(){
	let s =String::from("hello world");
    say_hello(&s);
    say_hello(&s[..]);
    say_hello(s.as_str());
}

fn say_hello(s:&str){
    println!("{}",s);
}
```

**deref**隐式强制转换

##### 操作字符串

- 追加（push）

​	在原有的字符串上追加，并不会返回新的字符串，字符串变量必须由mut关键字修饰

```rust
fn main(){
    let mut s= String::from("hello");
    s.push_str("rust");
    
    s.push_str("!");
}
```

- 插入

​	使用 **insert()**方法插入单个字符 **char**,也可以使用 **insert_str()**方法插入字符串字面量

```rust
fn main(){
    let mut s =String::from("hello rust!");
    s.insert(5,',');
    s.insert_str(5," i like");
}
```



- 替换

  - replace

  返回一个新的字符串，而不是操作原来的字符串

  ```rust
  fn main() {
      let string_replace = String::from("I like rust. Learning rust is my favorite!");
      let new_string_replace = string_replace.replace("rust", "RUST");
      dbg!(new_string_replace);
  }
  ```

  - replacen

  返回一个新的字符串，而不是操作原来的字符串

  ```rust
  fn main() {
      let string_replace = "I like rust. Learning rust is my favorite!";
      let new_string_replacen = string_replace.replacen("rust", "RUST", 1);
      dbg!(new_string_replacen);
  }
  ```

  - replace_range

  操作原来的字符串，不会返回新的字符串，该方法需要使用**mut**关键字修饰

  ```rust
  fn main() {
      let string_replace = "I like rust. Learning rust is my favorite!";
      let new_string_replacen = string_replace.replacen("rust", "RUST", 1);
      dbg!(new_string_replacen);
  }
  ```

- 删除（Delete）

  - pop --删除并返回字符串的最后一个字符

  **该方法是直接操作原来的字符串**

  ![image-20230914105154998](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230914105154998.png)

  - remove --删除并返回字符串中指定位置的字符

  **该方法是直接操作原来的字符串**

  ![image-20230914105337653](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230914105337653.png)

  - truncate --删除字符串中从指定位置开始到结尾的全部字符

  **该方法是直接操作原来的字符串**

  ![image-20230914105429926](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230914105429926.png)

  - clear -- 清空字符串

  ![image-20230914105454438](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230914105454438.png)

- 连接（Concatenate）

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

### 4.7 字符串转义

我们可以通过转义的方式\输出ASCII和Unicode字符

```rust
fn main() {
    // 通过 \ + 字符的十六进制表示，转义输出一个字符
    let byte_escape = "I'm writing \x52\x75\x73\x74!";
    println!("What are you doing\x3F (\\x3F means ?) {}", byte_escape);

    // \u 可以输出一个 unicode 字符
    let unicode_codepoint = "\u{211D}";
    let character_name = "\"DOUBLE-STRUCK CAPITAL R\"";

    println!(
        "Unicode character {} (U+211D) is called {}",
        unicode_codepoint, character_name
    );

    // 换行了也会保持之前的字符串格式
    // 使用\忽略换行符
    let long_string = "String literals
                        can span multiple lines.
                        The linebreak and indentation here ->\
                        <- can be escaped too!";
    println!("{}", long_string);
}
```

### 4.8 操作 UTF-8 字符串

#### 字符

```rust
for c in "中国人".chars(){
    println!("{}",c);
}
```

#### 子节

```rust
for b in "中国人".bytes(){
    println!("{}",b);
}
```

### 字符串深度刨析

### 4.9 流程控制

#### 使用if来做分支控制

- if语句块是表达式
- 用if来赋值时，要保证每个分支返回的类型一样

#### 使用else if 来处理多重条件

#### 循环控制

##### for 循环

```rust
for 元素 in 集合 {
	//使用元素干事情
}
```

如果想在循环中，修改该元素，可以使用mut关键字

```
for item in &mut collection{
	//...
}
```

| 使用方法                    | 等价使用方式                                    | 所有权     |
| --------------------------- | ----------------------------------------------- | ---------- |
| for item in collection      | for item in IntoIterator::into_iter(collection) | 转移所有权 |
| for item in &collection     | for item in collection.iter()                   | 不可变借用 |
| for item in &mut collection | for item in collection.iter_mut()               | 可变借用   |

###### 如何在循环中获取元素的索引：

```rust
fn main(){
	let a =[4,3,2,1];
    for(i,v) in a.iter().enumerate(){
        println!("第{}个元素是{}",i+1,v);
    }
}
```

###### 不声明变量循环

```rust
fn main(){
    for _ in 0..10{
        //...
    }
}
```

##### continue

使用continue可以跳过当前的循环

```rust
for i in 1..4{
	if i==2{
		continue;
	}
	println!("{}",i);
}
```

##### break

使用break可以跳出当前循环

```rust
for i in 1..4{
    if i==2{
        break;
    }
    println!("{}",i);
}
```

##### while循环

**for循环更安全也更简洁，同时避免了运行时候的边界检查**

##### loop循环

loop + if +break;

```rust
fn main(){
    let mut counter =0;
    let Result=loop{
        counter+=1;

        if counter==10{
            break counter*2;
        }
    };

    println!("the number of counter is {}",Result);
}
```

- break可以单独使用，也可以带一个返回值，优点类似return
- loop是一个表达式，因此可以返回一个值

#### 匹配守卫提供的额外条件

**匹配守卫**是一个位于match分支模式之后的额外if条件，它能为分支模式更进一步的匹配条件

```rust
let num =Some(4);
match num{
    Some(X) if x<5=>println!("less than five:{}",x),
    Some(x) =>println!("{}",x),
    None=>().
}
```

#### @绑定



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
  
  ```rust
  match target{
      模式1 => 表达式1，
      模式2 => {
          语句1;
          语句2;
          表达式
      },
      _=>表达式3
  }
  ```
  
  

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


### 6.5 matches!宏

可以将一个表达式跟模式进行匹配，然后返回匹配的结果true Or false

```rust
enum MyEnum{
    Foo,
    Bar
}

fn main(){
    let v= vec![MyEnum::Foo,MyEnum::Bar,MyEnum::Foo];
    
    v.iter().filter(|x|matches!(x,MyEnum::Foo));
}
```

### 6.6 变量遮蔽

无论是match还是 if let ，这里都是一个新的代码块，而且这里的绑定相当于新变量，如果你使用同名变量，会发生变量遮蔽：

```
fn main() {
   let age = Some(30);
   println!("在匹配前，age是{:?}",age);
   if let Some(age) = age {
       println!("匹配出来的age是{}",age);
   }

   println!("在匹配后，age是{:?}",age);
}
```

![image-20230914184912531](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230914184912531.png)

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

- 在Rust中，父模块完全无法访问子模块中的私有项，但是子模块却可以访问父模块、父父..模块的私有项

#### pub关键字

rust提供了 **pub**关键字，通过它你可以控制模块和模块中指定项的可见性

```rust
mod front_of_house{
	pub mod hosting{
		pub fn add_to_waitlist(){
			//....		
		}
	}
}
```

#### super关键字

#### 结构体和枚举的可见性

- 将结构体设置为pub，但它的所有字符依然是私有的
- 将枚举设置为pub，它的所有字段也将对外可见

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

```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // 绝对路径
    crate::front_of_house::hosting::add_to_waitlist();

    // 相对路径
    front_of_house::hosting::add_to_waitlist();
}
```



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

------

如果预先知道要存储的元素个数，可以使用Vec::with_capacity(capacity)创建动态数组，这样可以避免因为插入大量新数据导致频繁的内存分配和拷贝，提高性能

------



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

#### 浮点数数组的排序

```rust
// fn main(){
//     let mut vec=vec![1.0,5.6,2.0,15f32];
//     vec.sort_unstable();
//     assert_eq!(vec,vec![1.0,2.0,5.6,15f32]);

// }

fn main(){
    let mut vec=vec![1.0,5.6,2.0,15f32];
    vec.sort_unstable_by(|a,b| a.partial_cmp(b).unwrap());
    assert_eq!(vec,vec![1.0,2.0,5.6,15f32]);

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

#### 收集值到HashMap

```rust
use std::collections::HashMap;

fn main(){
    let team_list=vec![
        ("美国队".to_string(),100),
        ("美国队".to_string(),10),
        ("美国队".to_string(),50),
    ];

    let teams_map:HashMap<_,_>=team_list.into_iter().collect();
    println!("{:#?}",teams_map);
}

// fn main() {
//     use std::collections::HashMap;

//     let teams_list = vec![
//         ("中国队".to_string(), 100),
//         ("美国队".to_string(), 10),
//         ("日本队".to_string(), 50),
//     ];

//     let teams_map: HashMap<_,_> = teams_list.into_iter().collect();
    
//     println!("{:?}",teams_map)
// }
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

#### backtrave栈展开

#### panic时的两种终止方式

栈展开和直接终止

修改Cargo.toml文件，实现在release模式下遇到panic直接终止：

```rust
[profile.release]
panic="abort"
```

#### 线程panic后，程序是否会终止

如果是 **main**线程，则程序会终止，如果是其他子线程，该线程会终止，但是不会影响 **main**线程

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

```rust
fn main(){
    let mut personList:Vec<Box<dyn Role>>=Vec::new();

    let student=Student;
    let teacher=Teacher;

    personList.push(Box::new(teacher));
    personList.push(Box::new(student));

    for role in personList{
        role.print_role();
    }

}

struct Teacher;
struct Student;

trait Role{
    fn print_role(&self);
}

impl Role for Teacher{
    fn print_role(&self) {
        println!("this is a teacher");
    }
}

impl Role for Student{
    fn print_role(&self){
        println!("this is a student");
    }
}
```



### self和Self

在Rust中，有两个self,一个指代当前的实例对象，一个指代特征或者方法类型的别名：

```rust

trait Draw{
    fn draw(&self)->Self;
}

#[derive(Clone)]
struct Button;
impl  Draw for Button{
    fn draw(&self)->Self{
        return self.clone()
    }
}
    


fn main(){
    let button =Button;
    let newb=button.draw();    
}
```

### 特征对象的限制

不是所有特征都能拥有特征对象，只有对象安全的特征才行，当一个特征的所有方法都有如下属性时，它的对象才是安全的:

- 方法的返回类型不能是 **Self**
- 方法没有任何泛型参数



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

#### 无界生命周期

往往是在解引用一个裸指针时产生的

```rust
fn f<'a,T>(x:*const T)->&'a T{
    unsafe{
        &*x
    }
}'
```

**若一个输出生命周期被消除了，那么必定因为有一个输入生命周期与之对应**

#### 生命周期约束HRTB

通过形如 **‘a:'b**的语法来说明两个生命周期的长短关系，'a>=’b

```rust
struct DoubleRef<'a,'b:'a,T>{
    r:&'a T,
    s:&'b T
}
```

#### 闭包函数的消除规则

![image-20230919214745913](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230919214745913.png)

#### NLL（Non-Lexical Lifetime）

```rust
fn main() {
   let mut s = String::from("hello");

    let r1 = &s;
    let r2 = &s;
    println!("{} and {}", r1, r2);
    // 新编译器中，r1,r2作用域在这里结束

    let r3 = &mut s;
    println!("{}", r3);
}
```

#### Reborrow再借用

```rust
struct Interface<'b, 'a: 'b> {
    manager: &'b mut Manager<'a>,
}

impl<'b, 'a: 'b> Interface<'b, 'a> {
    pub fn noop(self) {
        println!("interface consumed");
    }
}

struct Manager<'a> {
    text: &'a str,
}

struct List<'a> {
    manager: Manager<'a>,
}

impl<'a> List<'a> {
    pub fn get_interface<'b>(&'b mut self) -> Interface<'b, 'a>
    where
        'a: 'b,
    {
        Interface {
            manager: &mut self.manager,
        }
    }
}
fn main() {
    let mut list=List{
        manager:Manager { text: "hello" }
    };

    list.get_interface().noop();
    println!("!!!!");
    use_list(&list);  

}

fn use_list(list:&List){
    println!("{}",list.manager.text);
}

```



### 10.5 &'static 和 T:'static

##### &'static

一个引用必须要活得和剩下的程序一样久，才能被标注为&’static

&'static生命周期针对的仅仅是引用，而不是持有该引用的变量，对于变量来说，还是要遵循相应的作用域规则

##### T:' static

```rust
use std::fmt::Debug;

fn print_it<T: Debug + 'static>( input: &T) {
    println!( "'static value passed in is: {:?}", input );
}

fn main() {
    let i = 5;

    print_it(&i);
}
```

#### static到底针对谁？

**答案是引用指向的数据**

```rust
fn main() {
    {
        let static_string = "I'm in read-only memory";
        println!("static_string: {}", static_string);

        // 当 `static_string` 超出作用域时，该引用不能再被使用，但是数据依然会存在于 binary 所占用的内存中
    }

    println!("static_string reference remains alive: {}", static_string);
}
```



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

## 13 闭包

### 13. 1 闭包 使用闭包创建抽象行为

#### 什么是闭包

- 闭包：可以捕获其所在环境的匿名函数
- 闭包：
  - 是匿名函数
  - 保存为变量、作为参数
  - 可在一个地方创建闭包，然后在另一个上下文中调用闭包来完成运算
  - 可从其定义的作用域捕获值

```rust
let expensive_closure =|num|{
    println!("calculating slowly ...");
    thread::sleep(Duration::from_secs(2));
    num
};
```

### 13.2 类型推断和标注

- 闭包不要求标注参数和返回值的类型
- 闭包通常很短小，只在狭小的上下文中工作，编译器通常能推断出类型
- 可以手动添加类型标注
- **当编译器推导出一种类型后，它就会一直使用该类型**

```rust
let expensive_closure =|num:u32|->u32{
    println!("calculating slowly...");
    therad::sleep(Duration::from_secs(2));
    num
};
```

![image-20230919222355685](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230919222355685.png)

#### 捕获作用域中的值

在之前代码中，我们一直用闭包的匿名函数特性（赋值给变量），然而闭包还拥有一项函数所不具备的特性：捕获作用域中的值

```rust
fn main(){
    let x=4;
    let equal_to_x=|z|z==x;
    let y=4;
    assert!(equal_to_x(y));
}
```

#### 闭包对内存的影响

当闭包从环境中捕获一个值时，会分配内存去存储这些值。

与之想比，函数就不会去捕获这些环境值，因此定义和使用函数不会拥有这种内存负担

#### 函数和闭包的定义语法

```rust
fn  add_one_v1 (x:u32) ->u32 {x+1}
let add_one_v2=|x:u32| ->u32 {x+1};
let add_one_v3=|x| 			 {x+1};
let add_one_v4=|x|			  x+1;
```

- 注意：闭包的定义最终只会为参数/返回值推断出唯一具体的类型

### 13.3 使用泛型和Fn Trait存储闭包

```32
struct Cacher<T>
where
	T:Fn(u32) ->u32,
{
	calculation:T,
	value:Option<u32>,
}	

impl<T> Cacher<T>
where
	T:Fn(u32) ->u32,
{
	fn new(calculation :T) ->Cacher<T>{
		Cacher{
			calculation,
			value:None,
		}
	}
	
	fn value(&mut self,arg:u32) ->u32{
		match self.value{
			Some(v)=>v,
			None=>{
				let v=(self.calculation)(arg);
				self.value=Some(v);
			}
		}
	}
}
```



#### 使用缓存器（Cacher）实现的限制

1. Cacher 实例假定针对不同的参数arg,value方法总会得到同样的值
   1. 可以使用HashMap代替单个值
      - key:arg 参数
      - value：执行闭包的结果
   2. 只能接收一个u32类型的参数和u32类型的返回值

### 13.4 使用闭包捕获上下文

- 闭包可以访问定义它的作用域内的变量，而普通函数则不能
- 会产生内存开销

#### 闭包从所在环境捕获值得方式

- 与函数获得参数得三种方式一样
  1. 取得所有权：FnOnce
  2. 可变借用：FnMut
  3. 不可变借用：Fn

#### move关键字

- 在参数列表使用move关键字，可以强制闭包取地它所使用得环境值得所有权
  - 当将闭包传递给新线程以移动数据使其归新线程所有时，此技术最为有用

#### filter 方法：

- 接收一个闭包
- 这个闭包在遍历迭代器的每个元素时，返回bool类型
- 如果闭包返回true,当前元素将会包含在filter产生的迭代器中
- 如果闭包返回false:放前元素将不会包含在filter产生的迭代器中

```rust
struct Shoe{
    size :u32,
    style:String,
}

fn shoes_in_my_size(shoes:Vec<Shoe>,shoe_size:u32)->Vec<Shoe>{
    shoes.into_iter().filter(|x| x.size==shoe_size).collect()
}
```

#### 三种Fn特征

1. FnOnce,该类型的闭包会拿走被捕获变量的所有权。Once说明该闭包只能运行一次：

   ```rust
   fn fn_once<F>(func: F)
   where
       F: FnOnce(usize) -> bool + Copy,// 改动在这里
   {
       println!("{}", func(3));
       println!("{}", func(4));
   }
   
   fn main() {
       let x = vec![1, 2, 3];
       fn_once(|z|{z == x.len()})
   }
   ```

   

2. FnMut，它以可变借用的方式捕获了环境中的值，因此可以修改该值

   ```tust
   fn main() {
       let mut s = String::new();
   
       let mut update_string =  |str| s.push_str(str);
       update_string("hello");
   
       println!("{:?}",s);
   }
   ```

   

3. Fn 不可变借用

#### 闭包作为返回值

```rust
fn factory(x:i32) -> Box<dyn Fn(i32) -> i32> {
    let num = 5;

    if x > 1{
        Box::new(move |x| x + num)
    } else {
        Box::new(move |x| x - num)
    }
}

```



### 13.5 迭代器

- 迭代器模式：对一系列项执行某些任务
- 迭代器负责：
  - 遍历每个项
  - 确定序列何时完成
- Rust的迭代器：
  - 懒惰的：除非调用消费迭代器的方法，否则迭代器本身没有任何效果

#### iterator trait

- 所有迭代器都实现了iterator trait
- iterator trait定义于标准库，定义大致如下：

```rust
pub trait iterator{
    type item;
    fn next(&mut self)->Option<Self::Item>;
}
```

- 仅要求实现一个方法：next
- next
  - 每次返回迭代器中的一项
  - 返回结果包裹在Some里
  - 迭代结束，返回None
- 可直接在迭代器上调用next方法

#### 几个迭代方法

- iter方法：在不可变引用上创建迭代器
- into_iter方法：创建的迭代器会获得所有权
- iter_mut方法：迭代可变的引用

#### 消耗迭代器的方法

- 调用next的方法叫做"消耗型适配器"

```rust
  #[test]
    fn iterator_sum(){
        let v1:Vec<i32> =vec![1,2,3];
        let v2:Vec<_> =v1.iter().map(|x|x+1).collect();
        
        assert_eq!(v2,[2,3,4]);
    }
```

#### 自定义迭代器

#### 零开销抽象

- 使用抽象时不会引入额外的运行时开销

#### 惰性初始化

### 13.6 包Crate



## 14  crateio

### 14.1 通过release profile来自定义构建

- release profile:
  - 是预定义的
  - 可自定义：可使用不同的配置，对代码编译拥有更多的控制
- 每个profile的配置都独立于其他的profile
- Cargo 主要的两个profile
  - dev profile :适用于开发， cargo build
  - release profile:适用于发布 ，cargo build -release

#### 自定义profile

```rust
[profile.dev]
opt-level =1

[profile.release]
opt-level =3
```

## 15 类型转换

### as

#### 使用as进行类型转换

## 16 注释和文档

### 注释的种类

- 代码注释，用来说明某一块代码的功能
- 文档注释，支持Markdown
- 包和模块注释,严格来说这也是文档注释中的一种

### 代码注释

- 行注释 //
- 块注释 /**/

### 文档注释

- 文档行注释 ///
  - 文档注释需要位于 **lib**类型的包中
  - 文档注释可以使用markdown语法
  - 被注释的对象需要使用pub对外可见
- 文档块注释 /**  */
- 查看文档 cargo doc 或 cargo doc --open

### 包和模块级别的注释

这些注释要添加到包、模块的最上方

- 行注释//!
- 块注释/*! ... */

### 文档测试（Doc Test）

## 17 格式化输出

### print! ,println! ,format!

- print! 将格式化文本输出到标准输出，不带换行符
- println! 同上，但是在行的末尾添加换行符
- format! 将格式化文本输出到String 字符串

### eprint! ,eprintln!

除了三大金刚外，还有两大护法，使用方式跟print！，println！很像，但是它们输出到标准错误输出

```rust
eprintln!("error: could not complete task")
```

### {} 与

与{}相似，{：？}也是占位符

- {}适用于实现std::fmt::Display 特征的类型
- {:?}适用于实现了std::fmt::Debug特征的类型

#### Debug特征

```rust
#[derive(Debug)]
struct Person {
    name: String,
    age: u8
}

fn main() {
    let i = 3.1415926;
    let s = String::from("hello");
    let v = vec![1, 2, 3];
    let p = Person{name: "sunface".to_string(), age: 18};
    println!("{:?}, {:?}, {:?}, {:?}", i, s, v, p);
}
```

对于数值、字符串、数组，可以直接使用{:?}进行输出，但是对于结构体，需要派生Debug特征后，才能进行输出

#### Display特征

##### 使用{:?}或{:#?}

##### 为自定义类型实现Display特征

```rust
struct Person {
    name: String,
    age: u8,
}

use std::fmt;
impl fmt::Display for Person {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(
            f,
            "大佬在上，请受我一拜，小弟姓名{}，年芳{}，家里无田又无车，生活苦哈哈",
            self.name, self.age
        )
    }
}
fn main() {
    let p = Person {
        name: "sunface".to_string(),
        age: 18,
    };
    println!("{}", p);
}
```

实现 **Display**特征中的 **fmt**方法

##### 使用newtype 为外部类型实现Display特征

```rust
struct Array(Vec<i32>);

use std::fmt;
impl fmt::Display for Array {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "数组是：{:?}", self.0)
    }
}
fn main() {
    let arr = Array(vec![1, 2, 3]);
    println!("{}", arr);
}
```

#### 位置参数

让指定位置的参数去替换某个占位符，例如{1}，表示用第二个参数替换该占位符

```rust
fn main() {
    println!("{}{}", 1, 2); // =>"12"
    println!("{1}{0}", 1, 2); // =>"21"
    // => Alice, this is Bob. Bob, this is Alice
    println!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob");
    println!("{1}{}{0}{}", 1, 2); // => 2112
}
```

#### 具名参数

为参数指定名称

```rust
fn main() {
    println!("{argument}", argument = "test"); // => "test"
    println!("{name} {}", 1, name = 2); // => "2 1"
    println!("{a} {c} {b}", a = "a", b = 'b', c = 3); // => "a 3 b"
}
```

**带名称的参数必须放在不带名称参数的后面**

![image-20230919144407842](C:\Users\xpro\AppData\Roaming\Typora\typora-user-images\image-20230919144407842.png)

#### 格式化参数

```rust
fn main(){
    let v=3.1415926;
    println!("{:.2}",v);   //Display =>3.14
    println!("{:.2?}",v);  //Debug=>3.14
}
```

##### 宽度

指定输出目标的长度，如果长度不够，则进行填充和对齐；

##### 字符串填充

字符串格式化默认使用空格进行填充，并且进行左对齐

```rust
fn main() {
    //-----------------------------------
    // 以下全部输出 "Hello x    !"
    // 为"x"后面填充空格，补齐宽度5
    println!("Hello {:5}!", "x");
    // 使用参数5来指定宽度
    println!("Hello {:1$}!", "x", 5);
    // 使用x作为占位符输出内容，同时使用5作为宽度
    println!("Hello {1:0$}!", 5, "x");
    // 使用有名称的参数作为宽度
    println!("Hello {:width$}!", "x", width = 5);
    //-----------------------------------

    // 使用参数5为参数x指定宽度，同时在结尾输出参数5 => Hello x    !5
    println!("Hello {:1$}!{}", "x", 5);
}
```

##### 数字填充：符号和0

数字格式化默认也是使用空格进行填充，但与字符串左对齐不同的是，数字是右对齐

```rust
fn main() {
    // 宽度是5 => Hello     5!
    println!("Hello {:5}!", 5);
    // 显式的输出正号 => Hello +5!
    println!("Hello {:+}!", 5);
    // 宽度5，使用0进行填充 => Hello 00005!
    println!("Hello {:05}!", 5);
    // 负号也要占用一位宽度 => Hello -0005!
    println!("Hello {:05}!", -5);
}
```

#### 对齐

```rust
fn main() {
    // 以下全部都会补齐5个字符的长度
    // 左对齐 => Hello x    !
    println!("Hello {:<5}!", "x");
    // 右对齐 => Hello     x!
    println!("Hello {:>5}!", "x");
    // 居中对齐 => Hello   x  !
    println!("Hello {:^5}!", "x");

    // 对齐并使用指定符号填充 => Hello x&&&&!
    // 指定符号填充的前提条件是必须有对齐字符
    println!("Hello {:&<5}!", "x");
}
```

#### 精度

可以用于控制浮点数的精度或者字符串的长度

```rust
fn main() {
    let v = 3.1415926;
    // 保留小数点后两位 => 3.14
    println!("{:.2}", v);
    // 带符号保留小数点后两位 => +3.14
    println!("{:+.2}", v);
    // 不带小数 => 3
    println!("{:.0}", v);
    // 通过参数来设定精度 => 3.1416，相当于{:.4}
    println!("{:.1$}", v, 4);

    let s = "hi我是Sunface孙飞";
    // 保留字符串前三个字符 => hi我
    println!("{:.3}", s);
    // {:.*}接收两个参数，第一个是精度，第二个是被格式化的值 => Hello abc!
    println!("Hello {:.*}!", 3, "abcdefg");
}
```

#### 进制

可以使用#号来控制数字的进展输出：

- #b,二进制
- #o,八进制
- #x,小写十六进制
- #X,大写十六进制
- x，不带前缀的小写十六进制

```rust
fn main() {
    // 二进制 => 0b11011!
    println!("{:#b}!", 27);
    // 八进制 => 0o33!
    println!("{:#o}!", 27);
    // 十进制 => 27!
    println!("{}!", 27);
    // 小写十六进制 => 0x1b!
    println!("{:#x}!", 27);
    // 大写十六进制 => 0x1B!
    println!("{:#X}!", 27);

    // 不带前缀的十六进制 => 1b!
    println!("{:x}!", 27);

    // 使用0填充二进制，宽度为10 => 0b00011011!
    println!("{:#010b}!", 27);
}
```

#### 指数

```rust
fn main() {
    println!("{:2e}", 1000000000); // => 1e9
    println!("{:2E}", 1000000000); // => 1E9
}
```

#### 指针地址

```rust
let v= vec![1, 2, 3];
println!("{:p}", v.as_ptr()) // => 0x600002324050
```

#### 转义

```rust
fn main() {
    // "{{" 转义为 '{'   "}}" 转义为 '}'   "\"" 转义为 '"'
    // => Hello "{World}" 
    println!(" Hello \"{{World}}\" ");

    // 下面代码会报错，因为占位符{}只有一个右括号}，左括号被转义成字符串的内容
    // println!(" {{ Hello } ");
    // 也不可使用 '\' 来转义 "{}"
    // println!(" \{ Hello \} ")
}
```



