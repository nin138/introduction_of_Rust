# メソッド構文
関数は素晴らしいのですが、幾つかのデータに対し複数の関数をまとめて呼び出したい時、困ったことになります。以下のコードについて考えてみます。
```Rust
baz(bar(foo));
```
私たちは普通を左から右へ、「baz bar foo」と読みますが、関数が呼び出される順番は異なり、内側から外へ「foo bar baz」となります。もし代わりにこうできたらいいとは思いませんか?
```Rust
foo.bar().baz();
```
Rustでは`impl`キーワードによってこの「メソッド呼び出し構文」の機能を提供しています。

# メソッド呼び出し

```Rust
struct Circle {
    x: f64,
    y: f64,
    radius: f64,
}

impl Circle {
    fn area(&self) -> f64 {
        std::f64::consts::PI * (self.radius * self.radius)
    }
}

fn main() {
    let c = Circle { x: 0.0, y: 0.0, radius: 2.0 };
    println!("{}", c.area());
}
```
これは 12.566371 と出力します。

私たちは円を表す struct を作りました。その際 impl ブロックを書き、その中に area というメソッドを定義しています。

メソッドに渡す特別な第1引数として、 self 、 &self 、 &mut self という3つの変形があります。第一引数は foo.bar() に於ける foo だと考えて下さい。3つの変形は foo が成り得る3種類の状態に対応しており、それぞれ self がスタック上の値である場合、 &self が参照である場合、 &mut self がミュータブルな参照である場合となっています。 area では &self を受け取っているため、他の引数と同じように扱えます。引数が Circle であるのは分かっていますから、他の struct でするように radius へアクセスできます。

所有権を渡すよりも借用を好んで使うべきなのは勿論のこと、ミュータブルな参照よりもイミュータブルな参照を渡すべきですから、 &self を常用すべきです。以下が3種類全ての例です。
```Rust
struct Circle {
    x: f64,
    y: f64,
    radius: f64,
}

impl Circle {
    fn reference(&self) {
       println!("taking self by reference!");
    }

    fn mutable_reference(&mut self) {
       println!("taking self by mutable reference!");
    }

    fn takes_ownership(self) {
       println!("taking ownership of self!");
    }
}
```
好きな数だけ impl ブロックを使用することができます。前述の例は以下のように書くこともできるでしょう。
```Rust
struct Circle {
    x: f64,
    y: f64,
    radius: f64,
}

impl Circle {
    fn reference(&self) {
       println!("taking self by reference!");
    }
}

impl Circle {
    fn mutable_reference(&mut self) {
       println!("taking self by mutable reference!");
    }
}

impl Circle {
    fn takes_ownership(self) {
       println!("taking ownership of self!");
    }
}
```
## メソッドチェーン

ここまでで、foo.bar() というようなメソッドの呼び出し方が分かりましたね。ですが元の例の foo.bar().baz() はどうなっているのでしょう?これは「メソッドチェーン」と呼ばれています。以下の例を見て下さい。
```Rust
struct Circle {
    x: f64,
    y: f64,
    radius: f64,
}

impl Circle {
    fn area(&self) -> f64 {
        std::f64::consts::PI * (self.radius * self.radius)
    }

    fn grow(&self, increment: f64) -> Circle {
        Circle { x: self.x, y: self.y, radius: self.radius + increment }
    }
}

fn main() {
    let c = Circle { x: 0.0, y: 0.0, radius: 2.0 };
    println!("{}", c.area());

    let d = c.grow(2.0).area();
    println!("{}", d);
}
```
以下の返す型を確認して下さい。

```Rust
fn grow(&self, increment: f64) -> Circle {
```
単に Circle を返しているだけです。このメソッドにより、私たちは新しい Circle を任意の大きさに拡大することができます。

## 関連関数

あなたは self を引数に取らない関連関数を定義することもできます。以下のパターンはRustのコードにおいて非常にありふれた物です。
```Rust
struct Circle {
    x: f64,
    y: f64,
    radius: f64,
}

impl Circle {
    fn new(x: f64, y: f64, radius: f64) -> Circle {
        Circle {
            x: x,
            y: y,
            radius: radius,
        }
    }
}

fn main() {
    let c = Circle::new(0.0, 0.0, 2.0);
}
```
この「関連関数」(associated function)は新たに Circle を構築します。この関数は ref.method() ではなく、 Struct::function() という構文で呼び出されることに注意して下さい。幾つかの言語では、関連関数を「静的メソッド」(static methods)と呼んでいます。

## Builderパターン

ユーザが Circle を作成できるようにしつつも、書き換えたいプロパティだけを設定すれば良いようにしたいとしましょう。もし指定が無ければ x と y が 0.0 、 radius が 1.0 であるものとします。Rustはメソッドのオーバーロードや名前付き引数、可変個引数といった機能がない代わりにBuilderパターンを採用しており、それは以下のようになります。
```Rust
struct Circle {
    x: f64,
    y: f64,
    radius: f64,
}

impl Circle {
    fn area(&self) -> f64 {
        std::f64::consts::PI * (self.radius * self.radius)
    }
}

struct CircleBuilder {
    x: f64,
    y: f64,
    radius: f64,
}

impl CircleBuilder {
    fn new() -> CircleBuilder {
        CircleBuilder { x: 0.0, y: 0.0, radius: 1.0, }
    }

    fn x(&mut self, coordinate: f64) -> &mut CircleBuilder {
        self.x = coordinate;
        self
    }

    fn y(&mut self, coordinate: f64) -> &mut CircleBuilder {
        self.y = coordinate;
        self
    }

    fn radius(&mut self, radius: f64) -> &mut CircleBuilder {
        self.radius = radius;
        self
    }

    fn finalize(&self) -> Circle {
        Circle { x: self.x, y: self.y, radius: self.radius }
    }
}

fn main() {
    let c = CircleBuilder::new()
                .x(1.0)
                .y(2.0)
                .radius(2.0)
                .finalize();

    println!("area: {}", c.area());
    println!("x: {}", c.x);
    println!("y: {}", c.y);
}
```
ここではもう1つの struct である CircleBuilder を作成しています。その中にBuilderメソッドを定義しました。また Circle に area() メソッドを定義しました。 そして CircleBuilder にもう1つ finalize() というメソッドを作りました。このメソッドはBuilderから最終的な Circle を作成します。さて、先程の要求を実施するために型システムを使いました。 CircleBuilder のメソッドを好きなように組み合わせ、作る Circle への制約を与えることができます。
