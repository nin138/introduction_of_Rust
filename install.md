# インストール
## LinuxまたはMacでのインストール
```
$ curl https://sh.rustup.rs -sSf | sh
```
このスクリプトはRustコンパイラとそのパッケージマネージャCargoをダウンロードし、`/usr/local`
へとインストールします。  
`--prefix=<path>` オプションを使うことで他の場所へインストール出来ます。  
インストーラは「sudo」下で走る。もし'sudo'を使ってほしくないなら`--disable-sudo` を使う。

## Windowsでのインストール
Windowsを使っているなら適切なインストーラをダウンロードして下さい。  
`https://www.rust-lang.org/en-US/`  

またlink.exeが見つからないとか言われた場合は  
`https://www.microsoft.com/ja-jp/download/details.aspx?id=48146`  
からMicrosoft Visual Studio Community 2015をC++の項目にチェックをつけてインストールしまししょう。
