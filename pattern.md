# パターン
```Rust
let x = 1;

match x {
    1 => println!("one"),
    2 => println!("two"),
    3 => println!("three"),
    _ => println!("anything"),
}
```
これは one を表示します。

パターンには一つ落とし穴があります。新しい束縛を導入すると、他の束縛を導入するものと同じように、シャドーイングします。例えば：
```Rust
let x = 'x';
let c = 'c';

match c {
    x => println!("x: {} c: {}", x, c),
}

println!("x: {}", x);
```
これの結果は以下のようになります
```
x: c c: c
x: x
```
別の言い方をすると、 x => はパターンへのマッチだけでなく、マッチの腕内で有効な x という名前の束縛を導入します。既に x は束縛されていたので、この新しい x はそれを覆い隠します。

## 複式パターン

`|`を使うと、複式パターンが導入できます：
```Rust
let x = 1;

match x {
    1 | 2 => println!("one or two"),
    3 => println!("three"),
    _ => println!("anything"),
}
```
これは、 one or two を出力します。

## デストラクチャリング

例えば struct のような複合データ型を作成したいとき、パターン内でデータを分解することができます。
```Rust
struct Point {
    x: i32,
    y: i32,
}

let origin = Point { x: 0, y: 0 };

match origin {
    Point { x, y } => println!("({},{})", x, y),
}
```
値に別の名前を付けたいときは、 : を使うことができます。
```Rust
struct Point {
    x: i32,
    y: i32,
}

let origin = Point { x: 0, y: 0 };

match origin {
    Point { x: x1, y: y1 } => println!("({},{})", x1, y1),
}
```
値の一部だけを扱いたい場合は、値の全てに名前を付ける必要はありません。
```Rust
struct Point {
    x: i32,
    y: i32,
}

let origin = Point { x: 0, y: 0 };

match origin {
    Point { y, .. } => println!("y is {}", y),
}
```
これは y is 0 を出力します。

この「デストラクチャリング(destructuring)」と呼ばれる振る舞いは、 タプル や 列挙型 のような、複合データ型で使用できます。

## 束縛の無視

パターン内の型や値を無視するために`_`を使うことができます。

例として、 Result<T, E> に対して match を適用してみましょう
```Rust
match some_value {
    Ok(value) => println!("got a value: {}", value),
    Err(_) => println!("an error occurred"),
}
```
最初の部分では Ok ヴァリアント内の値を`value`に結びつけています。しかし Err 部分では、特定のエラーを避けて、標準的なエラーメッセージを表示するために`_`を使っています。

`_`は束縛を伴うどんなパターンにおいても有効です。これは大きな構造の一部分を無視する際に有用です。
```Rust
fn coordinate() -> (i32, i32, i32) {
    // 3要素のタプルを生成して返す
}

let (x, _, z) = coordinate();
```
ここでは、タプルの最初と最後の要素を x と z に結びつけています。

同様に`..`でパターン内の複数の値を無視することができます。
```Rust
enum OptionalTuple {
    Value(i32, i32, i32),
    Missing,
}

let x = OptionalTuple::Value(5, -2, 3);

match x {
    OptionalTuple::Value(..) => println!("Got a tuple!"),
    OptionalTuple::Missing => println!("No such luck."),
}
```
これは Got a tuple! を出力します。

##  ref と ref mut

もし リファレンス を取得したいときは ref キーワードを使いましょう。
```Rust
let x = 5;

match x {
    ref r => println!("Got a reference to {}", r),
}
```
これは Got a reference to 5 を出力します。

ここで match 内の`r`は`&i32 型`を持っています。
```Rust
let mut x = 5;

match x {
    ref mut mr => println!("Got a mutable reference to {}", mr),
}
```
## レンジ

`...`で値のレンジのマッチを行うことができます：
```Rust
let x = 1;

match x {
    1 ... 5 => println!("one through five"),
    _ => println!("anything"),
}
```
これは one through five を出力します。

レンジは大体、整数か char 型で使われます：
```Rust
let x = 'x';

match x {
    'a' ... 'j' => println!("early letter"),
    'k' ... 'z' => println!("late letter"),
    _ => println!("something else"),
}
```
これは late letter を出力します。

## 束縛

`@` で値を名前と結びつけることができます。
```Rust
let x = 1;

match x {
    e @ 1 ... 5 => println!("got a range element {}", e),
    _ => println!("anything"),
}
```
これは got a range element 1 を出力します。 データ構造の一部に対する複雑なマッチが欲しいときに有用です：
```Rust
struct Person {
    name: Option<String>,
}

let name = "Steve".to_string();
let x: Option<Person> = Some(Person { name: Some(name) });
match x {
    Some(Person { name: ref a @ Some(_), .. }) => println!("{:?}", a),
    _ => {}
}
```
これは Some("Steve") を出力します。内側の name を a に結びつけます。

もし | で @ を使うときは、パターンのそれぞれの部分が名前と結びついているか確認する必要があります：
```Rust
let x = 5;

match x {
    e @ 1 ... 5 | e @ 8 ... 10 => println!("got a range element {}", e),
    _ => println!("anything"),
}
```

## ガード

if を使うことでマッチガードを導入することができます：
```Rust
enum OptionalInt {
    Value(i32),
    Missing,
}

let x = OptionalInt::Value(5);

match x {
    OptionalInt::Value(i) if i > 5 => println!("Got an int bigger than five!"),
    OptionalInt::Value(..) => println!("Got an int!"),
    OptionalInt::Missing => println!("No such luck."),
}
```
これは Got an int! を出力します。

複式パターンで if を使うと、 if は両方に適用されます：
```Rust
let x = 4;
let y = false;

match x {
    4 | 5 if y => println!("yes"),
    _ => println!("no"),
}
```
これは no を出力します。なぜなら if は 4 | 5 全体に適用されるのであって、 5 単独に対してではないからです。つまり if 節は以下のように振舞います：
```
(4 | 5) if y => ...
```

## 混ぜてマッチ

マッチの様々な方法を混ぜてマッチさせることもできます
```Rust
match x {
    Foo { x: Some(ref name), y: None } => ...
}
```
パターンはとても強力です。上手に使いましょう。
