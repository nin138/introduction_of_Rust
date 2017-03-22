# 列挙型
Rustの enum は、いくつかのヴァリアントのうちからどれか一つをとるデータを表す型です。
```Rust
enum Message {
    Quit,
    ChangeColor(i32, i32, i32),
    Move { x: i32, y: i32 },
    Write(String),
}
```
各ヴァリアントは、自身に関連するデータを持つこともできます。 ヴァリアントの定義のための構文は、構造体を定義するのに使われる構文と似ており
+ unit-like構造体のようなデータを持たないヴァリアント
+ 構造体のような名前付きデータを持つヴァリアント
+ タプル構造体のような名前なしデータを持つヴァリアント  

がありえます。
しかし、別々に構造体を定義する場合とは異なり、 enum は一つの型です。 列挙型の値はどのヴァリアントにもマッチしうるのです。  列挙型としてありうる値の集合は、各ヴァリアントとしてありうる値の集合の和であるためです。
各ヴァリアントの名前を使うためには、`::` 構文を使います。
```Rust
enum Person {
    //unit-like
    Engineer,
    // タプル構造体
    Weight(i32),
    // 構造体
    Info { name: String, height: i32 }
}

fn inspect(p: Person) {
    //enumを使うときすべてのケースをカバーしなければならない
    match p {
        Person::Engineer  => println!("Is an engineer!"),
        // タプル構造体を`i`に分解する
        Person::Weight(i) => println!("Has a weight of {}.", i),
        // 構造体を`name` と `height`に分解する
        Person::Info { name, height } => {
            println!("{} is {} tall!", name, height);
        },
    }
}

fn main() {
    let person1 = Person::Weight(10);
    // `to_owned()`　->　&strから owned `String` を作る。
    let person2 = Person::Info { name: "Dave".to_owned(), height: 72 };
    let person3 = Person::Engineer;

    inspect(person1);
    inspect(person2);
    inspect(person3);
}
```

列挙型の値は、ヴァリアントに関連するデータに加え、その値自身がどのヴァリアントであるかという情報を持っています。 これを「タグ付き共用体」(tagged union) ということもあります。 データが、それ自身がどの型なのかを示す「タグ」をもっているためです。 コンパイラはこの情報を用いて、列挙型内のデータへ安全にアクセスすることを強制します。 例えば、値をどれか一つのヴァリアントであるかのようにみなして、その中身を取り出すということはできません。
```Rust
fn process_color_change(msg: Message) {
    let Message::ChangeColor(r, g, b) = msg; // コンパイルエラー
}
```
