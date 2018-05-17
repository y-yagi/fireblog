---
title: "RailsとContent Security Policy"
date: 2018-05-02T09:49:13+09:00
draft: true
---

自分でやっているサイトでContent Security Policyの対応をやったので、何をやったのか備忘録として書いておきます。Railsのバージョンは5.2.0。

## Content Security Policy

Content Security Policy(CSP) とは、 Web におけるセキュリティを向上させる為のしくみです。コンテンツに対しContent-Security-Policyヘッダを付加することで、ブラウザに読み込を許可するコンテンツを制限することが出来ます。

これにより、意図しない外部スクリプトや、インラインスクリプト埋め込みによるXSSを防ぐ事が出来ます。

CSPについて自体は、ちゃんと説明しているサイトがいくつもあるので、詳細はそちらを参照。

* [Content Security Policy \(CSP\) \- HTTP \| MDN](https://developer.mozilla.org/ja/docs/Web/HTTP/CSP)
* [コンテンツ セキュリティ ポリシー  \|  Web  \|  Google Developers](https://developers.google.com/web/fundamentals/security/csp/?hl=ja)

辺りがおすすめです。


## RailsでContent Security Policy

で、こっからが本題。 Rails 5.2でRails本体にContent Security Policyの対応が入りました。PRは[こちら](https://github.com/rails/rails/pull/31162)。

