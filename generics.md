# ジェネリクス
　　
stdに、Option <T>型があります
```Rust
enum Option<T> {
    Some(T),
    None,
}
```
`<T>`は、これがジェネリックなデータ型であることを示しています。  
enum の宣言内であれば、どこでも`T `を使うことができ、宣言内に登場する同じ型をジェネリクスで`T`型に置き換えています。
```Rust
let x: Option<i32> = Some(5);
```
ジェネリクスにおいてジェネリックな型は1つまで、といった制限はありません。  
stdに入っている類似の型 `Result<T, E>` について考えてみます。
```Rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```
この型では`T`と`E`の 2つ がジェネリックです。
`Result<T, E>`型は計算の結果を返すために使われることが想定されており、正常に動作しなかった場合にエラーの値を返す機能を持っています。

## ジェネリック関数
似た構文でジェネリックな型を取る関数を記述できます。
```Rust
fn takes_anything<T>(x: T) {
}
```
複数の引数が同じジェネリックな型を持つこともできます。
```Rust
fn takes_two_of_the_same_things<T>(x: T, y: T) {
    // ...
}
```
複数の型を取るバージョンを記述することも可能です。
```Rust
fn takes_two_things<T, U>(x: T, y: U) {
    // ...
}
```
## ジェネリック構造体
また、 struct 内にジェネリックな型の値を保存することもできます。
```Rust
struct Point<T> {
    x: T,
    y: T,
}
let int_origin = Point { x: 0, y: 0 };
let float_origin = Point { x: 0.0, y: 0.0 };
```
関数と同様に、`<T>` がジェネリックパラメータを宣言する場所であり、型宣言において`x: T`を使うのも同じです。

ジェネリックな struct に実装を追加したい場合、 impl の後に型パラメータを宣言するだけです。
```Rust
impl<T> Point<T> {
    fn swap(&mut self) {
        std::mem::swap(&mut self.x, &mut self.y);
    }
}
```
