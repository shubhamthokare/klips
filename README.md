# Cutroom

Turn long YouTube videos into vertical Shorts, then line them up for every platform.

Cutroom has two parts that work together:

| Part | File | Where it runs | What it does |
|---|---|---|---|
| **Browser app** | `shortcut.html` (published page) | Any browser, on your device | Load a video file, auto-pick highlights, trim, frame to 9:16, add captions, export a clip, build a posting queue |
| **Link-to-clips server** | `cutroom_server.py` | Your computer or a server | Paste a YouTube link, find the most-replayed moments, download only those sections, and cut them to 9:16 |

## Description

Creators and editors spend hours finding the best 30 to 60 seconds of a long video, reframing it for phones, captioning it and posting it on each platform. Cutroom automates the repetitive parts:

1. **Find** the strongest moments (YouTube's "most replayed" data, audio energy, or Claude reading a transcript).
2. **Cut** them to 9:16 at 1080×1920.
3. **Caption** them.
4. **Queue** them with a caption, hashtags, platforms and post time per clip, and export the schedule for an automation tool that does the posting.

Use it only on videos you own or have permission to reuse. Downloading and reposting other people's videos can break YouTube's terms and copyright rules.

## Features

### Browser app
- Load a local video file. The video never leaves your device.
- **Auto-pick loudest moments:** scans the audio and suggests five non-overlapping 45-second windows.
- **Find highlights with Claude:** paste a timestamped transcript and get up to five clips with titles and caption lines.
- Trim with start and end fields or the Mark start / Mark end buttons.
- Crop position slider for the 9:16 frame.
- Burned-in captions, one line per caption, spread evenly across the clip.
- Export the clip as MP4 or WebM, depending on the browser.
- Posting queue: caption, hashtags, platform checkboxes (YouTube Shorts, TikTok, Instagram Reels, Facebook, X) and post time per clip.
- Stagger post times automatically.
- Save the schedule as JSON, or save an `ffmpeg` script that cuts every queued clip from the original file.

### Server
- Reads the video's "most replayed" heatmap with `yt-dlp`.
- Picks the top N non-overlapping windows (3, 5 or 8 clips of 30, 45 or 60 seconds).
- Falls back to chapters, then evenly spaced windows, when a video has no replay data.
- Downloads only the chosen sections, then crops and scales with `ffmpeg`.
- Shows a preview and download link for each clip. Files are also saved in the `clips` folder.

## Setup

### Browser app
Open the published page. No install needed.

### Server (local)
```bash
pip install flask yt-dlp
# install ffmpeg (brew install ffmpeg / apt install ffmpeg / ffmpeg.org)
python cutroom_server.py
# open http://127.0.0.1:5000
```

### Server (public link)
1. Put `cutroom_server.py`, `requirements.txt` and `Dockerfile` in a GitHub repo.
2. Create a web service from the repo on Render, Railway or Fly.io. It builds from the Dockerfile.
3. Open the URL the host gives you.

## Tasks and expectations

Each task lists what "done" looks like.

### Core tasks (built)

| # | Task | Expected result |
|---|---|---|
| 1 | Load a video file in the browser app | The 9:16 preview shows the first frame and the page reports the video length |
| 2 | Auto-pick moments from audio | Up to five suggestions appear, each with a time range and a Use button |
| 3 | Find highlights from a transcript | Up to five suggestions appear with titles, time ranges and caption lines |
| 4 | Trim and frame a clip | Start, end and crop position change the preview immediately |
| 5 | Add captions | Caption lines appear on the preview and change evenly through the clip |
| 6 | Export a clip | A vertical video plays back in the page and can be saved |
| 7 | Build a queue | Each clip has its own caption, hashtags, platforms and time, and the queue survives a page reload |
| 8 | Export the schedule | A JSON file and an `ffmpeg` script can be saved |
| 9 | Paste a YouTube link on the server | The server lists clip windows ranked by replay score, or says it used the fallback |
| 10 | Create clips on the server | Each selected window becomes a 1080×1920 MP4 with a preview and download link |

### Next tasks (not built)

| # | Task | Expected result |
|---|---|---|
| 11 | Automatic captions with `faster-whisper` | Word-timed captions appear without typing |
| 12 | Upload to YouTube Shorts with the YouTube Data API | Queued clips publish at their scheduled time |
| 13 | Upload to Instagram Reels and TikTok | Needs approved developer apps for each platform |
| 14 | Password protection for the public server | Strangers can't use the link |
| 15 | Webhook from the queue to Make, Zapier or n8n | Saving the schedule also triggers the posting flow |

## Known limits

- **Replay data isn't always available.** New or low-view videos often have no "most replayed" graph, so the server falls back to chapters or even spacing.
- **Loudness is a stand-in for interest.** The audio picker favors loud, energetic parts, not necessarily the most wanted ones.
- **Large files.** The browser app can't scan files over about 400 MB, and very long videos may fail to decode on a phone.
- **Real-time export.** The browser export records in real time, so a 30-second clip takes 30 seconds and the tab must stay open.
- **No posting from the browser.** Publishing to YouTube, TikTok or Instagram needs approved apps and logins, so it has to happen in a server or an automation tool.
- **Cloud hosts may be blocked by YouTube.** Downloads from data-center servers can fail with a bot check. Supplying cookies to `yt-dlp` or running the server at home avoids this.
- **Public servers need protection.** Without a password, anyone with the link can use your hosting.
- **Untested server.** `cutroom_server.py` and the deployment files were written without internet access and have not been run end to end.

## Requirements

- Browser app: a current Chrome, Edge, Safari or Firefox.
- Server: Python 3.10+, `flask`, `yt-dlp`, `ffmpeg`.
- Deployment: a Docker-capable host.

