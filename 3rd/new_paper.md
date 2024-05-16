# ResNeXt and Res2Net Structures for Speaker Verification
SVにおける，ResNeXtとRes2Netの有用性

Tianyan Zhou, Yong Zhao, Jian Wu

Microsoft Corporation, USA

2021
## 背景
テキスト非依存の話者照合システムにおける話者埋め込みの抽出には，ResNetベースの手法が広く用いられてきた．
> （2021(2020 11月)時点．2020 5月にECAPAが出ているけど，時間的な関係で触れられていない？）

ResNetは
- CNNへの残差接続を用いる
- 残差ブロックを正規化する

ことで高い照合制度を得る．

が，

入力特徴量空間が複雑になると，ResNetネットワークの深さと幅が増えても，潜在的なパフォーマンスを発揮できない．

→ResNetの拡張である，ResNeXtとRes2Netを提案
## 主旨
ResNetの拡張である，ResNeXtとRes2Netの話者照合タスクへ適用し，有用性を示す．
> ResNeXtとRes2Netはもともと画像認識領域で提案されていた構造

- 検証
  - 3つのタスクで検証を行う
  - VoxCeleb1テストセット
  - MS内製データセット2つ
### ResNetについて
- width, depthの2つを学習によって調整し，モデルの表現力を上げる
- 入力特徴量空間が複雑になると，学習時とテスト時の条件に多くのミスマッチが生じ，depthとwidthを増やすだけでは，過学習しがちな傾向がある．
### ResNeXtとRes2Netについて
- ResNetを改良したもの
- モデルの表現力を上げるために，depth, widthに加え，cardinaryとscaleという2つの次元を導入する
- Res2Net
  - scaleの次元を上げることで，様々な粒度で特徴を表現でき，短い発話での照合によく働く（ことがわかった）
- ResNeXt

> あまりちゃんと読んでおらず，詳しくは分かっていません

## 実験結果

### データセットについて
VoxCeleb+MS内製の2テストセット．
#### VoxCeleb
- テキスト非依存，いろんな環境下の会話を録音
- 多くの音響環境，短時間発話を含む
- 検証に用いたモデルは，VoxCeleb2-devのデータを用いて学習
  - babble, music, noise, reverbの4手法で学習データを拡張
- 評価は以下の3つのデータセットにて行う
  - VoxCeleb1 test
  - VoxCeleb1-E (extended)
  - VoxCeleb1-H (hard, 同じ性別や同じ国籍の話者を含む)

#### MS-SV test
- MS内製のデータセット
- 近接マイクで短文の発話を録音したもの
- 会議室での会話をマイクで録音し，処理したもの
- 約150人
- 2-15secの発話長 (平均約4sec)

#### Cortana test set
- テキスト依存型のSVに向けたデータセット
- `Cortana` or `Hey Cortana`から始まる発話
- いろいろなノイズ環境下，いろいろな距離で録音
- 登録話者の音声は，きれいな環境下で，マイクから1mの距離
- CTCベースのキーワード検出を用いて，発話を切り出し
### 実験結果
#### VoxCeleb1
1. 通常の検証
    - see: `table2`
    - ResNeXtとRes2NeXtはベースライン手法よりEERが低い(性能が高い)
    - モデルを複雑にすると…
      - ベースライン手法（deeper, wider）はそこまで性能が上がらない
      - 一方，Res2Net-26w6sはスコアが伸び，Res2Net-26w8sはさらに向上
        - Res2Netを用いて，cardinalityとscaleを導入すると効果的である．
        - 特にscaleをいじると性能が向上すると思われる
2. 発話を短く切ったデータで検証
   - VoxCeleb内の発話は平均して8secの発話長．
   - 短時間の発話に対する性能を検証するため，2, 3, 4secに切って検証．
   - see: `table3`
   - 発話長が短いと性能が低下する
   - しかし，提案手法の方がベースラインより性能が良い（特にRes2Net）

#### MS-SV
- モデルの汎化性能を見るため．
- 発話長は平均して20secなので，1-2s, 2-4s, >4sに発話を区切って検証する．
- see: `table4`
- 提案手法はベースラインより性能が良い

#### Cortana test set
- VoxCelebでトレーニングしたモデルを用いる．
- 学習データとテストデータに条件の不一致があるが，登録データとテストデータの発話内容が一致している．
- see: `table5`
- 全体として，ノイズがあるほど，マイクが遠いほど，精度は下がる傾向にある．
- totalでは，Res2NetはResNetより4.5%精度が向上．
- Res2Netは，`quiet`, `TV`のどちらの環境でも精度が向上
  - Res2Netはベースラインより精度が良い．
- ResNeXtはベースラインより精度が低い
  - Res2Netほどの頑健性はないと思われる

## 結論
- ResNextとRes2Netは話者照合タスクにおいて，有用性を示した．
  - VoxCelebテストセットによるデモを行い，どちらの手法もベースラインを上回った．
  - 特にRes2Netが優れていた．
    - 短い発話や，学習データと照合時データのミスマッチに対しても頑健