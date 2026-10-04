# YouTube Trending Data Cleaning

Cleaning real YouTube trending data collected via the YouTube API: 674,141 records, 93 countries, January–July 2025.

**Dataset:** [Youtube Trending Videos 2025 updated daily](https://www.kaggle.com/datasets/sebastianbesinski/youtube-trending-videos-2025-updated-daily) (Kaggle, by sebastianbesinski)

**Tools:** Python, Pandas, NumPy, Google Colab

> Note: YouTube retired its Trending page in July 2025, so this dataset ends in early July 2025. The cleaning logic applies to any YouTube API data with the same fields.

## Pipeline

Raw Data (674,141 rows, 9 columns) → Inspection → Cleaning → Validation → Clean Dataset (674,141 rows, 9 columns)

No rows were removed. All fixes were made in place.

## Cleaning Steps

### 1. Missing values
- No NaN values in any column.
- Checked separately for empty and whitespace-only strings (not detected by `.info()`): none found.

### 2. Duplicates
- Full duplicate rows: **0**.
- A `video_id` appears many times by design: the same video trended on several days and in several countries.
- The unique key is **`video_id + country + trending_date`**: 0 duplicates.

### 3. Changed video titles
- **888 videos** have more than one title across records (renamed by the creator or localized by country).
- Kept as is: each record shows the title as it was on that date in that country.

### 4. Dates and time zones
- `published_at` (`2025-02-10T03:22:55Z`, UTC) converted to datetime with time zone.
- `fetch_date` (no time zone) converted to datetime and renamed to `trending_date`.
- Logic check: no video appears in trending before its publish date (0 violations).

### 5. Hidden statistics
- Zeros in `views` (382), `likes` (8,698) and `comments` (7,185) replaced with NaN.
- For trending videos, zero means hidden likes, disabled comments or missing data, not a real zero. Example: a video with 0 views but 220,838 likes.

### 6. Text cleanup
- Leading/trailing spaces removed: 11 titles, 15,117 channel records.
- Titles: line breaks (14), double spaces (24,221) and invisible zero-width characters `\u200b` (2,443) removed.
- Channels: double spaces (1,159) removed.

### 7. Standardization and structure
- `country`: 93 valid two-letter codes, no changes needed.
- `video_id`: all IDs have the valid 11-character format.
- Columns reordered: video info → trending info → statistics.

## Assumptions
- `fetch_date` / `trending_date` is assumed to be in UTC (not documented in the source).
- 10 channel names differ only by letter case (e.g. `COLORS` / `Colors`). Kept unchanged: without a channel ID, it is not possible to confirm they are the same channel.

## Validation

| Check | Result |
|---|---|
| Rows before / after | 674,141 / 674,141 |
| Full duplicates | 0 |
| Duplicate video + country + date | 0 |
| Negative values | 0 |
| Invalid `video_id` length | 0 |
| Leading/trailing spaces in titles | 0 |
| Published after trending | 0 |

## Files
- `youtube_data_cleaning.ipynb` — full cleaning process
- `youtube_trending_clean_US.xlsx` — clean sample, US only (7,349 rows)

The full clean CSV (~100 MB) exceeds GitHub's file size limit. It can be reproduced by downloading the source dataset and running the notebook.

## Next Steps
This project will be extended with the YouTube Data API and automation: collecting channel data directly from YouTube and cleaning it automatically with the same logic.
