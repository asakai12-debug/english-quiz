# CLAUDE.md

## Development Server

```bash
npx http-server . -p 8080 --cors -c-1
```

## Project Structure

```
english-quiz/
  index.html              # ゲーム本体（HTML + CSS + JS 一体）
  questions/
    beginner.json         # 英語 初級（20問）
    intermediate.json     # 英語 中級（20問）
    advanced.json         # 英語 上級（20問）
  questions_dx.json       # DX研修（12問）
  questions_fudosan.json  # 不動産鑑定士（12問）
```

## 画面フロー

```
topic-screen（カテゴリ選択）
  ├─ 英語クイズ → level-screen（難易度選択）→ quiz-screen
  ├─ DX研修     → quiz-screen（直接）
  └─ 不動産鑑定士 → quiz-screen（直接）

quiz-screen → result-screen
  └─ 間違いあり → quiz-screen（復習モード）→ review-complete-screen
```

## Architecture (index.html)

**定数**: `TOPIC_CONFIG` — トピックごとの名称・難易度の有無・ファイルパスを管理

**State**:
- `allQuestions[]` — 選択したカテゴリ・難易度の全問題
- `queue[]` — シャッフル済みの出題インデックス列
- `current` — queue の現在位置
- `score` — 正解数
- `wrongIndices[]` — 間違えた allQuestions のインデックス（復習モード用）
- `isReviewMode` — 復習モード中かどうか
- `currentTopic` — 'english' | 'dx' | 'fudosan'
- `currentLevel` — 'beginner' | 'intermediate' | 'advanced' | null

**Key functions**:
- `startQuiz(topic, level, reviewMode)` — 問題 fetch（初回のみ）しクイズ初期化
- `showQuestion()` — queue[current] の問題を描画
- `selectAnswer(choice, q)` — 正誤判定、wrongIndices への追記
- `showResult()` — 結果画面表示、wrongIndices > 0 なら復習ボタンを表示
- `showReviewComplete()` — 復習完了画面

## 問題ファイルの形式（全カテゴリ共通）

```json
{
  "sentence": "空欄を___含む文章。",
  "answer": "正解",
  "choices": ["正解", "選択肢2", "選択肢3", "選択肢4"],
  "explanation": "解説文（日本語）"
}
```

- `___` が空欄位置（1問に1箇所）
- `choices` は必ず `answer` を含む4択
- 新しいカテゴリを追加するには `TOPIC_CONFIG` に追記し、対応 JSON を作成するだけでよい
