---
name: wechat-smartmind-layout
description: Convert Chinese long-form articles into WeChat Official Account compatible HTML using the SmartMind red-black-white visual system, including adaptive red-black gradient heading bars, horizontal scroll long-image modules, emphasis blocks, and strict inline CSS rules.
---

# SmartMind WeChat Article Layout Skill

## Purpose

Use this skill when converting a Chinese article, usually Markdown, plain text, or exported document text, into a WeChat Official Account compatible HTML fragment.

The output must be suitable for copying into WeChat editors such as Xiumi, 135 Editor, or the WeChat public account editor. The visual direction is restrained technology/finance analysis: white background, black text, SmartMind red accents, red-to-black gradient bars, clean spacing, and mobile-first readability.

## Core Principles

1. Preserve the article's visible text unless the user explicitly requests a change.
2. Do not rewrite, summarize, expand, compress, reorder, or polish the article content.
3. Do not add decorative visible text unless the user explicitly asks for that module. For this preset, the visible label `左右滑动查看` is allowed only for images explicitly marked as long horizontal scroll images.
4. Convert editorial source markers into presentation structure only when requested. Example: `(长图左右滑动)` is a production marker and should not appear in the final article.
5. Generate only an HTML fragment. Do not output a full HTML document shell.
6. Use inline CSS only. Do not rely on classes, external CSS, or `<style>` tags.
7. Prefer robust WeChat-compatible structures over clever CSS. When vertical bars need to match text height, use a table row with a gradient `td`.

## Input Expectations

The source article may include:

- Markdown heading lines starting with `#`.
- Bold text marked with `**...**`.
- Image URLs on separate lines.
- Long-image markers written after image URLs, such as `（长图左右滑动）`.
- Normal paragraphs separated by blank lines.

If a title contains an editorial workflow prefix such as `4. 【排版】`, remove that prefix only when the user requests a clean public title. Otherwise keep source text exactly.

## Output Contract

Create one standalone HTML fragment file, usually named:

```text
wechat_article.html
```

The file must contain:

- No `<!DOCTYPE html>`.
- No `<html>`, `<head>`, `<body>`, `<meta>`, `<title>`, or `<link>`.
- No `<style>` or `<script>`.
- No HTML comments.
- No external stylesheet or class-dependent styling.
- Save as UTF-8 without BOM. The file must not begin with an invisible `U+FEFF` character, because it may be copied into an editor as hidden text.
- Inline CSS on every styled element.

## WeChat HTML Rules

Allowed common tags:

```html
<section>
<p>
<span>
<strong>
<b>
<em>
<i>
<br>
<table>
<tbody>
<tr>
<td>
<img>
<a>
```

Avoid or do not use:

```html
<!DOCTYPE html>
<html>
<head>
<body>
<meta>
<title>
<link>
<style>
<script>
<form>
<input>
<button>
<select>
<textarea>
<iframe>
<audio>
<video>
<object>
<embed>
```

Use `<section>` for page containers and ordinary layout blocks. Use `<table>` only when equal-height vertical bars are needed, because table cells stretch reliably with content height in many WeChat editors.

## CSS Rules

Use only inline `style="..."`.

Recommended CSS:

```css
width
max-width
margin
padding
box-sizing
border
border-radius
background
background-color
background-image:linear-gradient(...)
color
font-size
font-weight
line-height
letter-spacing
text-align
word-break
height
display:block
display:inline-block
display:flex
overflow-x:auto
overflow-y:hidden
white-space:nowrap
-webkit-overflow-scrolling:touch
scrollbar-width:none
-ms-overflow-style:none
border-collapse
border-spacing
vertical-align
```

Avoid:

```css
position
float
clear
z-index
filter
columns
@font-face
transform
negative margins
fixed heights that may clip text
```

Use `px`, `%`, and `em`. Avoid `rem`, `vw`, and `vh`.

Do not depend on pseudo-elements such as `::before` or `::-webkit-scrollbar`, because WeChat editors may strip them and inline CSS cannot express them reliably.

### CSS Implementation Notes

- Use `box-sizing:border-box` on wrappers, tables, image containers, and key layout cells.
- Use `line-height:0` and `font-size:0` on image-only containers to remove browser-created inline gaps around images.
- Use `height:auto` on every image.
- Use `border:1px solid #EEEEEE` for image frames and long-image scroll frames.
- Use `border-top` and `border-bottom` instead of a full border when a module already has an independent gradient side bar.
- Use `background:#DF0000` before `background-image:linear-gradient(...)` as a fallback if the gradient is stripped.
- Use single quotes inside `font-family` values when the `style` attribute itself uses double quotes.
- Do not use `gap`; use explicit spacer cells or margins instead.
- Do not use fixed heights on text containers. Fixed heights are allowed only for non-text decorative dots or other tiny ornaments.

## Color System

Use the SmartMind palette:

```text
Black: #000000
Red:   #DF0000
Text:  #111111 / #1F1F1F
Soft background: #FAFAFA / #F7F7F7
Light border: #E6E6E6 / #EFEFEF
```

Primary accent gradient:

```css
background:#DF0000;
background-image:linear-gradient(180deg,#DF0000 0%,#000000 100%);
```

The red must be on top and black on bottom unless the user requests another direction.

## Base Article Wrapper

Use a constrained wrapper to approximate WeChat article width while remaining mobile friendly:

```html
<section style="width:100%;max-width:667px;margin:0 auto;padding:24px 16px 44px;box-sizing:border-box;color:#111111;font-family:-apple-system,BlinkMacSystemFont,'Helvetica Neue',Helvetica,'PingFang SC','Hiragino Sans GB','Microsoft YaHei',Arial,sans-serif;background:#FFFFFF;">
  ...
</section>
```

## Main Title

The public article title should not use the red-black side bar by default. Use a clean large text block with a light divider:

```html
<section style="margin:0 0 34px;padding:0 0 24px;border-bottom:1px solid #EAEAEA;box-sizing:border-box;width:100%;">
  <section style="font-size:25px;line-height:1.35;font-weight:700;color:#000000;letter-spacing:0;word-break:break-word;box-sizing:border-box;width:100%;">文章标题</section>
</section>
```

If the source title contains a production prefix such as `4. 【排版】`, remove it only when the user requests a clean title.

## Section Headings

For article section headings, use a red-black gradient vertical bar. The bar must follow the title's actual height, so use a table structure:

```html
<table style="margin:52px 0 24px;width:100%;border-collapse:collapse;border-spacing:0;box-sizing:border-box;">
  <tbody>
    <tr>
      <td style="width:7px;background:#DF0000;background-image:linear-gradient(180deg,#DF0000 0%,#000000 100%);padding:0;box-sizing:border-box;"></td>
      <td style="width:15px;padding:0;box-sizing:border-box;"></td>
      <td style="padding:0;font-size:22px;line-height:1.35;font-weight:700;color:#000000;letter-spacing:0;word-break:break-word;box-sizing:border-box;vertical-align:middle;">一、一级标题</td>
    </tr>
  </tbody>
</table>
```

Do not use a fixed bar height for section headings. A fixed height will look wrong when a heading wraps to two lines.

## Body Paragraphs

Use stable long-form paragraph rhythm:

```html
<p style="margin:0 0 20px;font-size:16px;line-height:1.9;color:#1F1F1F;letter-spacing:0;text-align:left;word-break:break-word;box-sizing:border-box;">正文段落</p>
```

For inline bold text converted from Markdown `**...**`:

```html
<strong style="font-weight:700;color:#000000;">加粗文本</strong>
```

Do not change the bold text itself.

## Emphasis Blocks

For standalone bold paragraphs or important quoted-style paragraphs, use a soft gray background and a red-black gradient side bar:

```html
<table style="margin:28px 0 30px;width:100%;border-collapse:collapse;border-spacing:0;background:#FAFAFA;border-top:1px solid #EFEFEF;border-bottom:1px solid #EFEFEF;box-sizing:border-box;">
  <tbody>
    <tr>
      <td style="width:5px;background:#DF0000;background-image:linear-gradient(180deg,#DF0000 0%,#000000 100%);padding:0;box-sizing:border-box;"></td>
      <td style="padding:16px 18px;box-sizing:border-box;">
        <p style="margin:0;font-size:17px;line-height:1.85;color:#111111;letter-spacing:0;text-align:left;word-break:break-word;box-sizing:border-box;"><strong style="font-weight:700;color:#000000;">重点段落文本</strong></p>
      </td>
    </tr>
  </tbody>
</table>
```

This replaces `border-left`, because ordinary borders cannot render a stable gradient.

## Normal Images

For ordinary images that should fit the article width, add the same thin, tight frame used by long-image modules. The frame should sit directly against the image, without extra padding or rounded corners.

```html
<section style="margin:26px 0 30px;width:100%;box-sizing:border-box;font-size:0;line-height:0;border:1px solid #EEEEEE;background:#FFFFFF;">
  <img src="IMAGE_URL" style="display:block;width:100%;max-width:100%;height:auto;border:0;border-radius:0;box-sizing:border-box;" />
</section>
```

Every image must include responsive inline sizing and `height:auto`.

Normal image frame rules:

- Use `border:1px solid #EEEEEE` on the outer image container.
- Use `background:#FFFFFF` on the outer image container.
- Use `font-size:0;line-height:0` on the outer image container so the frame hugs the image.
- Use `border-radius:0` on the image itself for a clean tight frame.
- Do not add padding inside the image frame unless the user explicitly requests breathing room.

## Long Horizontal Images

If an image URL is marked with `（长图左右滑动）`, remove that marker from the final output and render the image as a horizontal scroll module.

Default long-image width:

```text
480px
```

Use a larger width only if the user says the image is too small.

```html
<section style="margin:28px 0 34px;width:100%;box-sizing:border-box;">
  <section style="width:100%;overflow-x:auto;overflow-y:hidden;white-space:nowrap;-webkit-overflow-scrolling:touch;scrollbar-width:none;-ms-overflow-style:none;border:1px solid #EEEEEE;background:#FFFFFF;box-sizing:border-box;padding:0;font-size:0;line-height:0;">
    <img src="IMAGE_URL" style="display:inline-block;width:480px;max-width:none;height:auto;vertical-align:top;border:0;box-sizing:border-box;" />
  </section>
  <table style="margin:0;width:100%;border-collapse:collapse;border-spacing:0;background:#F7F7F7;border-top:1px solid #E6E6E6;border-bottom:1px solid #E6E6E6;box-sizing:border-box;">
    <tbody>
      <tr>
        <td style="width:5px;background:#DF0000;background-image:linear-gradient(180deg,#DF0000 0%,#000000 100%);padding:0;box-sizing:border-box;"></td>
        <td style="padding:12px 15px;color:#111111;font-size:15px;line-height:1.4;letter-spacing:0;box-sizing:border-box;">
          <span style="display:inline-block;width:7px;height:7px;background:#DF0000;margin-right:8px;vertical-align:2px;"></span><strong style="font-weight:700;color:#111111;">左右滑动查看</strong>
        </td>
      </tr>
    </tbody>
  </table>
</section>
```

Notes:

- `max-width:none` is required so the long image is not compressed to the viewport.
- `overflow-x:auto` enables horizontal scrolling.
- `scrollbar-width:none` and `-ms-overflow-style:none` may hide scrollbars in some preview environments. They are harmless if ignored.
- Do not use CSS pseudo-elements to hide scrollbars.
- The visible label `左右滑动查看` is allowed here because the user requested this long-image affordance.

## Markdown Conversion Rules

1. Blank lines separate paragraphs.
2. Lines beginning with `# ` become either the main title or section headings.
3. The first `# ` heading is usually the article title.
4. Later `# ` headings become section headings with the gradient vertical bar.
5. Convert `**text**` into `<strong style="font-weight:700;color:#000000;">text</strong>`.
6. URLs on their own lines become images when they point to image resources.
7. URLs followed by `（长图左右滑动）` become long horizontal scroll image modules.
8. Remove Markdown escape slashes used for punctuation or URLs, such as `\.` and `\_`, when producing HTML.
9. Preserve Chinese punctuation, English words, numbers, and sentence spacing exactly.

## Layout Rhythm

Use these defaults unless the user asks otherwise:

```text
Article wrapper padding: 24px 16px 44px
Main title bottom margin: 34px
Paragraph margin: 0 0 20px
Paragraph font size: 16px
Paragraph line height: 1.9
Section heading margin: 52px 0 24px
Section heading font size: 22px
Emphasis block margin: 28px 0 30px
Long image module margin: 28px 0 34px
Normal image margin: 26px 0 30px
Long image width: 480px
Normal image frame: border:1px solid #EEEEEE; background:#FFFFFF; font-size:0; line-height:0
```

## Quality Checks

Before delivering the final file, run these checks.

### Text Preservation Check

Extract visible text from the source, excluding:

- Image URLs that were converted into images.
- Production markers such as `（长图左右滑动）`.

Extract visible text from the generated HTML, excluding the intentionally added long-image label:

```text
左右滑动查看
```

The remaining source text and HTML text must match exactly.

### Structure Check

Confirm:

- The main title exists once.
- Section headings exist in the expected count.
- Ordinary images exist in the expected count.
- Ordinary images use the thin tight frame: `border:1px solid #EEEEEE;background:#FFFFFF;font-size:0;line-height:0`.
- Long horizontal image modules exist in the expected count.
- Long horizontal image modules use `overflow-x:auto`, `max-width:none`, and the expected long-image width.
- No source marker such as `（长图左右滑动）` remains visible.

### Compatibility Check

Confirm:

- No `<!DOCTYPE html>`.
- No `<html>`, `<head>`, `<body>`, `<meta>`, `<title>`, or `<link>`.
- No `<style>` or `<script>`.
- No HTML comments.
- All visual styles are inline.
- No risky CSS terms: `position`, `float`, `clear`, `z-index`, `filter`, `columns`, `@font-face`, `transform`, or negative margins.
- No fixed heights on text containers that can clip text.
- No UTF-8 BOM or invisible leading character at the beginning of the generated HTML.

## Delivery Notes

Tell the user where the generated file is located. If they are previewing it in a browser, tell them to refresh the page after edits.

Do not tell the user to manually recreate the file. The file should already exist in the shared workspace.
