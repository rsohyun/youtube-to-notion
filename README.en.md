[한국어](README.md) | **English**

# youtube-to-notion

> A Claude Code skill: queue YouTube links on a Notion page, run `/youtube-to-notion`, and each video gets summarized into its own page in a Notion database.

- Uses captions when available; **transcribes with Groq Whisper when there are none**.
- One Notion page per video: thumbnail, video info, one-line summary, key-points table, section-by-section notes, and a structure tree.
- Reuses existing channel / tag / category options and adds new ones when needed.
- Processed links are checked off (`- [x]`) in the inbox; failed videos are retried on the next run.

> Note: the skill instructions and the generated summaries are in **Korean**. To get summaries in another language, edit the "한국어로 작성" (write in Korean) line in `SKILL.md`.

## How it works

```
Notion inbox page (- [ ] YouTube link)
  → /youtube-to-notion
  → watch skill (yt-dlp + ffmpeg): captions + metadata, Groq Whisper if no captions
  → write summary, pick channel / tags / category
  → new page in the Notion DB (sequence number +1)
  → check off the line in the inbox
```

## Requirements

| Item | Notes |
|------|-------|
| [Claude Code](https://claude.com/claude-code) | Runs the skill |
| Notion connector | Connect Notion in the Claude app |
| [watch skill](https://github.com/bradautomates/claude-video) | **Required dependency** for download, captions and transcription |
| yt-dlp, ffmpeg | Used by watch (Windows: `winget install yt-dlp.yt-dlp`, `winget install Gyan.FFmpeg`) |
| Groq API key (optional) | For videos without captions; otherwise frames only |

## Install

Two lines in Claude Code (Claude Code 2.1.275 or later):

```
/plugin install watch --marketplace bradautomates/claude-video
/plugin install youtube-to-notion --marketplace rsohyun/youtube-to-notion
```

- The first line installs the required [watch skill](https://github.com/bradautomates/claude-video). Follow its setup to get yt-dlp and ffmpeg.
- **(Optional) Groq key** for videos without captions: create one at [console.groq.com/keys](https://console.groq.com/keys) and put it after `GROQ_API_KEY=` in `~/.config/watch/.env`. **Never paste the key into chat or commit it.**

<details>
<summary>Manual install (without plugins)</summary>

Copy `skills/youtube-to-notion/SKILL.md` to `~/.claude/skills/youtube-to-notion/SKILL.md` (everywhere) or `<project>/.claude/skills/youtube-to-notion/SKILL.md` (one project).
</details>

## Notion setup

**1) Inbox page** — any page with a checklist of links:

```
- [ ] [Video title](https://youtu.be/...)
```

**2) Database** — create these properties (exact Korean names):

| Property | Meaning | Type |
|----------|---------|------|
| 이름 | Name | Title |
| 순번 | Sequence no. | Number |
| 영상 제목 | Original video title | Text |
| 채널 | Channel | Select |
| 설명 | One-line description | Text |
| 태그 | Tags | Multi-select |
| 기록날짜 | Date recorded | Date |
| 영상날짜 | Upload date | Date |
| 스터디 분류 | Category | Multi-select |

Sort the table view by **순번 ascending** so new pages appear at the bottom.

**3) Configure** — nothing to edit. On the first run Claude asks for your inbox page and database links and saves them to `~/.config/youtube-to-notion/config.json`. If the database lacks the properties above, it offers to add them.

## Usage

In Claude Code:

```
/youtube-to-notion
```

(It may appear as `/youtube-to-notion:youtube-to-notion` when installed as a plugin.) You get a list of successes / failures with links to the new pages.

## Notes

- One failed video doesn't stop the rest.
- For videos without captions, audio is sent to Groq's servers.
- Very long videos may hit Groq's upload size limit.

## Acknowledgements

Video download, frame extraction and transcription are handled by the **[watch skill (claude-video)](https://github.com/bradautomates/claude-video)** by [bradautomates](https://github.com/bradautomates) (MIT). Its code is not included in this repository; install it separately.

## License

[MIT](LICENSE)
