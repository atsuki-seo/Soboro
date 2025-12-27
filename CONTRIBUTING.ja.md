> 🇺🇸 [English version](CONTRIBUTING.md)

# Contributing to Soboro

Soboro プロジェクトへの貢献に興味を持っていただきありがとうございます！

## はじめに

Soboro は Swift でユーザーランドを構築する実験的な OS プロジェクトです。
どなたでも貢献を歓迎します。

## 貢献の方法

### Issue の報告

- バグ報告や機能提案は GitHub Issues をご利用ください
- 日本語・英語どちらでも OK です
- テンプレートに沿って記載いただけると助かります

### Pull Request

1. リポジトリを Fork
2. ブランチを作成（`feature/{component}/{description}` または `fix/{component}/{description}`）
3. 変更をコミット
4. Pull Request を作成

### 開発の流れ

```bash
# Fork したリポジトリをクローン
git clone https://github.com/YOUR_NAME/soboro.git
cd soboro

# 開発ブランチを作成
git checkout -b feature/init/your-feature

# 開発...

# テスト
swift build
swift test

# コミット（英語、conventional commits 形式）
git commit -m "feat(init): add your feature"

# Push して PR 作成
git push origin feature/init/your-feature
```

## コーディング規約

- **コードスタイル**: `README.md` の命名規則に従ってください
- **Claude Code 利用時**: `CLAUDE.md` を参照してください
- **テスト**: 新機能にはテストを追加してください
- **ドキュメント**: 公開 API には DocC コメントを付けてください

## コミットメッセージ

```
{type}({scope}): {description}
```

- **type**: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`
- **scope**: コンポーネント名（`init`, `compositor`, `foundation` など）
- **description**: 英語で簡潔に

例: `feat(networkd): add DHCP client support`

## ドキュメントの多言語対応

設計ドキュメントや ADR を作成する場合は、日本語版と英語版の両方を用意してください。

```
docs/decisions/
├── ja/ADR-0001-your-decision.md  # 日本語版
└── en/ADR-0001-your-decision.md  # 英語版
```

## コミュニケーション

- **Issues / PR**: 日本語・英語どちらでも OK
- **コミットメッセージ**: 英語
- **コード内コメント**: 英語

## 行動規範

- 互いに敬意を持って接してください
- 建設的なフィードバックを心がけてください
- 初心者の質問も歓迎します

## ライセンス

貢献いただいたコードは MIT License のもとで公開されます。

---

質問があれば Issue でお気軽にどうぞ！
