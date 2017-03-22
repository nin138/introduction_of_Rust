# match

```Rust
let x = 5;

match x {
    1 => println!("one"),
    2 => println!("two"),
    3 => println!("three"),
    4 => println!("four"),
    5 => println!("five"),
    _ => println!("something else"),
}
```
match は一つの式とその式の値に基づく複数のブランチを引数に取ります。 一つ一つの「腕」は`val => expression` という形式を取ります。 値がマッチした時に、対応する腕の式が評価されます。

match をつかうと、「網羅性検査」が実施されます。 上のコードで、最後に`_`を用いている腕があります。その腕を削除した場合、コンパイルエラーが起こります。`_`は「全てキャッチする腕」です。 もし他の腕がどれもマッチしなかった場合、 _ の腕が実行されることになります。  
また`match`は式です。
```Rust
let x = 5;

let number = match x {
    1 => "one",
    2 => "two",
    3 => "three",
    4 => "four",
    5 => "five",
    _ => "something else",
};
```
