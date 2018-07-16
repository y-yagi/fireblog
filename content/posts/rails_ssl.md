---
title: "Railsの開発環境でHTTPSを有効にする"
date: 2018-07-16T09:49:13+09:00
tags: [rails]
---

いまだとどうするのが良いんだろうね、と思い調べたのメモ。サーバはPumaを使う前提で、2018/07時点での話。

### 証明書準備

昔はこれが色々手間だったのだが、今は[mkcert](https://github.com/FiloSottile/mkcert)という大変便利なツールがあり、これを使えばすぐ終わった。

[README](https://github.com/FiloSottile/mkcert#installation)に書いてあるとおりにインストールすれば、Linux / Macについては直ぐ準備出来ると思う。因みにWindowsは「Windows will be supported next.」とのこと(2018/07時点)。

で、このツールを使い、localhost用のcertificateとkeyファイルを生成する。

```
$ mkcert -install
Using the local CA at "/home/yaginuma/.local/share/mkcert" ✨
$ ./bin/mkcert localhost
Using the local CA at "/home/yaginuma/.local/share/mkcert" ✨

Created a new certificate valid for the following names 📜
 - "localhost"

The certificate is at "./localhost.pem" and the key at "./localhost-key.pem" ✅
```

後は生成されたcertificateとkeyを適当なディレクトリに格納して準備終了。


### Pumaの設定

PumaはPuma自体にSSL用の処理が含まれているので、configファイルにcertificateとkeyのpathを指定してあげればOK。

```ruby
# config/puma.rb

cert = "/path/to/localhost.pem"
key = "/path/to/localhost-key.pem"
ssl_bind "0.0.0.0", 9292, cert: cert, key: key
```

後は普通に`rails server`でserverを起動する。

```
$ rails server
```

上記ではportに9292に指定しているので、https://localhost:9292/ にアクセスするとHTTPSになっている事が確認出来る。

{{< figure src="/images/development_ssl/01.png" >}}

9292ではなく、普通にRails Serverを起動したと同じように3000番を使いたい場合、上記9292を3000に変更すればOK。ただその場合、同じ設定ファイルで`port`に3000を指定している場合、そこを変更する必要がある。

```diff
diff --git a/config/puma.rb b/config/puma.rb
index 385d394..cf2a801 100644
--- a/config/puma.rb
+++ b/config/puma.rb
@@ -9,7 +9,6 @@ threads threads_count, threads_count

 # Specifies the `port` that Puma will listen on to receive requests; default is 3000.
 #
-# port        ENV.fetch("PORT") { 3000 }

 # Specifies the `environment` that Puma will run in.
 #
@@ -35,4 +34,4 @@ plugin :tmp_restart

-ssl_bind "0.0.0.0", 9292, cert: cert, key: key
+ssl_bind "0.0.0.0", 3000, cert: cert, key: key
```

これで https://localhost:3000/ にアクセス出来る。

{{< figure src="/images/development_ssl/02.png" >}}

`config.force_ssl = true`を指定しても良いかもだが、その辺りはお好みで。

おしまい。
