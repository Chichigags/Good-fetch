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

You do not put that header in the JSON. `2026-10-19` is shown as `October 19, 2026` in English and as `2026年10月19日` in 中文 (`GOOD FETCH｜2026年10月19日`). The same Chinese date is used on the archive buttons, so 中文 mode does not show English month names.

Under the header, the page shows every story in `stories` (usually 1–3, sometimes more — there is no fixed count), then the stories in `region.stories`, in the same list. Then, only if you include it, a closing recommendation labelled exactly `Worth a look`.

There is no "Why it matters" field and no "summary" field. A story is a headline, optional opening paragraphs, any number of question sections, an optional `myTake`, an optional `details` block, and source links.

## Edition schema

```json
{
  "date": "YYYY-MM-DD",
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
  }
}
```

| Field | Required | Notes |
| --- | --- | --- |
| `date` | yes | `YYYY-MM-DD`. Drives the header and which archive button is highlighted. |
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

### Story

```json
{
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
| `headline` | yes | No separate one-line summary. The headline is the only title. |
| `opening` | no | A list of paragraphs before the question sections. Omit it, or use `[]`, when the story starts straight in with a question. A single string is also accepted. |
| `sections` | no | A list. Use as many as the story needs, including none. Each one is a question-style `heading` plus one or more `paragraphs`. Omit the list when the story has no subsections. |
| `sections[].heading` | with a section | A question, such as `What's changing?` or `Who pays?`. |
| `sections[].paragraphs` | with a section | A list of one or more paragraphs. Not a single summary line. |
| `myTake` | no | Shown in the soft box. The label on the page is **Riva's Take** (中文: **Riva 的看法**), with **Analysis** underneath. The JSON field stays `myTake`. Omit it to leave the box out. This is not a "Why it matters" field. |
| `details` | no | Extra paragraphs or short lines, hidden until the reader opens **More details** (中文: **更多细节**). The control starts closed. It sits after the question sections and Riva's Take, and before the sources. Omit the field, or use `[]`, and the story renders as before. A single string is also accepted. Each string is shown as body text, the same way opening paragraphs are. |
| `sources` | yes | One or more links, at the end of the story under the heading **Sources** (中文: **来源**). |
| `sources[].name` | yes | The link text. Put a reprint in the name, such as `Reuters, via Investing.com`. |
| `sources[].url` | yes | A full `https://` address. |
| `sources[].official` | no | `true` adds a small **Official** tag (中文: **官方**). Omit it, or use `false`, for everything else. |
| `sources[].download` | no | A file type, such as `WPS`. The page adds **Download WPS file** (中文: **下载 WPS 文件**) so the reader knows it is a file, not a web page. |
| `image` | no | Filename relative to `index.html`, such as `boat.png`. Omit it and the story is text only. |
| `imageAlt` | no | Alt text for the picture. |
| `category` | no | Small label above the headline, such as `Business` or `Singapore / Environment`. The same label for main stories and region stories. Omit it if the story has none. |

Lengths vary. A short story can be a headline, one opening paragraph, a `myTake`, and one source. A longer story can have several opening paragraphs and several question sections. Do not pad a story to a fixed length, and do not add empty sections.

### Optional Chinese

The EN / 中文 switch translates the site chrome (the tagline, "Riva's Take", "More details", "Daily News Briefing", the archive labels). Story text stays in English unless you add a `Zh` twin:

- `categoryZh` (the small label above the headline; if you omit it, 中文 mode keeps `category`)
- `headlineZh`
- `openingZh` (same shape as `opening`)
- `sections[].headingZh` and `sections[].paragraphsZh`
- `myTakeZh`
- `detailsZh` (same shape as `details`)
- `sources[].nameZh`
- `recommendation.labelZh`, `recommendation.titleZh`, `recommendation.textZh`, `recommendation.priceZh`, `recommendation.availabilityZh`, and `recommendation.noteZh`

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
