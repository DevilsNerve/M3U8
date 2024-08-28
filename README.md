# Download and Concatenate TS Files from M3U8 Playlist

This JavaScript script allows you to download and concatenate `.ts` video files from an M3U8 playlist and save them as a single `.mp4` file.

## Table of Contents

- [Usage](#usage)
- [Functions](#functions)
- [Example](#example)
- [Considerations](#considerations)
- [License](#license)

## Usage

The script consists of three main functions:

1. **sleep(ms)**: Pauses execution for a specified time (in milliseconds).

2. **downloadAndConcatTSFiles(m3u8Url, outputFileName = 'downloaded.mp4')**: Downloads and concatenates TS files from the specified M3U8 URL and saves them as an MP4 file.

3. **findAndProcessM3u8()**: Scans the current webpage for an M3U8 URL and triggers the download and concatenation process.

### Running the Script

1. Open your browser's developer console.
2. Paste the entire script into the console.
3. Press Enter to run the script.
4. If an M3U8 URL is found, the TS files will be downloaded, concatenated, and saved as an MP4 file.

## Functions

### `sleep(ms)`
Pauses execution for a given number of milliseconds.

### `downloadAndConcatTSFiles(m3u8Url, outputFileName = 'downloaded.mp4')`
Downloads and concatenates TS files from the specified M3U8 URL, then saves them as an MP4 file.

### `findAndProcessM3u8()`
Searches the current webpage for an M3U8 URL and initiates the download process if found.

## Example

To use this script on a webpage with an embedded HLS stream:

1. Open the browser's developer console.
2. Paste the entire script into the console.
3. Press Enter to execute.

If an M3U8 URL is detected, the TS files will be downloaded, concatenated, and saved as an MP4 file.

## Considerations

- **Rate Limiting**: The script includes a delay (`sleep(1000)`) between each TS file download to avoid hitting rate limits.
- **Cross-Origin Restrictions**: The script may not work on pages with CORS restrictions, as browsers may block cross-origin requests to the M3U8 file.
- **Error Handling**: Basic error handling is included to log issues encountered during the download process.

## License

This script is provided as-is without any warranties. Use it at your own risk.
