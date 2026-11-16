# Skills

個人用の Agent Skills 集です。

## Skills

### `kohe-emdash-blog`

作業メモや HTML サマリーなどから、`tech.anti-pattern.co.jp`（EmDash）向けの技術ブログ記事を執筆・サニタイズし、サムネイルや構成図の生成・EmDash MCP 経由での下書き保存まで行うスキルです。

## 必要環境

- GitHub CLI（`gh skill` 対応版）
- `gh auth login` による GitHub へのログイン

## インストール

```bash
# Antigravity / Claude Code / Codex など、利用するエージェントを指定してインストール
gh skill install 0xkohe/skills kohe-emdash-blog --agent antigravity-cli --scope user
```

リポジトリ内のすべての Skill をインストールする場合:

```bash
gh skill install 0xkohe/skills --all --agent antigravity-cli --scope user
```

インストール前に内容を確認する場合:

```bash
gh skill preview 0xkohe/skills kohe-emdash-blog
```

## 更新と確認

```bash
gh skill update kohe-emdash-blog
gh skill list
```
