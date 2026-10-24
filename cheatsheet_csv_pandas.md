# Python初心者のためのCSV・pandas チートシート

> **想定環境：Google Colab**
> `csv`・`json`・`pandas` は Colab に最初から入っているので、インストールせずに `import` するだけで使えます。
> ファイルは、Colab 画面左の📁（ファイル）に置いたものを読み込み、書き出したものも📁に保存されます。

> **チートシートの使い方**
> Pythonプログラミングの自習や応用・実践等で参照する使い方を想定しています。

## Part 1　CSVファイル（`csv` モジュール）

### CSVとJSONの違い　[📷CSVの基本](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-4-slides/pg2-4-slide1.png)
| 形式 | 特徴 |
|---|---|
| **JSON** | キーのついたデータ。**入れ子**（辞書の中のリストなど）を表せる |
| **CSV** | カンマ区切りの**平らな表**。1マスに1つの値。Excelで開ける |

### JSONを読み込む　[📷JSON読み込みの図解](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-4-slides/pg2-4-slide3.png)
```python
import json, csv
with open("data.json", encoding="utf-8") as f:   # f＝ファイルオブジェクト
    items = json.load(f)                          # → 辞書のリスト
list(items[0].keys())                             # キー一覧（＝CSVの見出し）
```

### CSVに書き出す（csv.DictWriter）　[📷ファイルを開く・書き出しの準備](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-4-slides/pg2-4-slide2.png)　[📷見出しとデータを書く](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-4-slides/pg2-4-slide4.png)
```python
with open("out.csv", "w", encoding="utf-8-sig", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=items[0].keys())  # 見出し＝キー
    writer.writeheader()      # 1行目：見出し
    writer.writerows(items)   # 2行目以降：データ
```
- `encoding="utf-8-sig"`：Excelで日本語が**文字化けしない**
- `newline=""`：**余計な空行**が入らない
- 値にカンマがあっても、`csv` モジュールが `"..."` で**自動で囲んで**くれる

### リストを1つの文字列にする（join）　[📷文字列のメソッド（join・split）](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/C8.png)
```python
";".join(["a", "b", "c"])          # → "a;b;c"
for v in videos:
    v["tags"] = ";".join(v["tags"])  # tags を見やすくしてから書き出す
```
- `つなぎ文字.join(リスト)`。逆の操作は `"a,b,c".split(",")`

### Colabからダウンロードする
📁アイコン → ファイル名の **︙ →「ダウンロード」**（見つからないときは🔄で更新）

---

## Part 2　pandas（表＝DataFrame）

### 読み込む　[📷pandasとは](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-pandas-intro.png)　[📷CSVの読み込み](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-read-csv.png)　[📷JSONの読み込み](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-read-json.png)
```python
import pandas as pd
df    = pd.read_csv("videos.csv")
memos = pd.read_json("memos.json")    # CSVでもJSONでも同じDataFrameになる
```

### 中身を確認する　[📷タプルとは](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/P4.png)　[📷タプルとリストのちがい](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/P5.png)
| コード | 意味 |
|---|---|
| `df.head()` | 先頭5行 |
| `df.shape` | `(行数, 列数)` の**タプル**。`df.shape[0]` で行数 |
| `list(df.columns)` | 列名をリストで |
| `df["view_count"]` | 1列を取り出す（Series） |

### 基本の集計・操作
| やりたいこと | コード | 図 |
|---|---|---|
| 値ごとの件数 | `df["category"].value_counts()` | [📷件数を数える](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/P6.png) |
| 条件で絞り込み | `df[df["view_count"] >= 10000000]` | [📷絞り込み](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/P7.png) |
| 並べ替え（大きい順） | `df.sort_values("view_count", ascending=False).head(10)` | [📷ランキング](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/P8.png) |
| グループごとに集計 | `df.groupby("category")["view_count"].mean()` | [📷グループ集計](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-groupby.png) |
| 表の形で集計 | `df.pivot_table(index="category", values="view_count", aggfunc="mean")` | [📷ピボット集計](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-pivot.png) |

- groupby は「**仕分け → 各グループで計算 → まとめる**」。結果の行数は**グループの数**になる
- 列を絞るときは `[["title", "view_count"]]`（**角かっこ2重**）

### 集計方法（aggfunc／groupbyのメソッド）
| `aggfunc` | 意味 | | `aggfunc` | 意味 |
|---|---|---|---|---|
| `"count"` | 件数 | | `"max"` | 最大 |
| `"sum"` | 合計 | | `"min"` | 最小 |
| `"mean"` | 平均 | | `"median"` | 中央値 |

### 表示を見やすくする
```python
g.round().astype(int)      # 整数に（指数表示が消える）
g.map("{:,.0f}".format)    # カンマ区切り（※文字列になるので計算には使えない）
```

---

## Part 3　応用

| やりたいこと | コード | 図 |
|---|---|---|
| 2つの表を結合 | `pd.merge(df, memos[["video_id","memo"]], on="video_id", how="inner")` | [📷表の結合](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-ch-merge.png) |
| 新しい列を作る | `df2["engage"] = df2["like_count"] / df2["view_count"]` | [📷新しい列](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-ch-rank.png) |
| 年ごとに数える | `df3["year"] = df3["published"].str[:4]`<br>`df3.groupby("year").size()` | [📷年ごとの集計](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-ch-year.png) |
| 文字を含む行 | `df[df["title"].str.contains("MV", na=False)]` | [📷キーワード検索](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-ch-keyword.png) |
| 2次元クロス集計 | `df.pivot_table(index="category", columns="year", values="video_id", aggfunc="count", fill_value=0)` | [📷クロス集計](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-ch-pivot2d.png) |

- `.str` を付けると、列の**全部の行に文字列の操作**ができる　[📷.strのしくみ](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/C7.png)
- 元の表を残したいときは、先に `df2 = df.copy()` でコピーしておく
- `how="inner"`：**両方の表にある行だけ**を残す

### 欠損データ（NaN）　[📷欠損データ](https://raw.githubusercontent.com/kzw1717/I1008_pg2_items/main/pg2-5-slides/pg2-5-ch-nan.png)
| コード | 意味 |
|---|---|
| `df["like_count"].isna().sum()` | 欠損の件数 |
| `df.dropna(subset=["like_count"])` | 欠損のある行を取り除く |
| `df["like_count"].fillna(0)` | 欠損を0で埋める |

- `mean()` は NaN を自動で除いて計算する。NaN を含む計算の結果は NaN になる

---

> **ご利用にあたっての注意**
> - 本チートシートの内容は、作成時点の情報に基づいています。正確性・完全性・最新性を保証するものではありません。
> - Python や pandas、Google Colab のバージョンや仕様が変わると、記載どおりに動かない場合があります。
> - 本チートシートの利用によって生じたいかなる損害・トラブルについても、作成者は責任を負いかねます。
> - コードの実行やデータの取り扱いは、**ご自身の責任**のもとで行ってください。
