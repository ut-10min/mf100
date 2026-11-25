# mf100 website

このリポジトリは、第100回五月祭の講演企画の公式サイトのソースコードを管理しています。

基本的にdata/ 以下のJSONファイルを編集して、HTML内の仮表示を上書きする形で運用します。HTMLやCSS、JavaScriptは基本的に編集不要です。
- data/config.json: 外部リンク、日付、会場、SNS、動画、地図
- data/talks.json: 講演内容
- data/schedule.json: タイムテーブル

.gihub内のworkflowにあるstatic-pages.ymlで、push,pullの時点で自動的に`npm run build` を実行してこれらの情報を反映させ、`dist/` に静的HTMLを生成するようにしています。

Github Pagesの設定で、`dist/` を公開するようにするため、
1.リポジトリトップのツールバー左上にあるSettingsを押す
2.左のバーからPagesにアクセス
3.Build and deploymentのSourceをGithub Actionsに変更
という手順を踏んでください。

アンケートを画面内にアクティベートする等の処理はindex.htmlを編集してください。

## data/config.json について

`config.json` には、毎回変更する情報を置いています。

- 開催日時
- 会場名・住所
- 外部リンク
- SNSリンク
- 運営統括・運営メンバー

開催回を切り替える際は、`festivalName`（例：`第77回駒場祭`／`第100回五月祭`）と、`venueName`・`venueAddress`、地図リンクを更新してください。トップページの投票案内・当日の案内にある祭名は `festivalShortName`（`五月祭`／`駒場祭`、現在は `五月祭`）で切り替えます。会場の駒場／本郷の表記は `venueName` から反映されます。ページタイトル・説明文・上部ラベルは、それぞれ `pageTitles`・`metaDescriptions`・`labels.top` で設定します。


## HTML内の仮表示とconfigの関係

`data-config` / `data-link` / `data-sns` が付いた場所は、ページ読み込み後に `data/config.json` の値で上書きされます。ただこの処理を毎回クライアントで行うと、ページの表示が遅くなったり、JavaScriptが動かない環境で情報が表示されない可能性があります。そこでGithub Pagesのビルドプロセスで、これらのJSONの内容をHTMLに事前反映する形で静的ビルドできるようにしています。

HTML内には実データを重複して書かず、以下のような仮表示だけを置いています。

- 日時: `XXXX年X月XX日 XX:XX–XX:XX`
- 会場: `会場未定`
- URL: `href="#"`

JavaScriptが正常に動くと、configの値が優先されます。JavaScriptが動かない場合は、HTML内の仮表示がそのまま表示されます。

## 準備中の表示と公開への切り替え

現在は `data/talks.json` と `data/schedule.json` をそれぞれ空配列 `[]` にしています。
既存の表示処理により、講演一覧（`talks.html`）とタイムテーブル（`timetable.html`）に
「現在整備中です。公開までしばらくお待ちください。」と表示されます。
このリポジトリには `schedule.html` はありません。
HTML本体を編集せず、JSONの更新と静的ビルドで表示を切り替えられます。

公開時は以下のテンプレートをコピーし、仮の内容を確定した情報に置き換えてください。
両ファイルとも、最上位は配列です。複数件を入れるときは配列内のオブジェクトをカンマで区切ります。
JSONにはコメントや末尾の余分なカンマを書けないため、入力例や復旧手順はこのREADMEに記載しています。

### data/talks.json のテンプレート

```json
[
  {
    "id": "speaker-example",
    "title": "講演タイトル（入力例）",
    "speaker": "講演者名",
    "speakerKana": "コウエンシャメイ",
    "englishName": "Speaker Name",
    "affiliation": "所属研究科",
    "department": "専攻名",
    "field": "研究分野",
    "description": "講演概要を入力してください。"
  }
]
```

### data/schedule.json のテンプレート

以下の日付・時刻は形式を示す仮の値で、開催日程ではありません。
`date` は `YYYY-MM-DD`、`start`・`end` は `HH:MM` 形式で入力します。
`dateLabel` は画面に表示する日付です。

```json
[
  {
    "date": "2027-05-01",
    "dateLabel": "5月1日（土）【入力例】",
    "start": "10:00",
    "end": "10:20",
    "type": "talk",
    "talkId": "speaker-example",
    "speaker": "講演者名"
  },
  {
    "date": "2027-05-01",
    "dateLabel": "5月1日（土）【入力例】",
    "start": "10:20",
    "end": "10:40",
    "type": "break",
    "title": "座談会・休憩などのタイトル",
    "speakers": ["講演者名"],
    "speakerIds": ["speaker-example"],
    "description": "内容紹介を入力してください。"
  }
]
```

講演枠の `talkId` と `speaker` は、`talks.json` の `id` と `speaker` に対応させてください。
`type: "break"` の枠は講演情報との紐付けなしで表示でき、休憩なら `speakers`・`speakerIds`・`description` は省略できます。
公開前に両方のJSONを更新し、`npm run build` 後に `dist/talks.html` と `dist/timetable.html` を確認してください。
再び準備中に戻すときは、両ファイルを `[]` にして再ビルドします。

### ウェビナーのリンク

現在の `data/config.json` の `links.zoomRegister` は `404.html` です。
トップページの2か所のオンライン事前登録リンクは、用意済みの準備中ページを開きます。
リンクの新しいタブで開く設定（`target="_blank"`、`rel="noopener"`）はそのままです。

以前と同じ登録先に戻すには、`links.zoomRegister` を次の値に変更して再ビルドします。

```json
"zoomRegister": "https://ut-10min.github.io/registration"
```

<!-- ウェビナー公開時: data/config.json の links.zoomRegister を上記の元のURL、
または第100回五月祭用に確定した登録URLへ変更し、npm run build で反映する。 -->

### 会場のGoogleマップ

`data/config.json` の `links.googleMap`（ボタンのリンク）と `links.mapEmbed`（埋め込み地図）は、
mf99の設定を参照して本郷キャンパスの工学部3号館を示す値に揃えています。

## Node.js による静的ビルド

通常の開発では、これまで通りルート直下の `index.html`, `talks.html`, `timetable.html` をそのまま使えます。ローカルでもNode.js を使える環境では、以下で `dist/` に静的HTMLを生成できます。

```bash
npm run build
```

静的ビルドでは、`data/config.json`, `data/talks.json`, `data/schedule.json` の内容をHTMLへ事前反映します。これで基本的なパーツは静的に配信となり、YouTube と Google Map の iframe 生成だけは `dist/js/embed-runtime.js` でクライアント側で行う形になります。

## 自動Build周りの管理
Node.js のビルドスクリプトは、`scripts/static-build.js` と'.github/workflows/static-pages.yml'に書いています。Node.jsのバージョン指定等を行っているため、古くなっていたらバージョン等を更新してください。

## PDF・Excel資料からのJSON変換

3資料（シフトPDF・参加登録Excel・講演題目Excel）を選んで自動変換するPythonツールを用意しています。起動方法・対応形式・IDの付け方・バックアップについては [変換ツールの説明](scripts/import_data/README.md) を参照してください。
