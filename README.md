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

You do not put that header in the JSON. `2026-10-19` is shown as `October 19, 2026`.

Under the header, the page shows every story in `stories` (usually 1–3, sometimes more — there is no fixed count). Then one regional block, labelled exactly `SINGAPORE` or `SOUTHEAST ASIA`. Then, only if you include it, a closing recommendation labelled exactly `Worth Following` or `Worth Trying`.

There is no "Why it matters" field and no "summary" field. A story is a headline, optional opening paragraphs, any number of question sections, an optional `myTake`, and source links.

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
    "label": "Worth Following",
    "title": "Name of the thing",
    "link": "https://example.com/thing",
    "text": "One or two sentences on why it is worth a look."
  }
}
```

| Field | Required | Notes |
| --- | --- | --- |
| `date` | yes | `YYYY-MM-DD`. Drives the header and which archive button is highlighted. |
| `stories` | yes | Main stories, in reading order. One or more. Not capped at 3. |
| `region` | yes | Exactly one regional block. |
| `region.label` | yes | Exactly `SINGAPORE` or `SOUTHEAST ASIA`. No other spelling. |
| `region.stories` | yes | One or more stories, same shape as the main stories. |
| `recommendation` | no | Omit the whole object when there is nothing to recommend. |
| `recommendation.label` | when recommending | Exactly `Worth Following` or `Worth Trying`. |
| `recommendation.title` | when recommending | The name of the link. |
| `recommendation.link` | when recommending | A full `https://` URL. |
| `recommendation.text` | when recommending | A short note, one or two sentences. |

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
  "sources": [
    { "name": "Reuters", "url": "https://www.reuters.com/..." }
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
| `sources` | yes | 1 or 2 links. Each has `name` (the publication) and `url` (full `https://` address). The page prints the name. |
| `image` | no | Filename relative to `index.html`, such as `boat.png`. Omit it and the story is text only. |
| `imageAlt` | no | Alt text for the picture. |
| `category` | no | Small label above the headline, such as `Business`. Omit it if the story has none. |

Lengths vary. A short story can be a headline, one opening paragraph, a `myTake`, and one source. A longer story can have several opening paragraphs and several question sections. Do not pad a story to a fixed length, and do not add empty sections.

### Optional Chinese

The EN / 中文 switch translates the site chrome (the tagline, "Riva's Take", "Daily News Briefing", the archive labels). Story text stays in English unless you add a `Zh` twin:

- `headlineZh`
- `openingZh` (same shape as `opening`)
- `sections[].headingZh` and `sections[].paragraphsZh`
- `myTakeZh`
- `recommendation.titleZh` and `recommendation.textZh`

Omit any of these and that part stays in English. Do not translate `region.label`. It stays `SINGAPORE` or `SOUTHEAST ASIA`.

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
    "label": "Worth Trying",
    "title": "Name of a place, product, or link",
    "link": "https://example.com/worth-trying",
    "text": "One or two sentences. Delete the whole recommendation object if you have nothing this good."
  }
}
```

`region.label` may be `SINGAPORE` instead of `SOUTHEAST ASIA`. `recommendation.label` may be `Worth Following` instead of `Worth Trying`. Use only those spellings.

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

Newest first. The sidebar shows the five newest dates, plus **View all**, which reveals the rest. Clicking a date with no `news/YYYY-MM-DD.json` file shows a short "not available" message instead of breaking the page. If `latest.json` itself fails to load, the page keeps the header and Riva, and shows a refresh message in the story column.

## Deploy

Vercel serves this site from the `good-fetch-web-v4` directory (Root Directory). Do not change that, and do not add a build command. After a change is merged to the branch Vercel deploys (usually `main`), the new JSON is the next edition. Nothing else has to be rebuilt.
