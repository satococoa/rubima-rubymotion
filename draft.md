# RubyMotion のご紹介

書いた人 : 海老沢 聡 ([@satococoa](https://twitter.com/))


## はじめに

この記事では 2012 年 5 月のゴールデンウィーク中に発売された、Ruby で iOS アプリが開発できるという [RubyMotion](http://www.rubymotion.com) を紹介します。

筆者が実際に仕事で使ってみた感想なども踏まえ、今後購入を考えている個人や導入を検討中の企業の方にも参考にしていただけると幸いです。


### RubyMotion とは

[RubyMotion](http://www.rubymotion.com) とは Ruby で iOS アプリを作るための製品です。
開発には使い慣れたテキストエディタ、テストは spec、ビルドは rake コマンドという、Rubyist にとっては普段慣れ親しんだ環境で開発を行うことができます。

[MacRuby](http://macruby.org) をベースに開発されていて、ソースコードの多くが MacRuby と同一だそうです。
RubyMotion 自体はリリースが 2012 年 5 月と非常に若い製品ではありますが、実績のある MacRuby をベースとしているのでその分安心感があります。
また主要開発者である、開発元の [HipByte社](http://www.hipbyte.com) 所属の Laurent Sansonetti さんは以前 Apple で MacRuby の開発をされていた方です。

なお RubyMotion は有料であり、お試しプランなどもありません。
(企業等での多数のライセンスの導入や、学割などを希望される場合は直接サポートに連絡すると優遇を受けられるそうです。)
RubyMotion の実際に動作しているところを見たい場合は、公式サイトの [RubyMotion - Getting Started](http://www.rubymotion.com/getting-started/) に掲載されている動画や [The Pragmatic Studio | RubyMotion Screencast](http://pragmaticstudio.com/screencasts/rubymotion) をご覧になるのがおすすめです。


### RubyMotion の特徴

RubyMotion の良いところは以下の 3 点だと思います。(一番良い点は「慣れ親しんだ Ruby で iOS のアプリが作れる」というただそれだけに尽きると感じていますが。)

1. Ruby で記述したコードは LLVM によって直接バイナリコードに変換されます。
1. シミュレータを再起動すること無く REPL から任意のコードを実行でき、View の調整やちょこっと API の挙動を確かめたり、ということが容易にできます。
1. Test コードを Ruby で書くことができます。モデルのユニットテストから UIAutomation 相当のテストも Ruby で書けます。


逆にちょっと気をつけた方がいいのは以下の点です。

1. Android アプリは開発できません。
1. `eval` や `define_method` などのメソッドは使えません。
1. `require` は使用できません。つまり既存の gem はほとんど使えません。ライブラリは基本的には RubyMotion 用に作られた gem か、Objective-C でできたものを使います。


## RubyMotion の始め方

### 購入〜インストール

[RubyMotion Store](http://sites.fastspring.com/hipbyte/product/rubymotion) からライセンスを購入します。支払いはクレジットカード、もしくは PayPal アカウントで購入が可能です。

ライセンスは 1 年間有効で、期間中はソフトウェアのアップデートとサポートチケットによるサポートを受けることができます。
アップデートとサポートが不要の場合は一度購入するだけでずっと製品を使い続けることもできます。
なおライセンスの継続料金は新規料金の半額を予定されているそうです。

購入が完了するとライセンスキーとインストーラの URL が購入時に登録したメールアドレスあてに送られてきますので、記載された指示に従ってインストールを行ってください。

インストールが完了すると、`motion` というコマンドが使えるようになります。

また、`/Library/RubyMotion` 以下に必要なファイルがインストールされます。

```
$ which motion
/usr/bin/motion
$ motion -v
1.20 # この原稿執筆時の最新バージョンです。
$ motion -h
Usage:
  motion [-h, --help]
  motion [-v, --version]
  motion <command> [<args...>]

Commands:
  create       Create a new project
  activate     Activate the software license
  update       Update the software
  support      Create a support ticket
```


### 動かしてみよう

インストールが完了したら、さっそく簡単なプロジェクトを作ってみたいと思います。
まずは `motion` コマンドでコードを生成します。

```
$ motion create Hello
    Create Hello
    Create Hello/.gitignore
    Create Hello/Rakefile
    Create Hello/app
    Create Hello/app/app_delegate.rb
    Create Hello/resources
    Create Hello/spec
    Create Hello/spec/main_spec.rb
```

- Rakefile  
アプリの設定や gem の require などはここに記述します。
- app/app_delegate.rb  
Xcode を用いて開発する場合の AppDelegate.h, AppDelegate.m に相当するのがこのファイルです。
- resources/  
画像やストーリーボード、CoreData のモデルファイルなどのリソースをここに置きます。
- spec/  
テストはこのディレクトリに置きます。


早速テキストエディタ (エディタ用の拡張等については後述) で `app/app_delegate.rb` を開いてみます。

```
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    true
  end
end
```

デフォルトでこのようなコードが書いてあると思います。
2 行目をご覧ください。Ruby 2.0 で取り入れられるという噂のキーワード引数のようなメソッド定義になっています。

これは RubyMotion 独自の拡張ではなく、既に MacRuby で実装されているものです。ちなみに MacRuby は CRuby 1.9 系をベースに開発されていて、同様に RubyMotion の処理系も基本的には 1.9 系に準拠しています。

早速実行してみます。

```
$ rake
     Build ./build/iPhoneSimulator-5.1-Development
   Compile ./app/app_delegate.rb
    Create ./build/iPhoneSimulator-5.1-Development/Hello.app
      Link ./build/iPhoneSimulator-5.1-Development/Hello.app/Hello
    Create ./build/iPhoneSimulator-5.1-Development/Hello.app/Info.plist
    Create ./build/iPhoneSimulator-5.1-Development/Hello.app/PkgInfo
    Create ./build/iPhoneSimulator-5.1-Development/Hello.dSYM
  Simulate ./build/iPhoneSimulator-5.1-Development/Hello.app
(main)> 
```

ログにあるように、`./app/app_delegate.rb` がコンパイルされ、iOS アプリとして動作するために必要なファイルが生成されたり、といった一連の処理が行われた後、自動的に iOS シミュレータが起動したと思います。

まだ何も描画するコードを書いていませんので、シミュレータには真っ黒の画面が表示されるのみです。

ここで rake コマンドを実行した端末の方に注目すると、入力待ちになっています。
実は REPL が実装されていて、irb のように直接コードを打ち込んで実行することができます。

少し横道にそれて実験してみましょう。プロンプトに続いてコードを入力してみてください。

```
(main)> RUBY_VERSION
=> "1.9.2"
(main)> RUBY_DESCRIPTION
=> "RubyMotion 0.12 (ruby 1.9.2) [universal-darwin12.0, i386]"
(main)> str = 'Hello, world'
=> "Hello, world"
(main)> puts str
Hello, world
=> nil
```

さて、驚きは次です。引き続き端末にコードを打ち込んでください。

```
(main)> str.class
=> String
(main)> str.class.ancestors
=> [String, NSMutableString, NSString, Comparable, NSObject, Kernel]
```

なんと、クラスツリーに `NSMutableString` や `NSString` といった Objective-C 由来のクラスが混ざり込んでいます。正確にはこれも RubyMotion というよりは MacRuby の特徴なのですが、このような構造をしているために実は Ruby コードから Objective-C で定義されているメソッドまで呼び出すことができてしまいます。

試してみましょう。

```
(main)> str.upcase # Ruby の String#upcase
=> "HELLO, WORLD"
(main)> str.uppercaseString # NSString クラスに定義された - (NSString *)uppercaseString
=> "HELLO, WORLD"
(main)> 'Hello, world'.insert(7, 'foo ') # Ruby の String#insert
=> "Hello, foo world"
(main)> 'Hello, world'.insertString('foo ', atIndex: 7) # NSMutableString クラスに定義された - (void)insertString:(NSString *)aString atIndex:(NSUInteger)anIndex
=> "Hello, foo world"
```

面白いですね。


では REPL を終了します。

```
(main)> exit
```

いつまでも真っ黒な画面では寂しいので、`app_delegate.rb` にコードを記述してみましょう。

```
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    @window = UIWindow.alloc.initWithFrame(UIScreen.mainScreen.bounds)
    controller = UIViewController.new
    controller.view.backgroundColor = UIColor.whiteColor
    @window.rootViewController = controller
    @window.makeKeyAndVisible
    true
  end
end
```

コードの中身については Objective-C でアプリを開発した経験が無いと理解が難しいと思いますが、筆者は Objective-C 経験ほぼゼロの状態で RubyMotion を始めましたので、平行して勉強していくことも十分可能です。
これで先ほど同様に rake コマンドを実行すると今度は白い背景の画面が表示されるようになったと思います。

では画面にボタンなどの部品を置いてみようと思います。
Storyboard を使うこともできますが、ここではコードのみで GUI を構築します。

```
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    @window = UIWindow.alloc.initWithFrame(UIScreen.mainScreen.bounds)
    controller = UIViewController.new
    controller.view.backgroundColor = UIColor.whiteColor

    label = UILabel.new.tap do |l|
      l.text = 'foo'
      l.frame = [[10, 10], [100, 60]]
    end
    controller.view.addSubview(label)

    button = UIButton.buttonWithType(UIButtonTypeRoundedRect).tap do |b|
      b.setTitle('Say Hello!', forState:UIControlStateNormal)
      b.frame = [[110, 300], [100, 60]]
    end
    controller.view.addSubview(button)

    @window.rootViewController = controller
    @window.makeKeyAndVisible
    true
  end
end
```

UILabel と UIButton を追加してみました。実行してみると、ラベルの位置が気になりませんか？
GUI の調整を修正しては実行して、というのを何度も繰り返すのは大変なので、REPL で位置の調整を試してみましょう。

![Screenshot1](./screenshot1.png)

コマンドキーを押しながらシミュレータ上でポインタを動かすと、赤い枠線がポインタの下の UI 部品を囲み、それと同時に端末上のプロンプトが `(main)>` から `(#<UILabel:0x6a015c0>)>` に変わるのが確認できましたでしょうか？(`0x6a015c0` は同じ数字ではなくても大丈夫です。)

この状態でクリックすると、REPL が 選択されたオブジェクトを self とした状態で入力待ちになります。以下のコードを入力してみましょう。

```
(#<UILabel:0x6c08f60>)> self.frame = [[110, 100], [100, 20]]
=> [[110, 100], [100, 20]]
```

ラベルの位置が変更できたと思います。
何度もコンパイルし直して位置を調整するよりも簡単に UI 部品の位置、大きさの調整ができることを実感できますね。
忘れずに元の `app_delegate.rb` の 9 行目を今確認した frame の値に書き直しておきましょう。

```
    label = UILabel.new.tap do |l|
      l.text = 'foo'
      l.frame = [[110, 100], [100, 20]]
    end
    controller.view.addSubview(label)
```

さて、今はボタンをタップしても何も起きません。タップするとラベルの文字列が変更されるように修正したいと思いますが、このまま `app_delegate.rb` にコードを書いていくと複雑になってしまうので、UIViewController を継承した独自の MyViewController クラスを定義し、ファイルを分けるリファクタリングを行います。

リファクタリング後の 2 ファイルは以下のようになります。
実行して、先ほどと同じ結果になることを確認してください。

```
# app/app_delegate.rb
class AppDelegate
  def application(application, didFinishLaunchingWithOptions:launchOptions)
    @window = UIWindow.alloc.initWithFrame(UIScreen.mainScreen.bounds)
    my_view_controller = MyViewController.new

    @window.rootViewController = my_view_controller
    @window.makeKeyAndVisible
    true
  end
end

# app/my_view_controller.rb
class MyViewController < UIViewController
  def viewDidLoad
    super

    view.backgroundColor = UIColor.whiteColor

    label = UILabel.new.tap do |l|
      l.text = 'foo'
      l.frame = [[110, 100], [100, 20]]
    end
    view.addSubview(label)

    button = UIButton.buttonWithType(UIButtonTypeRoundedRect).tap do |b|
      b.setTitle('Say Hello!', forState:UIControlStateNormal)
      b.frame = [[110, 300], [100, 60]]
    end
    view.addSubview(button)

  end
end
```

あれ？と思った方もいらっしゃるかもしれません。RubyMotion では`require`が使えない代わりに、`app`ディレクトリ以下のファイルは自動的に読み込まれるようになっています。
(もし読み込まれる順序が問題でコンパイルできない場合は `Rakefile` で `app.files_dependencies` メソッドを使って指定できます。[2.1. Files Dependencies](http://www.rubymotion.com/developer-center/guides/project-management/#_files_dependencies))

ではボタンをタップしたときの動作を追加してみます。
変更点を diff 形式で掲載します。

```
$ git diff
diff --git a/app/my_view_controller.rb b/app/my_view_controller.rb
index d29b2cc..03a06a3 100644
--- a/app/my_view_controller.rb
+++ b/app/my_view_controller.rb
@@ -4,17 +4,24 @@ class MyViewController < UIViewController
 
     view.backgroundColor = UIColor.whiteColor
 
-    label = UILabel.new.tap do |l|
+    @label = UILabel.new.tap do |l|
       l.text = 'foo'
       l.frame = [[110, 100], [100, 20]]
     end
-    view.addSubview(label)
+    view.addSubview(@label)
 
     button = UIButton.buttonWithType(UIButtonTypeRoundedRect).tap do |b|
       b.setTitle('Say Hello!', forState:UIControlStateNormal)
       b.frame = [[110, 300], [100, 60]]
+      b.addTarget(self,
+        action:'say_hello',
+        forControlEvents:UIControlEventTouchUpInside)
     end
     view.addSubview(button)
 
   end
+
+  def say_hello
+    @label.text = 'Hello!'
+  end
 end
\ No newline at end of file
```

まず別のメソッドからラベルが見えるように UILabel のオブジェクトをインスタンス変数に格納するように変更しました。
さらに、`say_hello`というメソッドを定義し、その中でラベルのプロパティを変更しています。
そしてボタンがタップされたときに呼び出されるメソッドを`addTarget:action:forControlEvents:`メソッドで指定して完成です。

実行してみて意図通り動作することを確認してください。


### テストを書こう

現在の RubyMotion では [Bacon](https://github.com/chneukirchen/bacon/) という rspec クローンを Objective-C 用に拡張した [MacBacon](https://github.com/alloy/MacBacon) を利用してテストを書くことができます。

ユニットテストのレイヤーから、バージョン 1.15 で追加された UIAutomation 相当の View, Controller のテストが書けるようになっています。

エディタで`spec/main_spec.rb`を開いてください。以下のようなコードが記述されています。

```
describe "Application 'Hello'" do
  before do
    @app = UIApplication.sharedApplication
  end

  it "has one window" do
    @app.windows.size.should == 1
  end
end
```

アプリケーションに一つの window があることをテストしています。
実行してみます。

```
$ rake spec
     Build ./build/iPhoneSimulator-5.1-Development
   Compile /Library/RubyMotion/lib/motion/spec.rb
   Compile /Library/RubyMotion/lib/motion/spec/helpers/ui.rb
   Compile ./spec/main_spec.rb
    Create ./build/iPhoneSimulator-5.1-Development/Hello_spec.app
      Link ./build/iPhoneSimulator-5.1-Development/Hello_spec.app/Hello
    Create ./build/iPhoneSimulator-5.1-Development/Hello_spec.app/Info.plist
    Create ./build/iPhoneSimulator-5.1-Development/Hello_spec.app/PkgInfo
    Create ./build/iPhoneSimulator-5.1-Development/Hello_spec.dSYM
  Simulate ./build/iPhoneSimulator-5.1-Development/Hello_spec.app
Application 'Hello'
  - has one window

1 specifications (1 requirements), 0 failures, 0 errors
```

図らずも既に仕様を満たしていたのでテストが通りました。
(筆者はテストが一発で通ることに恐怖を感じるので、一応`@app.windows.size.should == 0`としてテストが失敗することも確認しました。)

では先ほど作ったプロジェクトのテストを書いてみましょう。

```
describe "Application 'Hello'" do
  before do
    @app = UIApplication.sharedApplication
  end

  it "has one window" do
    @app.windows.size.should == 1
  end

  describe "rootViewController" do
    before do
      @controller = @app.keyWindow.rootViewController
    end

    it "is an instance of MyViewController" do 
      @controller.class.should == MyViewController
    end
  end

end
```

試しにコントローラのクラスが正しく `MyViewController` であることをテストしました。
テストを実行してみてください。

```
$ rake spec
     Build ./build/iPhoneSimulator-5.1-Development
   Compile ./spec/main_spec.rb
      Link ./build/iPhoneSimulator-5.1-Development/Hello_spec.app/Hello
    Create ./build/iPhoneSimulator-5.1-Development/Hello_spec.dSYM
  Simulate ./build/iPhoneSimulator-5.1-Development/Hello_spec.app
Application 'Hello'
  - has one window

rootViewController
  - is an instance of MyViewController

2 specifications (2 requirements), 0 failures, 0 errors
```

正しく通りましたでしょうか。

では次にボタンをタップしたときの動作を検証します。`spec/my_view_controller_spec.rb`を作成し、以下のように記述しました。

```
describe "The 'My View Controller' view" do
  tests MyViewController

  before do
    @label = view('foo')
  end

  it "change label's title" do
    tap('Say Hello!')
    @label.text.should == 'Hello'
  end
end
```

実行してみます。

```
$ rake spec
     Build ./build/iPhoneSimulator-5.1-Development
  Simulate ./build/iPhoneSimulator-5.1-Development/Hello_spec.app
Application 'Hello'
  - has one window

rootViewController
  - is an instance of MyViewController

The 'My View Controller' view
  - change label's title [FAILED]

Bacon::Error: "Hello!".==("Hello") failed
        spec.rb:553:in `satisfy:': The 'My View Controller' view - change label's title
        spec.rb:567:in `method_missing:'
        spec.rb:183:in `block in run_spec_block'
         4:in `execute_block'
        spec.rb:183:in `run_spec_block'
        spec.rb:198:in `run'

3 specifications (3 requirements), 1 failures, 0 errors
```

残念ながらエラーが出てしまいました。
spec の中で typo したようです。修正は皆さんにおまかせします。


## 開発おすすめツール、ライブラリ

RubyMotion で開発を行うにあたって、定番のツールを紹介したいと思います。
iOS 開発を行っている方にとっては既になじみ深いものもありますね。

### テキストエディタのプラグイン / 拡張

- [SublimeRubyMotionBuilder](https://github.com/haraken3/SublimeRubyMotionBuilder)  
最近人気の [Sublime Text](http://www.sublimetext.com) 用の拡張です。筆者もこれを使っています。

- [RubyMotion.tmbundle](https://github.com/libin/RubyMotion.tmbundle)  
[TextMate](http://macromates.com) の方はこちら。

- [snipmate-snippets-rubymotion](https://github.com/rcyrus/snipmate-snippets-rubymotion)  
vim の方はこの snippets と ctags を使うといいと思います。

- [Rubymotion Completion With Emacs](http://stirredbits.com/blog/2012/05/06/rubymotion-completion-with-emacs/)
Emacs な方はこのエントリに従って設定するのが良さそうです。


### [Dash](http://itunes.apple.com/jp/app/dash-docs-snippets/id458034879?mt=12)

iOS SDK のリファレンスを参照するために使います。他にも Ruby や Rails のリファレンスも参照できる優れものです。

### [TestFlight](https://testflightapp.com/)

iOS 開発では定番！
Adhoc 版アプリを素早く手軽に配布するために使います。


### 定番ライブラリ

#### [motion-cocoapods](https://github.com/HipByte/motion-cocoapods)

Objective-C 版 Bundler とも言える、ライブラリの依存関係の管理を行う [CocoaPods](http://cocoapods.org) を RubyMotion から簡単に使えるようにした gem です。

これのおかげで Objective-C で作られた [AFNetworking](https://github.com/AFNetworking/AFNetworking) などの定番ライブラリを手軽に使うことができます。


#### [motion-testflight](https://github.com/HipByte/motion-testflight)

上述の TestFlight を RubyMotion から手軽に使うための gem です。

この gem を利用すると`rake testflight notes="foo"`のように、コマンドラインから TestFlight を通じたテスト版の配布ができてしまいます。


#### [BubbleWrap](http://bubblewrap.io)

RubyMotion を使った iOS 開発をさらに Ruby らしく書けるようにした gem です。


### [rm-redgreen](https://github.com/mdks/rm-redgreen)

spec の実行結果に色を付ける gem です。


他にも [nitron](https://github.com/mattgreen/nitron), [formotion](https://github.com/clayallsopp/formotion) なども人気の高い gem です。


## 情報源、コミュニティ

まだ若い製品であるため、日本語のまとまった情報はまだあまりありません。  
しばらくは英語の情報を主に見るようにするといいでしょう。

### 英語

- [Developer Center](http://www.rubymotion.com/developer-center/) (公式)
- [Google Group](http://groups.google.com/group/rubymotion) (公式)
- [HipByte/RubyMotion](https://github.com/HipByte/RubyMotion) (公式)
- [RubyMotion Tutorial](http://rubymotion-tutorial.com)

### 日本語

- [Facebook グループ](https://www.facebook.com/groups/149315595198329/)
- [RubyMotionJP](http://rubymotion.jp/)

### RubyMotionJP について

先日[第1回 RubyMotion 勉強会](http://connpass.com/event/665/)を東京にて開催いたしました。

その懇親会の席でそろそろ日本語の RubyMotion の情報をどこかに集約した方がいいのでは……という話になり、Github 上に Organization を立ち上げました。([RubyMotionJP](https://github.com/RubyMotionJP))

当面は [RubyMotion Tutorial](http://rubymotion-tutorial.com) の翻訳を進めていこうと考えております。


## まとめ

やや駆け足ではありますが、これから RubyMotion を始めたいと考える方のスタートダッシュをお手伝いできるよう、広く浅くまとめてみました。

また導入検討中の方の参考になるように簡単なコード例を掲載することで、開発の雰囲気やメリット／デメリットを感じていただけたのではないでしょうか。

何か質問等がございましたらどうぞお気軽に facebook グループや Twitter で #rubymotionjp ハッシュタグをつけてつぶやいていただけると、誰かが拾って回答を差し上げることができると思います。

この記事を通じて、多くの方が楽しく iOS アプリの開発ができるようになることを願っています。


## 著者について
海老沢 聡 ([@satococoa](https://twitter.com/))  
原宿のとある会社でスマートフォンアプリの開発をしています。現在 Rails + RubyMotion でアプリ開発中。