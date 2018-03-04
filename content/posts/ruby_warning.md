---
title: "Rubyのwarningから不要なwarningを除外する"
date: 2018-02-01T09:49:13+09:00
draft: true
---

Rubyのwarningは、使用していない変数やメソッドの再定義を教えてくれたりして大変便利です。 しかし、残念ながらwarningは無効になっている事が多い印象です。

理由は色々あると思いますが、その一つに、使用しているgemで大量にwarningが発生してしまい、それが邪魔になってしまっているから、というのがあるのではと思います。

例えばRailsアプリの場合だと大量のgemを使用しているかと思います。本来知りたいwarningはアプリで発生しているwarningかと思いますが、gemからwarningが大量に出てしまう為、本来見たいwarningが埋もれてしまう事もあるかと思います。

そんなwarningが出るgemは、出来れば頑張って直していきたいところですが、中々難しい事もあるかと思います。PR投げてもマージしてくれない事もありますしね。そのため、そのようなwarnigは一旦無視したいところです。

という訳で本題です。Ruby 2.4からwarningを出力する為のWarning module が提供されるようになりました。Ruby 2.4からはwarningを出力する際は、この Warning moduleのwarnメソッド経由で呼ばれるようになっています。つまり、Warning moduleのwarnメソッドを再定義することで、warningの出力方法をカスタマイズできるようになっています。

たとえば、[warning](https://github.com/jeremyevans/ruby-warning)というgemは、この機能を利用し、特定のwarningを出力しないようにしてくれます。

この機能を利用し、gemのwarningを出力しないようにしてみましょう。Rubyのwarningにはそのwarningが発生した行(e.g. `.rbenv/versions/2.4.1/lib/ruby/gems/2.4.0/gems/spring-watcher-listen-2.0.1/lib/spring/watcher/listen.rb:39: warning: instance variable @listener not initialized` のような形式) が含まれています。 そのため、warningが"gems"という文字が含まれていたら、gemからのwarningだと判断し無視するようにしてみます。

```ruby
module Warning
  def warn(str)
    return if str.match?("gems")

    super
  end
end

$VERBOSE = true
```

これでwarningに"gems"という文字が含まれていたらwarningを表示しないように出来ました(大分荒い対応なので、もう少し正確にパスを入れた方が良い気はしますが)。私の場合はtest_helperに上記設定を行い、テスト実行時にwarningを出すようにしています。

Ruby 2.4以上でしか使えない手ではありますが、これで無事本来見たいwarningだけ見れるようになりました。良かった良かった。

参考：[Feature \#12299: Add Warning module for customized warning handling](https://bugs.ruby-lang.org/issues/12299)
