---
title: "Rails 5.2について発表してきた"
date: 2018-03-04T09:49:13+09:00
---

資料。

<script async class="speakerdeck-embed" data-id="d0c02bdec2494a25b92f059ad7c69588" data-ratio="1.37265415549598" src="//speakerdeck.com/assets/embed.js"></script>
[(GitHub Page)](https://y-yagi.github.io/presen_rails_5_2/#)

<script async class="speakerdeck-embed" data-id="532b9432e56746c383c4382752f3cadc" data-ratio="1.37265415549598" src="//speakerdeck.com/assets/embed.js"></script>
[(GitHub Page)](https://y-yagi.github.io/presen_rails_5_2_part2/#/)

5.1についてやった時に一回で詰め込みすぎたな、と反省したので、今回は二回に分けてやった(一回目がmajor featureで、二回目が各コンポーネント)。割と良かったんじゃないかと個人的には思っているので、多分次もそうすると思う。

なお、面倒なので資料は更新してないのですが、Content Security Policyについてはその後また手が少し入りまして、

* デフォルトでは無効化されたり [Disable CSP by default](https://github.com/rails/rails/pull/31830)
* Rails UJS用にnonceの自動生成処理が入ったりしました [Add support for automatic nonce generation for Rails UJS](https://github.com/rails/rails/pull/32018)

という感じになりました。

また今後変わる可能性もありますが、現状はそんな感じで。最終的には[5-2-stable](https://github.com/rails/rails/tree/5-2-stable)のコードを見て頂くのが良いと思います。

そんな感じで。
