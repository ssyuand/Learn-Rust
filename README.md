# Learn Rust


## Reference documents:

[Rust core... - sc](https://rustwiki.org/zh-CN/core/index.html)
[Rust core... - en](https://doc.rust-lang.org/core/)

[Rust std... - sc](https://rustwiki.org/zh-CN/std/index.html)
[Rust std... - en](https://doc.rust-lang.org/std/)

[The Rust Reference - sc](https://rustwiki.org/zh-CN/reference/introduction.html)
[The Rust Reference - en](https://doc.rust-lang.org/reference/introduction.html)

[Rust by Example - sc](https://rustwiki.org/zh-CN/rust-by-example/)
[Rust by Example - en](https://doc.rust-lang.org/rust-by-example/index.html)

[The Rust Programming Language - tc](http://askeing.github.io/rust-book/README.html)
[The Rust Programming Language - sc](https://rust-lang.tw/book-tw/)
[The Rust Programming Language - en](https://doc.rust-lang.org/book/title-page.html)

point:
語句 vs 表達式
[self vs Self](https://hackmd.io/copSHH3FTAuwdKiIZAPmcQ?view#%E7%B4%B0%E8%AA%AA-Self-self-%E5%B7%AE%E5%88%A5%EF%BC%9A)
[Static Function vs Method](https://hackmd.io/copSHH3FTAuwdKiIZAPmcQ?view#Static-Function) + [Function and Method Calls](https://hackmd.io/fAnfB65TSA6M8To1Yq3amA#Function-and-Method-Calls)
## A Tour of Rust

[A Simple Web Server](https://github.com/ssyuand/Learn-Rust/blob/main/src/main.rs)
> To show how this works, we’ll put together a simple web server using the iron web
framework, the hyper HTTP server, and various other crates on which they depend.
## Basic Types
### Machine Types
| Size (bits)  | Unsigned integer | Signed integer | Floating-point |
| ------------ | ---------------- | -------------- | -------------- |
| 8            | u8               | i8             |                |
| 16           | u16              | i16            |                |
| 32           | u32              | i32            | f32            |
| 64           | u64              | i64            | f64            |
| Machine word | usize            | isize          |                |

Here, a machine word is a value the size of an address on the machine the code runs
on, usually 32 or 64 bits.
### Integer Types
####  Unsigned integer types
Rust’s unsigned integer types use their full range to represent positive values and zero:

| Type | Range | 
| -------- | -------- |
|        u8|0 to $2^{8}-1$ (0 to 255)|
|     u16  |0 to $2^{16}−1$ (0 to 65,535)|
|      u32 |0 to $2^{32}−1$ (0 to 4,294,967,295)|
|      u64 |0 to $2^{64}−1$ (0 to 18,446,744,073,709,551,615, or 18 quintillion)|
|     usize|0 to either $2^{32}−1$ or $2^{64}−1$

#### Signed integer types
Rust’s signed integer types use the two’s complement representation, using the same
bit patterns as the corresponding unsigned type to cover a range of positive and nega‐
tive values:


| Type  | Range                                                                             |
| ----- | --------------------------------------------------------------------------------- |
| i8    | $−2^7$ to $2^7−1$(−128 to 127)                                                    |
| i16   | $−2^{15}$ to $2^{15}−1$ (−32,768 to 32,767)                                       |
| i32   | $−2^{31}$ to $2^{31}−1$ (−2,147,483,648 to 2,147,483,647)                         |
| i64   | $−2^{63}$ to $2^{63}−1$ (−9,223,372,036,854,775,808 to 9,223,372,036,854,775,807) |
| isize | Either $-2^{31}$ to $2^{31}−1$, or $−2^{63}$ to $2^{63}−1$                        |

- Rust generally uses the u8 type for byte values. Unlike C and C++, Rust treats characters as distinct from the numeric types; a char is neither a u8 nor an i8. // 預設byte為u8 , ***char不是i8/u8, usize/isize 精度取決目標機器的取址空間大小***。
-  Values representing the sizes of arrays or vectors or counts of the number of elements in some data structure also generally have the usize type. // array 的索引必須是 usize 類型的。

In debug builds, Rust checks for integer overflow in arithmetic:
```rust=
let big_val = std::i32::MAX; 
let x = big_val + 1; // panic: arithmetic operation overflowed
```
[參考Rust聖經](https://course.rs/basic/base-type/numbers.html) Rust 默認使用 i32 類型
- 增加數字易讀性使用下滑線 ex: 0xffff_ffff / 4_294_967 / 127_u8
如果沒有給定後綴 ex:42u8，則Rust 會利用上下文推斷其類型。
另外，整數字面量可以使用前綴 0x、0o 和 0b 分別表示十六進制、八進制和二進制。

Some rust style examples of integer literals:

| Literal     | Type     | Decimal value |
| ----------- | -------- | ------------- |
| 116i8       | i8       | 116           |
| 0xcafeu32   | u32      | 51966         |
| 0b0010_1010 | Inferred | 42            |
| 0o106       | Inferred | 70            |

最後兩個 Inferred, Rust 會依上下文推斷類型!

儘管Rust數值類型跟 char 類型不一樣， 但Rust提供了（byte literal），用類字符字面量表示 u8:b'x' 表示ASCII編碼的x,他是一個 u8 值。 ex: A 的 ASCII 值為 65, 因此 b'A' 等於 65u8。 


There are a few characters that you cannot simply place after the single quote,
because that would be either syntactically ambiguous or hard to read. The following
characters require a backslash placed in front of them( 須轉義字符 ):

| Character       | Byte literal | Numeric equivalent |
| --------------- | ------------ | ------------------ |
| Single quote, ' | b'\''        | 39u8               |
| Backslash, \    | b'\\'        | 92u8               |
| Newline         | b'\n'        | 10u8               |
| Carriage return | b'\r'        | 13u8               |
| Tab             | b'\t'        | 9u8                |


### Floating-Point Types
浮點數根據 IEEE-754 標準實現。 f32 類型是單精度浮點型，f64 為雙精度。

| Type | Precision                                          | Range                                |
| ---- | -------------------------------------------------- | ------------------------------------ |
| f32  | IEEE single precision (at least 6 decimal digits)  | Roughly –3.4 × 1038 to +3.4 × 1038   |
| f64  | IEEE double precision (at least 15 decimal digits) | Roughly –1.8 × 10308 to +1.8 × 10308 |

#### NaN
對於數學上未定義的結果，例如對負數取平方根 -42.1.sqrt() ，會產生一個特殊的結果：Rust 的浮點數類型使用 NaN (not a number)來處理這些情況。
> is_nan() 保險起見檢測是否是NaN
### **浮點數陷阱**
[截錄Rust聖經](https://course.rs/basic/base-type/numbers.html#%E6%B5%AE%E7%82%B9%E6%95%B0%E9%99%B7%E9%98%B1)
- 避免在浮點數上測試相等性
- 當結果在數學上可能存在未定義時，需要格外的小心
```rust=
fn main() {
  assert!(0.1 + 0.2 == 0.3);
}
```
\# RUST_BACKTRACE=1 cargo run
\# thread 'main' panicked at 'assertion failed: 0.1 + 0.2 == 0.3', src/main.rs:3:5
stack backtrace:
   0: rust_begin_unwind
             at /rustc/fe5b13d681f25ee6474be29d748c65adcd91f69e/library/std/src/panicking.rs:584:5
   1: core::panicking::panic_fmt
             at /rustc/fe5b13d681f25ee6474be29d748c65adcd91f69e/library/core/src/panicking.rs:143:14
   2: core::panicking::panic
             at /rustc/fe5b13d681f25ee6474be29d748c65adcd91f69e/library/core/src/panicking.rs:48:5
...

實際上它會 panic(程序崩潰，拋出異常)，因為二進制精度問題，導致了 0.1 + 0.2 並不嚴格等於 0.3，它們可能在小數點 N 位後存在誤差。

那如果非要進行比較呢？可以考慮用這種方式 (0.1_f64 + 0.2 - 0.3).abs() < 0.00001 ，具體小於多少，取決於你對精度的需求。
- panic (崩潰,Rust 使用這個術語來表明程序因錯誤而退出)

```rust=
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
output:
abc (f32)
   0.1 + 0.2: 3e99999a
         0.3: 3e99999a

xyz (f64)
   0.1 + 0.2: 3fd3333333333334
         0.3: 3fd3333333333333

thread 'main' panicked at 'assertion failed: xyz.0 + xyz.1 == xyz.2', src/main.rs:16:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace

> 對 f32 類型做加法時，0.1 + 0.2 的結果是 3e99999a，0.3 也是 3e99999a，因此 f32 下的 0.1 + 0.2 == 0.3 通過測試，但是到了 f64 類型時，結果就不一樣了，因為 f64 精度高很多，因此在小數點非常後面發生了一點微小的變化，0.1 + 0.2 以 4 結尾，但是 0.3 以3結尾，這個細微區別導致 f64 下的測試失敗了，並且拋出了異常。
 
 A floating-point literal
![](https://i.imgur.com/fPHiNHt.png)
除了integer part 其他部份都是可選的,小數也可以只有一個小數點。ex:5. 
一樣如果沒有type suffix, Rust 會依照上下文自己判斷,
如果都有可能， 默認則為f64。


| Literal | Type     | Mathematical value              |
| ------- | -------- | ------------------------------- |
| –1.5625 | Inferred | $-(1{\cfrac{9}{16}})$           |
| 2.      | Inferred | 2                               |
| 0.25    | Inferred | $\cfrac{1}{4}$                  |
| 1e4     | Inferred | 10,000                          |
| 40f32   | f32      | 40                              |
| Text    | f64      | Roughly 9.10938356 × $10^{–31}$ |
> 程式設計中用e或E來表示10的冪次 1E4/1e4 = $10^4$

有時上下文無法判斷出類型，就必須給定後綴type suffix
```rust=
println!("{}", (2.0).sqrt());
```
output:
error: no method named `sqrt` found for type `{float}` in the current scope

改用 type suffix 或寫出你寫到的類型
```rust=
println!("{}", (2.0_f64).sqrt());
println!("{}", f64::sqrt(2.0));
```
由此可見Rust 幾乎沒有隱性數值轉換。如果函數接收 f64 參數，傳入 i32 值就會導致錯誤。事實上，Rust 甚至都不會隱式地將 i16 值轉換為 i32 值，即使每個 i16 值也是 i32 值。
C/C++ 常見的隱式轉換導致的bug跟安全漏洞, Rust 要求明確寫出數值類型轉換，讓我們多次避
免了問題。

### The bool Type
c/c++ 會隱性地將浮點數，字符，整數，指針轉換成boolean, 這些值可以直接用在if while等判斷式中。
Python 允許在bool上下文中使用字符串、列表、字典，甚至集合，如果這些值不為空，則將它們視為 true。
Rust 則非常嚴格：if 和 while 等控制結構要求它們的條件必須是 bool 表達式，短路邏輯操作符(short-circuiting logical operators) && 和||同樣也是如此。換句話說，if x { ... } 不正確，必須寫成 if x != 0 { ... } 才行。

雖然 bool 只需要一位即可表示，但 Rust 在內存裡會使用整整一個字節作為 bool 值，因此可以創建一個指向它的指針。

### Characters
char 大小為為32-bit的形式表示單個 Unicode 字符。
unlike C string 字符數組 char array, Rust 的字串String是將其文本表示為一個UTF-8的位元組/字節(byte)序列。
***String 在rust是一個很複雜的概念：字符串，切片(slice)，string...[詳細閱讀](https://course.rs/basic/compound-type/string-slice.html)***
### String 
C++有兩種String， 有const char \* 和標準庫裡的 std::string。Rust也採用類似的設計。 
[以下引用至開源教學Rust 程式設計字串 (String)](https://opensourcedoc.com/rust-programming/string/)
Rust 的字串有以下兩種：

- String 類別是以 UTF8 編碼、可伸縮的字串
- str 是基礎型別，通常是使用 &str，即指向 str 的參考

另外，Rust 還有字元 (char) 型別，同樣也是以 UTF8 編碼。

建立字串常數時，預設型別是 &str，即指向 str 的參考，但是，若想要使用 String 類別的方法時，可以轉為 String 物件。
```rust=
fn main() {
   // Reference to str
   let s: &str = "Hello";
 
   // String object
   let s_obj: String = String::from("Hello");
}
```
#### 對字串進行索引
由於 Rust 字串為 UTF8 字串，其內部長度和字元數不同，不能直接對其進行索引。以下的範例是錯誤的：
```rust=
fn main() {
assert_eq!("Hello"[0], "H");
}
```
如果想要得到單一的字元，需用 chars 方法，會得到走訪字元的迭代器。

```rust=
fn main() {
assert_eq!("你好，麥可".chars().nth(1).unwrap(), '好');
}
```
字串相接
字串間可以用 +相接，相接的方式是以 String 物件接 &str，如下：
```rust=
fn main() {
    assert_eq!("Hello ".to_string() + "World", "Hello World");
}
```
如果要將兩個 String 物件相接，則要將 String 物件取參考，轉為 &str，如下：
* 使用 + 或者 += 連接字符串，要求右邊的參數必須為字符串的切片引用（Slice )類型。
* 因為這裡的 + 調用了標準庫的.[fn add(self, other: &str) -> String](https://doc.rust-lang.org/std/string/struct.String.html#method.add)方法的第二個參數是一個引用的類型。因此我們在使用 +， 必須傳遞切片引用類型。不能直接傳遞 String 類型。 + 和 += 都是返回一個新的字符串。所以變量聲明可以不需要 mut 關鍵字修飾。
```rust=
fn main() {
    let s1 = String::from("Hello ");
    let s2 = String::from("World");
    assert_eq!(s1 + &s2, "Hello World");
}
```
```rust=
fn main() {
    let s1 = String::from("hello,");
    let s2 = String::from("world!");
    // 在下句中，s1的所有權被轉移走了，因此後面不能再使用s1
    let s3 = s1 + &s2;
    assert_eq!(s3,"hello,world!");
    // 下面的語句如果去掉註釋，就會報錯
    // println!("{}",s1);
}
```
s1 這個變量通過調用 add() 方法後，所有權被轉移到 add() 方法裡面， add() 方法調用後就被釋放了，同時 s1 也被釋放了。再使用 s1 就會發生錯誤。這裡涉及到[所有權轉移（Move）](https://course.rs/basic/ownership/ownership.html#%E8%BD%AC%E7%A7%BB%E6%89%80%E6%9C%89%E6%9D%83)的相關知識。

簡單說明：
> 當變量離開作用域後，Rust 會自動調用 drop 函數並清理變量的堆內存。不過由於兩個 String 變量指向了同一位置。這就有了一個問題：當 s1 和 s2 離開作用域，它們都會嘗試釋放相同的內存。這是一個叫做 二次釋放（double free） 的錯誤，也是之前提到過的內存安全性 BUG 之一。兩次釋放（相同）內存會導致內存污染，它可能會導致潛在的安全漏洞。

因此，Rust 這樣解決問題：當 s1 賦予 s2 後，Rust 認為 s1 不再有效，因此也無需在 s1 離開作用域後 drop 任何東西，這就是把所有權從 s1 轉移給了 s2，s1 在被賦予 s2 後就馬上失效了。

總結規則：
1. Rust 中每一個值都被一個變量所擁有，該變量被稱為值的所有者
2. 一個值同時只能被一個變量所擁有，或者說一個值只能擁有一個所有者
3. 當所有者(變量)離開作用域範圍時，這個值將被丟棄(drop)
例子：
```rust=
let s1 = String::from("hello");
let s2 = s1;

println!("{}, world!", s1);
```
![](https://i.imgur.com/HvpdZj1.png)
這樣就解決了我們之前的問題，s1 不再指向任何數據，只有 s2 是有效的，當 s2 離開作用域，它就會釋放內存。相信此刻，你應該明白了，為什麼 Rust 稱呼 let a = b 為變量綁定了吧？

再来看一段代码:
```rust=
fn main() {
    let x: &str = "hello, world";
    let y = x;
    println!("{},{}",x,y);
}
```
這段代碼，大家覺得會否報錯？如果參考之前的 String 所有權轉移的例子，那這段代碼也應該報錯才是，但是實際上呢？

這段代碼和之前的 String 有一個本質上的區別在 String 的例子中 s1 持有了通過String::from("hello") 創建的值的所有權，而這個例子中，x 只是引用了存儲在二進制中的字符串 "hello, world"，並沒有持有所有權。

因此 let y = x 中，僅僅是對該引用進行了拷貝，此時 y 和 x 都引用了同一個字符串。如果還不理解也沒關係，當學習了下一章節 "引用與借用" 後，大家自然而言就會理解。
#### deep copy
rust 任何時候都不會自動 deep copy 除非調用 
方法。
```rust=
#![allow(unused)]
fn main() {
let s1 = String::from("hello");
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
}
```
#### shallow copy
shallow copy 只發生在 stack 上，因此性能很高，在日常編程中無處不在。

```rust=
#![allow(unused)]
fn main() {
let x = 5;
let y = x;
println!("x = {}, y = {}", x, y);
}
```
但這段代碼似乎與我們剛剛學到的內容相矛盾：沒有調用 clone，不過依然實現了類似 deep copy 的效果 —— 沒有報所有權的錯誤。
[std::marker::Copy](https://doc.rust-lang.org/std/marker/trait.Copy.html)
#### 格式化字串
Rust 提供數個格式化字串的巨集，包括：

- format!：將格式化字串轉為 String 物件
- println!：將格式化字串印到終端機，附帶換行字元
- print!：將格式化字串印到終端機，但不附加換行字元
- writeln!：將格式化字串寫入特定的 buffer 物件，附帶換行字元
- write!：將格式化字串寫入特定的 buffer 物件，但不附帶換行字元
```rust=
fn main() {
    // Hexadeicmal number
    assert_eq!(format!("0x{:X}", 255), "0xFF");
 
    // Octal number
    assert_eq!(format!("0o{:o}", 127), "0o177");
 
    // Decimal with specific precision
    assert_eq!(format!("{:.2}", 3.14159), "3.14");
 
    // Formatted string for debug purpose
    assert_eq!(format!("{:?}", vec![1, 2, 3]), "[1, 2, 3]");
 
    // Formatted string with specific position
    assert_eq!(format!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob"),
               "Alice, this is Bob. Bob, this is Alice");
 
    // Formatted string with specific name
    assert_eq!(format!("{subject} {verb} {object}", object = "the lazy dog",
                       verb = "jumps over", subject = "The swift fox"),
               "The swift fox jumps over the lazy dog");
}
```
[以下引用(Rust Course)](https://course.rs/basic/compound-type/string-slice.html?highlight=string#%E6%93%8D%E4%BD%9C%E5%AD%97%E7%AC%A6%E4%B8%B2)
在字符串尾部可以使用 push() 方法追加字符 char，也可以使用 push_str() 方法追加字符串字面量。這兩個方法都是在原有的字符串上追加，並不會返回新的字符串。由於字符串追加操作要修改原來的字符串，字符串變量必須由 mut 關鍵字修飾。
```rust=
fn main() {
    let mut s = String::from("Hello ");
    s.push('r');
    println!("追加字符 push() -> {}", s);

    s.push_str("ust!");
    println!("追加字符串 push_str() -> {}", s);
}
```
另外建議參考官方的標準庫的文檔[std::string::String](https://doc.rust-lang.org/std/string/struct.String.html)
有像下面的例子：

pub fn as_mut_str(&mut self) -> &mut str
Converts a String into a mutable string slice.

Basic usage:
```rust=
let mut s = String::from("foobar");
let s_mut_str = s.as_mut_str();

s_mut_str.make_ascii_uppercase();

assert_eq!("FOOBAR", s_mut_str);
```
增加：push, insert, 
刪除：pop, [remove](https://doc.rust-lang.org/std/string/struct.String.html#method.remove), remove_matches, [truncate](https://doc.rust-lang.org/std/string/struct.String.html#method.truncate)
查找：[find](https://doc.rust-lang.org/std/string/struct.String.html#method.find), rfind
修改：[replace](https://doc.rust-lang.org/std/string/struct.String.html#method.replace), replacen, replace_range
### Tuples
元組是由多種類型組合到一起形成的，因此它是複合類型，元組的長度是固定的，元組中元素的順序也是固定的。
聲明一個tuple:
```rust=
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```
Tuples 要使用常量 tup.4 或 模式匹配, 不能用 tup.i 來取第 i 個元素。
Rust 常用 Tuples 來返回多個值， ex: split_at 將字符串切兩半， 並回傳兩個字符串
```rust=
fn split_at(&self, mid: usize) -> (&str, &str);
```
返回兩個字符串切片的Tuple
```rust=
let text = "I see the eigenvalue in thine eye";
let (head, tail) = text.split_at(21);
assert_eq!(head, "I see the eigenvalue ");
assert_eq!(tail, "in thine eye");
```
This is more legible than the equivalent:
```rust=
let text = "I see the eigenvalue in thine eye";
let temp = text.split_at(21);
let head = temp.0;
let tail = temp.1;
```
# Arrays, Vectors, and Slices
Rust has three types for representing a sequence of values in memory:
Rust 有 3 種在內存中表示一系列值的類型:
### array
```rust=
let lazy_caterer: [u32; 6] = [1, 2, 4, 7, 11, 16];
let taxonomy = ["Animalia", "Arthropoda", "Insecta"];
```
我們看到的用於數組的方法，比如迭代元素、搜索、排序、填充、篩選等，都是切片的方法，並非數組的方法。不過，Rust 會在查找方法時***隱式地把對數組的引用轉換為對切片的引用***，因此可以直接在數組上
調用切片的任何方法：
```rust=
let mut chaos = [3, 5, 4, 1, 2];
chaos.sort();
assert_eq!(chaos, [1, 2, 3, 4, 5]);
```
這裡的 sort 方法實際上是在切片上定義的，但因為 sort 是通過***引用***取得其操作數的，所以可以直接在chaos 上使用它。這個調用會***隱式地產生一個將整個數組作為切片的引用 &mut*** [i32]。事實上， len 方法也是一個切片方法。
### vector
[](https://course.rs/basic/collections/vector.html?highlight=vector#%E5%8A%A8%E6%80%81%E6%95%B0%E7%BB%84-vector)
創建向量的方式有好幾種。最簡單的方式是使用 vec! 這macro，這種語法看起來非常像創建數組字面量：
```rust=
let mut v = vec![2, 3, 5, 7];
assert_eq!(v.iter().fold(1, |a, b| a * b), 210);
v.push(11);
v.push(13);
assert_eq!(v.iter().fold(1, |a, b| a * b), 30030);
```
調用 vec! 宏等價於調用 Vec::new 創建一個新的空向量，然後再向其中推入元素，這也是一種寫法：
```rust=
let mut v = Vec::new();
v.push("step");
v.push("on");
\\ 在 index 3 插入 35
v.insert(3, 35);
\\ 刪除 index 3
v.remove(3);
assert_eq!(v, vec!["step", "on", "no", "pets"]);
```
與數組類似，對向量也可以使用切片方法：
```rust=
let mut v = vec!["a man", "a plan", "a canal", "panama"];
v.reverse();
assert_eq!(v, vec!["panama", "a canal", "a plan", "a man"]);
```
這裡的 reverse 方法實際是在切片上定義的，只不過這個調用隱式地從向量借用了 &mut[&str]，並在這個切片引用上調用了 reverse。
### slice  [切片](https://www.youtube.com/watch?v=ZCfgA8t9IeM)
切片（slice），寫作不指定長度的 [T]，表示數組或向量的一個範圍。由於切片可以是任意長度，因此不能直接保存到變量中，也不能作為函數參數傳遞。***切片永遠只能按引用傳遞***。

切片的引用是一個胖指針（fat pointer），即一個雙字寬的值，保存著指向切片第一個元素的指針和切
片中元素的個數。
假設運行如下代碼：
```rust=
let v: Vec<f64> = vec![0.0, 0.707, 1.0, 0.707];
let a: [f64; 4] = [0.0, -0.707, -1.0, -0.707];
let sv: &[f64] = &v;
let sa: &[f64] = &a;
```
運行最後兩行時，Rust 會自動把引用 &Vec<f64> 和 &[f64; 4] 轉換為直接指向數據的切片引用。
    ![](https://i.imgur.com/kKv8wdw.png)
在索引中使用範圍可以獲取對數組或向量切片的引用，或者對現有切片的切片的引用：
```rust=
print(&v[0..2]); // 打印v的前兩個元素
print(&a[2..]); // 從a[2]開始打印a的元素
print(&sv[1..3]); // 打印v[1]和v[2]
```
我們經常把 &[T] 或 &str 這樣的引用類型稱為切片，其實是有點偷懶了。實際上，應該稱它們為對切片的引用。不過因為提到切片基本上都是指對它的引用，所以也就用“切片”來指代“切片引用”了。

# [Ownership](https://hackmd.io/pwwhZA8tQZWpU55o7GuTQg) <- click here
  - [Copy Types](https://hackmd.io/pwwhZA8tQZWpU55o7GuTQg?view#Copy-Types-The-Exception-to-Moves)

# [Reference](https://hackmd.io/fvRHsaQHRD6JTqSR93YSxQ) <- click here
  - [＆ vs ref vs ＊operator](https://hackmd.io/fvRHsaQHRD6JTqSR93YSxQ?view#%EF%BC%86-vs-ref-vs-%EF%BC%8Aoperator%EF%BC%9A) 解答 ref 跟 ＆ 的差別！

# [Expressions](https://hackmd.io/fAnfB65TSA6M8To1Yq3amA) <- click here
    
# [Error Handling](https://hackmd.io/ql1gBH4XS7GHz0Jq7Y2uFQ) <- click here
  - [Ok / Ok / Some](https://hackmd.io/ql1gBH4XS7GHz0Jq7Y2uFQ#Ok--Ok--Some) 解釋 Option<T> Result<T, E> 的用途！
# [Structs](https://hackmd.io/copSHH3FTAuwdKiIZAPmcQ) <- click here
  - [細說 Self self 差別](https://hackmd.io/copSHH3FTAuwdKiIZAPmcQ#%E7%B4%B0%E8%AA%AA-Self-self-%E5%B7%AE%E5%88%A5%EF%BC%9A) 解答各種 self 的疑惑？
# [Enums and Patterns](https://hackmd.io/5sq3sQj5R_OYTMqlUvdxeg) <- click here
