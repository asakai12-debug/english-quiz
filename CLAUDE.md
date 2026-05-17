# CLAUDE.md

## Development Server

```bash
npx http-server . -p 8080 --cors -c-1
```

## Project Structure

```
english-quiz/
  index.html        # ゲーム本体（HTML + CSS + JS 一体）
  questions/
    beginner.json     # 初級（20問）
    intermediate.json # 中級（20問）
    advanced.json     # 上級（20問）
```

## Architecture (index.html)

**画面遷移**: `start-screen` → `quiz-screen` → `result-screen` → （`review-complete-screen`）

**State**:
- `allQuestions[]` — 選択した難易度の全問題
- `queue[]` — シャッフル済みの出題インデックス列
- `current` — queue の現在位置
- `score` — 正解数
- `wrongIndices[]` — 間違えた allQuestions のインデックス（復習モード用）
- `isReviewMode` — 復習モード中かどうか
- `currentLevel` — 現在の難易度 ('beginner' | 'intermediate' | 'advanced')

**Key functions**:
- `startQuiz(level, reviewMode)` — 問題を fetch（初回のみ）し quiz を初期化
- `showQuestion()` — queue[current] の問題を描画
- `selectAnswer(choice, q)` — 正誤判定、wrongIndices への追記
- `showResult()` — 結果画面表示、wrongIndices > 0 なら復習ボタンを表示
- `showReviewComplete()` — 復習完了画面

## Questions Format

```json
{
  "sentence": "She has been living here ___ 2018.",
  "answer": "since",
  "choices": ["since", "for", "from", "during"],
  "explanation": "解説文（日本語）"
}
```

- `___` が空欄位置（1問に1箇所）
- `choices` は必ず `answer` を含む4択
- `answer` は `choices` 内の文字列と完全一致
- 問題を追加・差し替えるには各 JSON ファイルを編集するだけでよい
