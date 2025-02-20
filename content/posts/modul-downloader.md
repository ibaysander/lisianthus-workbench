---
date: '2025-02-06T10:09:33+07:00'
title: 'Universitas Terbuka Modul Downloader Into Image'
# weight: 1
# aliases: ["/first"]
tags: ["kuliah"]
author: "lisianthus"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Desc Text."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: false
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---
This PowerShell script is designed to download pages from a document hosted on a web service, save them as PNG files, and handle various edge cases such as retries, existing files, and small files. Below is a breakdown of the script's functionality and how it works:

---

### **Key Components of the Script**

1. **`Download-Page` Function**:
   - Downloads a single page from a given URL with retry logic.
   - Skips downloading if the file already exists and is larger than 10KB.
   - Uses `Invoke-WebRequest` to fetch the page and save it as a PNG file.
   - Retries up to `$MaxRetries` times if the download fails, with a delay of `$RetryDelay` seconds between attempts.

2. **`Process-Document` Function**:
   - Processes a single document by iterating through its pages.
   - Constructs the URL for each page using the `doc` parameter, `subfolder`, and `page` number.
   - Checks if the file already exists and is valid before attempting to download.
   - Stops processing if it encounters a series of small files (less than 10KB) or if the download fails after retries.
   - Tracks the number of downloaded pages, existing files, and small files.

3. **Main Script Execution**:
   - Prompts the user for a `subfolder` name (e.g., `MSIM4309`).
   - Creates an output directory for the downloaded files.
   - Loops through a range of `doc` parameters (e.g., `M1` to `M11`) and processes each document using the `Process-Document` function.
   - Stops the script if no pages are downloaded for a document or if the script reaches the end of the document range.

---

### **How the Script Works**

1. **User Input**:
   - The script prompts the user to enter a `subfolder` name, which is used to construct the URLs and organize the output files.

2. **Output Directory**:
   - The script creates a directory named after the `subfolder` to store the downloaded PNG files. If the directory already exists, it uses the existing one.

3. **Document Processing**:
   - The script loops through a predefined range of `doc` parameters (e.g., `M1` to `M11`).
   - For each document, it constructs the URL for each page and attempts to download it using the `Download-Page` function.
   - If a file already exists and is valid (larger than 10KB), it skips the download.
   - If a file is smaller than 10KB, it removes the file and increments a counter. If the counter reaches a threshold (`$maxSmallFiles`), the script stops processing the current document.

4. **Retry Logic**:
   - If a download fails, the script retries up to `$MaxRetries` times with a delay of `$RetryDelay` seconds between attempts.
   - If all retries fail, the script logs the error and moves on to the next page or document.

5. **Stopping Conditions**:
   - The script stops processing a document if it encounters too many small files or if the download fails after retries.
   - If no pages are downloaded for a document and the script fails in the first few pages, it stops the entire script.

6. **Completion**:
   - Once all documents are processed, the script outputs a completion message and indicates where the files are saved.

---

### **Example Usage**

1. **Input**:
   - When prompted, enter a subfolder name, e.g., `MSIM4309`.

2. **Output**:
   - The script creates a folder named `MSIM4309` and downloads pages for documents `M1` to `M11` (or the specified range).
   - Each page is saved as a PNG file with a filename like `M1_Page_1.png`, `M1_Page_2.png`, etc.

3. **Logs**:
   - The script logs its progress, including skipped files, successful downloads, and errors.

---

### **Customization**

- **PHPSESSID**:
  - Update the `PHPSESSID` in the `$headers` hashtable if necessary. This is required for authentication with the web service.

- **Range of `doc` Parameters**:
  - Adjust the `$startNumber` and `$endNumber` variables to process a different range of documents.

- **Retry Settings**:
  - Modify `$MaxRetries` and `$RetryDelay` in the `Download-Page` function to change the retry behavior.

- **Small File Threshold**:
  - Adjust the `$maxSmallFiles` variable in the `Process-Document` function to change the number of consecutive small files allowed before stopping.

---

### **Error Handling**

- The script handles errors such as failed downloads, invalid files, and missing directories.
- It logs errors and provides clear messages to the user.

---

### **Output Example**

```
Enter the subfolder name (e.g., MSIM4309): MSIM4309
Created directory: MSIM4309

--- Processing document: M1 ---
⏭️ File already exists and is valid: MSIM4309\M1_Page_1.png
✅ Successfully downloaded M1 Page 2
⚠️ Page 3 is less than 10KB (512 bytes)
✅ Successfully downloaded M1 Page 4
...

✅ Download process completed for doc 'M1'.
Total pages: 10 (2 existing, 8 new)

--- Processing document: M2 ---
...

✅ Script completed. All files are saved in the 'MSIM4309' folder.
```