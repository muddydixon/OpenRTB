OpenRTB Protocol Specification
------------------------------------------------------------

## 目次

1. 概要とゴール
   1. ミッション
2. SSP と DSP 固有のメリット
   1. SSP のメリット
   2. DSP のメリット
3. パブリッシャと広告主のニーズ
   1. パブリッシャ側 (SSP) のニーズ
   2. 広告主側 (DSP) のニーズ
4. 高次実装概要: オフラインバッチとリアルタイム
   1. インクリメントアプローチ
   2. オフラインバッチの同期
   3. リアルタイムブロッキングプロトコル
5. オフライン / バッチ 同期の仕様
   1. バッチ同期のメッセージバリデーション
   2. 広告主中心のオフライン同期サービス
      1. 広告主中心のリクエストパラメタ
      2. 広告主中心のレスポンスパラメタ
6. リアルタイムブロックリストの仕様
   1. (SSP から DSP への) _Universal Creative Attributes_ を利用したクリエイティブのリアルタイムブロッキング
   2. (DSP から SSP への) リアルタイムクリエイティブ ID
   3. (SSP から DSP への) リアルタイムパブリッシャ ID とサイト ID
   4. コンテントカテゴリのリアルタイムブロッキング
      1. ビッドリクエスト中の UGC Code のリアルタイムレポーティング
7. 作業中
   1. (オプション) パブリッシャ中心のオフライン同期サービス
   2. (SSP から DSP への) リアルタイムテクノロジーフラグ
   3. 優先 / 重点座席の統一的な扱い


## 1. 概要とゴール

### 1.1. ミッション

OpenRTB プロジェクトのミッションは、広告の売手とパブリッシャ目録の買手とのコミュニケーションに対して、オープンな業界標準を提供することにより、リアルタイムビッディング (RTB) マーケットプレイスの急速な拡大に拍車をかけることです。

特に、最初のゴールとして、エコシステムの中のすべてのプレイヤーを含めて、広告主からの要求とパブリッシャの要求を同期させるコストを減じ、一貫性を高めることにあります。

「ブロックリスト」と呼ばれる、広告主とパブリッシャの要求は、広告主によって DSP (demand side platform) に提供され、パブリッシャによって SSP (sell side platform) に提供されます。
これらのブロックリストは異なる方法で異なるプロバイダに提供され、キャンペーンの事前に DSP と SSP の打ち合わせで手渡しで交換されなければなりません。
RTB 業界における労働集約型のボトルネックを解消することで、 DSP および SSPの両方に対して操作コストを減じることで、この業界の成長を活性化できると考えています。

|Demand Side Platfomrs (DSPs) | Sell Side Platforms (SSPs) |
|DataXu| AdMeld|
|MediaMath| Pubmatic|
|Turn| The Rubicon Project|

## 2. SSP と DSP 固有のメリット

OpenRTB ワーキンググループの最初のゴールは、プロトコルを標準化することで、 RTB エコシステムにおける、労働集約型でエラーが発生しやすいプロセスを除外することです。
例えば、パブリッシャと広告主のブロックリストの管理などが、それにあたります。
OpenRTBの目標は、このような障害を最小化することにあり、これによって DSP と SSP のしつらえた RTB エコシステムがより速く成長し、広告主とパブリッシャにより高い品質のサービスを保証することです。

ワーキンググループでは特に、特定の広告主やある種のクリエイティブをブロックしなければならないパブリッシャからの需要と、特定のパブリッシャまたは RTB 環境におけるあるタイプをブロックしたいという広告主からの需要に着目しています。
本提案は、標準的なリアルタイムの入札リクエストのフィーチャーセットに対してよりよい定義として、オフライン (batch) プロトコルを含んでいまうす。

### 2.1. SSP のメリット

1. __高いCPM__:
SSP がブロックリストをオフライン、バッチ処理で DSP パートナに提供した場合、 DSP が入札機能に勝つことが出来ないリストに入札を行わないためのフィルターを取り込むことができるようになります。
この結果として、インプレッションに対して有効な入札の割合を高くすることが出来ます。
統計的に見て、パブリッシャにとってインプレッションに対して処理しなければならない入札を減らし、高い CPM を実現することができるようになります。
2. __リスクの削減__:
よりよく、より統一的なパブリッシャ制約の執行。
もし、 DSP と SSP が広告のブロックに対して統一的な用語を使うことができるのであれば、 DSP にとって入札時のパブリッシャのルールに厳密に従うことがようになります。
ヒューマンエラーによりパブリッシャで潜在的に起こるリスクを減じることができるでしょう。
3. DSP の統合を用意にすることによる__利益の最大化__:
ブロックリストを SSP のフォーマットから DSP のフォーマットに変換する際の手作業を減らすことにより、新しいキャンペーンの実施までの時間を減じることが出来ます。
この結果として、 DSP をより容易に統合し、より競争の激しい入札を行い、 SSP にとってより高い収益を実現することになるでしょう。

### 2.2. SSP のメリット

1. より効果的な広告配信を通した__低いコスト__:
入札前フィルター (pre-bid-filter) を利用することで、 DSP はパブリッシャやクリエイティブ制限のせいで勝てないであろう入札を止めることができます。
これにより、 DSP はインプレッションに対して有効な入札の割合を増やすことが出来、結果として DSP のコストを下げることに成功するでしょう。
2. よりよい広告主・パブリッシャ制約の執行を通した__リスクの軽減__:
もし DSP と SSP が広告ブロッキングにおいて統一的な用語を持っているならば、 DSP にとっては入札の際に 広告主・パブリッシャの規則に厳格に従うことができるようになります。
この結果として、広告主と SSP によるヒューマンエラーが原因となる潜在的な問題を減じることができるでしょう。
3. SSP へのより容易な統合を通した__リーチの拡大__:
新しいキャンペーンやクリエイティブの手続きが用意になることで、 SSP のフォーマットから DSP のフォーマットにブロックリストを変換するために必要な些細な手作業を減じることが出来ます。
OpenRTB を利用することで、新しい SSP の対応コストを減じ、その結果、 DSP はより広いリーチを獲得することができるでしょう。


## 3. パブリッシャと広告主のニーズ
### 3.1. パブリッシャ側 (SSP) のニーズ
パブリッシャはサイト上のコンテンツのクオリティを管理したがっています。
管理したい対象の中には、パブリッシャのページ上の広告として表示されるブランドや産業の種別のコントロールも含んでいます。
クリエイティブをブロックする理由として、チャネルの競合やパブリッシャのミッションとのコンテンツの競合などが含まれるでしょう。

__パブリッシャの品質管理のニーズ__

* P1. __ランディングページ URL による広告ブロック__: 例) "www.carbrand.com" のブロック
* P2. __クリエイティブの属性によるクリエイティブのブロック__: 例) 「騒がしく・チカチカする」音声や動画を含む広告のブロック
* P3. __産業による広告のブロック__: 例) "自動車のブロック、クレジットカードのブロック"

パブリッシャは、産業にとって広告主のランディングページをあわせる内部の方法によって、産業をブロックすることがあります。
ケース P3 を扱う方法は、売り手側提供のおよび売り手側提供のパブリッシャのURLを文脈付け、フィルターすることで実現されるものであるため、 OpenRTB には含まれていません。

### 3.2. 広告主側 (DSP) のニーズ
広告主は彼らのブランドの安全かどうか、特に広告のメッセージに適していないコンテンツの近くに広告が置かれるのではないかをとても気にしています。

__広告主の品質管理ニーズ__

* A1.

## 4. 高次実装概要: オフラインバッチとリアルタイム
### 4.1. インクリメントアプローチ
### 4.2. オフラインバッチの同期
### 4.3. リアルタイムブロッキングプロトコル
## 5. オフライン / バッチ 同期の仕様
### 5.1. バッチ同期のメッセージバリデーション
### 5.2. 広告主中心のオフライン同期サービス
#### 5.2.1. 広告主中心のリクエストパラメタ
#### 5.2.2. 広告主中心のレスポンスパラメタ
## 6. リアルタイムブロックリストの仕様
### 6.1. (SSP から DSP への) _Universal Creative Attributes_ を利用したクリエイティブのリアルタイムブロッキング
### 6.2. (DSP から SSP への) リアルタイムクリエイティブ ID
### 6.3. (SSP から DSP への) リアルタイムパブリッシャ ID とサイト ID
### 6.4. コンテントカテゴリのリアルタイムブロッキング
#### 6.4.1. ビッドリクエスト中の UGC Code のリアルタイムレポーティング
7. 作業中
   1. (オプション) パブリッシャ中心のオフライン同期サービス
   2. (SSP から DSP への) リアルタイムテクノロジーフラグ
   3. 優先 / 重点座席の統一的な扱い
