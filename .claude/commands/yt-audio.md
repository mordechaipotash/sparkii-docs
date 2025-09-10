# /yt-audio - YouTube Channel Audio Downloader

## Command Usage

### Basic Commands
```bash
/yt-audio --download <channel>     # Download audio from a YouTube channel
/yt-audio --list                   # Show this help
/yt-audio --status                 # Check download progress
/yt-audio --channels               # List configured channels
/yt-audio --setup <channel>        # Set up new channel for download
```

### Advanced Options
```bash
/yt-audio --download <channel> --reverse    # Download oldest videos first
/yt-audio --download <channel> --latest <n> # Download only latest n videos
/yt-audio --download <channel> --quality <q> # Set audio quality (worst/best)
/yt-audio --archive <channel>              # Show downloaded videos archive
/yt-audio --clear-archive <channel>        # Clear archive to re-download
```

## 📥 Quick Download Commands

### Greg Isenberg (Default)
```bash
# Download all Greg Isenberg videos as MP3 (newest first)
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  --download-archive ~/Downloads/GregIsenberg/archive.txt \
  --sleep-interval 3 \
  --sleep-requests 1.5 \
  --cookies-from-browser chrome \
  -o "~/Downloads/GregIsenberg/%(title)s.%(ext)s" \
  https://www.youtube.com/@GregIsenberg/videos

# Download with reverse order (oldest first)
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  --playlist-reverse \
  --download-archive ~/Downloads/GregIsenberg/archive.txt \
  --sleep-interval 3 \
  --sleep-requests 1.5 \
  --cookies-from-browser chrome \
  -o "~/Downloads/GregIsenberg/%(title)s.%(ext)s" \
  https://www.youtube.com/@GregIsenberg/videos
```

## 🎯 Channel Configurations

### Pre-configured Channels
```yaml
GregIsenberg:
  url: https://www.youtube.com/@GregIsenberg/videos
  folder: ~/Downloads/GregIsenberg
  quality: worstaudio
  format: mp3

YCombinator:
  url: https://www.youtube.com/@ycombinator/videos
  folder: ~/Downloads/YCombinator
  quality: worstaudio
  format: mp3

LexFridman:
  url: https://www.youtube.com/@lexfridman/videos
  folder: ~/Downloads/LexFridman
  quality: worstaudio
  format: mp3

TimFerriss:
  url: https://www.youtube.com/@timferriss/videos
  folder: ~/Downloads/TimFerriss
  quality: worstaudio
  format: mp3
```

## 🔧 Complete Command Templates

### Standard Download (Newest First)
```bash
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  --download-archive ~/Downloads/{CHANNEL}/archive.txt \
  --sleep-interval 3 \
  --sleep-requests 1.5 \
  --cookies-from-browser chrome \
  -o "~/Downloads/{CHANNEL}/%(title)s.%(ext)s" \
  {CHANNEL_URL}
```

### Reverse Order (Oldest First)
```bash
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  --playlist-reverse \
  --download-archive ~/Downloads/{CHANNEL}/archive.txt \
  --sleep-interval 3 \
  --sleep-requests 1.5 \
  --cookies-from-browser chrome \
  -o "~/Downloads/{CHANNEL}/%(title)s.%(ext)s" \
  {CHANNEL_URL}
```

### Latest N Videos Only
```bash
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  --playlist-end {N} \
  --download-archive ~/Downloads/{CHANNEL}/archive.txt \
  --sleep-interval 3 \
  --sleep-requests 1.5 \
  --cookies-from-browser chrome \
  -o "~/Downloads/{CHANNEL}/%(title)s.%(ext)s" \
  {CHANNEL_URL}
```

### With Metadata & Thumbnails
```bash
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  --add-metadata \
  --embed-thumbnail \
  --download-archive ~/Downloads/{CHANNEL}/archive.txt \
  --sleep-interval 3 \
  --sleep-requests 1.5 \
  --cookies-from-browser chrome \
  -o "~/Downloads/{CHANNEL}/%(title)s.%(ext)s" \
  {CHANNEL_URL}
```

## ⚙️ Command Options Explained

### Quality Options
- `worstaudio` - Smallest file size, acceptable quality
- `bestaudio` - Best available quality
- `140` - M4A 128kbps (YouTube standard)
- `251` - WebM Opus (often best quality)

### Format Options
- `mp3` - Universal compatibility
- `m4a` - Better quality, Apple-friendly
- `opus` - Best compression/quality ratio
- `flac` - Lossless (if available)

### Rate Limiting (Avoid Bans)
- `--sleep-interval 3` - Wait 3 seconds between videos
- `--sleep-requests 1.5` - Wait 1.5 seconds between requests
- `--limit-rate 50K` - Limit download speed to 50KB/s

### Archive Management
- `--download-archive` - Track downloaded videos
- `--break-on-existing` - Stop when finding already downloaded
- `--force-overwrites` - Re-download existing files

## 📁 Directory Setup Script

```bash
#!/bin/bash
# Setup directories for channel downloads

setup_channel() {
  CHANNEL=$1
  mkdir -p ~/Downloads/$CHANNEL
  touch ~/Downloads/$CHANNEL/archive.txt
  echo "✅ Created directory: ~/Downloads/$CHANNEL"
}

# Setup common channels
setup_channel "GregIsenberg"
setup_channel "YCombinator"
setup_channel "LexFridman"
setup_channel "TimFerriss"
```

## 🚀 Quick Start Examples

### 1. Download Greg Isenberg's Latest 10 Videos
```bash
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  --playlist-end 10 \
  --download-archive ~/Downloads/GregIsenberg/archive.txt \
  --sleep-interval 3 \
  -o "~/Downloads/GregIsenberg/%(title)s.%(ext)s" \
  https://www.youtube.com/@GregIsenberg/videos
```

### 2. Download All Y Combinator Podcasts (Oldest First)
```bash
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  --playlist-reverse \
  --download-archive ~/Downloads/YCombinator/archive.txt \
  --sleep-interval 3 \
  -o "~/Downloads/YCombinator/%(upload_date)s-%(title)s.%(ext)s" \
  https://www.youtube.com/@ycombinator/videos
```

### 3. Download with Better Naming (Date Prefix)
```bash
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  --download-archive ~/Downloads/GregIsenberg/archive.txt \
  --sleep-interval 3 \
  -o "~/Downloads/GregIsenberg/%(upload_date)s-%(title)s.%(ext)s" \
  https://www.youtube.com/@GregIsenberg/videos
```

## 🔍 Useful Queries

### Check What Would Be Downloaded (Dry Run)
```bash
yt-dlp --simulate \
  --download-archive ~/Downloads/GregIsenberg/archive.txt \
  https://www.youtube.com/@GregIsenberg/videos
```

### List Video Titles Only
```bash
yt-dlp --get-title \
  --playlist-end 10 \
  https://www.youtube.com/@GregIsenberg/videos
```

### Get Video Info as JSON
```bash
yt-dlp --dump-json \
  --playlist-end 1 \
  https://www.youtube.com/@GregIsenberg/videos | jq '{title, upload_date, duration}'
```

## 📊 Archive Management

### View Downloaded Videos
```bash
# Show archive contents
cat ~/Downloads/GregIsenberg/archive.txt | wc -l  # Count
cat ~/Downloads/GregIsenberg/archive.txt           # List IDs

# Get titles of archived videos
while read id; do
  yt-dlp --get-title "https://youtube.com/watch?v=$id" 2>/dev/null
done < ~/Downloads/GregIsenberg/archive.txt
```

### Clear Archive (Re-download All)
```bash
# Backup first!
cp ~/Downloads/GregIsenberg/archive.txt ~/Downloads/GregIsenberg/archive.backup.txt

# Clear archive
> ~/Downloads/GregIsenberg/archive.txt
```

### Merge Archives
```bash
# Combine multiple archives
cat ~/Downloads/*/archive.txt | sort -u > ~/Downloads/all_archives.txt
```

## 🐛 Troubleshooting

### Common Issues

#### 1. Cookie Problems
```bash
# Use different browser
--cookies-from-browser firefox
--cookies-from-browser safari
--cookies-from-browser edge

# Or export cookies manually
--cookies cookies.txt
```

#### 2. Rate Limited
```bash
# Increase delays
--sleep-interval 10
--sleep-requests 5
--limit-rate 25K
```

#### 3. Format Not Available
```bash
# List available formats
yt-dlp -F {URL}

# Choose specific format
yt-dlp -f 140 {URL}  # Specific format ID
```

#### 4. Geographic Restrictions
```bash
# Use proxy
--proxy socks5://127.0.0.1:9050/
```

## ⚡ Pro Tips

### 1. Parallel Downloads (Multiple Channels)
```bash
# Download multiple channels simultaneously
yt-dlp {OPTIONS} {CHANNEL1_URL} &
yt-dlp {OPTIONS} {CHANNEL2_URL} &
wait
```

### 2. Resume Interrupted Downloads
```bash
# yt-dlp automatically resumes partial downloads
# Just run the same command again
```

### 3. Download Playlists Instead of All Videos
```bash
# Specific playlist
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  "https://www.youtube.com/playlist?list=PLAYLIST_ID"
```

### 4. Filter by Upload Date
```bash
# Videos from last 30 days
yt-dlp -f "worstaudio" -x --audio-format mp3 \
  --dateafter 20240101 \
  --datebefore 20241231 \
  {URL}
```

### 5. Custom Filename Template
```bash
# Organized by year/month
-o "~/Downloads/%(channel)s/%(upload_date>%Y)s/%(upload_date>%m)s/%(title)s.%(ext)s"
```

## 📝 Batch Processing Script

```bash
#!/bin/bash
# batch-download.sh - Download from multiple channels

CHANNELS=(
  "GregIsenberg|https://www.youtube.com/@GregIsenberg/videos"
  "YCombinator|https://www.youtube.com/@ycombinator/videos"
  "LexFridman|https://www.youtube.com/@lexfridman/videos"
)

for channel_data in "${CHANNELS[@]}"; do
  IFS='|' read -r channel url <<< "$channel_data"
  
  echo "📥 Downloading $channel..."
  
  mkdir -p ~/Downloads/$channel
  
  yt-dlp -f "worstaudio" -x --audio-format mp3 \
    --download-archive ~/Downloads/$channel/archive.txt \
    --sleep-interval 3 \
    --sleep-requests 1.5 \
    --cookies-from-browser chrome \
    -o "~/Downloads/$channel/%(title)s.%(ext)s" \
    "$url"
    
  echo "✅ Finished $channel"
done
```

## 🎯 Command Summary

```bash
# Quick download Greg Isenberg
/yt-audio --download GregIsenberg

# Download with options
/yt-audio --download GregIsenberg --reverse --latest 10

# Check status
/yt-audio --status GregIsenberg

# Setup new channel
/yt-audio --setup MyNewChannel

# List all configured channels
/yt-audio --channels
```

---

Remember: Always respect content creators and YouTube's Terms of Service. Use rate limiting to avoid overwhelming servers.

Last Updated: 2025-09-08