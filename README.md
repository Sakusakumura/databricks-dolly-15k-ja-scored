---
license: cc-by-sa-3.0
task_categories:
- question-answering
- summarization
language:
- ja
size_categories:
- 10K<n<100K
---
For the English version, please click [here](README_en.md).

# 概要

`databricks-dolly-15k-ja-scored`は`kunishou/databricks-dolly-15k-ja`の派生であり、BERTScoreによって提供される翻訳品質スコアが追加されています。

このデータセットは、学術的・商業的問わず[クリエイティブ・コモンズ 表示 - 継承 3.0 非移植ライセンス](https://creativecommons.org/licenses/by-sa/3.0/deed.ja)の条件の下で何にでも使用することができます。

# 翻訳の品質スコア

`databricks-dolly-15k-ja`は、`databricks-dolly-15k`を機械翻訳したものです。  
`databricks-dolly-15k-ja`に含まれるデータを調べてみると、以下のような品質の悪いデータが存在することが分かりました。  

1. `input`と`output`が全く同じであるデータ
2. `output`が`instruction`にコピーされているデータ
3. 表記ゆれによって表現の一貫性が保たれていないデータ
4. 固有名詞などの翻訳に失敗しているデータ

なお、`databricks-dolly-15k`では、
- `1.`に当てはまるデータは削除されています。  
- `2.`に当てはまるデータはinstructionが削除されてカテゴリがopen-qaに変更されています。  
- 差別的な内容を含むデータ（１件）が削除されています。

そこで、これらのデータを日本語から英語に逆翻訳し、[BertScore](https://arxiv.org/abs/1904.09675)によって原文との類似度を調べました。  
以下がprecisionとrecallの調和平均であるf1 scoreのヒストグラムです。

<div align="center">
  <img src="https://media.githubusercontent.com/media/Sakusakumura/databricks-dolly-15k-ja-scored/e46aec8f9d2602e9e7c074674390263462534a9a/images/f1-score-full.png">
</div>   

これらのスコアでフィルタリングをすることで、低品質のデータを除外することができます。  
いくつか例を示します。  

<details>
<summary>`output`が`instruction`にコピーされているデータ</summary>

|index|f1 score|
|---|---|
|1151|0.599859416|

### candidate（`databricks-dolly-15k-ja`のデータを逆翻訳したもの）

|instruction|input|output|
|--|--|--|
|Linda Hargreaves is a British actress who has appeared on both stage and screen, making her television debut on the BBC soap opera "Doctors," and has since returned to the soap in three different roles, most recently as a regular on Constance Buchanan. She has also appeared in various television series, including "Containment" and "Call the Midwife."|About Linda Hargreaves|Linda Hargreaves is a British actress who has appeared on both stage and screen, making her television debut on the BBC soap opera "Doctors," and has since returned to the soap in three different roles, most recently as a regular on Constance Buchanan. She has also appeared in various television series, including "Containment" and "Call the Midwife."|

### reference（`databricks-dolly-15k`）

|instruction|input|output|
|--|--|--|
|(empty)|Who is Linda Hargreaves|Linda Hargreaves is a British actress who has appeared on both stage and screen. She made her television debut in the BBC soap opera Doctors and has since returned to the soap on three separate occasions in different roles, most recently in the recurring role of Constance Buchanan. She has also appeared in various television series including Containment and Call the Midwife.|

json
```json
{
    "index": "1151",
    "instruction": "リンダ・ハーグリーヴスとは",
    "input": "リンダ・ハーグリーヴスはイギリスの女優で、舞台とスクリーンの両方に出演している。BBCのソープオペラ「ドクターズ」でテレビデビューし、その後、3度にわたって異なる役柄でソープに復帰し、最近ではコンスタンス・ブキャナンのレギュラー役を演じている。また、「Containment」や「Call the Midwife」など、さまざまなテレビシリーズにも出演している。",
    "output": "リンダ・ハーグリーヴスはイギリスの女優で、舞台とスクリーンの両方に出演している。BBCのソープオペラ「ドクターズ」でテレビデビューし、その後、3度にわたって異なる役柄でソープに復帰し、最近ではコンスタンス・ブキャナンのレギュラー役を演じている。また、「Containment」や「Call the Midwife」など、さまざまなテレビシリーズにも出演している。",
    "category": "summarization"
}
```

</details>

<details>
<summary>固有名詞などの翻訳に失敗しているデータ</summary>

|index|f1 score|
|---|---|
|1157|0.580275774|

### candidate（`databricks-dolly-15k-ja`のデータを逆翻訳したもの）

|instruction|input|output|
|--|--|--|
|(empty)|Which type of fish? Mutsugoro and Afro|mullet (hairstyle)|

### reference（`databricks-dolly-15k`）

|instruction|input|output|
|--|--|--|
|(empty)|Which is a species of fish? Mullet or Afro|Mullet|

source:

```json
{
    "index": "1157",
    "instruction": "魚の種類はどれ？ムツゴロウとアフロ",
    "input": "",
    "output": "マレット",
    "category": "classification"
}
```

</details>

## 注意点

導き出されるスコアは、和英翻訳に使用するサービス、BertScoreで用いるモデルによって変動します。  
学習に使用しても問題ないデータでも、和英翻訳の精度によってスコアが下がることがあります。

<details>
<summary>例</summary>

|index|f1 score|
|---|---|
|565|0.799999774|

### candidate（`databricks-dolly-15k-ja`のデータを逆翻訳したもの）

|instruction|input|output|
|--|--|--|
|(empty)|At the 64th Grammy Awards, the winner of the Best Album of the Year award was|John Baptiste Wins Best Album of the Year at the 64th Annual Grammy Awards|

### reference（`databricks-dolly-15k`）

|instruction|input|output|
|--|--|--|
|(empty)|In 64th Annual Grammy Awards, best album of the year award was given to|Jon Batiste was awarded the best album of the year in 64th Annual Grammy Awards"	|

source:

```json
{
    "index": "1157",
    "instruction": "魚の種類はどれ？ムツゴロウとアフロ",
    "input": "",
    "output": "マレット",
    "category": "classification"
}
```

source:
```json
{
    "index": "565",
    "instruction": "第64回グラミー賞で、年間最優秀アルバム賞を受賞したのは",
    "input": "",
    "output": "ジョン・バティスト、第64回グラミー賞で年間最優秀アルバム賞を受賞",
    "category": "open_qa"
}
```

</details>
<br>

# 各フィールドの説明

追加した部分と、注意が必要な項目のみ説明します。

## 1. index

データの一意の識別子。  
元々は`databricks-dolly-15k`のどの行を翻訳したものかを示すフィールドでしたが、`databricks-dolly-15k`で削除された要素を反映していないため、実際の行数とは異なっています。

| フィールド名 | 説明 |
|---|---|
| index | データの一意の識別子 |

## 2. bertscore

BERTモデルによる評価スコア。   
recall、precision、f1の3つの指標を持ちます。

| フィールド名 | 説明 |
|---|---|
| recall | 再現率（正解テキストに含まれる情報が、生成されたテキストにどれだけ反映されているかを示す指標） |
| precision | 適合率（生成されたテキストの各部分が、正解テキストとどれだけ一致しているかを示す指標） |
| f1 | F1スコア（精度と再現率の調和平均。） |

## 3. translator

使用された翻訳サービスの情報。  
"en_ja" と "ja_en" の2つのフィールドがあり、それぞれ英語から日本語へ、日本語から英語への翻訳に使用されたサービスを表しています。

| フィールド名 | 説明 |
|---|---|
| en_ja | 英語から日本語への翻訳に使用されたサービス |
| ja_en | 日本語から英語への翻訳に使用されたサービス |

# 謝辞

このデータセット`databricks-dolly-15k-ja-scored`は、クニえもんさんが作成した`databricks-dolly-15k-ja`データセットを基にしています。  
クニえもんさんの貴重な作業とコミュニティへの貢献に深く感謝申し上げます。

# License/Credits

- databricks-dolly-15k-ja-scored  
Copyright (2023) Sakusakumura. This dataset is licensed under CC BY-SA 3.0.

- databricks-dolly-15k-ja  
Developed by kun1em0n. Available at https://github.com/kunishou/databricks-dolly-15k-ja. This dataset is licensed under CC BY-SA 3.0.

- databricks-dolly-15k  
Developed by Databricks, Inc. Available at https://huggingface.co/datasets/databricks/databricks-dolly-15k. This dataset is licensed under CC BY-SA 3.0.