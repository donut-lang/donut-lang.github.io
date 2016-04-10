---
layout: page
title: GettingStarted
permalink: /getting_started/
---

ど〜なっつは、<strong>時を司るプログラミング言語</strong>です。…はて。

普通のプログラミング言語では、実行の流れはただひとつ、過去から未来に流れていき、戻ることはありません。覆水盆に返らずってやつです。

でも、それでは困ることもあります。過去の時点に戻りたい、やり直したい、そういう時もあるでしょう。プログラミング言語「ど～なっつ」では、ソフトウェアの中でだけですが、そういった事を実現することができます。


ど～なっつは…
<ul>
	<li>処理系の状態を<strong>過去に遡らせ、未来に還せる</strong>プログラミング言語です。</li>
	<li>任意の時点での<strong>セーブデータを書き出し、後で実行を再開</strong>できます。</li>
	<li><strong>C++ネイティブのアプリケーションから組み込まれる</strong>事を意識しています。</li>
	<li>「反副作用」というアプローチによって、<strong>組み込み先の外部環境も過去に戻せます</strong>。</li>
	<li>C++11の可変長引数テンプレートを使って、<strong>簡単にネイティブとの接続</strong>ができます。</li>
	<li><a href="http://www.amazon.co.jp/JavaScript-The-Good-Parts-%E2%80%95%E3%80%8C%E8%89%AF%E3%81%84%E3%83%91%E3%83%BC%E3%83%84%E3%80%8D%E3%81%AB%E3%82%88%E3%82%8B%E3%83%99%E3%82%B9%E3%83%88%E3%83%97%E3%83%A9%E3%82%AF%E3%83%86%E3%82%A3%E3%82%B9/dp/4873113911">JavaScript: The Good Parts</a>を元にした、JavaScriptを意識しつつ残念な点を取り除いたシンプルな<strong>プロトタイプ型オブジェクト指向言語</strong>です。</li>
	<li>同じポリシーに基づいた「時を司るGUIツールキット：ちさ」も開発中です。</li>
</ul>

# サンプル

いくつかサンプルを示します。

## Hello world

```
System.println("Hello world");
```

## 再帰を使った階乗の計算

```
f = func(idx, acc){
    if( idx <= 0 ){
        acc; //最後に評価された値が戻り値になる
    }else{
        return f(idx-1, acc+idx); //戻り値を明示することもできます。
    };
};
System.println(f(10, 0)); // prints 55
```

ブロックの一番最後に評価された式がそのブロックの値になります。return式を使ってそのことを明示することもできます。

なお、ど～なっつにはCなどにある「文」はありません。ifも式です。

##スクリプト内から時を操作する

「時を司るプログラミング言語」の二つ名の通り、「ど～なっつ」ではスクリプト言語の中の時間を自由に操作することができます。

```
//普通の変数は、時間操作の影響を受けますが…
tabeta=0;

//Homuraは時間操作前の事を覚えていて、時間操作の影響を受けません。
Homura.counter=0;

//このあとの命令を使って、この時点まで時を戻せるようになります。
save_time=Homura.tick();

if(Homura.counter < 10){
  // ドーナッツを食べましょう。
  // この足し算の結果は、時間操作で戻ってしまいます。
  tabeta++;

  // ドーナッツを食べた回数を表示しましょう
  System.println(tabeta, "番目のドーナッツを食べた！");

  // Homuraに入った値は時間操作の影響を受けないので、
  // 時間操作を行なってもこの足し算の結果は戻りません。
  Homura.counter+=1;

  // 上で記録した時間まで、スクリプトエンジン全体を戻します。
  Homura.seek(save_time);
} else {
  //ど～なっつでは、else節を省略することはできません。
};
```

これを実行すると…

```bash
% donut time_op.donut
 1番目のドーナッツを食べた！
 1番目のドーナッツを食べた！
 1番目のドーナッツを食べた！
 1番目のドーナッツを食べた！
 1番目のドーナッツを食べた！
 1番目のドーナッツを食べた！
 1番目のドーナッツを食べた！
 1番目のドーナッツを食べた！
 1番目のドーナッツを食べた！
 1番目のドーナッツを食べた！
```

このように何度ループをめぐっても１番めのドーナッツを食べ続けることができますが、Homuraは時間操作の影響を受けないので、無限ループには陥らずに済みます。

# ホストアプリケーションとのデータのやり取り
ど～なっつは、C++に組み込むことを前提に作られたスクリプト言語です。以下のようにして比較的簡単にネイティブアプリに組み込むことができます。

```cpp
#include <donut/Donut.h> //includeが必要なのはひとつだけです。
using namespace donut;

int main(char argc, char** argv){
    Handler<Donut> donut(new Donut()); //ど〜なっつ本体の作成を行います

    //セーブデータの読み込みではなく、新規の環境を作ります。
    donut->bootstrap();

    //実際に実行を行う仮想マシンを取得します
    Handler<Machine> machine = donut->queryMachine();

    //ソースをコンパイルします。
    Handler<Source> src = donut->parse( "1+1;" );

    //コンパイルしたソースを実行し、結果が帰ってきます。
    Handler<Object> obj = machine->start( src );

    //ど〜なっつのオブジェクトをC++の数値に変換します。
    int num = obj->toInt(donut->heap());

    //その結果を表示します
    std::cout << num << std::endl;

    return 0;
}
```

その他のサンプルは<a href="https://github.com/ledyba/Chisa/tree/master/__donut_sample__">サンプルフォルダ</a>にあるかもしれません。

# ライセンス
ど～なっつのライセンスは<a href="http://www.gnu.org/licenses/gpl.html">GPLv3</a>となっています。

# ど～なっつの誕生経緯
ど～なっつは<a href="http://www.ipa.go.jp/jinzai/mitou/">IPAの未踏IT人材発掘・育成事業</a>に採択された<a href="http://www.ipa.go.jp/jinzai/mitou/2012/2012_1/gaiyou/h-1.html">「CPUの理解を容易にするシステムと解説サイトの構築」</a>の一部分として開発され、支援を受けています。
