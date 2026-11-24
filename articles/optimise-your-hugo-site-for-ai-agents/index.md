---
title: "Optimise Your Hugo Site for AI Agents"
url: https://www.mizouzie.dev/articles/optimise-your-hugo-site-for-ai-agents/
date: 2026-11-24
description: "If you have used a Hugo generated static site to record your thoughts, it's easy to modernise and make it accessible to LLM agents"
tags: ["hugo","no build","artificial intelligence"]
related:
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
  - title: "What's the Point of All the Static Analysis"
    url: https://www.mizouzie.dev/articles/whats-the-point-of-all-the-static-analysis/index.md
  - title: "A Reply from the Machine"
    url: https://www.mizouzie.dev/articles/a-reply-from-the-machine/index.md
  - title: "New Side Project - AI as a Creator"
    url: https://www.mizouzie.dev/articles/new-side-project-ai-as-a-creator/index.md
  - title: "Why I won't use AI on this blog"
    url: https://www.mizouzie.dev/articles/why-i-wont-use-ai/index.md
---

# Optimise Your Hugo Site for AI Agents

![A happy robot hugging a gift box labelled Clean Markdown Content, next to a table of token savings, rwx 6k to 1.3k (79%), MySQL tutorial 15k to 4.8k (68%), The Road 6.4k to 350 (94%)](https://www.mizouzie.dev/images/agent-loves-clean-markdown.webp)


With the rapid improvement and adoption of AI, I would argue that it makes perfect sense to optimise things for agentic usage. When a blog, like this one for example, is optimised, it can not only make it clearer for our silicon based friends, but also save some tokens along the way. This article walks you through the steps that I took to make all the useful content on this site ready for LLM consumption.

## The goal

- a clean markdown copy of every post, sitting next to its HTML at `<permalink>index.md`
- each HTML page pointing at its copy from the `<head>`
- one `/llms.txt` index of the whole site, linking to those copies
- nothing to install, Hugo can do all of it out of the box

## What is readily available?

Hugo already ships a markdown output format that fits the bill perfectly. Sure, there are some modules that suit more complex site structures, but for a simple blog like this one, the built in Hugo features are perfect.

- `baseName` is `index`
- media type `text/markdown`
- plain text
- `rel` is `alternate`

```sh
hugo config --format json | jq -c '.outputformats.markdown'
```

```json
{"basename":"index","isplaintext":true,"mediatype":"text/markdown","rel":"alternate"}
```

It's a case of switching it on for regular pages (like articles) in the site config. Be sure to only touch **page** though, as the list pages will not require the additional output. Instead, we'll provide a whole list up front to the LLMs in their own index home page. [But more on that below](#index-the-whole-site).

```yaml
outputs:
  page:
    - HTML
    - markdown
```

>WATCH OUT!
>
>`outputs` for a page kind *replaces* Hugo's defaults rather than adding to them, so make sure to leave `HTML` in the list or the normal pages vanish

## Where the template goes

Depending on your version (you should really be using the most up to date... security these days is a bit scary!):

- newer Hugo layout naming, `layouts/single.md`
- older naming, `layouts/_default/single.md`
- `.Permalink` inside it still gives the HTML URL, handy for a `url` field

## The markdown template

1. Front matter first, strings through `jsonify` so titles with quotes, colons or backslashes stay valid YAML

```go-text-template
{{- $json := dict "noHTMLEscape" true -}}
---
title: {{ .Title | jsonify $json }}
url: {{ .Permalink }}
```

- `noHTMLEscape` is very important here because without it `jsonify` will turn characters like `&` and `<` into unicode escapes

```txt
default: "Q\u0026A \u003ctips\u003e"
noHTMLEscape: "Q&A <tips>"
```

2. Body, the obvious choice is `.RawContent`, but that leaves shortcodes as raw `{{< ... >}}` syntax, noise for anything reading it
- `.RenderShortcodes` runs the shortcodes but leaves the rest of the markdown alone
- Note: this requires Hugo v0.117 or above

```go-text-template
# {{ .Title }}

{{ .RenderShortcodes | strings.TrimSpace }}
```

3. Shortcodes can have a template per output format. For example, a custom image shortcode can get an `img.md` next to its `img.html`
- the HTML pages keep their full `<img>`, the markdown copies get a plain markdown image with an absolute URL

```go-text-template
![{{ .Get "alt" }}]({{ printf "/%s" (.Get "src") | absURL }})
```

- so `{{< img src="images/rwx.jpeg" alt="working on a laptop" >}}` comes out as

```markdown
![working on a laptop](https://www.mizouzie.dev/images/rwx.jpeg)
```

- built in shortcodes like `{{< param >}}` just work

## Point to related links

The main bit of polish I wanted to add was making sure the related links showed up for the LLMs too, similar to how my site theme renders them automatically for the HTML output. At first I had just tacked them on at the end with a `## Related Links` heading, but they looked like an extension of the article, which they were not. Seeing as the `.md` versions were a super stripped down version of the theme generated pages, I found it best if the related links went in the front matter, not the body. I would imagine this approach would suit a number of applications.

- front matter is the boundary most markdown tools and models already read as "about this document"
- body stays exactly what I wrote as that is the **content**, so a summary or quote won't sweep the links in
- uses Hugo's related content, the same lookup as a Related list on the HTML page
- links point at the related pages' own `index.md`, so an agent can keep following markdown

```go-text-template
{{- with site.RegularPages.Related . | first 5 }}
related:
{{- range . }}
  - title: {{ .Title | jsonify $json }}
    url: {{ with .OutputFormats.Get "markdown" }}{{ .Permalink }}{{ else }}{{ .Permalink }}{{ end }}
{{- end }}
{{- end }}
```

## The whole markdown template

```go-text-template
{{- $json := dict "noHTMLEscape" true -}}
---
title: {{ .Title | jsonify $json }}
url: {{ .Permalink }}
date: {{ .Date.Format "2006-01-02" }}
{{- with or .Description .Params.summary }}
description: {{ . | jsonify $json }}
{{- end }}
{{- with .Params.tags }}
tags: {{ . | jsonify $json }}
{{- end }}
{{- with site.RegularPages.Related . | first 5 }}
related:
{{- range . }}
  - title: {{ .Title | jsonify $json }}
    url: {{ with .OutputFormats.Get "markdown" }}{{ .Permalink }}{{ else }}{{ .Permalink }}{{ end }}
{{- end }}
{{- end }}
---

# {{ .Title }}

{{ .RenderShortcodes | strings.TrimSpace }}
```

- what a post's copy ends up looking like

```markdown
---
title: "-r(ead)w(rite)x(ecute): Documentation"
url: https://www.mizouzie.dev/articles/rwx/
date: 2022-08-09
description: "I wrote a big piece of documentation recently. Here's how it drastically improved my developer flow."
tags: ["web dev","documentation","learning"]
related:
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
  - title: "Tum | Modo | Postea"
    url: https://www.mizouzie.dev/articles/then-now-after/index.md
  # ...three more
---

# -r(ead)w(rite)x(ecute): Documentation

![working on a laptop](https://www.mizouzie.dev/images/rwx.jpeg)

I've hit a year now working as part of a development team with PHP and Laravel...
```

## Pointing each page at its copy

- `<link rel="alternate" type="text/markdown">` in the `<head>`, so anything landing on the HTML can find the clean version
- if your head partial already loops over `.AlternativeOutputFormats` (lots of themes do, for RSS) you get it for free, otherwise add the loop

```go-html-template
{{ range .AlternativeOutputFormats -}}
<link rel="{{ .Rel }}" type="{{ .MediaType.Type | html }}" href="{{ .Permalink | safeURL }}">
{{ end -}}
```

```html
<link rel="alternate" type="text/markdown" href="https://www.mizouzie.dev/articles/rwx/index.md">
```

## Index the whole site

- the [llmstxt.org](https://llmstxt.org/) proposal sets out a single file at `/llms.txt` listing everything on a site worth an agent's time
- `index.md` next to `index.html` is one of the forms it accepts for pages without a file name in the URL, so the copies above already fit
- an H1 with the site name is the only required part, after that a blockquote summary and H2 sections of `- [name](url): notes` lists
- a `## Optional` section, by convention, holds links an agent can skip when it's short on context

1. A custom output format, switched on for the home page

```yaml
outputFormats:
  llms:
    baseName: llms
    mediaType: text/plain
    isPlainText: true
    notAlternative: true

outputs:
  home:
    - HTML
    - RSS
    - llms
  page:
    - HTML
    - markdown
```

- `notAlternative: true` keeps it out of the home page's `<link rel="alternate">` tags, as it's an index of the site rather than another copy of the home page

>Same WATCH OUT! as before
>
>`home` outputs replace the defaults, so keep `HTML` and `RSS` in there

2. The template, newer naming `layouts/home.llms.txt`, older naming `layouts/index.llms.txt`

```go-text-template
# {{ site.Title }}
{{ with site.Params.description }}
> {{ . }}
{{ end }}
## Articles

{{ range where site.RegularPages "Section" "articles" -}}
- [{{ .Title }}]({{ with .OutputFormats.Get "markdown" }}{{ .Permalink }}{{ else }}{{ .Permalink }}{{ end }})
{{- with .Description }}: {{ . | plainify | replaceRE `\s+` " " | strings.TrimSpace }}{{ end }}
{{ end -}}
```

- swap `articles` for whatever your section is called, and point the blockquote at wherever your config keeps a site description
- links go to the `index.md` copies, using the same `.OutputFormats.Get "markdown"` trick as the related links
- `replaceRE` squashes a description that runs over several lines onto one, so each item stays a single list line
- pages come out newest first without any sorting, as that's Hugo's default order

## Check the output

While you decide exactly what you want included in your agent friendly pages, it's simple enough to see what is generated, the same way you'd check how your articles render as you're writing them. Generate your site and have a peek:

```sh
hugo -D
cat public/articles/<your-post>/index.md
cat public/llms.txt
```

Or, simpler still with `hugo server -D` running, open `http://localhost:1313/articles/<your-post>/index.md`.

## To wrap up

With just those few little tweaks, your whole static site just got an upgrade and became more accessible to LLMs. Seeing as I've already been through all this with this site, why not point your agent at [this page's `index.md`](https://www.mizouzie.dev/articles/optimise-your-hugo-site-for-ai-agents/index.md) and tell it to have a crack at your site?

I've found it quite helpful in my day to day work too, because I can treat my whole site as a knowledge base (I did before anyway, just now it costs far fewer tokens!) and when I come across something in my work that I have tackled before, I can point my agent at how I wrote up the way I like it done. Also it's served me well in self-reflection because I can get it to scan, quite quickly, through all my articles and it gets a pretty good picture of my career progression, so I dare say it's even helped me set my own goals more clearly.

All in all, it depends on whether or not you _want_ to feed the greedy, corporate machine that's out here stealing creative people's hard work. In my humble opinion, it's gonna take it anyway, so I'm just gonna lean into it and make it work for me.
