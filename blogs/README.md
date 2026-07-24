# Blog

The blog is powered by plain files — **no database, no build step, no CMS**.
The portfolio page (`../index.html`) opens a full-screen overlay, reads
`index.json`, lists the posts, and renders each `.md` file with `marked.js`
on click.

## Add a post in 2 steps

1. Drop a Markdown file in this folder, e.g. `my-new-post.md`.
2. Add an entry to `index.json` (newest first):

```json
{
  "slug": "my-new-post",
  "title": "My New Post",
  "date": "2025-07-24",
  "excerpt": "One or two sentences shown on the card.",
  "tags": [".NET", "AI"],
  "thumbnail": "https://example.com/cover.jpg"
}
```

`slug` **must** match the filename without the `.md` extension.

## Thumbnails & pictures

- **Card / hero thumbnail** — set the `thumbnail` field in `index.json` to any
  image URL (or a repo-relative path like `blogs/img/cover.jpg`). It shows on
  the post card and as the hero image at the top of the article. If it's
  missing or fails to load, a titled maroon gradient is used instead.
- **Inline images** — use normal Markdown anywhere in the `.md` body:

  ```markdown
  ![Alt text](https://example.com/diagram.png)
  ```

  They render full-width with rounded corners. Repo-relative paths work too,
  e.g. `![Diagram](blogs/img/diagram.png)` — just commit the image alongside
  the post.

That's it — push and the blog updates automatically.
