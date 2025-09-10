# /yt-search - Simple YouTube Search & Download

## Usage
```bash
/yt-search "search term"         # Search and show counts by time period
/yt-search "search term" --24h   # Download MP3s from last 24 hours
/yt-search "search term" --3d    # Download MP3s from last 3 days  
/yt-search "search term" --7d    # Download MP3s from last 7 days
/yt-search "search term" --30d   # Download MP3s from last 30 days
/yt-search "search term" --1y    # Download MP3s from last year
```

## Example: Search "GLM Coding"

### Step 1: Get Counts
```bash
# Search and show video counts by time period
/yt-search "GLM Coding"

# Output:
📊 Results for "GLM Coding":
• Last 24 hours: 2 videos
• Last 3 days: 5 videos  
• Last 7 days: 12 videos
• Last 30 days: 45 videos
• Last year: 127 videos

Download options:
  /yt-search "GLM Coding" --24h   (2 videos)
  /yt-search "GLM Coding" --3d    (5 videos)
  /yt-search "GLM Coding" --7d    (12 videos)
  /yt-search "GLM Coding" --30d   (45 videos)
  /yt-search "GLM Coding" --1y    (127 videos)
```

### Step 2: Download MP3s
```bash
# Download all videos from last 7 days as MP3
/yt-search "GLM Coding" --7d
```

## Core Commands

### Get Video Counts by Time Period
```bash
# Function to get counts
get_counts() {
  SEARCH="$1"
  TODAY=$(date +%Y%m%d)
  DAY_AGO=$(date -d "1 day ago" +%Y%m%d)
  DAYS_3_AGO=$(date -d "3 days ago" +%Y%m%d)
  WEEK_AGO=$(date -d "7 days ago" +%Y%m%d)
  MONTH_AGO=$(date -d "30 days ago" +%Y%m%d)
  YEAR_AGO=$(date -d "1 year ago" +%Y%m%d)
  
  echo "📊 Searching for: $SEARCH"
  
  # Get all results and filter by date
  yt-dlp "ytsearch100:$SEARCH" --get-id --get-title --print "%(upload_date)s|%(id)s|%(title)s" 2>/dev/null > /tmp/yt_results.txt
  
  # Count by time period
  COUNT_24H=$(awk -F'|' -v date="$DAY_AGO" '$1 >= date' /tmp/yt_results.txt | wc -l)
  COUNT_3D=$(awk -F'|' -v date="$DAYS_3_AGO" '$1 >= date' /tmp/yt_results.txt | wc -l)
  COUNT_7D=$(awk -F'|' -v date="$WEEK_AGO" '$1 >= date' /tmp/yt_results.txt | wc -l)
  COUNT_30D=$(awk -F'|' -v date="$MONTH_AGO" '$1 >= date' /tmp/yt_results.txt | wc -l)
  COUNT_1Y=$(awk -F'|' -v date="$YEAR_AGO" '$1 >= date' /tmp/yt_results.txt | wc -l)
  
  echo "• Last 24 hours: $COUNT_24H videos"
  echo "• Last 3 days: $COUNT_3D videos"
  echo "• Last 7 days: $COUNT_7D videos"
  echo "• Last 30 days: $COUNT_30D videos"
  echo "• Last year: $COUNT_1Y videos"
}
```

### Download MP3s by Time Period
```bash
# Download last 24 hours
yt-dlp "ytsearch50:$SEARCH" \
  --match-filter "upload_date >= $(date -d '1 day ago' +%Y%m%d)" \
  -f "worstaudio" -x --audio-format mp3 \
  -o "~/Downloads/YouTube/%(upload_date)s-%(title)s.%(ext)s"

# Download last 7 days
yt-dlp "ytsearch50:$SEARCH" \
  --match-filter "upload_date >= $(date -d '7 days ago' +%Y%m%d)" \
  -f "worstaudio" -x --audio-format mp3 \
  -o "~/Downloads/YouTube/%(upload_date)s-%(title)s.%(ext)s"

# Download last 30 days
yt-dlp "ytsearch100:$SEARCH" \
  --match-filter "upload_date >= $(date -d '30 days ago' +%Y%m%d)" \
  -f "worstaudio" -x --audio-format mp3 \
  -o "~/Downloads/YouTube/%(upload_date)s-%(title)s.%(ext)s"
```

## Complete Script

```bash
#!/bin/bash
# yt-search.sh - Simple YouTube search and download

SEARCH_TERM="$1"
TIME_PERIOD="$2"

# Create download directory
mkdir -p ~/Downloads/YouTube

if [ -z "$TIME_PERIOD" ]; then
  # Just show counts
  echo "📊 Results for \"$SEARCH_TERM\":"
  
  # Get video list
  yt-dlp "ytsearch100:$SEARCH_TERM" --print "%(upload_date)s" 2>/dev/null > /tmp/dates.txt
  
  # Calculate dates
  TODAY=$(date +%Y%m%d)
  DAY_AGO=$(date -d "1 day ago" +%Y%m%d)
  DAYS_3=$(date -d "3 days ago" +%Y%m%d)
  WEEK=$(date -d "7 days ago" +%Y%m%d)
  MONTH=$(date -d "30 days ago" +%Y%m%d)
  YEAR=$(date -d "1 year ago" +%Y%m%d)
  
  # Count videos
  COUNT_24H=$(awk -v d="$DAY_AGO" '$1 >= d' /tmp/dates.txt | wc -l)
  COUNT_3D=$(awk -v d="$DAYS_3" '$1 >= d' /tmp/dates.txt | wc -l)
  COUNT_7D=$(awk -v d="$WEEK" '$1 >= d' /tmp/dates.txt | wc -l)
  COUNT_30D=$(awk -v d="$MONTH" '$1 >= d' /tmp/dates.txt | wc -l)
  COUNT_1Y=$(awk -v d="$YEAR" '$1 >= d' /tmp/dates.txt | wc -l)
  
  echo "• Last 24 hours: $COUNT_24H videos"
  echo "• Last 3 days: $COUNT_3D videos"
  echo "• Last 7 days: $COUNT_7D videos"
  echo "• Last 30 days: $COUNT_30D videos"
  echo "• Last year: $COUNT_1Y videos"
  echo ""
  echo "Download options:"
  echo "  /yt-search \"$SEARCH_TERM\" --24h   ($COUNT_24H videos)"
  echo "  /yt-search \"$SEARCH_TERM\" --3d    ($COUNT_3D videos)"
  echo "  /yt-search \"$SEARCH_TERM\" --7d    ($COUNT_7D videos)"
  echo "  /yt-search \"$SEARCH_TERM\" --30d   ($COUNT_30D videos)"
  echo "  /yt-search \"$SEARCH_TERM\" --1y    ($COUNT_1Y videos)"
  
else
  # Download based on time period
  case "$TIME_PERIOD" in
    --24h)
      DATE_FILTER=$(date -d "1 day ago" +%Y%m%d)
      echo "📥 Downloading videos from last 24 hours..."
      ;;
    --3d)
      DATE_FILTER=$(date -d "3 days ago" +%Y%m%d)
      echo "📥 Downloading videos from last 3 days..."
      ;;
    --7d)
      DATE_FILTER=$(date -d "7 days ago" +%Y%m%d)
      echo "📥 Downloading videos from last 7 days..."
      ;;
    --30d)
      DATE_FILTER=$(date -d "30 days ago" +%Y%m%d)
      echo "📥 Downloading videos from last 30 days..."
      ;;
    --1y)
      DATE_FILTER=$(date -d "1 year ago" +%Y%m%d)
      echo "📥 Downloading videos from last year..."
      ;;
    *)
      echo "Invalid time period. Use: --24h, --3d, --7d, --30d, or --1y"
      exit 1
      ;;
  esac
  
  # Download MP3s
  yt-dlp "ytsearch100:$SEARCH_TERM" \
    --match-filter "upload_date >= $DATE_FILTER" \
    -f "worstaudio" -x --audio-format mp3 \
    --sleep-interval 2 \
    -o "~/Downloads/YouTube/$SEARCH_TERM/%(upload_date)s-%(title)s.%(ext)s"
    
  echo "✅ Download complete! Files saved to ~/Downloads/YouTube/$SEARCH_TERM/"
fi
```

## Quick Examples

### Search for "AI Coding"
```bash
/yt-search "AI Coding"
# Shows counts, then choose:
/yt-search "AI Coding" --7d  # Download last week's videos
```

### Search for "Sam Altman"
```bash
/yt-search "Sam Altman"
# Shows counts, then choose:
/yt-search "Sam Altman" --24h  # Download last 24 hours
```

### Search for "Startup advice"
```bash
/yt-search "Startup advice"
# Shows counts, then choose:
/yt-search "Startup advice" --30d  # Download last month
```

## One-Line Commands

### Just get counts
```bash
yt-dlp "ytsearch50:GLM Coding" --print "%(upload_date)s" | \
  awk -v today=$(date +%Y%m%d) -v day=$(date -d "1 day ago" +%Y%m%d) -v week=$(date -d "7 days ago" +%Y%m%d) -v month=$(date -d "30 days ago" +%Y%m%d) \
  'BEGIN {c24=0; c7=0; c30=0} 
   $1>=day {c24++} 
   $1>=week {c7++} 
   $1>=month {c30++} 
   END {print "24h:", c24, "| 7d:", c7, "| 30d:", c30}'
```

### Download last 7 days as MP3
```bash
yt-dlp "ytsearch50:GLM Coding" \
  --match-filter "upload_date >= $(date -d '7 days ago' +%Y%m%d)" \
  -f "worstaudio" -x --audio-format mp3 \
  -o "~/Downloads/YouTube/%(title)s.mp3"
```

---

That's it! Simple search → Show counts → Download by time period.

Last Updated: 2025-09-08