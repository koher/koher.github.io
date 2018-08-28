---
layout: markdown
date: 2018-08-29
css:
    - rouge
---

# 今、僕が一番注目している Swift の新機能について、 iOSDC Japan 2018 で話します

{% include date.html %}

僕が Swift 4.2 および 5.0 の新機能で特に注目しているのが `@dynamicMemberLookup` と `@dynamicCallable` です。これらは **Swift の生みの親である Chris Lattner が提案** したもので、僕はこの二つを Swift の今後にとって、とても重要な新機能だと考えています。

`@dynamicMemberLookup` と `@dynamicCallable` はそれぞれ次の Proposal で提案されています。

- [SE-0195: Introduce User-defined "Dynamic Member Lookup" Types](https://github.com/apple/swift-evolution/blob/master/proposals/0195-dynamic-member-lookup.md)
- [SE-0216: Introduce user-defined dynamically "callable" types](https://github.com/apple/swift-evolution/blob/master/proposals/0216-dynamic-callable.md)

これらの Proposal の Author はどちらも Chris Lattner です（ SE-0216 は共著）。どうして彼はこの二つの機能を提案したのでしょうか？

Swift を発表した後、 Chris Lattner は Apple を辞めて、今は Google で働いています（ Swift と関わるのを止めたわけではなく、今でも [Core Team](https://swift.org/community/#core-team) の一員です）。 Google では機械学習・人工知能関連のチームで、 [Swift for TensorFlow](https://medium.com/tensorflow/introducing-swift-for-tensorflow-b75722c58df0) のプロジェクトを率いています。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="en" dir="ltr">Swift for <a href="https://twitter.com/TensorFlow?ref_src=twsrc%5Etfw">@TensorFlow</a> Talk video and announcement are up!  Check it out: <a href="https://t.co/3B4127kbFZ">https://t.co/3B4127kbFZ</a> and join the conversation:  <a href="https://t.co/dNIpVzphLO">https://t.co/dNIpVzphLO</a></p>&mdash; Chris Lattner (@clattner_llvm) <a href="https://twitter.com/clattner_llvm/status/979886581371740160?ref_src=twsrc%5Etfw">2018年3月31日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


`@dynamicMemberLookup` と `@dynamicCallable` は Python との連携を容易にします。これらを使って Python 連携機能を作れば、たとえば次のような Python のコードを

```python
# Python

import numpy as np

a = np.array([[1, 2], [3, 4]])
b = np.array([[5], [6]])
c = np.matmul(a, b)

print(c)
```

Swift で

```swift
// Swift

import Python

let np = Python.import("numpy")

let a = np.array([[1, 2], [3, 4]])
let b = np.array([[5], [6]])
let c = np.matmul(a, b)

print(c)
```

と書くことができるようになります。 Swift から Python のライブラリである [NumPy](http://www.numpy.org/) が違和感なく利用できています。

そして、 Python は機械学習で一番用いられている言語です。糸がつながってきましたね。

- `@dynamicMemberLookup` と `@dynamicCallable` は Chris Lattner が提案した
- Chris Lattner は機械学習関連のプロジェクトに従事している
- `@dynamicMemberLookup` と `@dynamicCallable` は Python との連携を容易にする
- Python は機械学習でよく用いられている

---

この `@dynamicMemberLookup` と `@dynamicCallable` を軸に、 **["iOSエンジニアのための、SwiftからPythonのライブラリを使って機械学習する方法"](https://fortee.jp/iosdc-japan-2018/proposal/5df766a7-d5f8-4dac-b6dd-3646f77347f7)** というタイトルで、 [iOSDC Japan 2018](https://iosdc.jp/2018/) で発表します。上記の内容に興味がわいた方、より深く知りたいという方は是非お越し下さい。

ちなみに、 `@dynamicMemberLookup` と `@dynamicCallable` はそれぞれ Swift 4.2 および 5.0 の新機能ですが、すでにこれらを使って Python 連携をすることができます。 **一足先に、 Swift の未来に触れてみることができます。**

なお、 iOSDC 参加者は機械学習には詳しくない人が多いと思うので、取り扱う内容が理解できるように **機械学習入門についても話します**。さらに、せっかくの **iOS** DC なので、学習したモデルを **Core ML 用に変換して、 iOS アプリから使ってみる** ところまで説明します。

そのため、次のような幅広いトピックを扱うお得な発表になっています。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">↓の広範囲のトピックを扱うおトクな発表をします。 <a href="https://twitter.com/hashtag/iosdc?src=hash&amp;ref_src=twsrc%5Etfw">#iosdc</a> <br><br>単に浅く広くになってしまわないように、発表を聴いて理解することと後から学ぶことを区別して話します。まずこれを知っておくと学習効率が良いだろうという内容を説明し、後から自分で学ぶための方法を示します。<a href="https://t.co/MMMpYrYG30">https://t.co/MMMpYrYG30</a> <a href="https://t.co/ITgwgLqSKJ">pic.twitter.com/ITgwgLqSKJ</a></p>&mdash; koher (@koher) <a href="https://twitter.com/koher/status/1033895145693794305?ref_src=twsrc%5Etfw">2018年8月27日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

一つでも気になるトピックがあった方は是非お越し下さい！
