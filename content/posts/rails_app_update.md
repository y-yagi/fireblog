---
title: "[Rails] app:update タスクについて"
date: 2018-05-31T09:30:13+09:00
tags: [rails]
draft: true
---

タイトルの通り。ちょっと調べたのでそのメモ。なお、確認した際のGoのバージョンは1.10。

## functionやstruct fieldをdeprecateにしたい場合どうするのが良いか

Go公式では、deprecateにしたい機能のdocに、"Deprecated:"ではじまるコメントを記載することを推奨している。[Godoc: documenting Go code](https://blog.golang.org/godoc-documenting-go-code)のページ中部あたりにそのむね説明がある(コミットは[これ](https://github.com/golang/blog/commit/257114af91a0defb1fc2c16c7f4ae2429b8a4e0f))。

[standard library](https://golang.org/search?q=Deprecated:)でもそのように対応しており、例えば、`os/file.go`の[`SEEK_SET` const](https://golang.org/src/os/file.go?h=Deprecated%3A#L80)では、下記のようになっている。

```go
// Deprecated: Use io.SeekStart, io.SeekCurrent, and io.SeekEnd.
const (
	SEEK_SET int = 0 // seek relative to the origin of the file
	SEEK_CUR int = 1 // seek relative to the current offset
	SEEK_END int = 2 // seek relative to the end
)
```

なので、自分で作成したライブラリ等で何かdeprecateにしたい場合は、同じように対応するのが良さそう。

ただ、このルールに従ってコメントを書いても、godocで特別な扱いになる訳ではなく、他のdocと同じように表示される。

deprecatedになっているAPIは表示しないようにしよう、という提案([proposal: godoc: hide deprecated things by default](https://github.com/golang/go/issues/17056))はあるものの、この記事作成時点ではまだ対応されていない。

golintでチェック出来るようにしよう、という提案([Report the use of deprecated stuff](https://github.com/golang/lint/issues/238))もあった。こちらはPRも出されただが、golintではout of scopeという事でcloseされてしまった([Implement deprecation warning](https://github.com/golang/lint/pull/318))。

という訳で、今の所公式では"Deprecated: "なコメントに対する特別な対応は無い模様。


## staticcheckでチェックする

流石に目視でチェックするのは辛い。で、調べた所、[go-tools](https://github.com/dominikh/go-tools)にある[staticcheck](https://github.com/dominikh/go-tools/tree/master/cmd/staticcheck)でdeprecatedのチェックをしてくれる事がわかった。

staticcheckでは、deprecatedになっている機能を使っている場合、下記のようにレポートをしてくれる。

```go
# main.go
package main

import (
	"net/http/httputil"
)

func main() {
	httputil.NewClientConn(nil, nil)
}
```

```bash
$ staticcheck main.go
deprecated.go:8:2: httputil.NewClientConn is deprecated: Use the Client or Transport in package net/http instead.  (SA1019)
```

"Deprecated: "より後ろのコメントをそのまま表示してくれるようになっている。

これで期待通りにチェック出来た。staticcheckはチェックに引っかかった場合、デフォルトで非ゼロをreturnするようになっているので、CIでコマンドを実行し、問題があったらCIでエラーにしてしまう、という運用が良さそう。

