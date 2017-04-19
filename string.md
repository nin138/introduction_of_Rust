# 文字列
 「文字列」は、UTF-8のバイトストリームとしてエンコードされたユニコードのスカラー値のシーケンスです。 すべての文字列は、正しくエンコードされたUTF-8のシーケンスであることが保証されています。文字列はnull終端でなく、nullバイトを含むことが可能です。

Rustは主要な文字列型を二種類持っています`&str` と `String`です。
`String`と`&str`は`as_str()`と`to_string()`で交互に変換できる。  
String を &str として見るコストは低いが、&str を String に変換するのはメモリのアロケーションを発生させる。

## &str
`&str`は「文字列のスライス」と呼ばれ、固定のサイズを持ち、変更不可能で、UTF-8のバイトシーケンスへの参照です。
```Rust
let greeting = "Hello there."; // greeting: &'static str
```
`Hello there.` は文字列リテラルであり、 `&'static str 型`を持ちます。 文字列リテラルは、静的にアロケートされた文字列のスライスです。つまりコンパイルされたプログラム中に保存されており、 プログラムの実行中全てにわたって存在していることを意味しています。

文字列リテラルは複数行に渡ることができます。　　
複数行の文字列リテラルを記述する方法は、2つの形式があります。
一つ目の形式は、改行と行頭の空白を文字列に含む形式です:
```Rust,
let s = "foo
    bar";

assert_eq!("foo\n        bar", s);
```
もう一つは`\`を用いて、空白と改行を削る形式です:
```Rust
let s = "foo\
    bar";

assert_eq!("foobar", s);
```

## String
Stringはヒープにアロケートされる文字列の形式です。 この文字列は伸張可能であり、またUTF-8であることが保証されています。 String は一般的に`&str`を`to_string()` を用いて変換することで生成されます。
```Rust
let mut s = "Hello".to_string(); // mut s: String
println!("{}", s);


```
String は & によって &str に型強制されます。
```Rust
let mut x = "abc".to_string();
let y = "def".to_string();

x.push_str(&y);
```
この変換は &str ではなく、 &str のトレイトを引数として期待している関数では自動的には行われません。 たとえば、 TcpStream::connect は引数として型 ToSocketAddrs を要求しています。 このような関数には &str は渡せますが、 String は`&* `を用いて明示的に変換する必要があります。
```Rust
use std::net::TcpStream;

TcpStream::connect("192.168.0.1:3000"); // 引数として &str を渡す

let addr_string = "192.168.0.1:3000".to_string();
TcpStream::connect(&*addr_string); // &str に変換して渡す
```


## インデクシング
文字列が正しいUTF-8であるため、文字列はインデクシングをサポートしていません:
```Rust
let s = "hello";

println!("The first letter of s is {}", s[0]); // エラー
```
通常、ベクタへの `[]` を用いたアクセスはとても高速です。 しかし、UTF-8にエンコードされた文字列中の文字は複数のバイトである可能性があるため、 文字列のn番の文字を探すためには文字列上を移動していく必要があり、高コストな演算です。
```Rust
let hachiko = "忠犬ハチ公";

for b in hachiko.as_bytes() {
    print!("{}, ", b);
}

println!("");

for c in hachiko.chars() {
    print!("{}, ", c);
}
```
これは、以下の様な出力をします:
```
229, 191, 160, 231, 138, 172, 227, 131, 143, 227, 131, 129, 229, 133, 172,
忠, 犬, ハ, チ, 公,
```
ご覧のように、 char の数よりも多くのバイトが含まれています。

インデクシングするのと近い結果を以下の様にして得ることができます:
```Rust
let dog = hachiko.chars().nth(1).unwrap();
println!("{}", dog);  // 犬

頻繁にインデックスアクセスを行いたい場合
let x = "あいう".chars().collect::<Vec<char>>();
assert_eq!('い', x[1])
```

## スライシング
文字列のスライスは以下のようにスライス構文を用いて取得することができます:
```Rust
let dog = "hachiko";
let hachi = &dog[0..5];
```
しかし、注意しなくてはならない点はこれらのオフセットはバイトであって **文字 のオフセットではない** という点です。
```Rust
let dog = "忠犬ハチ公";
let x = &dog[0..2]; // エラー
let y = &dog[0..3]; // 忠
```

## 結合
リテラルなら`concat!`を使うとコンパイル時にまとめてくれる
```Rust
println!("{}", concat!("abc", "def", 0, 1.2));
```
リテラル以外の場合は`format!()`が簡単
```Rust
println!("{}", format!("{}{}", "abc", "def"));
```
Stringは &str を末尾に結合することができます:
```Rust
let x = "abc".to_string();
let y = "def";
let xy = x + y;
```
または
```Rust
let mut x = "abc".to_string();
let y = "def";
x.push_str(y);
```
２つの String を結合するには、 & が必要になります:
```Rust
let hello = "Hello ".to_string();
let world = "world!".to_string();

let hello_world = hello + &world;
```
### 正規表現
stdには含まれないので、ライブラリを使う
https://github.com/rust-lang/regex
### 文字数
```Rust
assert_eq!(3, "abc".len());
```
### 分割
```Rust
let v: Vec<&str> = "Mary had a little lamb".split(' ').collect();
assert_eq!(v, ["Mary", "had", "a", "little", "lamb"]);

let v: Vec<&str> = "".split('X').collect();
assert_eq!(v, [""]);

let v: Vec<&str> = "lionXXtigerXleopard".split('X').collect();
assert_eq!(v, ["lion", "", "tiger", "leopard"]);

let v: Vec<&str> = "lion::tiger::leopard".split("::").collect();
assert_eq!(v, ["lion", "tiger", "leopard"]);

let v: Vec<&str> = "abc1def2ghi".split(char::is_numeric).collect();
assert_eq!(v, ["abc", "def", "ghi"]);

let v: Vec<&str> = "lionXtigerXleopard".split(char::is_uppercase).collect();
assert_eq!(v, ["lion", "tiger", "leopard"]);
```

### 置換
```Rust
assert_eq!("abcd", "abdc".replace("dc", "cd"));
```

### 文字列が含まれているか
```Rust
assert!("abc".starts_with("ab"));
assert!(!"abc".starts_with("cb"));
assert!("abcd".contains("bc"));
```
### trim
```Rust
assert_eq!("abc", " abc ".trim());
assert_eq!("abc", " abc".trim_left());
assert_eq!("abc", "abc ".trim_right());
```
