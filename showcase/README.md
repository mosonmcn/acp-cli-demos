# Showcase Manifests

Showcase projects are published from this repo into the EconomyOS Community
Showcase after the contribution PR is approved and merged.

Each public entry lives in `showcase/<project-slug>/`. The manifest is
`showcase/<project-slug>/showcase.json`, and the same folder can contain the
proof notes, project-specific skill, artifacts, and reviewer context needed by
the docs site.

## End-To-End Flow

1. Build a real EconomyOS workflow and capture public proof.
2. Add the project package under `showcase/<project-slug>/`.
3. Add a project-specific reusable skill under
   `showcase/<project-slug>/skills/<skill-name>/` when the workflow can be
   repeated. Use top-level `skills/<skill-name>/` only when the skill is shared
   across projects.
4. Open a PR against `Virtual-Protocol/acp-cli-demos`.
5. Reviewers check the demo package, redaction, skill quality, and manifest.
6. After merge to `main`, the sync workflow publishes the manifest into the
   EconomyOS docs Showcase data.

The publish step requires the `SHOWCASE_SYNC_TOKEN` repository secret to be set
in `acp-cli-demos`. It should be a GitHub token that can create a repository
dispatch event in `Virtual-Protocol/whitepaper-economyOS`.

## Required Shape

Use the Paid Substack example as the reference:

- `showcase/paid-substack-subscription/showcase.json`
- `skills/acp-paid-subscription-checkout/` as a shared skill source for this
  example
- `skills/acp-paid-subscription-checkout/examples/substack/`

Note: that reference manifest uses a site-relative `visual.posterUrl`, which is
a maintainer-managed asset in the docs repo. Contributor PRs must use an
`https://` poster URL instead — see [Video Fields](#video-fields).

Every manifest needs:

- `slug`, `title`, `tagline`, `description`, `status`, `topic`, and `topics`
- `builder.name` and `builder.url`
- `links.repo`, `links.share`, and `links.feedback`
- `primitives`, using `wallet`, `email`, `card`, `token`, or `acp`
- `visual.kind`, `visual.eyebrow`, and `visual.title`
- `skills`, when the workflow is reusable; each entry needs `name`, `href`,
  `summary`, and `install`
- `skills[].sourcePath`, when the skill is committed in this repo and should be
  validated against a local `SKILL.md`
- `artifacts`, including proof and redacted reports for live workflows
- exactly three `feedbackPrompts`

An X video is highly recommended when possible because it is visual and easy to
share, but it is not required. Use any inspectable artifact that shows the
project or workflow ran: screenshot, hosted video, animated demo, live page,
interactive demo, public PR, demo repo, or redacted result report.

## Video Fields

Use these rules whenever the submission includes a video. The docs site plays
a video inline when `visual.videoUrl` is a direct video file, embeds a
click-to-play YouTube player when `links.video` is a YouTube URL, and links
every other video page out to its own platform.

| Where the video lives | `links.video` | `visual.videoUrl` | `visual.posterUrl` (recommended, not required) | `visual.videoLabel` |
| --- | --- | --- | --- | --- |
| X post | the `x.com/<user>/status/<id>` post URL | the direct `https://video.twimg.com/....mp4` file URL | the `https://pbs.twimg.com/amplify_video_thumb/...` image from the same capture | `Watch the 1:50 demo on X` |
| YouTube | the watch or Shorts URL | omit — the docs site embeds the YouTube player from `links.video` | `https://img.youtube.com/vi/<video-id>/maxresdefault.jpg` | `Watch the 1:50 demo on YouTube` |
| Vimeo, TikTok, Loom, or another video page | the page URL | omit — page URLs cannot play inline, and tokenized CDN file URLs extracted from players expire | commit a captured frame under `showcase/<slug>/assets/` | `Watch the 1:50 demo on Vimeo` |
| Self-hosted file | a public page when one exists, otherwise the file URL | the direct `.mp4` file URL (see hosting notes below) | recommended | `Watch the 1:50 demo` |
| No video | omit | omit | optional — prefer adding screenshots under `artifacts`; a poster without a video renders as a static image | omit |

Replace `1:50` with the real video duration.

Field meanings and what the validator enforces:

- `links.video` is the public watch page. Point `links.share` at the same post
  or page unless a separate announcement post should be shared. Also list the
  video under `artifacts` with kind `video` so it is reachable from the
  project detail view.
- `visual.videoUrl` must be a direct video file that a browser video tag can
  stream. `.mp4` (H.264) is strongly preferred because it plays everywhere;
  `.webm` does not play in older Safari, and `.mov`/`.m4v` are accepted but
  discouraged. The validator rejects video page URLs (YouTube, X, Vimeo,
  TikTok, Loom), repo pages (`github.com/.../blob/...`), and share or preview
  links (Dropbox, Google Drive, OneDrive, and similar) because they serve an
  HTML page instead of the file. For a file committed in a GitHub repo, use
  its `raw.githubusercontent.com` URL. For Dropbox, use a
  `dl.dropboxusercontent.com` URL. Do not use tokenized CDN URLs pulled out
  of Vimeo or similar players; they expire and silently break the card.
- `visual.posterUrl` must be an `https://` image URL. It is recommended for
  every video and required for none. For YouTube, use the thumbnail
  `https://img.youtube.com/vi/<video-id>/maxresdefault.jpg`; the `<video-id>`
  is the `v=` query parameter on watch URLs or the last path segment of
  `youtube.com/shorts/<id>` and `youtu.be/<id>` links. If `maxresdefault.jpg`
  returns 404, use `sddefault.jpg`; `hqdefault.jpg` is 4:3 and gets visibly
  cropped on the 16:9 card. For X videos, copy the
  `https://pbs.twimg.com/amplify_video_thumb/...` image URL from the same
  devtools capture as the video file. To ship a poster inside this repo,
  commit it as `showcase/<slug>/assets/poster.jpg` and reference
  `https://raw.githubusercontent.com/Virtual-Protocol/acp-cli-demos/main/showcase/<slug>/assets/poster.jpg`;
  the `main` URL resolves only after the PR merges, so verify the same path
  on your fork or branch instead. Site-relative paths such as
  `/showcase/<slug>-poster.jpg` are maintainer-managed assets in the docs
  repo; do not use them in contributor PRs.
- `visual.videoLabel` is required whenever `links.video` is set. It is the
  watch-button text for featured builds and the accessible label on the
  card's play link, so it must name the platform the viewer lands on. The
  validator enforces the platform mention for YouTube, X, Vimeo, TikTok, and
  Loom links. For self-hosted files a plain `Watch the 1:50 demo` is correct.

To get the direct file URL from an X post: open the post in a browser, open
developer tools, filter network requests by `video.twimg.com`, play the video,
and copy the highest-resolution `.mp4` request URL.

Verify before requesting review (humans and AI agents):

```bash
# Expect HTTP 200 and content-type: video/...
# GitHub raw serves video files as application/octet-stream; in that case
# confirm playback by opening the URL in a browser instead.
curl -sI "<visual.videoUrl>"

# Expect HTTP 200 and content-type: image/...
# For a repo-committed poster, check the raw URL on your fork or branch;
# the main URL only resolves after merge.
curl -sI "<visual.posterUrl>"

node scripts/validate-showcase.mjs
```

Optional visibility control:

- `hidden: true` keeps the package valid in this repo but prevents the EconomyOS
  docs sync from publishing the card. Remove it in a later PR when the showcase
  should go live.

Optional agent context:

- `soul.md` can be included when the builder intentionally wants to publish
  public agent context. Prefer committing the text as
  `showcase/<project-slug>/soul.md`; use a `soul/` folder only for multi-agent
  or multi-file context. Redact private instructions, credentials, account data,
  wallet material, and operational secrets before linking it from
  `showcase.json` as `soul.href` with a short `soul.summary`.

Run this before requesting review:

```bash
node scripts/validate-showcase.mjs
```
