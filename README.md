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

## Example

### Modifying an Existing Script Using Chrome DevTools

If the website already includes a script, and you want to append this custom code to it, follow these steps:

1. **Open Chrome DevTools**: Right-click on the webpage and select "Inspect" to open Chrome DevTools.

2. **Go to the Sources Tab**: In DevTools, navigate to the "Sources" tab.

3. **Find the Script to Modify**: Locate the existing script you want to modify in the "Sources" panel. It will usually be listed under the website's domain.

4. **Edit the Script**:
    - Once you find the script, click on it to view its content.
    - Scroll to the bottom of the script and paste your custom code below the existing code.

    Example:
    ```javascript
    // Existing website js blah blah script code...

    // Add the following custom code at the end of the file:
    function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    async function downloadAndConcatTSFiles(m3u8Url, outputFileName = 'downloaded.mp4') {
        try {
            const masterPlaylistResponse = await fetch(m3u8Url);
            const masterPlaylistText = await masterPlaylistResponse.text();

            const resolutionM3u8Match = masterPlaylistText.match(/(index_\d+p\.m3u8|chunklist_.+\.m3u8|.+\.m3u8)/);
            if (!resolutionM3u8Match) {
                console.error("No matching resolution playlist found.");
                return;
            }

            const resolutionM3u8Url = new URL(resolutionM3u8Match[0], m3u8Url).href;
            const playlistResponse = await fetch(resolutionM3u8Url);
            const playlistText = await playlistResponse.text();

            const tsFiles = playlistText.split('\n').map(line => line.trim()).filter(line => line && !line.startsWith('#') && line.endsWith('.ts'));
            if (!tsFiles.length) {
                console.error("No TS files found in the playlist.");
                return;
            }

            console.log("TS Files:", tsFiles);

            const videoBuffers = [];
            for (const file of tsFiles) {
                const tsResponse = await fetch(new URL(file, resolutionM3u8Url).href);
                videoBuffers.push(await tsResponse.arrayBuffer());

                // Handle rate limiting by adding a delay between requests
                await sleep(1000);
            }

            const totalLength = videoBuffers.reduce((acc, buffer) => acc + buffer.byteLength, 0);
            const combinedBuffer = new Uint8Array(totalLength);
            let offset = 0;
            videoBuffers.forEach((buffer) => {
                combinedBuffer.set(new Uint8Array(buffer), offset);
                offset += buffer.byteLength;
            });

            const blob = new Blob([combinedBuffer], { type: 'video/mp4' });
            const url = URL.createObjectURL(blob);

            const a = document.createElement('a');
            a.href = url;
            a.download = outputFileName;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
            console.log("Download complete!");
        } catch (error) {
            console.error("An error occurred:", error);
        }
    }

    async function findAndProcessM3u8() {
        const pageContent = document.documentElement.outerHTML;
        const m3u8Match = pageContent.match(/https?:\/\/[^\s"']+\.m3u8/);
        if (m3u8Match) {
            console.log("Detected m3u8 URL:", m3u8Match[0]);
            await downloadAndConcatTSFiles(m3u8Match[0]);
        } else {
            console.error("No m3u8 URL found on this page.");
        }
    }

    findAndProcessM3u8();
    ```

5. **Save the Changes**: After pasting the new code at the end of the file, press `Ctrl + S` (Windows/Linux) or `Cmd + S` (Mac) to save the changes.

6. **Reload the Page**: Reload the webpage to see your modified script in action. The script will automatically search for an M3U8 URL on the page and, if found, will download and concatenate the TS files into an MP4 file.

7. **Download the Video**: The video will be automatically downloaded with the specified file name once the process completes.

## Functions

### `sleep(ms)`
Pauses execution for a given number of milliseconds.

### `downloadAndConcatTSFiles(m3u8Url, outputFileName = 'downloaded.mp4')`
Downloads and concatenates TS files from the specified M3U8 URL, then saves them as an MP4 file.

### `findAndProcessM3u8()`
Searches the current webpage for an M3U8 URL and initiates the download process if found.

## Considerations

- **Rate Limiting**: The script includes a delay (`sleep(1000)`) between each TS file download to avoid hitting rate limits.
- **Error Handling**: Basic error handling is included to log issues encountered during the download process.

## License

This script is provided as-is without any warranties. Use it at your own risk.
