# Good Fetch

Good Fetch is a static daily news briefing. The page design lives in `good-fetch-web-v4/index.html`. The news lives in JSON. To publish a new edition, change only the files in `good-fetch-web-v4/news/`. Do not edit the HTML.

The site is deployed on Vercel with the project root set to `good-fetch-web-v4`. There is no build step.

## Publish a new edition

Use the edition date as `YYYY-MM-DD` (for example `2026-10-19`).

1. Create `good-fetch-web-v4/news/YYYY-MM-DD.json` using the schema below.
2. Copy that same file onto `good-fetch-web-v4/news/latest.json` (overwrite it). The homepage always reads `latest.json`.
3. Open `good-fetch-web-v4/news/index.json` and insert the new date at the **start** of the `editions` array:

   ```json
   { "date": "2026-10-19", "label": "Oct 19, 2026" }
   ```

   `label` is the archive button text. Write it as `Mon D, YYYY` with no leading zero on the day (`Sep 6, 2026`, `Oct 19, 2026`). If you omit `label`, the page builds that text from `date`.
4. Do not edit `index.html`, the stylesheet, or the script.
5. Optional picture: add an image file next to `index.html` (for example `good-fetch-web-v4/story.png`) and set the story's `image` to `"story.png"`. Pictures already on the site: `openai.png`, `iphone.png`, `pills.png`, `boat.png`.

The dated file and `latest.json` must contain the same edition. Archive buttons open `news/YYYY-MM-DD.json`. The homepage does not read the dated file until someone clicks that date.

Leave the `placeholder: true` dates already in `index.json` (Sep 6–9, 2026). They keep the original sidebar, and they have no story file. Do not invent stories for them. Put real editions above them.

## What the page shows

From `date`, the page draws the edition header itself:

```text
GOOD FETCH | October 19, 2026
Daily News Briefing
```

You do not put that header in the JSON. `2026-10-19` is shown as `October 19, 2026` in English and as `2026年10月19日` in 中文 (`GOOD FETCH｜2026年10月19日`). The same Chinese date is used on the archive buttons, so 中文 mode does not show English month names. If you include `publishedAt`, a smaller muted line under that date reads **Published 05:42 SGT** (中文: **新加坡时间 05:42 发布**). The clock is always Singapore time. `updatedAt` extends it to **Published 05:42 SGT · Updated 09:10 SGT** (中文: **新加坡时间 05:42 发布，09:10 更新**). Leave both out and that line is not shown.

Under the header, the page shows every story in `stories` (usually 1–3, sometimes more — there is no fixed count), then the stories in `region.stories`, in the same list. Then, only if you include it, a closing recommendation labelled exactly `Worth a look`. After that, only if you include `closing`, a short sign-off from Riva.

There is no "Why it matters" field and no "summary" field. A story is a headline, optional opening paragraphs, any number of question sections (including none), an optional `myTake`, an optional `details` block, and source links. Set `update` to `true` for a small Update tag beside the category. `updateNote`, when present, is one short line under the headline.

## Edition schema

Editions with no `format` field use this schema. From 2026-10-21, a new edition sets `"format": "v2"` and uses [Edition format v2](#edition-format-v2) instead. Do not rewrite an older file into v2.

```json
{
  "date": "YYYY-MM-DD",
  "publishedAt": "2026-10-20T05:42:00+08:00",
  "updatedAt": "2026-10-20T09:10:00+08:00",
  "stories": [ Story, ... ],
  "region": {
    "label": "SINGAPORE",
    "stories": [ Story, ... ]
  },
  "recommendation": {
    "label": "Worth a look",
    "title": "Name of the thing",
    "link": "https://example.com/thing",
    "text": "One sentence on why it is worth a look.",
    "price": "Optional. What it costs.",
    "availability": "Optional. When or where it is available.",
    "note": "Optional. We haven't tested it."
  },
  "closing": "Optional. Only fetched this much today."
}
```

| Field | Required | Notes |
| --- | --- | --- |
| `date` | yes | `YYYY-MM-DD`. Drives the header and which archive button is highlighted. |
| `publishedAt` | no | When the edition went out, as ISO 8601 with an offset, such as `2026-10-20T05:42:00+08:00`. The page shows **Published 05:42 SGT** under the date (中文: **新加坡时间 05:42 发布**). The time is converted to Asia/Singapore no matter which offset you write and no matter the reader's timezone. Omit it, or leave it blank, and the line is left out. |
| `updatedAt` | no | Same format as `publishedAt`. When both are set, the line continues ** · Updated 09:10 SGT** (中文: **，09:10 更新**). Omit it and only the published time is shown. It is not shown on its own. |
| `stories` | yes | Main stories, in reading order. One or more. Not capped at 3. |
| `region` | yes | One group of stories that follow the main stories. |
| `region.label` | yes | Keep `SINGAPORE` or `SOUTHEAST ASIA` in the file. The page does not print this heading. |
| `region.stories` | yes | One or more stories, same shape as the main stories. Each one uses its own `category` as the small label. There is no separate region heading. |
| `recommendation` | no | Omit the whole object when there is nothing to recommend. The page shows it as a tinted editor's-pick card, not as another numbered story. |
| `recommendation.label` | when recommending | Small label. Use `Worth a look` for a new edition. In English the page prints this text. In 中文 it prints `labelZh`, or **值得一看** when `labelZh` is omitted. |
| `recommendation.title` | when recommending | The name of the link. It is the card title. |
| `recommendation.link` | when recommending | A full `https://` URL. The title links here. |
| `recommendation.text` | when recommending | One sentence on why it is worth a look, under the title. |
| `recommendation.price` | no | A short cost line under the sentence. Omit it to leave the line out. |
| `recommendation.availability` | no | A short availability line under the price. |
| `recommendation.note` | no | A short closing line, such as `We haven't tested it`. |
| `closing` | no | A short sign-off from Riva, after the last story, region story, and recommendation, and before the archive. Omit it, or leave it blank, and that line is left out. 中文 uses `closingZh` when you write one. |

### Story

```json
{
  "category": "Business",
  "update": true,
  "updateNote": "Optional. What changed since the earlier edition.",
  "headline": "The headline, in sentence case",
  "opening": [
    "Optional first paragraph: what happened.",
    "Optional second paragraph."
  ],
  "sections": [
    {
      "heading": "What's changing?",
      "paragraphs": [
        "One or more paragraphs under this question."
      ]
    }
  ],
  "myTake": "Optional. Your own read, in a sentence or two.",
  "details": [
    "Optional extra paragraph, hidden until the reader opens More details.",
    "Another short line, if you need it."
  ],
  "sources": [
    { "name": "Reuters, via Investing.com", "url": "https://www.reuters.com/..." },
    { "name": "MOFCOM: Annex 1", "url": "https://example.com/annex.wps", "official": true, "download": "WPS" }
  ],
  "image": "openai.png",
  "imageAlt": "Short description of the picture"
}
```

| Field | Required | Notes |
| --- | --- | --- |
| `category` | no | Small label above the headline, such as `Business` or `Singapore / Environment`. The same label for main stories and region stories. Omit it if the story has none. |
| `update` | no | `true` shows a small **Update** tag beside the category (中文: **更新**). Omit it, or use `false`, and there is no tag. |
| `updateNote` | no | One short line under the headline, such as what is new since an earlier edition. Omit it, or leave it blank, and the headline is followed by the opening as before. The line is shown when the text is present, whether or not `update` is `true`. |
| `headline` | yes | No separate one-line summary. The headline is the only title. |
| `opening` | no | A list of paragraphs before the question sections. Omit it, or use `[]`, when the story starts straight in with a question. A single string is also accepted. |
| `sections` | no | A list. Use as many as the story needs, including none. Each one is a question-style `heading` plus one or more `paragraphs`. Omit the list when the story has no subsections. |
| `sections[].heading` | with a section | A question, such as `What's changing?` or `Who pays?`. |
| `sections[].paragraphs` | with a section | A list of one or more paragraphs. Not a single summary line. |
| `myTake` | no | Shown in the soft box. The label on the page is **Riva's Take** (中文: **Riva 的看法**), with **Analysis** underneath. The JSON field stays `myTake`. Omit it, or leave it blank, and the box is left out. This is not a "Why it matters" field. |
| `details` | no | Extra paragraphs or short lines, hidden until the reader opens **More details** (中文: **更多细节**). The control starts closed. It sits after the question sections and Riva's Take, and before the sources. Omit the field, or use `[]`, and nothing is shown there. A single string is also accepted. Each string is shown as body text, the same way opening paragraphs are. |
| `sources` | yes | One or more links, at the end of the story under the heading **Sources** (中文: **来源**). |
| `sources[].name` | yes | The link text. Put a reprint in the name, such as `Reuters, via Investing.com`. |
| `sources[].url` | yes | A full `https://` address. |
| `sources[].official` | no | `true` adds a small **Official** tag (中文: **官方**). Omit it, or use `false`, for everything else. |
| `sources[].download` | no | A file type, such as `WPS`. The page adds **Download WPS file** (中文: **下载 WPS 文件**) so the reader knows it is a file, not a web page. |
| `image` | no | Filename relative to `index.html`, such as `boat.png`. Omit it and the story is text only. |
| `imageAlt` | no | Alt text for the picture. |

Lengths vary. A short story can be a headline, one opening paragraph, and one source, with no `sections`, no `details`, and no `myTake`. A longer story can have several opening paragraphs and several question sections, and still omit `details`. Do not pad a story to a fixed length, and do not add empty sections. A blank `myTake` is the same as leaving the field out.

### Optional Chinese

The EN / 中文 switch translates the site chrome (the tagline, "Riva's Take", "More details", "Update", "Daily News Briefing", the archive labels). Story text stays in English unless you add a `Zh` twin:

- `categoryZh` (the small label above the headline; if you omit it, 中文 mode keeps `category`)
- `updateNoteZh` (the short line under the headline; if you omit it, 中文 mode keeps `updateNote`)
- `headlineZh`
- `openingZh` (same shape as `opening`)
- `sections[].headingZh` and `sections[].paragraphsZh`
- `myTakeZh`
- `detailsZh` (same shape as `details`)
- `sources[].nameZh`
- `recommendation.labelZh`, `recommendation.titleZh`, `recommendation.textZh`, `recommendation.priceZh`, `recommendation.availabilityZh`, and `recommendation.noteZh`
- `closingZh` (the sign-off; if you omit it, 中文 mode keeps `closing`)

`update` has no Chinese twin. The page prints **Update** or **更新**.

Omit any of these and that part stays in English. Where Chinese meets Latin or digits, write one ordinary space in the JSON (`AI 融资`, `27% 降至 9%`). The page prints that space as written. `region.label` stays `SINGAPORE` or `SOUTHEAST ASIA` in the file. The page does not show it.

## Example edition

This is a shape example, not a story to publish. Replace every sentence before you save it.

```json
{
  "date": "2026-10-19",
  "stories": [
    {
      "headline": "Example: a chipmaker raises prices",
      "opening": [
        "Two or three sentences on what happened, written out as a normal paragraph."
      ],
      "sections": [
        {
          "heading": "What's changing?",
          "paragraphs": [
            "The first paragraph under this question.",
            "A second paragraph if the point needs it."
          ]
        },
        {
          "heading": "Who pays?",
          "paragraphs": [
            "One paragraph is enough when that is all you have."
          ]
        }
      ],
      "myTake": "Your read on why a reader should care. Leave this field out if you have no take.",
      "sources": [
        { "name": "Reuters", "url": "https://www.reuters.com/example" },
        { "name": "Channel NewsAsia", "url": "https://www.channelnewsasia.com/example" }
      ],
      "image": "openai.png",
      "imageAlt": "Illustration for the story"
    },
    {
      "headline": "Example: a shorter second story",
      "opening": [
        "A short story can stop after the opening. No sections, no fixed extra box."
      ],
      "sources": [
        { "name": "Reuters", "url": "https://www.reuters.com/example-2" }
      ]
    }
  ],
  "region": {
    "label": "SOUTHEAST ASIA",
    "stories": [
      {
        "category": "Singapore / Environment",
        "headline": "Example: a regional story",
        "opening": [
          "What happened in Singapore or elsewhere in Southeast Asia."
        ],
        "sections": [
          {
            "heading": "What do you do today?",
            "paragraphs": [
              "The practical consequence, if there is one."
            ]
          }
        ],
        "myTake": "Optional.",
        "sources": [
          { "name": "Channel NewsAsia", "url": "https://www.channelnewsasia.com/example-3" }
        ],
        "image": "boat.png",
        "imageAlt": "Illustration for the regional story"
      }
    ]
  },
  "recommendation": {
    "label": "Worth a look",
    "title": "Name of a place, product, or link",
    "link": "https://example.com/worth-a-look",
    "text": "One or two sentences. Delete the whole recommendation object if you have nothing this good."
  }
}
```

`region.label` may be `SINGAPORE` instead of `SOUTHEAST ASIA`. Either way, the page does not print that word as a heading. Give the regional story its own `category`. For a new edition, `recommendation.label` is `Worth a look`.

The edition on the homepage is `good-fetch-web-v4/news/latest.json`, dated 19 October 2026. The same file is saved as `good-fetch-web-v4/news/2026-10-19.json`.

If a draft uses `intro` instead of `opening`, `question` instead of `heading`, or `url` instead of `link` on the recommendation, the page still reads it. Save files with the names in the schema above: `opening`, `heading`, and `link`. The `myTake` text is shown in the soft box labelled **Riva's Take**, with the word **Analysis** under that label, so it reads as analysis rather than another news paragraph. Do not start the `myTake` text with "Riva's Take:" or "My Take:".

## Edition format v2

A v2 edition is two full articles, in this order: **01 · What Changed?** (small label **Look Out**) and **02 · Stay Curious** (small label **Look Around**). 中文 is **01 · 有什么变了？** / **向外看** and **02 · 保持好奇** / **看身边**. Those four labels are drawn by the page. They are not fields on the story.

The whole article is on the page. There is no teaser, no truncation, and no "Read more".

```json
{
  "format": "v2",
  "date": "YYYY-MM-DD",
  "publishedAt": "2026-10-21T05:40:00+08:00",
  "updatedAt": "2026-10-21T09:10:00+08:00",
  "whatChanged": { },
  "stayCurious": { },
  "closing": "Optional sign-off.",
  "closingZh": "可选的收尾。"
}
```

| Field | Required | Notes |
| --- | --- | --- |
| `format` | yes | The string `v2`. Anything else, including a missing field, keeps the earlier layout. |
| `date` | yes | `YYYY-MM-DD`. Same header rules as before. |
| `publishedAt`, `updatedAt` | no | Same Singapore-time line as the earlier schema. |
| `whatChanged` | yes | The first article. |
| `stayCurious` | yes | The second article. Same shape as `whatChanged`. |
| `closing`, `closingZh` | no | Same optional sign-off, after both articles. |

Each article:

```json
{
  "category": "Sample category",
  "categoryZh": "示例分类",
  "update": false,
  "updateNote": "Optional. Only for a What Changed follow-up.",
  "updateNoteZh": "可选。",
  "headline": "The headline",
  "headlineZh": "标题",
  "intro": ["Paragraph beside the image.", "Another opening paragraph."],
  "introZh": ["插图旁的段落。"],
  "image": {
    "src": "images/YYYY-MM-DD-what-changed.png",
    "alt": "What the drawing shows",
    "altZh": "这幅画的内容",
    "credit": "Illustration: Good Fetch",
    "creditZh": "插图：Good Fetch"
  },
  "sections": [
    {
      "heading": "Who came up with that idea?",
      "headingZh": "这个说法是谁提出的？",
      "paragraphs": ["One or more paragraphs."],
      "paragraphsZh": ["一段或几段。"]
    }
  ],
  "ending": "One quiet closing thought.",
  "endingZh": "一句安静的收尾。",
  "sources": [
    { "name": "Source name", "nameZh": "来源名称", "url": "https://example.com/story", "official": true }
  ]
}
```

| Field | Required | Notes |
| --- | --- | --- |
| `category` | no | Small tag under the section label. 中文 uses `categoryZh`. |
| `update` | no | `true` adds the **Update** tag (中文: **更新**) beside the category. Use it for a What Changed follow-up. |
| `updateNote` | no | One line under the category tag and before the headline. 中文 uses `updateNoteZh`. |
| `headline` | yes | The article title. 中文 uses `headlineZh`. |
| `intro` | no | Opening paragraphs. On a wide screen they sit to the left of the image. On a narrow screen they sit under the image. 中文 uses `introZh`. A single string is also accepted. |
| `image` | no | One drawing for the article. Omit it and the article is text only. |
| `image.src` | with an image | A path relative to the `news/` folder: `images/YYYY-MM-DD-what-changed.png` for the first article and `images/YYYY-MM-DD-stay-curious.png` for the second. The file itself lives at `good-fetch-web-v4/news/images/YYYY-MM-DD-what-changed.png` or `good-fetch-web-v4/news/images/YYYY-MM-DD-stay-curious.png`. A full `https://` URL is also accepted. |
| `image.alt` | no | Alt text. 中文 uses `altZh`. |
| `image.credit` | no | A small credit under the drawing. 中文 uses `creditZh`. |
| `sections` | no | Question headings and paragraphs, full width under the opening. Same `heading` / `headingZh` / `paragraphs` / `paragraphsZh` rules as before. |
| `ending` | no | One italic line after the body, before the sources. 中文 uses `endingZh`. |
| `sources` | yes | Same link list as before, under the heading **Sources** (中文: **来源**). |

Pictures are simple line drawings on a light background, portrait or near-portrait, about 4:5, under about 400 KB. The page does not crop them.

`news/_sample-v2.json` is a layout sample only. It is not an edition. It is not listed in `news/index.json`. Open `index.html?sample=v2` to preview it. Do not copy it onto `latest.json`.

## Archive file

`good-fetch-web-v4/news/index.json`:

```json
{
  "editions": [
    { "date": "2026-10-19", "label": "Oct 19, 2026" }
  ]
}
```

Newest first. While there are fewer than five editions, the list sits above the footer and the stories use the full width. At five or more, it returns to the right-hand sidebar. **View all** appears only when there are more than five dates. In 中文 each button is the date as `2026年10月19日`, even if `label` is the English form. Clicking a date with no `news/YYYY-MM-DD.json` file shows a short "not available" message instead of breaking the page. If `latest.json` itself fails to load, the page keeps the header and Riva, and shows a refresh message in the story column.

## Deploy

Vercel serves this site from the `good-fetch-web-v4` directory (Root Directory). Do not change that, and do not add a build command. After a change is merged to the branch Vercel deploys (usually `main`), the new JSON is the next edition. Nothing else has to be rebuilt.
