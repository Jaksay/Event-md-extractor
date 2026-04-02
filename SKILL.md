---
name: "event-md-extractor"
description: "Convert unstructured event text into structured Markdown for a single event. Use when the user pastes Chinese or mixed Chinese-English activity/event copy and wants: (1) a normalized event Markdown file, (2) a matching raw archive file under raw/, (3) extraction of time, venue, guests, category, a short SEO-friendly one-line introduction, and agenda or guest/theme fallback into a flat H3 section structure, or (4) consistent event-file naming and formatting."
---

# Event Markdown Extractor

Convert one event text blob into two Markdown artifacts:

- a structured event file in the current working directory
- a raw source archive under `raw/`

Follow the workflow exactly.

## Output Structure

- `### 基本信息`: always present. Contains the event title, one-line introduction, and confirmed metadata such as organizer, time, format, city, venue/link, and category.
- `### 活动主题`: optional. Use for confirmed discussion themes, focus areas, or event highlights.
- `### 活动安排`: optional. Use for agenda content with explicit time anchors. This is the primary descriptive block when a detailed schedule is available.
- `### 分享嘉宾`: optional. Use for course-like, workshop-like, or single-speaker events with one clearly identified main speaker.
- `### 嘉宾阵容`: optional. Use for multi-person guest lineups when there is no detailed agenda, or when an independent lineup adds value.

Use a flat H3 structure. Do not wrap these sections inside `### 活动介绍`.

## Workflow

1. Parse the event text and extract:
   - activity title
   - one-line introduction
   - organizer
   - price
   - start time
   - end time
   - format
   - city
   - venue/link
   - category
   - agenda details when explicitly present
   - guest lineup when agenda details are not explicit
   - theme and supporting notes
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

1. `### 基本信息`
2. `### 活动主题`
3. `### 活动安排`
4. `### 分享嘉宾`
5. `### 嘉宾阵容`

Do not include `# 活动标题`, `## 活动介绍原文`, or any placeholder text.
Do not wrap topic, agenda, or guest sections inside `### 活动介绍`.

Use this template shape:

```md
### 基本信息

- 活动标题：
- 一句话介绍：
- 主办方：
- 价格：
- 开始时间：
- 结束时间：
- 形式：
- 城市：
- 地点/链接：
- 分类：

### 活动主题
- 主题或讨论方向
- 主题或讨论方向

### 活动安排
- 14:00-14:30 主题分享
  - **《议题名称》**
    - **姓名** | 身份说明
  - **《另一项议题》**
    - **姓名** | 身份说明

- 14:30-15:00
  - **《议题名称》**
    - **姓名** | 机构 title

### 分享嘉宾
- **姓名** | 身份说明
- 履历或实践经验
- 履历或实践经验

### 嘉宾阵容
- **姓名** | 机构 title
- **姓名** | 机构 title
```

## Section Rules

- `### 基本信息` should always appear.
- `### 活动主题`, `### 活动安排`, `### 分享嘉宾`, and `### 嘉宾阵容` should appear only when supported by the source.
- Inside `### 基本信息`, use the fixed field order from the template.
- Emit `一句话介绍` when a reliable one-line introduction can be generated from confirmed content.
- Other basic info fields should be emitted only when confirmed by the source.
- Emit optional `###` sections only when they are supported by the source.
- Never emit empty fields, empty sections, `没有信息`, `未知`, `未提供`, or editorial notes about missing content.
- Do not invent facts to keep the structure full.
- If a fragment cannot be classified with confidence, omit it rather than forcing it into the output.

## Degradation Rules

Choose the strongest supported `###` blocks for the descriptive part of the event:

1. `### 活动安排`
   - Use when the source contains explicit agenda evidence.
   - Agenda evidence includes time ranges, ordered schedule items, repeated `时间 + 内容` patterns, or clear process labels such as `签到`, `开场`, `主题分享`, `圆桌`, `Q&A`.
   - When `活动安排` is available, it is the primary block.
2. `### 分享嘉宾`
   - Use when the event is a course, workshop, training session, salon, or single-speaker sharing session and the source identifies one main speaker, lecturer, or share guest.
   - Trigger on labels such as `分享嘉宾`, `主讲人`, `讲师`, `授课老师`, `导师`.
   - Preserve the main speaker line and up to 2-3 high-value credential bullets when the source provides them.
3. `### 嘉宾阵容`
   - Use when there is no explicit detailed agenda, but there is a clear multi-person guest lineup.
   - Guest evidence requires identifiable names and at least one meaningful attribute such as organization or title.
4. `### 活动主题`
   - Use when there is no explicit agenda and no clear guest lineup, but the source states discussion topics, focus areas, or event highlights.

Preferred fallback chain:

- detailed agenda -> `活动安排`
- no detailed agenda but clear single speaker -> `分享嘉宾`
- no detailed agenda but clear multi-person guests -> `嘉宾阵容`
- no agenda and no guest lineup but clear themes -> `活动主题`

When multiple blocks are supported, keep them in this display order:

1. `### 活动主题`
2. `### 活动安排`
3. `### 分享嘉宾`
4. `### 嘉宾阵容`

Do not create `### 活动介绍` or `### 补充信息`.

## Agenda Formatting Rules

- In `### 活动安排`, time is the main anchor.
- Each agenda item should use a top-level bullet.
- The top-level bullet may be either:
  - `- 14:00-14:30 主题分享`
  - `- 14:00-14:30`
- If the source does not give a clear session label, do not invent one.
- Use child bullets only for real supporting details under that time slot.
- Child bullets should be direct prose, not prefixed with `时间：`, `环节：`, `议题：`, or `嘉宾：`.
- When one time slot contains multiple sub-talks, cases, or topics, use a second-level bullet for each sub-item.
- For each sub-item, use a third-level bullet for the matched guest or other tightly bound detail.
- Do not split one topic and its corresponding guest into separate sibling bullets.
- If a standalone fragment under a time slot cannot be identified confidently as a topic, speaker, or tightly bound factual detail, omit it.
- Bold the explicit share title or sub-topic text in second-level bullets.
- In third-level guest bullets, bold only the guest name and keep the identity text unbolded.
- Format guest bullets as `**姓名** | 身份说明`.
- Prefer this nesting order:
  1. top-level bullet: time slot
  2. second-level bullet: sub-topic or concrete item
  3. third-level bullet: matched guest, cooperation note, or tightly bound detail
- If a time slot has only one short confirmed item, keep it on one line without child bullets.
- If a time slot has a short item plus one guest, use a second-level bullet for the item and a third-level bullet for the guest.
- If a topic title is explicit, wrap it in `《》`.
- If multiple guests belong to the same sub-item, list one guest per third-level bullet.

Example:

```md
### 活动安排

- 13:30-14:00
  - 签到入场

- 14:00-14:10
  - 主办方致辞

- 14:10-14:40
  - **《AI应用创业的下一轮机会》**
    - **周其** | 未来产业研究院执行院长

- 15:30-16:20
  - **《AI创业项目如何建立可验证的商业闭环》**
    - **李昂** | 启明创投合伙人
    - **赵临** | 星河智能联合创始人
    - **王岚** | 产业创新基金投资总监

- 16:20-17:10 OpenClaw 多智能体实战应用分享
  - **《OpenClaw 实战落地：智能营销进入全员 Agent 时代》**
    - **李佳骏** | Noumena（物自体）CTO 兼联合创始人
  - **《基于开源 AI 智能体框架打造爆款应用》+ 冠军项目演示**
    - **金阳** | 清华 OpenClaw 黑客松冠军
```

## Basic Field Rules

- Add a space when Chinese text is adjacent to an independent English word, product name, acronym, or Latin-letter abbreviation.
- Keep standard mixed-language names readable, for example:
  - `OpenClaw 多智能体实战应用分享`
  - `Sage 平台`
  - `AI 硬件`
  - `GitHub 仓库`
  - `Demo 演示`
  - `Windows 和 Mac 本地部署`
- Do not force a space inside stable date or venue forms such as `2026年3月29日` or `1F讲坛`.
- When a time or number is followed by a short Chinese state word, add a space if it improves readability, for example `12:00 起`, `9:00 开始`.
- Do not add a space between a Chinese organization name and a Chinese title.
- Preserve original wording in the raw file.
- Use `YYYY-MM-DD HH:mm` for start and end time.
- Use `09:00-10:00` for agenda time ranges. Keep full date-time only if an agenda crosses days.
- `形式` must be one of `线上`, `线下`, `混合`.
- `一句话介绍` should appear immediately after `活动标题`.
- `价格` comes after `主办方`.
- Omit `价格` if the source does not provide it.
- Leave `TAG`, `报名方式`, and `附件` unprocessed in the table workflow; this skill does not need to emit them in the structured event file.

## One-line Introduction Rules

- `一句话介绍` is a generated one-line event introduction for listing display and SEO.
- Base `一句话介绍` only on confirmed event content from the source.
- Keep `一句话介绍` within 40 Chinese characters.
- Write `一句话介绍` in natural editorial wording, not abstract AI-style summarization.
- Reuse strong source keywords, product names, event names, and topic terms when they improve searchability.
- Prefer concrete wording over empty high-level phrasing.
- Vary sentence structure naturally according to the source; do not force a fixed summary template.
- Avoid repetitive openings such as `聚焦`、`围绕`、`共同探讨` as a default pattern.
- Do not mechanically copy the full title unless the title itself is already concise and suitable as a summary.
- Do not add promotional filler such as `重磅`, `精彩`, `不容错过`, `行业盛会`.
- Do not invent benefits, conclusions, or claims that are not supported by the source.
- Omit `一句话介绍` only when the source is too sparse to support a reliable one-line introduction.

## Guest Rules

- Record all confirmed guests.
- Use `### 分享嘉宾` for a single clearly identified speaker in course-like or workshop-like events.
- Use `### 嘉宾阵容` for multi-person lineups, forums, summits, roundtables, or mixed guest lists.
- In `分享嘉宾`, put the main speaker on the first bullet as `**姓名** | 身份说明`.
- In `分享嘉宾`, keep up to 2-3 concise bullets of verifiable credential or practice background when they materially support the speaker's credibility.
- Do not rewrite long biographies into paragraphs; keep them as short factual bullets.
- In `嘉宾阵容`, format each line as `**姓名** | 身份说明`.
- In `活动安排`, use the same guest style for third-level guest bullets.
- Keep the `|` separator even when the right side is a mixed organization-and-title phrase.
- If the source gives only a name and no reliable identity text, emit just `**姓名**`.
- Keep the same mixed-language spacing rules in guest lines.
- Do not force a split between organization and title; keep the right side as one clean identity phrase.
- In agenda sub-items, preserve topic-to-speaker pairing first and normalize formatting second.
- When `活动安排` already contains the guest details tied to time slots, do not duplicate the same guest list again under `分享嘉宾` or `嘉宾阵容` unless the source also provides an independent lineup that adds value.
- If content does not fit `活动主题`, `活动安排`, `分享嘉宾`, or `嘉宾阵容`, omit it rather than creating a new section.
- If a phrase such as `A x B` appears but its role cannot be confirmed, do not output it.

Examples:

- `**辛一** | ai798 Lab 联合创始人`
- `- 长期从事 AI 产品与出海业务，参与过多个 AI 项目的早期搭建`
- `- 在 AI 能力进入真实商业场景方面，具有丰富的一线实践经验`
- `**胡斌** | 渶策资本创始合伙人`
- `**Warren Li** | Google Cloud 创投生态业务负责人`
- `**安史** | 数字艺术家`

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
- Keep the structured output concise, formal, and high-density.
- Preserve meaningful agenda labels, topic titles, organization names, and guest names.
- Use bold sparingly and only for share titles and guest names.
- Prefer omission over filler.
