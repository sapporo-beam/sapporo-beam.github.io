---
layout: post
title: Guessing Game
date: 2014-06-27 00:04:48 UTC
---

ExUnitとMixの使い方についてです。

昨日は[Elixir Guessing Game](http://elixirdose.com/elixir-guessing-game)という投稿を見つけたので、この方法にならって進めました。

詳しくはこの投稿を見てもらうとして、MixはBundlerのように、`bundle gem`コマンドにあたるのが`mix new`というコマンドです。

また、テスト駆動の方法を紹介しているので親切な印象を受けました。

まず文中の`:random.uniform`という記述はErlangの[random](http://erlang.org/doc/man/random.html)を直接呼び出しているようです。

また、Erlangの`random.uniform`は引数なしでFloatの乱数、`random.uniform(10)`とすると、整数の範囲内で乱数を返します。

Rubyの感覚で`random.uniform(10..20)`とすると、エラーが発生してしまうようです。そこで、[elixir-random](https://github.com/mururu/elixir-random)を利用すると`Random.sample(10..20)`というように範囲内の乱数が返ってきます。

**NOTE:** Erlangだけで範囲の乱数を取得する場合は`random.uniform(10) + 10`です。

Elixirでは`require`とか`import`のような記述をしなくてもモジュール単位でファイルを読み込んでくれるようです。`iex`でファイルを読む必要があるならば、*mix.exs*のあるディレクトリで`iex -S mix`です。

```elixir
def check_user_input(number) when number <= 10 do
    {:ok, number}
end
```

文中にあるこのコードですが、**numberが1以上且つ、10以下である**条件に書き換えようとしたところ、エラーが発生してしまいました。

```elixir
iex> def check_user_input(number) when number <= 10 and number > 0 do
...>   {:ok, number}
...> end
** (ArgumentError) cannot invoke def/2 outside module
    (elixir) lib/kernel.ex:3748: Kernel.assert_module_scope/3
    (elixir) lib/kernel.ex:2980: Kernel.define/4
    (elixir) expanding macro: Kernel.def/2
    (elixir) iex: :elixir_compiler.__FILE__/2
```

これを修正する方法はbeamのチャットで教えていただいたのですが`&&`のかわりに`and`を定義されたモジュールの中で書き換えます。

```elixir
defmodule GuessingGame do
  def check_user_input(number) when (number <= 10 and number > 0) do
    {:ok, number}
  end
end
```

これで先ほどのテストが無事通りました。

[User Interface](http://elixirdose.com/elixir-guessing-game/#userinterface)以降がまだうまくいかなかったため、次回に続きます。
