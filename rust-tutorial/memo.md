# Rust Tutorial メモ

チュートリアル：https://doc.rust-lang.org/book/index.html

## chapter1

- `!` をつけると関数呼び出しではなく、マクロの呼び出しになる
  - マクロと関数の違いについては 19 章で確認する
- Cargo : Rust’s build system and package manager
  - Cargo.toml に外部パッケージの情報を書くようにする
  - ソースコードは、`src`以下に置く
  - コマンドの使いかた
  - `cargo run` : build + binary run
  - `cargo check` ; build check
  - `cargo build --release` : release build (compile it with optimizations)

## chapter2

- `mut` : 変数が mutable であることを定義している
  - Rust では原則 immutable
- crate : https://crates.io/
  - rust のパッケージを探したい時はここで探す
- `&` で参照を明示しているらしい...
  - つける時とつけないときがあってよくわからん...
- `match` : switch 文的な立ち位置だと理解
  - 返り値が enum のとき、それぞれの値に対して処理を記述する

## chapter3

- シャドーイング
  - 同じ変数を再定義して、上書きすること
  - 変数は immutable だが、`let` で再定義は可能
  - `mut` との違い
    - 値の型を変えることが可能
- tuple について
  - 配列のとアクセスの仕方が違う
- 式と文の違い
  - 文：何らかの動作はするが値は返さないコード
  - 式：なにかに評価されるコード (=値を返す...?)
    - 式は文の一部になりえる
    - 式は終端にセミコロンを含まない
- 関数
  - 返り値は基本的に、関数内の最後の式となる
- if 式の使用
  - 条件文内で違う型の値を返そうとするとコンパイル時にエラーを出してくれる

## chapter4

- スタックとヒープ
  - https://doc.rust-jp.rs/the-rust-programming-language-ja/1.6/book/the-stack-and-the-heap.html
  - メモリの管理方法
  - スタックメモリ
    - こっちは、アルゴリと一緒
    - 呼ばれた変数からメモリを確保していき、呼ばれた順でメモリが開放される
    - メモリのアドレスの小さい方から積まれていくイメージ
    - Rust は、何も指定しなければすべてスタックでメモリを管理
  - ヒープメモリ (アルゴリと少し異なる)
    - ポインタに対応するメモリは、メモリの確保しやすい地点から動的に取得される
    - スタックにはポインタが積まれる、スタックが開放されてもヒープ上のメモリが開放されないこともある
    - サイズが可変のデータについて使われる
    - ポインタを追って目的のデータにアクセスするのでスタックよりは低速
- 所有権
  - ヒープのような複雑なメモリ管理を担う操作
  - 規則
    - Rust の各値は、所有者と呼ばれる変数と対応している
    - いかなる時も所有者は一つである
    - 所有者がスコープから外れたら、値は破棄される
- shallow copy / deep copy
  - shallow copy : ヒープ上のメモリまではコピーしない
    - **ヒープを使った操作は基本こっちなので、実態がどこにあるのか気をつける必要がある**
  - deep copy : ヒープ上のメモリまでコピーする
- ヒープ領域にメモリを持つ変数を扱う
  - 参照する際は、`&val` みたいに `&` をつけてアクセスする
    - つけないと実態がスコープ間で移動してしまう

ポインタとは？

> コンピュータというのはプログラム内の変数をメモリ上に確保して、その場所を特定するためのアドレスを記憶しています。プログラムでそのアドレスを取り扱いたい時に使用するのがポインタです。つまり、ポインタというのはアドレスを取り扱う変数のことを言います。

疑問点

- 借用規則がゆるくなっている？
  - > 何かへの不変な参照がある時、さらに可変な参照を得ることはできない
    - rustc (v1.43.0) だと問題ない

## chapter5

- 構造体
  - 型ありのオブジェクト（クラス）みたいなもの
  - フィールドには、普通は参照を持ってこれない
    - `&str` は `String` の参照なので Struct の型にはつかえない
  - `impl`で始めると、関数も定義できる
  - `&self` を引数にとるかとらないかで、クラスメソッドかインスタンスメソッドかの使い分けをしている
    - ほかの言語とも流石に似ていてわかりやすかった

## chapter6

- Enum
  - 取りうる値をすべて列挙でき、その列挙子のいずれか一つにしかなり得ないデータ
- Option<T>
  - Null の可能性のある値を取り扱うときに使用する
- match
  - 条件分岐、switch とほとんど同じ
  - 各ケースの処理も型で守られる
  - ケースが１つなら、`if let`でもかける

## chapter7

- モジュールファイルシステムの規則
  - サブモジュールがなければ、foo の定義は、 foo.rs というファイルに書く
  - サブモジュールがあったら、foo の定義は、 foo/mod.rs というファイルに書く
- `pub`で外部からのアクセスを制御する
  - デフォルトでは全てのメソッドがアクセスできないようになっている
- `::` でモジュールの各メソッドにアクセス
  - `use` で省略可能
  - 上の階層のモジュールを呼ぶには、`super`を使う (相対パスみたいな感じらしい)

## chapter8

- collections
  - データ構造周りの標準ライブラリ
  - https://doc.rust-lang.org/std/collections/index.html
- Vector
  - 可変長のリスト
  - 基本同じ型だが、enum を使うと中身の型も多様にできる
  - データはヒープ領域で管理
- String
  - 思ったより複雑らしい
  - 結合はとりあえず `format!` を使うのが混乱はなさそう
  - 添字アクセスは、言語によって 1 文字 1 byte でアクセスできないかも知れないからやめるべき
- Heap
  - データはヒープ領域で管理

## chapter9

- panic
  - 配列の添字が要素の長さより大きい時はパニックする
    - なるほど
    - > 配列の後に格納された読めるべきでないデータを読み出せるように添え字を操作できたら、 セキュリティ脆弱性につながる可能性があります
- panic か Result か?
  - panic
    - 悪い状態がときに起こるとは予想されないとき
    - プロトタイピングにはよい
  - Result
    - 任意の値を受け取れてしまうような状況
    - 基本的には、こっちを使うべき

## chapter10

- ジェネリクス
  - 書き方は、あまり TS と変わらない
  - 実行時コストはそこまでかからない
    - 実行時にはすべて明示的に型が与えられたインスタンスで置き換えられている
    - C++ と似ていて、Haskell とは異なるらしい
- トレイト
  - インターフェイスのようなもの
  - 構造体にメソッドの実装を強制したい時とかに使う
- ライフタイム
  - これが一番むずかしい
  - 参照を扱うメソッドは昔はすべて明示的にすべて書く必要があった
  - ただ、現在は冗長なこともあって、いくつかのルールに従って暗黙的にライフタイム処理されている

## chapter11

- テスト
  - デフォルトでかなりサポートされている印象
  - テストはもちろんビルドされない
- Rust で Bot 作るのよいかも...?

## chapter12

- clone で借用権の問題を解決することは、そこまで問題無い
  - コードが綺麗になる恩恵もあるから

## chapter13

- クロージャー
  - JS のケース
    - ローカル変数に対する限定的なアクセスを提供する手段
    - グローバル変数を隠蔽したい時につかうと便利かも
    - https://uhyohyo.net/javascript/9_5.html
    - https://artgear.hatenablog.com/entry/20120115/1326635158
  - 外側のスコープから値をキャプチャできる
    - 所有権を奪う、可変で借用する、不変で借用する
- イテレータ
  - ループより高速らしい
  - 基本、関数型チックなイテレータが好まれる

## chapter14

- ドキュメンテーションコメントが便利
- Rust はデフォルトで monorepo ができる

## chapter15

- Box
  - Box はヒープでメモリ管理するデータ型
  - 不変借用も可変借用もコンパイル時に精査できる
  - 再帰の型定義に使えるらしいが、いまいち理解できていない部分もある
- Deref
  - 参照外しを提供するトレイト
  - Box は Deref トレイトが実装されていて、参照のように扱うこともできる
  - 最後はよくわからなかった...
- Drop
  - 値がメモリから完全に削除されるときに呼ばれるメソッドを提供するトレイト
  - 明示的に開放はできなくて、明示的に開放するなら`std::mem::drop` を使う必要がある
- RC
  - 読み取り専用の参照を複数のデータで共有したい時に使える
  - 所有権を共有する強い参照 (弱い参照もある : Weak)
  - 不変借用のみがコンパイル時に精査できる
  - グラフ構造とかにつかえる (Node は複数の Edge から参照される可能性)
- RefCell
  - 不変も可変もどっちも OK なデータを扱える
    - 今までは mut のキーワード使っていたが、mut なしで mutable に扱える
    - RC と一緒に扱われることが多い
  - 不変借用も可変借用も実行時に精査される
    - 実行時評価なのでなるべく使わない方が良さそう

## chapter16

良い資料 : https://slideship.com/users/@statiolake/presentations/2019/04/MbPVekdPSTQ2hqGtiP5BK5/

- スレッド
  - thread::spawn で作成できる
  - handle.join() でスレッドを任意の箇所でブロックする
  - mpsc::channel() でスレッド間のデータのやり取りができる
- スレッド間のデータの共有
  - Mutex と Arc を使う
  - Mutex : スレッド間でも安全にデータを扱うデータ型
    - データにアクセスできるスレッドを 1 つに制限できる
    - スコープを抜けるタイミングで、アンロックされる
  - Arc : スレッド間でも安全に所有権を共有できるデータ型

## chapter17

- カプセル化
  - プライベート/パブリックの切り替えは、pub でやる
  - Rust で継承の資料 : https://qiita.com/nacika_ins/items/cf3782bd371da79def74#trait-object
- トレイト/トレイトオブジェクト
  - メソッドの束縛ができる = ダックタイピングみたいなイメージ

## chapter18

- パターンマッチ
  - match を使う方が if let を使う方より安全
  - Rust で継承の資料 : https://qiita.com/nacika_ins/items/cf3782bd371da79def74#trait-object
- \_ と \_var では挙動が異なる
  - _var : 値を変数に束縛する, _ : 値を変数に全く束縛しない

## chapter19

- Unsafe Rust
  - 以下の 4 つのことを行うために使うキーワード (上の 2 つが馴染みやすかった)
    - unsafe な関数やメソッドを呼ぶこと
      - コンパイラーが気づけない安全な処理をポインタを使って実装する
    - 可変で静的な変数にアクセスしたり変更すること
      - mutable なグローバル変数を定義したりする
    - 生ポインタを参照外しすること
    - unsafe なトレイトを実装すること
- 高度な型
  - never 型
    - return を返すことのない関数(または常にスローする)は never を返す
  - isize, usize: そのマシンのポインタサイズと同じサイズの整数型
  - トレイトオブジェクト
    - 実行時にオブジェクトの型を調べ、対応するメソッドを呼び出す
    - Box をかませるとできる
- 高度なライフタイムとトレイトは、難しかったのでさらっとやった

## chapter20

サーバー作る時のメモ (後半はかなり難しかった。。。)

- CRLF = 改行コードのこと
  - windows, **HTTP** : \r\n
  - Linux, MacOSX : \n
- スレッドプール: 待機し、タスクを処理する準備のできた一塊りの大量に生成されたスレッドのこと
  - プール内のスレッド数は、小さい数字に制限する -> DoS(Denial of Service; サービスの拒否)攻撃から保護するため
    - DOS 攻撃：無制限にスレッドを大量生産し、サーバーのリソースを食い尽くす攻撃
- ユニット型: 要素 0 のタプル型のこと