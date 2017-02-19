# インストール
## LinuxまたはMacでのインストール
```
$ curl -sSf https://static.rust-lang.org/rustup.sh | sh
```
このスクリプトはRustコンパイラとそのパッケージマネージャCargoをダウンロードし、`/usr/local`
へとインストールします。  
`--prefix=<path>` オプションを使うことで他の場所へインストール
出来ます。  

インストーラは「sudo」下で走るのでパスワードを尋きます。もし'sudo'を使ってほしくないなら
`--disable-sudo` フラグを渡します。

## Windowsでのインストール
Windowsを使っているなら適切なインストーラをダウンロードして下さい。  
`https://www.rust-lang.org/en-US/`  

またlink.exeが見つからないとか言われた場合は  
`https://www.microsoft.com/ja-jp/download/details.aspx?id=48146`  
からMicrosoft Visual Studio Community 2015をC++の項目にチェックをつけてインストールしまししょう。
