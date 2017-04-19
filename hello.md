# Hello, world!

`main.rs`というファイルを作る。  
main.rs を開いて以下のコードを打ちましょう。
```Rust
fn main() {
    println!("Hello, world!");
}
```
保存したらコンソールから
```
$ rustc main.rs
$ ./main
```
と入力してください。
Hello, world!と表示されるはずです

## Rustプログラムの解剖
```Rust
fn main() {
}
```
これはRustの 関数 を定義です。`main関数`はエントリポイントです。

Rustでは全ての関数に`{}`が必要です。関数宣言と同じ行に1スペース空けて開き波括弧を置くのが良いスタイルとされます。

```Rust
println!("Hello, world!");
```
これはテキストを出力するRustのメタプログラミング機構`マクロ`の呼び出しです。
もし関数を呼び出しているのなら、`println()`のようになります(! がありません)。  
Rustは 式指向言語 で、ほとんどのものは文ではなく式です。  
`;`は式が終わり、次の式が始まることを示します。Rustのコードのほとんどの行は ; で終わります。


## Cargo
CargoはRustのビルドシステム兼パッケージマネージャです。  
Cargoはコードのビルド、コードが依存するライブラリのダウンロード、ライブラリのビルドを管理します。  
公式のインストーラを使ったならCargoはRustに同梱されています。  
以下のコマンドでCargoがインストールされているか確認出来ます。
```
$ cargo --version
```
## Cargoへ変換する
Hello WorldプログラムをCargoに変換しましょう。  
main.rs を src ディレクトリにコピーして、 rustc でコンパイルして作ったファイルを削除します。
```
$ mkdir src
$ mv main.rs src/main.rs
$ rm main
```
以下のような構造にします
```
プロジェクトフォルダ
|
+- src--main.rs
|
+- Cargo.toml
```

### 設定ファイルを作る

プロジェクト直下にCargo.tomlというファイルを作ります。（Cは大文字です）
このファイルは `TOML (Tom's Obvious, Minimal Language)`　フォーマットで書かれます。

ファイル内に以下の情報を打ち込みます。  
```TOML
[package]

name = "hello_world"
version = "0.0.1"
authors = [ "あなたの名前 <you@example.com>" ]
```

### Cargoプロジェクトのビルドと実行
以下のコマンドを打つだけです。
```
$ cargo run
```

### リリースビルド
最終的にプロジェクトのリリース準備が整ったら以下のコマンドを実行します
```
 cargo build --release
```
最適化を掛けてプロジェクトをコンパイル出来ます。 Rustのコードは速くなりますが、コンパイル時間は長くなります。

### 新規Cargoプロジェクトを作るコマンド
以下のコマンドを打つだけです。
```
cargo new プロジェクト名 --bin
```
ただしライブラリを作る際は`--bin`を消しましょう。  
このコマンドによりCargo.toml と main.rs の入ったsrcディレクトリを生成します。  
Cargoは引数とgitの設定を基にCargo.tomlに適当な値を埋めます。  
このままcargo run をすると`"Hello, world!"`が実行されるはずです。
