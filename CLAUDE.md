# CLAUDE.md

## Development Server

```bash
npx http-server . -p 8080 --cors -c-1
```

## Project Structure

Single-page app with no build step.

- `index.html` — ゲーム本体（HTML + CSS + JS 一体）
- `questions.json` — 問題データ

## Architecture

**State**: `questions[]`, `order[]`（シャッフル済みインデックス）, `current`（現在の問題番号）, `score`, `answered`

**Flow**: `loadQuestions()` → `startQuiz()` → `showQuestion()` → `selectAnswer()` → 次へ or `showResult()`

**問題のシャッフル**: 毎回 `shuffle()` でランダム順。選択肢も同様にシャッフル。

## Questions Format (questions.json)

```json
{
  "sentence": "She has been living here ___ 2018.",
  "answer": "since",
  "choices": ["since", "for", "from", "during"],
  "explanation": "解説文（日本語）"
}
```

- `___` が空欄になる位置
- `choices` は必ず正解を含む4択
- `answer` は `choices` 内の文字列と完全一致させる
