# magician-daisuke-jp

https://magician-daisuke.jp/ を **Cloudflare Pages（サーバー代ゼロ）** に移管したサイトです。
GitHub と連携し、**push するだけで自動公開**されます。

## この構成でできること

- サーバー代 0円（Cloudflare Pages 無料枠）
- `main` に push → 自動デプロイ（数十秒）
- 自然言語で編集（`to-cloudflare edit . "…"`）／VS Code で直接編集
- 変更履歴が Git に残る（誰がいつ何を変えたか）

## はじめての連携手順（1回だけ）

### 1. GitHub にこのリポジトリを上げる

```
git remote add origin https://github.com/<あなたのアカウント>/magician-daisuke-jp.git
git branch -M main
git push -u origin main
```

（`gh` CLI がある場合は `to-cloudflare handoff <job> --push <owner>/magician-daisuke-jp` で自動化できます）

### 2. Cloudflare Pages に接続する

1. Cloudflare ダッシュボード → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**
2. 上の GitHub リポジトリを選択
3. ビルド設定：
   - Framework preset: **None**
   - Build command: **（空欄）**
   - Build output directory: **`site`**
   - Root directory: **`/`**
4. **Save and Deploy** → `https://magician-daisuke-jp.pages.dev` が発行されます

### 3. 独自ドメインを向ける（本番切り替え）

DNS の準備ができたら、Pages プロジェクトの **Custom domains** で magician-daisuke.jp を追加します。
※ メール（MX）や現行サーバーの解約は、移行が完了してから。詳しくは元ジョブの `operator-guide.md` / `final-report.md` を参照。

## 日々の更新

編集して push するだけです：

```
to-cloudflare edit . "お知らせを1件追加して"   # または VS Code で site/ を編集
git add -A && git commit -m "お知らせ追加" && git push
```

編集ルールは [CLAUDE.md](./CLAUDE.md) を参照してください。
