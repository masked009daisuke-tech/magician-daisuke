# magician-daisuke-jp

https://magician-daisuke.jp/ を **Cloudflare Pages（サーバー代ゼロ）** に移管したサイトです。
GitHub と連携し、**push するだけで自動公開**されます。

## この構成でできること

- サーバー代 0円（Cloudflare Pages 無料枠）
- `main` に push → 自動デプロイ（数十秒）
- 自然言語で編集（`to-cloudflare edit . "…"`）／VS Code で直接編集
- 変更履歴が Git に残る（誰がいつ何を変えたか）

---

## セットアップ（最初の1回だけ）

### STEP A. GitHub に空のリポジトリを作る（Web）

1. https://github.com/new を開く
2. **Repository name** に `magician-daisuke-jp` を入力
3. **Private** を選択
4. ⚠️ **「Add a README file」「.gitignore」「license」は全てチェックしない**（初期化するとpushが弾かれます）
5. **Create repository** を押す → 表示されるURL（`https://github.com/<owner>/magician-daisuke-jp.git`）をメモ

> `gh`（GitHub CLI）を使うなら、上の作成は不要です：`winget install GitHub.cli` → `gh auth login` → このフォルダで `gh repo create <owner>/magician-daisuke-jp --source=. --private --push`

### STEP B. このフォルダを push する

このフォルダ（`handoff`）で以下を実行します：

```
git remote add origin https://github.com/<owner>/magician-daisuke-jp.git
git branch -M main
git push -u origin main
```

初回pushで認証画面（ブラウザ）が出たらGitHubにログインして許可すればOKです。
（パスワードを求められた場合はアカウントのパスワードではなく Personal Access Token を使います）

### STEP C. Cloudflare Pages に接続する（自動公開の要）

1. Cloudflare ダッシュボード → **Workers & Pages** → **Create application** → **Pages** タブ
2. **Connect to Git**（「Import an existing Git repository」と表示される場合も同じ）
3. GitHub を認可して、`magician-daisuke-jp` リポジトリを選択 → **Begin setup**
4. **ビルド設定を次の通り入力**（ここが最重要）：

   | 項目 | 値 |
   |------|-----|
   | Framework preset | **None** |
   | Build command | **`exit 0`** |
   | Build output directory | **`site`** |
   | Root directory (advanced) | **空欄のまま**（＝リポジトリのルート） |

   > ⚠️ Root directory は **空欄**にします。`site` にしないでください（配信フォルダの指定がずれます）。Build output directory は `/site` でも `site/` でもなく **`site`** です。

5. **Save and Deploy** → `https://magician-daisuke-jp.pages.dev` が発行されます

これで完了です。以後は **`main` に push するたびに自動で本番反映**されます。

---

## 日々の更新（セットアップ後）

編集して push するだけです：

```
to-cloudflare edit . "お知らせを1件追加して"   # または VS Code で site/ を直接編集
git add -A && git commit -m "お知らせ追加" && git push
```

（移管コンソールのUIなら STEP4「言葉で編集」→「変更を公開する（push）」だけで完結します）
編集ルールは [CLAUDE.md](./CLAUDE.md) を参照してください。

---

## 独自ドメインを向ける（本番切り替え）※ここは慎重に

テスト（`magician-daisuke-jp.pages.dev`）で問題なければ、独自ドメイン `magician-daisuke.jp` を向けます。
Pages プロジェクト → **Custom domains** → **Set up a domain** → ドメインを入力 → 案内に従う。

### 🛑 メール事故を防ぐ最重要ポイント

独自ドメインの **ルート（apex）** を向けるには、多くの場合ネームサーバーを Cloudflare に変更します。
**ネームサーバーを変えると、メール（MX）や各種DNSも Cloudflare 側の設定に置き換わります。**
先に必ず次を行ってください（エンジニアと一緒に）：

1. 現在のDNS提供元で、**今のDNSレコードを全部控える**（特に **MX**／**SPF・DKIM・DMARC（TXT）**）
2. Cloudflare 側にそれらを**同じ内容で作り直す**（メール系レコードは「DNS only／グレー雲」にする）
3. 切り替え後、**「そのドメイン宛にメールが届くか」「送ったメインが迷惑扱いされないか」を必ずテスト**
4. **旧サーバー・旧DNS・旧メールは、確認できるまで数週間は解約しない**（切り戻せる状態を保つ）

> www. のようなサブドメインだけでよければ、ネームサーバー変更なし（CNAME1本）で済み、メール事故のリスクを避けられます。詳しくは元ジョブの `operator-guide.md` / `final-report.md` も参照。

---

## 困ったとき

- 初回pushで弾かれる → GitHubリポジトリを「初期化なし（空）」で作り直す
- `.pages.dev` が 522 になる → Custom domains の「Set up a domain」を先に実施する（手動でCNAMEだけ足さない）
- 画像が表示されない → `site/assets/_external/` の取り込み漏れ。移管コンソールで再取り込みを検討
