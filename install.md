# インストール
## LinuxまたはMacでのインストール
RustコンパイラとそのパッケージマネージャCargoをダウンロードし、`/usr/local`
へとインストールする。
```
$ curl https://sh.rustup.rs -sSf | sh
```
`--prefix=<path>` オプションを使うことで他の場所へインストール出来る。  
インストーラは「sudo」下で走る。もし'sudo'を使ってほしくないなら`--disable-sudo` を使う。

## Windowsでのインストール
インストーラをダウンロードする。  
[https://www.rust-lang.org/en-US/]  

またlink.exeが見つからないとか言われた場合は  
[https://www.visualstudio.com/ja/downloads/?rr=https%3A%2F%2Fwww.microsoft.com%2Fja-jp%2Fdev%2Fproducts%2Fcommunity.aspx]  
からMicrosoft Visual Studio CommunityをC++の項目にチェックをつけてインストールする。
