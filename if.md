<link rel="stylesheet" href="./css/reset.css">
<a class="forkme" href="https://yanokunpei.github.io/introduction_of_Rust">Fork me on GitHub</a>
# if
Rustのif式は以下のように書きます
```Rust
let x = 5;
if x == 5 {
    println!("x は 5 です!");
} else if x == 6 {
    println!("x は 6 です!");
} else {
    println!("x は 5 でも 6 でもありません");
}
```

Rustのifは文ではなく式なので以下のようなこともできます。
```Rust
let x = 5;
let y = if x == 5 {
    10
} else {
    15
};
```
次のように書くこともできます（そして、大抵はこう書くべきです）:
```Rust
let x = 5;
let y = if x == 5 { 10 } else { 15 }; // y: i32
```
if式の値は、選択された分岐中の最後の式の値となります。 else のない if では、その値は常に () が期待されます。
```Rust
let x = 1;
let y = if x == 1 { 2 };　// コンパイルエラー
println!("{}",y);
```
