# 関数型プログラミングがJavaに導入された経緯を一発で理解する

## はじめに
Java SE8 Goldを取得し、出題の大半を占めていた関数型インターフェースとラムダ式、Stream APIは使えるようになったけど、結局だから何？状態でした。

そこで、そもそもなぜ関数型プログラミングが導入されたのか、関数型インターフェースやラムダ式の考え方はどうやって生まれたのかをまとめてみることにしました。(~~試験受ける前にまとめとけよ~~)

## 命令型プログラミング と 関数型プログラミング
### Javaは命令型プログラミングを支持してきた！
いきなりですが、以下のコードを見てください。

```java
  public static void findMatsumoto(List<String> names) {

    boolean found = false;

    for (String name : names) {
      if (name.equals("Matsumoto")) {
        found = true;
        break;
      }
    }
     
    if (found)
      System.out.println("Found Matsumoto");
    else
      System.out.println("Sorry, Matsumoto not found");
  }
```
とても単純なメソッドですね。  
引数で渡されたnamesリストに対して、要素１つずつループを回して、Matsumotoと一致するかどうかをチェックしています。  
Matsumotoと一致する要素があれば、事前に用意したローカル変数foundをtrueにしてループを抜けます。  
最後に、foundがtrueなら「found Matsumoto」、falseなら「Sorry, Matsumoto not found」を表示するというメソッドです。

以上のように、このメソッドは各要素を繰り返し、値を比較し、フラグを設定し、ループを抜けるというすべての手順を定義している、所謂『**命令型プログラミング**』で書かれています。

### 関数型プログラミングで記述してみるとどうなる？

↑のメソッドを関数型プログラミングで記述してみると以下のようになります。  
※実際この記述では関数型というより宣言型ですが一旦置いておきます。詳しくは文献[[1]](https://www.ibm.com/developerworks/jp/java/library/j-java8idioms1/index.html)を参照ください。

```java
public static void findMatsumoto(List<String> names) {

  if (names.contains("Matsumoto"))
    System.out.println("Found Matsumoto");
  else
    System.out.println("Sorry, Matsumoto not found");

}
```

命令型プログラミングに比べて非常にすっきりしていますよね。  
特に関数型ではループ処理を記述していません。  
containsメソッドに処理をすべてお任せして、開発者はどのように処理するのかまったく関与していません。  
しかしながら、これだけの記述で期待する結果を得ることができるのです。  

つまり、関数型プログラミングで記述すると命令型プログラミングに比べて**簡潔で理解しやすいコード**にすることができるわけです。

★参考文献  
[1] [関数型プログラミングへの移行の近道　-Java プログラムに宣言型の考え方で関数型手法を導入する-](https://www.ibm.com/developerworks/jp/java/library/j-java8idioms1/index.html)

## 関数型プログラミングのメリットは他にもたくさんある！

コードを簡潔で理解しやすくできる以外にも関数型プログラミングにはメリットがたくさんあります。

関数型プログラミングにおいて関数は以下のような性質を持ちます。  
　**● 入力した値に対して必ず同じ結果を返す**  
　**● 関数の評価が他に影響を及ぼさない**

以下のメソッドは関数と呼ぶことができます。

```java
public int add(int x, int y) {

  return x + y;

}
```
入力( x と y )に対して必ず同じ結果を返し、ここでの評価が他に一切の影響を与えません。

対して、以下のメソッドは関数と呼べるでしょうか？

```java
public int add(int y) {

  return x += y; //xはメソッド外で定義された変数

}
```

このメソッドは入力 y に対して必ずしも同じ結果を返しません。(外部の変数 x に依ります。)  
また、外部の変数 x に対して影響を及ぼしてしまっています。  
以上のことからこのメソッドは関数とは呼べません。

上記の性質から、関数を使うことで以下のようなメリットが考えられます。  
**1. テストがしやすい**  
入力した値に対して必ず同じ結果を返ってくるので、テストケースは１つだけ考えればよくなります。

**2. 安全性が高い**  
関数による評価が他に影響を与えないので、予期せぬ変更が生じることがなく、とても安全であると言えます。

**3. 並列処理を効率化**  
関数同士が依存しないため、並列処理に向いています。
近年、シングルコア性能の向上は鈍化し、CPUアーキテクチャはコア数を増やすマルチコア化の傾向にあります。
関数を使うことでこのマルチコア化の流れにフィットすることが期待されます。
つまり、並列処理を効率化し処理速度向上が望めるということです。

### 関数型プログラミングの特筆すべき性質
さらに、関数型プログラミングの特筆すべき性質として以下の2つが挙げられます。  
　**● 関数の引数に関数を渡せる**  
　**● 関数の戻り値に関数を渡せる**

具体例を、関数型プログラミング言語として有名な「**Haskell**」を用いて示したいと思います。

以下は配列の要素をコンソールに表示するプログラムです。

```haskell
ary = [ 1, 3, 5, 7, 9 ]
main = print $ foldl (+) 0 ary
```

print という関数の引数に foldl という関数を渡し、foldl には配列 ary 含む複数の値を渡しています。  
非常に簡潔に書かれていることがわかると思います。

同様の処理をJavaで行おうとすると、

```java

int[] ary = {1, 3, 5, 7, 9};
    
public int addAll(int[] ary) {
    
    int total = 0;
    
    for (int i : ary) {
        total += i;
    }

    return total;
}
```
となります。ループ処理を記述しているので、コードが少し長くなってしまいました。

関数型プログラミングを使えば、**たった1行**で配列の要素をコンソールに表示する処理を記述できるので、記述量の差は歴然ですね。

※ここまで関数型プログラミングのメリットばかり取り上げてきましたが、盲目的に関数型プログラミング最高!!!と思っているわけではありません。**経緯を明確にするため**メリットのみを取り上げています。  
関数型プログラミングと命令型プログラミング、手続き型プログラミングなどとの優劣については、本稿の趣旨から外れるため議論しません。（私自身が議論できるほど関数型プログラミングに造詣が深くないです。。）

★参考文献  
[2] [関数型プログラミングで行こう 〜 みんな大好きJavaから入る関数型の入り口の入り口](http://tercel-tech.hatenablog.com/entry/2014/12/29/173711)  
[3] [関数型プログラミングの威力を知ろう-後編](https://blog.codecamp.jp/function-programming-2)

## そんなにメリットだらけならJavaでも関数型プログラミングを導入すればいいじゃない！

だらだら長々と書いてきましたが、


### Javaに関数型プログラミングのエッセンスを導入する上での問題点
関数型プログラミングにおける関数の性質に「関数の引数に関数を渡せる」があったことからわかるように、関数型プログラミングにおける関数＝メソッドではないことに気をつけてください。

もう少し詳しく話すと、関数型プログラミングでは関数を第1級オブジェクトとして扱うため、関数の引数に関数を渡したりできますが、Javaにおける第１級オブジェクトはクラスなので、**メソッドの引数にはメソッドを渡すことはできない**わけです。

### 『関数型インターフェース』の登場
前述したようにJavaではメソッドの引数にメソッドを渡すことはできません。  
ですが、オブジェクト（インスタンス）なら引数として渡すことができるんです！  
というわけで、だったら**関数っぽいオブジェクト**作ったろ！となり『**関数型インターフェース**』が考案されたというわけです。

### 関数型インターフェースって何？
関数っぽいオブジェクトととして生み出された「関数型インターフェース」ですが、その定義は以下のようになります。

　**● 単一の抽象メソッドを持つインターフェース（SAM インターフェース）**  
　　※static メソッドやdefault メソッドは定義可  
　　※java.lang.Object クラスの public メソッドも定義可


関数型インターフェースの具体例

```java
@FunctionalInterface
public interface Example {
    public int calc(int x);      // 抽象メソッド（SAM）
    static void say() { }        // static メソッド
    default void hello() { }     // default メソッド
    public String toString();    // Object クラスのpublic メソッド
}
```

これが関数型インターフェースです！

</br>
</br>
</br>

**え？**

どこが関数っぽいの？と感じたのではないかと思います。  
まあまあ、それは使ってみればわかるってことで、実際にプログラムを書いてみましょう。
