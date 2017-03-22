# 構造体
`struct`はより複雑なデータ型を作る方法の1つです。
```Rust
let origin_x = 0;
let origin_y = 0;
```
struct でこれら2つを1つのデータ型にまとめることができます。
```Rust
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let origin = Point { x: 0, y: 0 }; // origin: Point

    println!("({}, {})", origin.x, origin.y);
}
```
`struct`キーワードを使って構造体とその名前を宣言します。構造体は初めが大文字のキャメルケースで記述します。
`let`で struct のインスタンスを作ることができます。`key: value`スタイルの構文でそれぞれのフィールドに値をセットできます。順序は元の宣言と同じである必要はありません。

`struct`が持つ値はイミュータブルがデフォルトです。`mut`を使うと値をミュータブルにできます。  
Rustは言語レベルでフィールドのミュータビリティに対応していないため、以下の様に書くことはできません。
```Rust
struct Point {
    mut x: i32,
    y: i32,
}
```
フィールドレベルのミュータビリティを使うのであれば、初めこそ奇妙に見えるものの、非常に簡単に実現できる方法があります。以下の方法で少しの間だけミュータブルな構造体を作ることができます。
```Rust
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let mut point = Point { x: 0, y: 0 };

    point.x = 5;

    let point = point; // この新しい束縛でここから変更できなくなります

    point.y = 6; // これはエラーになります
}
```
また`std::cell`を使うこともできます。
```Rust
use std::cell::Cell;

struct Point {
    x: i32,
    y: Cell<i32>,
}

fn main() {
    let point = Point { x: 5, y: Cell::new(6) };
    point.y.set(7);
    println!("y: {:?}", point.y.get());
}
```

## アップデート構文

struct の初期化時には、値の一部を他の構造体からコピーすることを示す .. を含めることができます。
```Rust
struct Point3d {
    x: i32,
    y: i32,
    z: i32,
}

let point1 = Point3d { x: 0, y: 0, z: 0 };
let point2 = Point3d { y: 1, .. point };
```
pointに新しいyを与え、xとzは古い値を維持します。どれかのstructと同じ値を作る他にも、この構文を新たな値の作成に使用でき、明示することなく値のコピーが行えます。

## タプル構造体

Rustには「タプル構造体」と呼ばれる、タプルと struct のハイブリットのようなデータ型があります。タプル構造体自体には名前がありますが、そのフィールドには名前がありません。

struct Color(i32, i32, i32);
struct Point(i32, i32, i32);
これら2つは同じ値を持っていたとしても等しくありません。
```Rust
let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
```
ほとんどの場合タプル構造体よりも struct を使ったほうが良いです。 Color や Point はこのようにも書けます。
```Rust
struct Color {
    red: i32,
    blue: i32,
    green: i32,
}

struct Point {
    x: i32,
    y: i32,
    z: i32,
}
```

タプルはフィールドの並びによって区別され、構造体はフィールドの名前によって区別されます。これはタプルと構造体の最たる違いであり、構造体を持つことは名前を付けられたデータの集まりを持つことに等しいため、構造体における名前付けは重要です。

タプル構造体が非常に便利な場合もありますが、1要素で使う場合だけです。タプル構造体の中に入っている値と、それ自体のセマンティックな表現を明確に区別できるような新しい型を作成できることから、「newtype」パターンと呼ばれます。

```Rust
struct Inches(i32);

let length = Inches(10);

let Inches(integer_length) = length;
println!("length is {} inches", integer_length);
```
`let`を使って分解することで、標準のタプルと同じように内部の整数型を取り出すことができます。

## Unit-like 構造体

全くメンバを持たない struct を定義できます。
```Rust
struct Electron;

let x = Electron;
```
空のタプルである`()`は`unit`と呼ばれ、それに似ていることからこのような構造体を`unit-like`と呼んでいます。タプル構造体のように、これは新しい型を定義します。

ライブラリはあなたにイベントを処理できる特定のトレイトが実装されたストラクチャの作成を要求するかもしれません。もしそのストラクチャの中に保存すべき値が何もなければ、unit-likeな struct を作るだけで良いのです。
