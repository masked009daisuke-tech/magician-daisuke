# CLAUDE.md — このサイトを編集するときのルール

このリポジトリは **to-cloudflare** で移管した静的サイトです。
GitHub に push すると **Cloudflare Pages が自動でビルド・公開**します（ビルド不要の静的サイト）。

- 元サイト: https://magician-daisuke.jp/
- Pages プロジェクト名（目安）: `magician-daisuke-jp`
- 公開の仕組み: `main` ブランチへ push → Cloudflare Pages が `site/` をそのまま配信

## ディレクトリ構成

| パス | 役割 | 編集してよいか |
|------|------|----------------|
| `site/` | 公開されるサイト本体（HTML/CSS/画像） | ✅ ここを編集する |
| `site/assets/_external/<host>/` | 元サイトから取り込んだ外部画像・CSS・フォント | ⚠️ 参照は保つ（外部URLに戻さない） |
| `site/_redirects` `site/_headers` | Cloudflare Pages のリダイレクト/ヘッダ設定 | ⚠️ 意味を壊さない |
| `functions/` | Pages Functions（未使用） | 通常は触らない |
| `CLAUDE.md` `README.md` | ドキュメント（公開されない） | ✅ 自由 |

## 編集のルール（重要）

1. **公開対象は `site/` の中だけ**。それ以外（reports/ 等）は編集しても公開に影響しません。
2. **外部URLに戻さない**。画像やCSSは `site/assets/_external/…` に取り込み済みです（自立率 100%）。元の `https://…` に貼り替えると、元サービス解約時に表示が崩れます。
3. **`site/_redirects` / `site/_headers` は削除しない**。URL の互換性・セキュリティヘッダを担っています。
4. 文字コードは **UTF-8**、日本語をそのまま扱ってください。
5. **秘密情報（APIキー・トークン・パスワード）をコミットしない**。`.env` は `.gitignore` 済みです。

## 自然言語で編集する（推奨フロー）

このフォルダ（リポジトリのルート）で、to-cloudflare の AI 編集を使えます：

```
to-cloudflare edit . "トップページの電話番号を 03-1234-5678 に変えて"
```

`site/` の中だけを安全に書き換えます。まず内容を確認したいときは `--dry-run` を付けてください。
編集して問題なければ、公開はこれだけです：

```
git add -A
git commit -m "電話番号を更新"
git push
```

push すると Cloudflare Pages が数十秒で自動公開します。

## 上級者（Claude Code / VS Code）向け

- `site/` の HTML を直接編集して push すればOKです。
- ビルドステップはありません（静的サイト）。ローカル確認は `site/` を任意の静的サーバーで開くだけです。
