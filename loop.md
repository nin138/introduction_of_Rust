<link rel="stylesheet" href="./css/reset.css">
<a class="forkme" href="https://yanokunpei.github.io/introduction_of_Rust">Fork me on GitHub</a>
# ループ

Rustには３つのループ機構があります。loop, while, forです。
## loop
無限 loop です。`break`などをするまでずっとループし続けます。
```Rust
loop {
    println!("Loop forever!");
}
```
## while
Rustには while ループもあります。
```Rust
let mut x = 0;
let mut done = false;

while !done {
    x += 1; // x++　としたいところですがRustにはインクリメント演算子とデクリメント演算子はありません！
    println!("{}", x);
    if x % 5 == 0 {
        done = true;
    }
}
```

## for
Rustの for には下記のような構文はありません
```c
for (i = 0; i < 10; i++) {
    printf( "%d\n", i );
}
```
代わりに、このように書きます
```Rust
for i in 0..10 {
    println!("{}", i);
}
```
抽象的な用語を使うと
```Rust
for 変数 in 式 {
    処理
}
```
式は`IntoIterator`を用いてイテレータへと変換可能なアイテムです。  
例では、`0..10` が開始位置と終了位置をとる式であり、終了位置はその値自身を含まないため、このループは`0`から`9`までを表示します。

### 列挙
ループ中で何回目の繰り返しかを知る必要があるなら、 `.enumerate()` 関数が使えます。
#### レンジを対象に:
```Rust
for (i,j) in (5..10).enumerate() {
    println!("i = {}, j = {}", i, j);
}
```
`i`にループ回数`j`にレンジの値が入ります。
```
出力:
i = 0 , j = 5
i = 1 , j = 6
i = 2 , j = 7
i = 3 , j = 8
i = 4 , j = 9
```
#### イテレータを対象に:
```Rust
let lines = "hello\nworld".lines();
for (linenumber, line) in lines.enumerate() {
  println!("{}: {}", linenumber, line);
}
```
```
出力:
0: hello
1: world
```
## ループラベル
入れ子のループの時`break`や`continue`は最も内側のループに適用されるのがデフォルトですが、外側のループに`break`や`continue`を使いたい場合はラベルを使います。  
この例は x と y 両方が奇数のときだけ表示します:
```Rust
'outer: for x in 0..10 {
    'inner: for y in 0..10 {
        if x % 2 == 0 { continue 'outer; } // x のループを継続
        if y % 2 == 0 { continue 'inner; } // y のループを継続
        println!("x: {}, y: {}", x, y);
    }
}
```
