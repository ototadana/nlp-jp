# nlp-jp

日本語処理用ソフトウェア詰め合わせセット

*   [Miniconda (Python 3)](https://hub.docker.com/r/continuumio/miniconda3/)
*   [Gensim](https://radimrehurek.com/gensim/)
*   [MeCab (和布蕪)](http://taku910.github.io/mecab/)
*   [CaboCha/南瓜](https://taku910.github.io/cabocha/)
*   [mecab-ipadic-NEologd](https://github.com/neologd/mecab-ipadic-neologd)
*   [日本語ワードネット （1.1版）© 2009-2011 NICT, 2012-2015 Francis Bond and 2016-2017 Francis Bond, Takayuki Kuribayashi](http://compling.hss.ntu.edu.sg/wnja/index.ja.html)

### 利用例
#### Bash
```bash
$ docker run --rm -it ototadana/nlp-jp bash
```

#### Python
```bash
$ docker run --rm -it ototadana/nlp-jp python
```

#### MeCab
```bash
$ echo "昨年話題になったUFO墜落事件、今はただの観光資源。街の名物" | docker run --rm -i ototadana/nlp-jp mecab
昨年    名詞,副詞可能,*,*,*,*,昨年,サクネン,サクネン
話題    名詞,一般,*,*,*,*,話題,ワダイ,ワダイ
に      助詞,格助詞,一般,*,*,*,に,ニ,ニ
なっ    動詞,自立,*,*,五段・ラ行,連用タ接続,なる,ナッ,ナッ
た      助動詞,*,*,*,特殊・タ,基本形,た,タ,タ
UFO     名詞,固有名詞,一般,*,*,*,UFO,ユーエフオー,ユーエフオー
墜落    名詞,サ変接続,*,*,*,*,墜落,ツイラク,ツイラク
事件    名詞,一般,*,*,*,*,事件,ジケン,ジケン
、      記号,読点,*,*,*,*,、,、,、
今      名詞,副詞可能,*,*,*,*,今,イマ,イマ
は      助詞,係助詞,*,*,*,*,は,ハ,ワ
ただ    名詞,一般,*,*,*,*,ただ,タダ,タダ
の      助詞,連体化,*,*,*,*,の,ノ,ノ
観光資源        名詞,固有名詞,一般,*,*,*,観光資源,カンコウシゲン,カンコーシゲン
。      記号,句点,*,*,*,*,。,。,。
街      名詞,一般,*,*,*,*,街,マチ,マチ
の      助詞,連体化,*,*,*,*,の,ノ,ノ
名物    名詞,一般,*,*,*,*,名物,メイブツ,メイブツ
EOS
```

#### CaboCha
```bash
$ echo "昨年話題になったUFO墜落事件、今はただの観光資源。街の名物" | docker run --rm -i ototadana/nlp-jp cabocha
         昨年---D
         話題に-D
           なった-D
      UFO墜落事件、-----D
                 今は---D
                 ただの-D
               観光資源。---D
                       街の-D
                         名物
```

#### 日本語WordNet

**example-wordnet.py:**
```python
import sqlite3

query = """
    select c.def from sense a, word b, synset_def c
      where b.lemma = ? and c.lang = 'jpn'
      and a.wordid = b.wordid and a.synset = c.synset
    """

with sqlite3.connect('/dictionary/wnjpn.db') as conn:
    print([row[0] for row in conn.cursor().execute(query, ['話題'])])
```

```bash
$ docker run --rm -i -v $PWD:/app ototadana/nlp-jp python /app/example-wordnet.py
['会話または議論の主題']
```


#### MeCab + 日本語WordNet
**example-mecab+wordnet.py:**
```python
import MeCab, sqlite3

def get_definition(word):
    query = """
        select c.def from sense a, word b, synset_def c
          where b.lemma = ? and c.lang = 'jpn'
          and a.wordid = b.wordid and a.synset = c.synset
        """
    with sqlite3.connect('/dictionary/wnjpn.db') as conn:
        return [row[0] for row in conn.cursor().execute(query, [word])]

tagger = MeCab.Tagger()
tagger.parse('')

node = tagger.parseToNode('昨年話題になったUFO墜落事件、今はただの観光資源。街の名物').next

while node:
    print('%s:' % node.surface)
    print('  - %s' % node.feature)
    for definition in get_definition(node.feature.split(',')[6]):
        print('  - %s' % definition)
    print()
    node = node.next
```

```bash
$ docker run --rm -i -v $PWD:/app ototadana/nlp-jp python /app/example-mecab+wordnet.py
昨年:
  - 名詞,副詞可能,*,*,*,*,昨年,サクネン,サクネン

話題:
  - 名詞,一般,*,*,*,*,話題,ワダイ,ワダイ
  - 会話または議論の主題

に:
  - 助詞,格助詞,一般,*,*,*,に,ニ,ニ

なっ:
  - 動詞,自立,*,*,五段・ラ行,連用タ接続,なる,ナッ,ナッ
  - 変化または発展受け入れる
  - 大きく朗々と鳴り響く
  - 病気にかかって、病気の犠牲になる
  - 公式に1年年を取る
  - 適当な
  - 作るかまたは表すこと：
  - 存在に至る
  - 数または数量の計算が合う
  - 発達して、成熟期に達する
  - 成熟する
  - 特定の方法で起こる
  - 状態、関係、条件、用途または地位に達するか、入る
  - できるか、変わるか、作られること、あるいはそれらが可能である
  - 徐々に状態に移って、特定の性質か属性を呈する
  - なる
  - 特定の状態または状態になる、あるいはとみなす
  - 変形または位置または行動の変化を受ける
  - 人の注意、関心、考え、または興味を、ある物に向ける、あるいはある物からそらす
  - 発展する

た:
  - 助動詞,*,*,*,特殊・タ,基本形,た,タ,タ

UFO:
  - 名詞,固有名詞,一般,*,*,*,UFO,ユーエフオー,ユーエフオー

墜落:
  - 名詞,サ変接続,*,*,*,*,墜落,ツイラク,ツイラク
  - 重力による急速な自由落下
  - 重力の影響を受けている時、歯止めなく落下する
  - 落ちる、あるいは激しく下降する

事件:
  - 名詞,一般,*,*,*,*,事件,ジケン,ジケン
  - 公の騒動
  - 調査を要する問題
  - 単一の顕著な出来事
  - 何かの発生

、:
  - 記号,読点,*,*,*,*,、,、,、

今:
  - 名詞,副詞可能,*,*,*,*,今,イマ,イマ
  - 現時点あるいは現代
  - 瞬間的な現在
  - 現在起こっている時間
  - スピーチの瞬間を含む時間の一続きの時間
  - ほんのすこしだけ前の時間
  - 史的現在では
  - 過去の一連の出来事のナレーション中のこの時点で
  - 今の時勢に、時節柄
  - つい今しがた
  - 現時点で
  - 現在
  - 現時点において

は:
  - 助詞,係助詞,*,*,*,*,は,ハ,ワ

ただ:
  - 名詞,一般,*,*,*,*,ただ,タダ,タダ
  - 含まれるまたは関係する他のものなしで
  - そして、多くは何もない

の:
  - 助詞,連体化,*,*,*,*,の,ノ,ノ

観光資源:
  - 名詞,固有名詞,一般,*,*,*,観光資源,カンコウシゲン,カンコーシゲン

。:
  - 記号,句点,*,*,*,*,。,。,。

街:
  - 名詞,一般,*,*,*,*,街,マチ,マチ
  - 機会を与える状況
  - 際立った特性を持つ町の一地域

の:
  - 助詞,連体化,*,*,*,*,の,ノ,ノ

名物:
  - 名詞,一般,*,*,*,*,名物,メイブツ,メイブツ
  - 大衆に提供される娯楽

:
  - BOS/EOS,*,*,*,*,*,*,*,*

```
