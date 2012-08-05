# RubyMotion のご紹介

書いた人 : 海老沢 聡 ([@satococoa](https://twitter.com/))

## はじめに
この記事では Ruby で iOS アプリが開発できる [RubyMotion](http://www.rubymotion.com) を紹介します。

筆者が実際に仕事で使ってみた感想なども踏まえ、今後購入を考えている個人や、導入を検討中の企業の方にも参考にしていただけると幸いです。


### RubyMotion とは
簡潔に言ってしまうと、Ruby で iOS の SDK を使うことができるツールです。開発は使い慣れたテキストエディタ、テストは spec (正確にはbacon)、ビルドは rake コマンドで行うという、とても Rubyist フレンドリーなものです。

[MacRuby](http://macruby.org) をベースに開発されていて、ソースコードの多くが MacRuby と同一だそうです。

主要開発者である、開発元の [HipByte社](http://www.hipbyte.com) 所属の Laurent Sansonetti さんは以前 Apple で MacRuby の開発をされていた方です。

なお RubyMotion は有料であり、お試しプランなどもありません。
(企業等での多数のライセンスの導入や、学割などを希望される場合は直接サポートに連絡すると優遇を受けられるそうです。)


### 特徴
- LLVM によって直接バイナリコードに変換されます。つまり Objective-C にコードを変換したり、Ruby の VM 経由でスクリプトを動かす訳ではありません。
- シミュレータを再起動すること無く REPL から任意のコードを実行できますので、View の調整やちょこっと API の挙動を確かめたり、ということが容易にできます。
- Test コードを Ruby で書くことができます。モデルのテスト〜 UIAutomation 相当のテストも Ruby で書けます。
- Android アプリは開発できません。


## RubyMotion の始め方
簡単なサンプルを作ってみて動かせるようなチュートリアルをコード付きで掲載します。

### 購入〜インストール
### 動かしてみよう
### テストについて


## ライブラリ
ざっくりと広く使われていそうなライブラリの紹介をします。
おそらく、以下のgemあたり。

- motion-cocoapods
- motion-testflight
- BubbleWrap
- nitron
- formotion


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

その懇親会の席でそろそろ日本語の RubyMotion の情報をどこかに集積した方がいいのでは……という話になり、Github 上に Organization を立ち上げました。([RubyMotionJP](https://github.com/RubyMotionJP))

当面は [RubyMotion Tutorial](http://rubymotion-tutorial.com) の翻訳を進めて行こうと考えております。


## まとめ



## 著者について
海老沢 聡 ([@satococoa](https://twitter.com/))  
原宿のとある会社でスマートフォンアプリの開発をしています。現在 Rails + RubyMotion でアプリ開発中。