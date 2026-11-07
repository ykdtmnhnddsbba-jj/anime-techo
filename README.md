# アニメ手帖 — セットアップ手順

`index.html` 1ファイルだけで動く静的サイトです。費用はかかりません。

## 1. 公開する（GitHub Pages・約5分）
1. GitHub で新しいリポジトリを作る（例: `anime-techo`、Public）
2. `index.html` をアップロード（Add file → Upload files）
3. Settings → Pages → Source を `Deploy from a branch`、Branch を `main` / `(root)` にして Save
4. 数分後 `https://<ユーザー名>.github.io/anime-techo/` で開ける

この時点で、クール一覧・検索・10段階評価・一言メモ・好み分析・AI用プロンプト出力が使えます（データはブラウザ内に保存）。

## 2. 配信情報を出す（TMDB・約3分）
1. https://www.themoviedb.org でアカウント作成 → 設定 → API → 「Create」(個人利用/Developer)
2. 「API キー」(v3) をコピー
3. サイトの「設定」タブ → TMDB APIキーに貼って保存
4. キーはブラウザ内にだけ保存されます（JSON・Driveには入りません）

## 3. Google Drive に自動保存する（約10分・無料）
1. https://console.cloud.google.com で新規プロジェクトを作成
2. 「APIとサービス」→「ライブラリ」→ **Google Drive API** を有効化
3. 「OAuth 同意画面」: 外部 / アプリ名は任意 / ユーザーサポートメールは自分 → スコープ追加は不要（ここでは未設定でOK）→ **テストユーザーに自分のGmailを追加**
4. 「認証情報」→「認証情報を作成」→ **OAuth クライアントID** → 種類「ウェブ アプリケーション」
   - 承認済みの JavaScript 生成元: `https://<ユーザー名>.github.io`（末尾のパスなし）
5. 発行された「クライアントID」をサイトの「設定」タブに貼り、「連携 / 今すぐ同期」
6. 初回だけ Google の確認画面が出ます（「未確認のアプリ」と表示されたら 詳細 → 移動）。以後、評価を保存するたびに Drive の `anime-db.json` に自動保存されます。

メモ
- アクセス権は `drive.file`（このアプリが作ったファイルのみ）。Drive内の他のファイルは見ません。
- テストモードではトークンの有効期限が短いため、ときどき画面右上の「☁ 要再接続」をクリックして再接続してください。
- 別端末でも同じクライアントIDを設定して同期すれば、同じデータが使えます。

## 4. 他のAIに乗り換える／使う
- 「好み分析」タブの **プロンプトを作る → コピー** を、好きなAIに貼り付け。評価データとクールの候補が入っています。
- 「設定」→「JSONを書き出す」で `anime-db.json` を取得し、そのままAIに渡してもOK（先頭の `_readme` に項目の説明が入っています）。

## データ形式（anime-db.json）
```json
{
  "_readme": "...説明...",
  "schema_version": 1,
  "updated_at": "ISO8601",
  "ratings": {
    "<AniList ID>": {
      "title": "表示名", "title_native": "日本語名", "cover": "画像URL",
      "season": "2026-FALL", "score": 8, "memo": "一言メモ",
      "genres": ["Action"], "tags": ["Swordplay"],
      "rated_at": "ISO8601", "updated_at": "ISO8601"
    }
  },
  "streaming_override": { "<AniList ID>": { "prime": true, "netflix": null, "updated_at": "ISO8601" } },
  "streaming_cache": { "<AniList ID>": { "prime": true, "netflix": false, "source": "tmdb", "tmdb_id": 123, "checked_at": "ISO8601" } }
}
```
- 削除した評価は `deleted: true` が付き、同期で別端末にも反映されます。
- 競合は `updated_at` が新しい方を採用して自動統合します。

## 注意
- 配信データは JustWatch（TMDB経由）です。アニメは照合ミスが起こりえるため、作品を開いて手動で上書きできます。
- 個人利用（非商用）の範囲で使ってください（TMDB規約）。
