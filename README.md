#### 開発メモ
ワークフロー
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/127757659-6d253032-6f97-4433-801e-2773f3ef7e72.png">
### 1.選択中のテキストを変数に格納する
　選択中のテキストはLesson3同様HOTKEYの設定タブのSelection in macOSで取得します
<br>　その後、ArgandValsユーティリティを使って変数に格納します
<br>　ArgandValsユーティリティというのはワークフローで『＞』となっているオブジェクトです
<br>　開くと、{query}をselectionに格納する設定方法がわかりますので、ご参考まで
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/127757682-c03fea9d-b07e-4fce-a890-930a7a23f6a0.png">

### 2.ワークフローを放置する
　上記selectionの中身をチェックします
<br>　Conditionalユーティリティを利用しています
<br>　先ほどセットしたselectionは{var:selection}として記載します
<br>　Null値の場合（トップのアプリケーションで何も選択されていない場合）は
<br>　ワークフローを放置しています
<br>　放置でOkというのは、スクリプトとしてはなかなか衝撃的ですね
<br>　実際に稼働するときは、エラー音（？）が鳴りますが問題ありません
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/127757711-fb543c8b-a690-4a5f-a0cf-09490d724ed0.png">

### 3.自然数のチェック
　selectionに何か文字列がある場合、alfredバーが表示されます
<br>　ここでユーザーは切り取りたい文字数や行数を入力し、ScriptFilterに制御が渡ります
<br>　
<br>　ScriptFilterの先頭で入力された内容が自然数かどうか判定しています
<br>　その方法はif文の条件を正規表現としてチェックするものです
```
  if  [[ $1 =~ ^[1-9][0-9]* ]];then
```
<br>　ポイントは
<br>　  ・二重のブランケットで囲う『[[』と『]]』
<br>　  ・等号は『=~』
<br>　  ・正規表現は直書き。コーテーションなどで囲わない
<br>　
<br>　　『^[1-9][0-9]*』の意味は、『1から9で始まって、その後0から9が0回以上連続する』
<br>　　というものです。　
### 4.エラー回避のための置換
```  
  selection=`echo "$selection" | tr '"' '”' | tr '\' '＼' | tr "'" "’" `
```  
<br>　$selectionというのが、ArgandValsユーティリティで取得・設定したテキストです
<br>　trで3つの置換をしています。ちょっと見難いですが、
<br>　『'』『"』『\』をそのまま使うと、JSONフォーマットがうまく作れないので全角に
<br>　変更しています
### 5.n行目の抽出、n文字目の抽出
　n行目の抽出は、sedの-nオプションで実施しています
```
`echo "$selection" | sed -n $1p`
```
<br>　$1は入力した数字です
<br>　
<br>　n文字目の抽出は変数の部分参照を使っています
```
　${selection:$(($1-1)):20}
```
<br>　『$((数式))』は数式の計算結果です
<br>　$1は入力した数字なので、例えば5を入力した場合、5マイナス1で4となります
<br>　これは、変数の取り出したい位置を指定するものですが、先頭の1文字目を0として表すための
<br>　計算です
<br>　最後の20は取り出したい文字数になります
<br>　
<br>　つまり、下記の書式となります
```
　${変数:取り出したい位置:取り出したい文字数}
```
### 6.JSONフォーマット作成
　Alfredのリターンとして2つ使います
<br>　1つは○文字目、2つ目は○行目です
<br>　なお、ブランク行の場合は、2つ目を表示させないようにしています
<br>　ブランク行かどうかは、変数がNull値かどうかで判定しています
```  
  if [ -n "$line" ] ; then 
```
### 7.再びの放置
　JSONフォーマットでargを""として、後続につなげていません
<br>　いわば、ここでも放置です
<br>　Alfredバーで選択してもなにも起こりません
<br>　ただ、Alfredバーの入力訂正はできるので、○文字目、○行目を何度でも書き換えてOKです
<br>　
<br>　<img width="600" src="https://user-images.githubusercontent.com/40127279/127757763-fb5495b7-15e6-4008-bb51-c39624e367a4.png">

#### 背景
　alfredのデバッグツールでエラーメッセージが出力される際に、○○行目とか○○文字目という
<br>　ような記載があったりします
<br>　数字が大きくなると流石に手動で数えるのは面倒なのでツール化してみました
<br>　ちなみに○○行目というのであれば、テキストエディットの⌘Lで検索することができますが
<br>　○○文字目を見つける機能はなさそうでした
<br>　
#### 取扱説明
### 機能:
　選択中テキストのn文字目とn行目を表示する
### インストール:
　1.[Alfredworkflow](https://github.com/KitanoTamotsu/sourcecutter/releases/download/1.0/Sourcecutter.alfredworkflow)をダウンロード 
<br>　2.ファイルをダブルクリックしてワークフローに登録
### 使い方:
　・文字列を選択してHotkey『⌃c』を入力（Hotkeyはご自身での設定が必要です）
<br>　・表示されたAlfredバーに数字を入力
<br>
<br>
[トップページに戻る](https://kitanotamotsu.github.io/)

