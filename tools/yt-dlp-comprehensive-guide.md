# 📼 yt-dlp: The Ultimate Command-Line Media Downloader
## Comprehensive Technical Guide & Reference

---

## Table of Contents

1. [Overview & History](#overview--history)
2. [Installation](#installation)
3. [Core Features](#core-features)
4. [Basic Usage](#basic-usage)
5. [Format Selection](#format-selection)
6. [Advanced Features](#advanced-features)
7. [Authentication & Cookies](#authentication--cookies)
8. [Output Templates](#output-templates)
9. [Post-Processing](#post-processing)
10. [Configuration Files](#configuration-files)
11. [Common Use Cases](#common-use-cases)
12. [Troubleshooting](#troubleshooting)
13. [Performance Optimization](#performance-optimization)
14. [Legal & Ethical Considerations](#legal--ethical-considerations)

---

## Overview & History

### What is yt-dlp?

**yt-dlp** is a feature-rich command-line audio/video downloader supporting thousands of sites. It's a fork of the now-inactive youtube-dlc (itself a fork of youtube-dl), created to provide faster updates, more features, and better performance.

### Key Statistics
- **126k+ GitHub stars** (as of 2025)
- **10k+ forks**
- **23,000+ commits**
- **Supports 1,800+ websites**
- **Active development** with frequent updates

### Evolution Timeline
```
youtube-dl (original) → youtube-dlc (fork) → yt-dlp (current)
   2006-2020             2020 (inactive)      2020-present
```

### Why yt-dlp Over Alternatives?

| Feature | yt-dlp | youtube-dl | Other Tools |
|---------|--------|------------|-------------|
| Update Frequency | Daily/Weekly | Monthly | Varies |
| Site Support | 1,800+ | 1,000+ | Limited |
| SponsorBlock | ✅ Built-in | ❌ | ❌ |
| Format Sorting | Advanced | Basic | Limited |
| Speed | Fastest | Moderate | Varies |
| Active Development | ✅ Very Active | ⚠️ Slow | Varies |

---

## Installation

### Windows

#### Method 1: Direct Download
```bash
# Download latest exe from GitHub releases
https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp.exe

# Place in PATH or use from current directory
yt-dlp.exe [URL]
```

#### Method 2: Using pip
```bash
python -m pip install -U yt-dlp
```

#### Method 3: Using winget
```bash
winget install yt-dlp
```

### macOS

#### Method 1: Homebrew
```bash
brew install yt-dlp
```

#### Method 2: MacPorts
```bash
sudo port install yt-dlp
```

#### Method 3: pip
```bash
python3 -m pip install -U yt-dlp
```

### Linux

#### Ubuntu/Debian
```bash
# From repository
sudo apt update && sudo apt install yt-dlp

# Latest version via pip
python3 -m pip install -U yt-dlp

# Direct download
sudo wget https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -O /usr/local/bin/yt-dlp
sudo chmod a+rx /usr/local/bin/yt-dlp
```

#### Arch Linux
```bash
sudo pacman -S yt-dlp
```

#### Fedora
```bash
sudo dnf install yt-dlp
```

### Dependencies

#### FFmpeg (Required for many features)
```bash
# Windows
winget install ffmpeg

# macOS
brew install ffmpeg

# Linux
sudo apt install ffmpeg  # Debian/Ubuntu
sudo dnf install ffmpeg  # Fedora
```

---

## Core Features

### 🌐 Network Options
- **Proxy Support**: HTTP, HTTPS, SOCKS proxies
- **Rate Limiting**: Control download speed
- **Concurrent Fragments**: `-N` for parallel downloading
- **Retry Logic**: Automatic retry on failure
- **User Agent Spoofing**: Bypass detection

### 📹 Video Selection
- **Playlist Support**: Download entire playlists/channels
- **Range Selection**: Specific videos from playlists
- **Date Filters**: Videos within date ranges
- **View/Like Filters**: Based on popularity metrics
- **Duration Filters**: Short/long videos

### 🎵 Audio Features
- **Extract Audio**: `-x` flag
- **Format Conversion**: MP3, AAC, FLAC, etc.
- **Quality Selection**: Bitrate control
- **Split Chapters**: Separate tracks
- **Metadata Embedding**: Tags, artwork

### 📝 Subtitle Support
- **Auto-Generated**: YouTube captions
- **Multiple Languages**: Download all or specific
- **Format Conversion**: SRT, VTT, ASS
- **Embedding**: Into video files

### 🛡️ Advanced Features
- **SponsorBlock Integration**: Skip sponsors automatically
- **Thumbnail Downloads**: With embedding options
- **Metadata Extraction**: JSON info files
- **Live Stream Support**: Real-time downloading
- **Fragment Recovery**: Resume interrupted downloads

---

## Basic Usage

### Essential Commands

#### Download Single Video
```bash
# Default (best quality)
yt-dlp "https://www.youtube.com/watch?v=VIDEO_ID"

# With progress bar and info
yt-dlp --progress --verbose "URL"
```

#### Download Playlist
```bash
# Entire playlist
yt-dlp "https://www.youtube.com/playlist?list=PLAYLIST_ID"

# Specific range (videos 5-10)
yt-dlp --playlist-start 5 --playlist-end 10 "PLAYLIST_URL"

# Reverse order
yt-dlp --playlist-reverse "PLAYLIST_URL"
```

#### Download Channel
```bash
# All videos from channel
yt-dlp "https://www.youtube.com/c/CHANNEL_NAME/videos"

# Latest 10 videos
yt-dlp --playlist-end 10 "CHANNEL_URL"
```

#### Audio Only
```bash
# Best audio
yt-dlp -x "URL"

# Specific format (MP3 320kbps)
yt-dlp -x --audio-format mp3 --audio-quality 0 "URL"
```

---

## Format Selection

### Understanding Formats

#### List Available Formats
```bash
yt-dlp -F "URL"
# or
yt-dlp --list-formats "URL"
```

Output example:
```
ID  EXT   RESOLUTION FPS │  FILESIZE   TBR PROTO │ VCODEC          VBR ACODEC      ABR
────────────────────────────────────────────────────────────────────────────────────
sb2 mhtml 48x27        0 │                  mhtml │ images                      
sb1 mhtml 80x45        0 │                  mhtml │ images                      
sb0 mhtml 160x90       0 │                  mhtml │ images                      
139 m4a   audio only    │    3.73MiB   49k https │ audio only          mp4a.40.5   49k
249 webm  audio only    │    3.87MiB   50k https │ audio only          opus        50k
250 webm  audio only    │    5.13MiB   67k https │ audio only          opus        67k
140 m4a   audio only    │    9.91MiB  130k https │ audio only          mp4a.40.2  130k
251 webm  audio only    │   10.05MiB  131k https │ audio only          opus       131k
160 mp4   256x144     30 │    5.14MiB   67k https │ avc1.4d400c     67k video only
278 webm  256x144     30 │    6.44MiB   84k https │ vp9             84k video only
133 mp4   426x240     30 │   10.49MiB  137k https │ avc1.4d4015    137k video only
242 webm  426x240     30 │   10.16MiB  133k https │ vp9            133k video only
134 mp4   640x360     30 │   18.44MiB  241k https │ avc1.4d401e    241k video only
18  mp4   640x360     30 │   44.12MiB  578k https │ avc1.42001E    387k mp4a.40.2  96k
243 webm  640x360     30 │   18.87MiB  247k https │ vp9            247k video only
135 mp4   854x480     30 │   31.03MiB  406k https │ avc1.4d401f    406k video only
244 webm  854x480     30 │   30.46MiB  399k https │ vp9            399k video only
136 mp4   1280x720    30 │   62.42MiB  818k https │ avc1.4d401f    818k video only
22  mp4   1280x720    30 │             578k https │ avc1.64001F         mp4a.40.2
247 webm  1280x720    30 │   64.36MiB  843k https │ vp9            843k video only
137 mp4   1920x1080   30 │  120.50MiB 1579k https │ avc1.640028   1579k video only
248 webm  1920x1080   30 │  134.95MiB 1769k https │ vp9           1769k video only
```

### Format Selection Syntax

#### Basic Selection
```bash
# Best quality (default)
yt-dlp -f best "URL"

# Specific format by ID
yt-dlp -f 137 "URL"

# Best video + best audio
yt-dlp -f bestvideo+bestaudio "URL"
```

#### Advanced Selection
```bash
# Best MP4 video + best M4A audio
yt-dlp -f "bv*[ext=mp4]+ba[ext=m4a]" "URL"

# 1080p or lower
yt-dlp -f "bv*[height<=1080]+ba" "URL"

# Best video under 100MB
yt-dlp -f "best[filesize<100M]" "URL"

# Prefer VP9 codec
yt-dlp -f "bv*[vcodec^=vp9]+ba" "URL"

# 720p with specific codec
yt-dlp -f "bv*[height=720][vcodec=avc1]+ba[acodec=mp4a]" "URL"
```

#### Complex Format Strings
```bash
# Fallback chain (try first, then second, etc.)
yt-dlp -f "137+140/136+140/135+140/134+140/133+140/best" "URL"

# Multiple conditions
yt-dlp -f "bv*[height<=720][fps<=30][ext=mp4]+ba[ext=m4a]" "URL"

# Prefer 60fps but accept 30fps
yt-dlp -f "bv[fps=60]/bv[fps=30]+ba" "URL"
```

---

## Advanced Features

### SponsorBlock Integration

```bash
# Skip sponsor segments
yt-dlp --sponsorblock-mark all "URL"

# Remove sponsor segments from file
yt-dlp --sponsorblock-remove sponsor,intro,outro "URL"

# Categories: sponsor, intro, outro, selfpromo, preview, filler, interaction, music_offtopic
```

### Metadata & Thumbnails

```bash
# Download with thumbnail
yt-dlp --write-thumbnail "URL"

# Embed thumbnail in video
yt-dlp --embed-thumbnail "URL"

# Save metadata to JSON
yt-dlp --write-info-json "URL"

# Embed metadata
yt-dlp --embed-metadata "URL"

# Add chapters
yt-dlp --embed-chapters "URL"
```

### Subtitles

```bash
# Download all subtitles
yt-dlp --write-subs --all-subs "URL"

# Specific languages
yt-dlp --write-subs --sub-langs "en,es,fr" "URL"

# Auto-generated only
yt-dlp --write-auto-subs "URL"

# Embed subtitles
yt-dlp --embed-subs "URL"

# Convert to SRT
yt-dlp --convert-subs srt "URL"
```

### Live Streams

```bash
# Download live stream
yt-dlp --hls-use-mpegts "LIVE_URL"

# From the start
yt-dlp --live-from-start "LIVE_URL"

# Wait for stream to start
yt-dlp --wait-for-video "SCHEDULED_STREAM_URL"
```

### Download Archive

```bash
# Track downloaded videos
yt-dlp --download-archive archive.txt "CHANNEL_URL"

# Skip if in archive
yt-dlp --download-archive archive.txt --no-overwrites "URL"
```

---

## Authentication & Cookies

### Method 1: Browser Cookies (Recommended)

```bash
# Use cookies from browser
yt-dlp --cookies-from-browser firefox "URL"
yt-dlp --cookies-from-browser chrome "URL"
yt-dlp --cookies-from-browser edge "URL"

# Specific profile
yt-dlp --cookies-from-browser chrome:Profile1 "URL"
```

### Method 2: Cookies File

```bash
# Export cookies using browser extension (e.g., "Get cookies.txt LOCALLY")
# Then use:
yt-dlp --cookies cookies.txt "URL"
```

### Method 3: Authentication

```bash
# Username/password
yt-dlp -u USERNAME -p PASSWORD "URL"

# Two-factor authentication
yt-dlp -u USERNAME -p PASSWORD --twofactor CODE "URL"

# Netrc file
yt-dlp --netrc "URL"
```

### Cookie Management Tips

1. **For Private/Member Content**:
```bash
# Use fresh cookies from incognito session
yt-dlp --cookies fresh_cookies.txt "PRIVATE_VIDEO_URL"
```

2. **Avoiding Detection**:
```bash
# Mimic browser behavior
yt-dlp --cookies cookies.txt \
       --add-header "User-Agent: Mozilla/5.0..." \
       --sleep-interval 5 \
       --max-sleep-interval 10 \
       "URL"
```

---

## Output Templates

### Basic Templates

```bash
# Default naming
yt-dlp -o "%(title)s.%(ext)s" "URL"

# With uploader
yt-dlp -o "%(uploader)s - %(title)s.%(ext)s" "URL"

# With date
yt-dlp -o "%(upload_date)s - %(title)s.%(ext)s" "URL"

# Organized in folders
yt-dlp -o "%(uploader)s/%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s" "URL"
```

### Available Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `%(title)s` | Video title | "My Video Title" |
| `%(id)s` | Video ID | "dQw4w9WgXcQ" |
| `%(uploader)s` | Channel name | "ChannelName" |
| `%(upload_date)s` | Upload date | "20240115" |
| `%(duration)s` | Duration in seconds | "213" |
| `%(view_count)s` | View count | "1000000" |
| `%(like_count)s` | Like count | "50000" |
| `%(playlist)s` | Playlist name | "My Playlist" |
| `%(playlist_index)s` | Position in playlist | "001" |
| `%(ext)s` | File extension | "mp4" |
| `%(resolution)s` | Video resolution | "1080p" |
| `%(fps)s` | Frame rate | "30" |
| `%(format_id)s` | Format code | "137+140" |

### Advanced Templates

```bash
# Clean filename (remove special chars)
yt-dlp -o "%(title)s-%(id)s.%(ext)s" --restrict-filenames "URL"

# Conditional templates
yt-dlp -o "%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s" \
       -o "pl_thumbnail:%(playlist)s/thumbnail.%(ext)s" "URL"

# Different templates for different types
yt-dlp -o "%(uploader)s/%(title)s.%(ext)s" \
       -o "thumbnail:%(uploader)s/%(title)s-thumb.%(ext)s" \
       -o "subtitle:%(uploader)s/%(title)s.%(ext)s" "URL"
```

---

## Post-Processing

### Format Conversion

```bash
# Convert to MP4
yt-dlp --merge-output-format mp4 "URL"

# Recode video
yt-dlp --recode-video mp4 "URL"

# Post-processor args
yt-dlp --postprocessor-args "ffmpeg:-c:v libx264 -crf 23" "URL"
```

### Audio Processing

```bash
# Extract and convert to MP3
yt-dlp -x --audio-format mp3 --audio-quality 0 "URL"

# Split by chapters
yt-dlp -x --split-chapters "URL"

# Keep original + extract audio
yt-dlp -k -x --audio-format mp3 "URL"
```

### Custom Post-Processing

```bash
# Run command after download
yt-dlp --exec "echo 'Downloaded: {}'" "URL"

# Move files after download
yt-dlp --exec "mv {} /path/to/destination/" "URL"

# Multiple commands
yt-dlp --exec "echo 'Processing...' && ffmpeg -i {} output.mp4" "URL"
```

---

## Configuration Files

### Location

- **Windows**: `%APPDATA%\yt-dlp\config.txt` or `C:\Users\USERNAME\yt-dlp.conf`
- **macOS/Linux**: `~/.config/yt-dlp/config` or `~/.yt-dlp.conf`
- **Portable**: `yt-dlp.conf` in same directory as executable

### Example Configuration

```ini
# ~/.config/yt-dlp/config

# Output
-o "~/Videos/%(uploader)s/%(title)s-%(id)s.%(ext)s"
--restrict-filenames

# Format Selection
-f "bv*[height<=1080]+ba/best[height<=1080]"
--merge-output-format mp4

# Subtitles
--write-subs
--sub-langs "en,es"
--convert-subs srt

# Metadata
--embed-metadata
--embed-thumbnail
--embed-chapters

# SponsorBlock
--sponsorblock-remove sponsor,intro

# Network
--concurrent-fragments 4
--retries 10
--fragment-retries 10

# Archive
--download-archive ~/Videos/.archive.txt

# Rate Limit (if needed)
# --limit-rate 1M

# Cookies (if needed)
# --cookies-from-browser firefox
```

### Per-Site Configuration

```ini
# YouTube specific
--match-filter "host~=youtube" -f "bestvideo[height<=1080]+bestaudio"

# Twitch specific  
--match-filter "host~=twitch" --hls-use-mpegts

# Twitter specific
--match-filter "host~=twitter" -f "best[protocol!*=m3u8]"
```

---

## Common Use Cases

### 1. YouTube Music/Podcast Archival

```bash
#!/bin/bash
# Download music playlist as MP3 with metadata

yt-dlp \
  -x --audio-format mp3 --audio-quality 0 \
  --embed-thumbnail \
  --embed-metadata \
  --sponsorblock-remove all \
  -o "~/Music/%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s" \
  --download-archive music-archive.txt \
  "PLAYLIST_URL"
```

### 2. Educational Content Download

```bash
# Download course with subtitles and organize by chapters

yt-dlp \
  -f "bv[height<=720]+ba" \
  --write-subs --sub-langs "en" --embed-subs \
  --write-info-json \
  --split-chapters \
  -o "~/Courses/%(playlist)s/%(chapter_number)s - %(chapter)s/%(title)s.%(ext)s" \
  "COURSE_URL"
```

### 3. Channel Backup

```bash
# Complete channel backup with metadata

yt-dlp \
  --write-info-json \
  --write-thumbnail \
  --write-subs --all-subs \
  --embed-metadata \
  --download-archive channel-archive.txt \
  -o "~/Backup/%(uploader)s/%(upload_date)s - %(title)s [%(id)s].%(ext)s" \
  "CHANNEL_URL/videos"
```

### 4. Live Stream Recording

```bash
# Record live stream from start

yt-dlp \
  --live-from-start \
  --hls-use-mpegts \
  -o "~/Streams/%(uploader)s - %(title)s [%(upload_date)s].%(ext)s" \
  --embed-metadata \
  --write-info-json \
  "LIVE_URL"
```

### 5. Batch Download with Quality Control

```bash
# batch-download.txt:
# URL1
# URL2
# URL3

yt-dlp \
  --batch-file batch-download.txt \
  -f "bv*[height<=1080][fps<=30]+ba/best[height<=1080]" \
  --merge-output-format mp4 \
  --concurrent-fragments 4 \
  --retries 10 \
  -o "~/Downloads/%(title)s.%(ext)s"
```

---

## Troubleshooting

### Common Issues & Solutions

#### 1. "Unable to extract video data"
```bash
# Update yt-dlp
python -m pip install -U yt-dlp

# Try with cookies
yt-dlp --cookies-from-browser firefox "URL"

# Use different extractor
yt-dlp --force-generic-extractor "URL"
```

#### 2. Slow Downloads
```bash
# Increase concurrent fragments
yt-dlp -N 8 "URL"

# Use aria2c for downloading
yt-dlp --downloader aria2c --downloader-args "aria2c:-x 16 -k 1M" "URL"
```

#### 3. 403 Forbidden Errors
```bash
# Add headers and cookies
yt-dlp \
  --cookies-from-browser chrome \
  --add-header "Referer: https://www.youtube.com/" \
  --add-header "User-Agent: Mozilla/5.0..." \
  "URL"
```

#### 4. Format Not Available
```bash
# List formats first
yt-dlp -F "URL"

# Use fallback format selection
yt-dlp -f "bv*+ba/best" "URL"
```

#### 5. Geo-Restriction
```bash
# Use proxy
yt-dlp --proxy "socks5://127.0.0.1:1080" "URL"

# Specific geo-bypass
yt-dlp --geo-bypass-country US "URL"
```

---

## Performance Optimization

### Speed Improvements

```bash
# Maximum performance settings
yt-dlp \
  -N 8 \                          # 8 concurrent fragments
  --concurrent-fragments 8 \      
  --buffer-size 16K \            
  --http-chunk-size 10M \        
  --downloader aria2c \          
  --downloader-args "aria2c:-x 16 -k 1M" \
  "URL"
```

### Resource Management

```bash
# Limit bandwidth
yt-dlp --limit-rate 500K "URL"

# Sleep between downloads
yt-dlp --sleep-interval 5 --max-sleep-interval 10 "URL"

# Limit simultaneous downloads
yt-dlp --max-downloads 10 "PLAYLIST_URL"
```

### Storage Optimization

```bash
# Avoid duplicates
yt-dlp --no-overwrites --download-archive archive.txt "URL"

# Clean up after merge
yt-dlp --keep-video false "URL"

# Compress on the fly
yt-dlp --postprocessor-args "ffmpeg:-c:v libx265 -crf 28" "URL"
```

---

## Legal & Ethical Considerations

### ⚖️ Legal Guidelines

1. **Copyright Compliance**
   - Only download content you have rights to
   - Respect Creative Commons licenses
   - Personal use vs. distribution rights

2. **Terms of Service**
   - Platform ToS may prohibit downloading
   - Educational/fair use exceptions vary by jurisdiction
   - Regional copyright laws differ

3. **Ethical Use Cases**
   - Personal backup of purchased content
   - Offline viewing where permitted
   - Educational content for research
   - Public domain materials
   - Creative Commons licensed content
   - Your own uploaded content

### ⚠️ Best Practices

1. **Respect Content Creators**
   - Support creators through official channels
   - Don't redistribute copyrighted content
   - Give proper attribution when required

2. **Rate Limiting**
   ```bash
   # Be respectful to servers
   yt-dlp --limit-rate 200K --sleep-interval 5 "URL"
   ```

3. **Privacy Considerations**
   - Use dedicated/secondary accounts for cookies
   - Rotate cookies regularly
   - Clear sensitive data from config files

---

## Quick Reference Cheat Sheet

### Most Used Commands

```bash
# Best quality video + audio
yt-dlp "URL"

# Audio only (MP3)
yt-dlp -x --audio-format mp3 "URL"

# Specific quality
yt-dlp -f "best[height<=720]" "URL"

# With subtitles
yt-dlp --write-subs --embed-subs "URL"

# Playlist with numbering
yt-dlp -o "%(playlist_index)s - %(title)s.%(ext)s" "PLAYLIST"

# Channel archival
yt-dlp --download-archive archive.txt "CHANNEL/videos"

# Live stream
yt-dlp --live-from-start "LIVE_URL"

# With authentication
yt-dlp --cookies-from-browser firefox "URL"

# Update yt-dlp
yt-dlp -U
```

### Format Selection Quick Reference

```bash
best            # Best single file
bestvideo       # Best video-only
bestaudio       # Best audio-only
worst           # Worst quality
bestvideo+bestaudio  # Best video + audio (may require merge)

# Common YouTube format IDs:
18   - 360p  MP4 (with audio)
22   - 720p  MP4 (with audio)
137  - 1080p MP4 (video only)
140  - M4A audio (128kbps)
251  - WebM audio (best)
```

---

## Conclusion

yt-dlp is the most powerful and versatile command-line media downloader available today. Its extensive feature set, active development, and broad platform support make it the go-to tool for:

- Content archival and preservation
- Offline viewing preparation
- Audio extraction and podcast downloading
- Educational content management
- Research and analysis

With proper understanding of its capabilities and responsible use, yt-dlp serves as an essential tool in the modern digital toolkit.

### Resources

- **GitHub**: https://github.com/yt-dlp/yt-dlp
- **Documentation**: https://github.com/yt-dlp/yt-dlp#readme
- **Supported Sites**: https://github.com/yt-dlp/yt-dlp/blob/master/supportedsites.md
- **Discord Community**: https://discord.gg/H5MNcFW63r

---

*Last Updated: January 2025*
*Version: Based on yt-dlp 2025.01.09*