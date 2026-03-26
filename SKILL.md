---
name: "event-md-extractor"
description: "Convert unstructured event text into structured Markdown for a single event. Use when the user pastes Chinese or mixed Chinese-English activity/event copy and wants: (1) a normalized event Markdown file, (2) a matching raw archive file under raw/, (3) extraction of time, venue, guests, category, and agenda into a fixed format, or (4) consistent event-file naming and formatting."
---

# Event Markdown Extractor

Convert one event text blob into two Markdown artifacts:

- a structured event file in the current working directory
- a raw source archive under `raw/`

Follow the workflow exactly.

## Workflow

1. Parse the event text and extract:
   - activity title
   - organizer
   - price
   - start time
   - end time
   - format
   - city
   - venue/link
   - category
   - agenda items
   - supporting notes
2. Save the user-provided source text into `raw/`.
3. Save the structured event Markdown into the current working directory.
4. Use the same base filename for both files, with `_raw` added only to the raw file.

## Filename Rules

- Use `YYYY-MM-DD_活动名称.md` for the structured file.
- Use `raw/YYYY-MM-DD_活动名称_raw.md` for the raw file.
- Derive `YYYY-MM-DD` from the start time.
- Replace spaces with `_`.
- Remove special symbols from the activity name in filenames.
- Keep only Chinese characters, English letters, digits, `_`, and the date hyphen `-`.

Example:

- `2026-03-25_HANGZHOU_AI_WEEK_2026_AI创业创投峰会.md`
- `raw/2026-03-25_HANGZHOU_AI_WEEK_2026_AI创业创投峰会_raw.md`

## Structured File Layout

Write the structured file with this section order:

1. `# 活动标题`
2. `## 基本信息`
3. `## 活动介绍（结构化整理）`

Do not include `## 活动介绍原文` in the structured file.

Use this template:

```md
# 活动标题

## 基本信息

- 活动标题：
- 主办方：
- 价格：
- 开始时间：
- 结束时间：
- 形式：
- 城市：
- 地点/链接：
- 分类：

## 活动介绍（结构化整理）

### 活动主题
- 

### 活动安排
- 时间：
  环节：
  议题：
  嘉宾：姓名｜机构｜title

- 时间：
  环节：
  议题：
  嘉宾：
  - 姓名｜机构｜title
  - 姓名｜机构｜title

### 补充信息
- 
```

## Field Rules

- Keep a space only when Chinese and English are adjacent.
- Do not add a space between a Chinese organization name and a Chinese title.
- Preserve original wording in the raw file.
- Use `YYYY-MM-DD HH:mm` for start and end time.
- Use `09:00-10:00` for agenda time ranges. Keep full date-time only if the agenda crosses days.
- `形式` must be one of `线上`, `线下`, `混合`.
- `价格` comes after `主办方`.
- `价格` format must match one of:
  - `免费`
  - `币种符号/币种代码 + 数字`
  - `币种符号/币种代码 + 数字-币种符号/币种代码 + 数字`
  - `未知`
- Leave `TAG`, `报名方式`, and `附件` unprocessed in the table workflow; this skill does not need to emit them in the structured event file.
- If a top-level field cannot be extracted from the source, write `没有信息`.
- Inside `活动安排`, omit missing subfields instead of writing `没有信息`.

## Guest Rules

- Record all guests.
- Format each guest as `姓名｜机构｜title`.
- If one segment is missing, omit that segment and omit the matching separator.
- If an agenda item has one guest, write that guest on the `嘉宾：` line.
- If an agenda item has multiple guests, write `嘉宾：` and then a bullet list.
- Combine organization and title without a space when both are Chinese.
- Keep a space only when Chinese and English touch.

Examples:

- `胡斌｜渶策资本｜创始合伙人`
- `Warren Li｜Google Cloud｜创投生态业务负责人`
- `安史｜数字艺术家`

## Category Rules

Choose exactly one category:

- `人工智能`
- `艺术与文化`
- `科技`
- `运动`
- `健康`
- `金融`
- `其他`

Prefer these decisions:

- If AI, large models, agents, model applications, or AIGC are the clear core topic, choose `人工智能`.
- If the topic is general technology and not clearly AI-first, choose `科技`.
- If both AI and finance appear but AI is still the core topic, choose `人工智能`.
- Choose `金融` only when investment, fundraising, capital, funds, securities, wealth management, financial markets, or roadshows are the main topic and AI is not the primary theme.
- Choose `艺术与文化` for exhibitions, art, performances, workshops, or cultural exchange.
- Choose `运动` for exercise, sports, training, and competitions.
- Choose `健康` for meditation, yoga, therapy, nutrition, psychology, or health management.
- Choose `其他` when nothing else clearly fits.

## Raw File Rules

- Save the source text under `raw/`.
- Keep the source text as provided except for trimming unrelated trailing content.
- Remove appended prompts, recommendations, comparison-table suggestions, and editor notes that are not part of the event body.

## Output Style

- Do not invent facts.
- Reorganize and summarize only from the provided source.
- Keep the structured output concise and readable.
- Preserve meaningful agenda labels, topic titles, organization names, and guest names.
