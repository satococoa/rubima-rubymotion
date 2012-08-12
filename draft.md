# RubyMotion のご紹介

書いた人 : 海老沢 聡 ([@satococoa](https://twitter.com/))


## はじめに
この記事では 2012 年 5 月のゴールデンウィーク中に突如発売された、Ruby で iOS アプリが開発できる [RubyMotion](http://www.rubymotion.com) を紹介します。

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

```


### テストを書こう
現在の RubyMotion では [Bacon](https://github.com/chneukirchen/bacon/) という rspec クローンを Objective-C 用に拡張した [MacBacon](https://github.com/alloy/MacBacon) を利用してテストを書くことができます。

ユニットテストのレイヤーから、バージョン 1.15 で追加された UIAutomation 相当の View, Controller のテストが書けるようになっています。

試しに先ほど作ったプロジェクトのテストを書いてみましょう。

まずは実行してみます。

```
$ rake spec
```


## RubyMotion の制限



## 開発おすすめツール
RubyMotion で開発を行うにあたって、定番のツールを紹介したいと思います。
iOS 開発を行っている方にとっては既になじみ深いものもありますね。

### テキストエディタのプラグイン / 拡張

### Dash

### TestFlight


## ライブラリ
現在広く使われているライブラリを紹介します。
なお RubyMotion では従来の RubyGems は使用できず、RubyMotion 用に作られた gem しか使えませんのでお気をつけ下さい。

- motion-cocoapods
- motion-testflight
- BubbleWrap

- nitron
- formotion
- rm-redgreen


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

当面は [RubyMotion Tutorial](http://rubymotion-tutorial.com) の翻訳を進めて行こうと考えております。


## まとめ



## 著者について
海老沢 聡 ([@satococoa](https://twitter.com/))  
原宿のとある会社でスマートフォンアプリの開発をしています。現在 Rails + RubyMotion でアプリ開発中。