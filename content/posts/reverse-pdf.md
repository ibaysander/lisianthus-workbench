---
title: "Reverse PDF Universitas Terbuka Modul"
date: 2025-02-04T07:39:28+00:00
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
Below, I'll guide you through the entire process step-by-step, covering:

1. **Setting Up Your Environment**
2. **Installing Required Software and Python Packages**
3. **Understanding the Workflow**
4. **Writing the Python Script**
5. **Enhancements and Best Practices**
6. **Testing and Troubleshooting**

Let’s get started!

---

## **1. Setting Up Your Environment**

Before diving into coding, ensure that your development environment is correctly set up.

### **1.1. Install Python**

If you haven't installed Python yet:

- **Download Python:**
  - Visit the [official Python website](https://www.python.org/downloads/) and download the latest stable release.
  - It's recommended to install Python 3.7 or higher.

- **During Installation:**
  - **Windows:** Ensure you check the box **"Add Python to PATH"**. This makes it easier to run Python and pip from the command line.
  - Follow the prompts to complete the installation.

- **Verify Installation:**

  Open **Command Prompt** or **PowerShell** and run:

  ```bash
  python --version
  ```

  You should see something like:

  ```
  Python 3.11.4
  ```

### **1.2. Install an Integrated Development Environment (IDE)**

While not mandatory, an IDE can significantly enhance your productivity. Here are some popular choices:

- **[Visual Studio Code](https://code.visualstudio.com/):** Lightweight and highly customizable.
- **[PyCharm](https://www.jetbrains.com/pycharm/):** Feature-rich, especially for larger projects.
- **[Atom](https://atom.io/):** Simple and hackable text editor.

*For this guide, we'll assume you're using Visual Studio Code (VS Code).*

---

## **2. Installing Required Software and Python Packages**

### **2.1. Install Tesseract OCR**

Tesseract is essential for OCR operations.

- **Download Tesseract:**
  - Visit the [Tesseract OCR GitHub repository](https://github.com/tesseract-ocr/tesseract).
  - For Windows users, you can directly download the installer from [UB Mannheim's Tesseract builds](https://github.com/UB-Mannheim/tesseract/wiki).

- **Installation Steps (Windows):**
  1. Run the installer.
  2. During installation, note the installation path (e.g., `C:\Program Files\Tesseract-OCR\`).
  3. Ensure that the installer adds Tesseract to your system’s PATH. If not, you'll need to add it manually:
     - **Manually Adding to PATH:**
       - Right-click on **This PC** > **Properties** > **Advanced system settings**.
       - Click on **Environment Variables**.
       - Under **System variables**, find and select **Path**, then click **Edit**.
       - Click **New** and add the path to your Tesseract installation (e.g., `C:\Program Files\Tesseract-OCR\`).
       - Click **OK** to save.

- **Verify Installation:**

  Open **Command Prompt** or **PowerShell** and run:

  ```bash
  tesseract --version
  ```

  You should see version information, e.g.:

  ```
  tesseract v5.3.0
  ```

### **2.2. Install Language Data Files**

Since you need support for English (`eng`) and Indonesian (`ind`):

- **Download Language Data:**
  - The language data files (trained data) are usually included by default.
  - However, to ensure you have both English and Indonesian, you can download them from [Tesseract's tessdata repository](https://github.com/tesseract-ocr/tessdata).

- **Place Language Files:**
  - Copy `eng.traineddata` and `ind.traineddata` to Tesseract’s `tessdata` directory (e.g., `C:\Program Files\Tesseract-OCR\tessdata\`).

### **2.3. Set Up a Python Virtual Environment (Optional but Recommended)**

Using a virtual environment isolates your project’s dependencies.

1. **Navigate to Your Project Directory:**

   ```bash
   mkdir C:\Users\marve\Downloads\Modul1
   cd C:\Users\marve\Downloads\Modul1
   ```

2. **Create a Virtual Environment:**

   ```bash
   python -m venv venv
   ```

3. **Activate the Virtual Environment:**

   - **Windows:**

     ```bash
     .\venv\Scripts\activate
     ```

   - **PowerShell:**

     ```powershell
     .\venv\Scripts\Activate.ps1
     ```

   - **Unix or MacOS:**

     ```bash
     source venv/bin/activate
     ```

4. **Verify Activation:**

   Your prompt should change to indicate that the virtual environment is active, e.g., `(venv) C:\Users\marve\Downloads\Modul1>`

### **2.4. Install Required Python Packages**

We'll need several Python libraries:

- **`pytesseract`:** Python wrapper for Tesseract OCR.
- **`Pillow`:** For image processing.
- **`PyPDF2` or `pdfmerge`:** For merging PDFs.
- **`tqdm`:** For progress bars (optional but useful).

**Install via pip:**

```bash
pip install pytesseract Pillow PyPDF2 tqdm
```

*Note:* `PyPDF2` is a popular library for PDF manipulation. Alternatively, you can use `pdfrw`, `pdfminer`, or others based on specific needs.

---

## **3. Understanding the Workflow**

Before writing code, it's crucial to understand the steps involved:

1. **Locate Image Files:**
   - Identify all images (e.g., `1.jpeg` to `58.jpeg`) within a specified directory.

2. **Convert Images to Searchable PDFs:**
   - For each image:
     - Use Tesseract to perform OCR, specifying appropriate languages.
     - Save the output as an individual PDF.

3. **Merge Individual PDFs into a Single PDF:**
   - Combine all individual PDFs into one comprehensive PDF, maintaining the correct order.

4. **Cleanup (Optional):**
   - Remove individual PDFs if only the merged PDF is needed.

---

## **4. Writing the Python Script**

We'll create a Python script named `convert_and_merge.py` that accomplishes the tasks mentioned above.

### **4.1. Script Structure**

We'll structure the script into functions for better readability and maintenance:

- `convert_image_to_pdf(image_path, output_pdf_path, languages)`: Converts a single image to a searchable PDF.
- `merge_pdfs(pdf_paths, output_path)`: Merges a list of PDFs into a single PDF.
- `main()`: Orchestrates the entire process.

### **4.2. Writing the Script**

Create a new file named `convert_and_merge.py` in your project directory and open it in your IDE.

```python
# convert_and_merge.py

import os
import sys
import subprocess
from pathlib import Path
from typing import List

import pytesseract
from PIL import Image
from PyPDF2 import PdfMerger
from tqdm import tqdm

def convert_image_to_pdf(image_path: Path, output_pdf_path: Path, languages: str = "eng+ind"):
    """
    Converts an image to a searchable PDF using Tesseract OCR.

    Args:
        image_path (Path): Path to the input image.
        output_pdf_path (Path): Path to save the output PDF.
        languages (str, optional): Languages for Tesseract OCR. Defaults to "eng+ind".
    """
    try:
        # Ensure the image exists
        if not image_path.exists():
            print(f"Image file {image_path} does not exist.")
            return

        # Construct the Tesseract command
        # tesseract input_image output_base -l lang --dpi 300 pdf
        # We use '--dpi 300' to specify resolution; adjust as needed
        # The output_pdf_path should not have the .pdf extension in the second parameter
        command = [
            'tesseract',
            str(image_path),
            str(output_pdf_path.with_suffix('')),  # Tesseract appends .pdf
            '-l',
            languages,
            'pdf'
        ]

        # Run the Tesseract command
        subprocess.run(command, check=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    except subprocess.CalledProcessError as e:
        print(f"Error processing {image_path}: {e}")
    except Exception as e:
        print(f"Unexpected error processing {image_path}: {e}")

def merge_pdfs(pdf_paths: List[Path], output_path: Path):
    """
    Merges multiple PDFs into a single PDF.

    Args:
        pdf_paths (List[Path]): List of PDF file paths to merge.
        output_path (Path): Path to save the merged PDF.
    """
    merger = PdfMerger()

    try:
        for pdf in pdf_paths:
            if pdf.exists():
                merger.append(str(pdf))
            else:
                print(f"PDF file {pdf} does not exist and will be skipped.")

        # Write out the merged PDF
        merger.write(str(output_path))
        merger.close()
    except Exception as e:
        print(f"Error merging PDFs: {e}")
        merger.close()

def main():
    """
    Main function to convert images to PDFs and merge them.
    """
    # Define the directory containing images
    images_dir = Path("C:/Users/marve/Downloads/Modul1")  # Adjust as needed

    # Define the output merged PDF path
    merged_pdf_path = images_dir / "combined_document.pdf"

    # Define languages for OCR
    languages = "eng+ind"

    # Define the range of images or pattern
    # Here, assuming images are named as 1.jpeg to 58.jpeg
    image_files = [images_dir / f"{i}.jpeg" for i in range(1, 59)]

    # Define a list to store individual PDF paths
    individual_pdfs = []

    print("Starting conversion of images to PDFs...")
    for image_path in tqdm(image_files, desc="Converting Images", unit="image"):
        output_pdf = image_path.with_suffix('.pdf')
        convert_image_to_pdf(image_path, output_pdf, languages)
        if output_pdf.exists():
            individual_pdfs.append(output_pdf)

    print("Converting complete.\nStarting merging of PDFs...")

    # Sort PDFs based on numerical order
    individual_pdfs_sorted = sorted(individual_pdfs, key=lambda x: int(x.stem))

    # Merge PDFs
    merge_pdfs(individual_pdfs_sorted, merged_pdf_path)

    print(f"Merged PDF saved as {merged_pdf_path}")

    print("\nOptional: Cleaning up individual PDF files.")
    # Uncomment the following lines if you want to delete individual PDFs after merging
    # for pdf in individual_pdfs_sorted:
    #     try:
    #         pdf.unlink()
    #         print(f"Deleted {pdf}")
    #     except Exception as e:
    #         print(f"Error deleting {pdf}: {e}")

    print("Process completed successfully!")

if __name__ == "__main__":
    main()
```

### **4.3. Script Explanation**

1. **Imports:**
   - **`os, sys, subprocess`:** For interacting with the system and running external commands.
   - **`Path` from `pathlib`:** For convenient path manipulations.
   - **`List` from `typing`:** For type hinting.
   - **`pytesseract, PIL.Image`:** For image processing and OCR (though in this script, `pytesseract` isn't directly used; Tesseract is called via `subprocess`).
   - **`PyPDF2.PdfMerger`:** To merge PDFs.
   - **`tqdm`:** For displaying progress bars.

2. **`convert_image_to_pdf` Function:**
   - Constructs and runs the Tesseract command to convert an image to a searchable PDF.
   - Checks if the input image exists.
   - Handles exceptions and provides feedback on errors.

3. **`merge_pdfs` Function:**
   - Uses `PyPDF2.PdfMerger` to append each PDF in the correct order.
   - Skips any missing PDFs and informs the user.
   - Writes the merged PDF to the specified `output_path`.

4. **`main` Function:**
   - Defines the directory containing images and the output path for the merged PDF.
   - Specifies the OCR languages (`eng+ind` for English and Indonesian).
   - Generates a list of image file paths (`1.jpeg` to `58.jpeg`).
   - Iterates through each image, converting them to PDFs while displaying a progress bar.
   - Sorts the individual PDFs numerically to maintain order.
   - Merges all PDFs into a single document.
   - Optionally, cleans up individual PDF files after merging (currently commented out).

5. **Execution:**
   - The script runs the `main()` function when executed directly.

### **4.4. Important Considerations**

- **Tesseract's Output Handling:**
  - Tesseract appends the `.pdf` extension automatically. Therefore, when specifying the output base name, you should omit the `.pdf` suffix in the second argument.

- **Error Handling:**
  - The script includes basic error handling. Depending on your use case, you might want to enhance this (e.g., logging errors to a file).

- **Progress Feedback:**
  - The `tqdm` library provides a neat progress bar during the conversion process, enhancing user experience.

- **Cleanup Process:**
  - After merging, you might not need individual PDF files. The script includes an optional cleanup section that you can enable by uncommenting the relevant lines.

---

## **5. Enhancements and Best Practices**

To make your application more robust and user-friendly, consider implementing the following enhancements:

### **5.1. Command-Line Arguments**

Allow users to specify parameters like input directory, output file name, language options, etc.

**Implementation:**

Use the `argparse` module to parse command-line arguments.

```python
import argparse

def parse_arguments():
    parser = argparse.ArgumentParser(description="Convert images to searchable PDF and merge them.")
    parser.add_argument(
        '-i', '--input_dir',
        type=str,
        default="C:/Users/marve/Downloads/Modul1",
        help='Directory containing input images.'
    )
    parser.add_argument(
        '-o', '--output_pdf',
        type=str,
        default=None,
        help='Filename for the merged output PDF. Defaults to "combined_document.pdf".'
    )
    parser.add_argument(
        '-l', '--languages',
        type=str,
        default="eng+ind",
        help='Languages for OCR separated by +, e.g., "eng+ind".'
    )
    parser.add_argument(
        '-e', '--extension',
        type=str,
        default=".jpeg",
        help='Image file extension to process, e.g., ".jpeg", ".jpg", ".png".'
    )
    parser.add_argument(
        '-c', '--cleanup',
        action='store_true',
        help='Cleanup individual PDFs after merging.'
    )
    parser.add_argument(
        '-r', '--range',
        type=str,
        default="1-58",
        help='Range of image numbers to process, e.g., "1-58".'
    )
    return parser.parse_args()
```

**Integrate into `main()`:**

```python
def main():
    args = parse_arguments()

    # Define the directory containing images
    images_dir = Path(args.input_dir)

    # Define the output merged PDF path
    if args.output_pdf:
        merged_pdf_path = images_dir / args.output_pdf
    else:
        merged_pdf_path = images_dir / "combined_document.pdf"

    # Define languages for OCR
    languages = args.languages

    # Define the range of images
    try:
        start, end = map(int, args.range.split('-'))
        image_files = [images_dir / f"{i}{args.extension}" for i in range(start, end + 1)]
    except ValueError:
        print("Invalid range format. Use start-end, e.g., 1-58.")
        sys.exit(1)

    # Define a list to store individual PDF paths
    individual_pdfs = []

    print("Starting conversion of images to PDFs...")
    for image_path in tqdm(image_files, desc="Converting Images", unit="image"):
        output_pdf = image_path.with_suffix('.pdf')
        convert_image_to_pdf(image_path, output_pdf, languages)
        if output_pdf.exists():
            individual_pdfs.append(output_pdf)

    print("Converting complete.\nStarting merging of PDFs...")

    # Sort PDFs based on numerical order
    def extract_number(pdf: Path) -> int:
        try:
            return int(pdf.stem)
        except ValueError:
            return 0  # Or handle differently

    individual_pdfs_sorted = sorted(individual_pdfs, key=extract_number)

    # Merge PDFs
    merge_pdfs(individual_pdfs_sorted, merged_pdf_path)

    print(f"Merged PDF saved as {merged_pdf_path}")

    if args.cleanup:
        print("\nCleaning up individual PDF files.")
        for pdf in individual_pdfs_sorted:
            try:
                pdf.unlink()
                print(f"Deleted {pdf}")
            except Exception as e:
                print(f"Error deleting {pdf}: {e}")

    print("Process completed successfully!")
```

**Benefits:**

- Enhanced flexibility.
- Users can specify custom parameters without modifying the script.

### **5.2. Logging**

Implement logging to record the process details, especially useful for debugging.

**Implementation:**

```python
import logging

def setup_logging():
    logging.basicConfig(
        filename='convert_and_merge.log',
        filemode='a',
        format='%(asctime)s - %(levelname)s - %(message)s',
        level=logging.INFO
    )

# Update functions to use logging
def convert_image_to_pdf(image_path: Path, output_pdf_path: Path, languages: str = "eng+ind"):
    try:
        if not image_path.exists():
            logging.warning(f"Image file {image_path} does not exist.")
            return

        command = [
            'tesseract',
            str(image_path),
            str(output_pdf_path.with_suffix('')),
            '-l',
            languages,
            'pdf'
        ]

        subprocess.run(command, check=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
        logging.info(f"Converted {image_path} to {output_pdf_path}")
    except subprocess.CalledProcessError as e:
        logging.error(f"Error processing {image_path}: {e}")
    except Exception as e:
        logging.error(f"Unexpected error processing {image_path}: {e}")

def merge_pdfs(pdf_paths: List[Path], output_path: Path):
    merger = PdfMerger()

    try:
        for pdf in pdf_paths:
            if pdf.exists():
                merger.append(str(pdf))
                logging.info(f"Appended {pdf} to merger.")
            else:
                logging.warning(f"PDF file {pdf} does not exist and will be skipped.")

        merger.write(str(output_path))
        merger.close()
        logging.info(f"Merged PDF saved as {output_path}")
    except Exception as e:
        logging.error(f"Error merging PDFs: {e}")
        merger.close()
```

**Benefits:**

- Persistent records of the application's operations.
- Easier identification and troubleshooting of issues.

### **5.3. GUI Implementation (Optional)**

For users who prefer a graphical interface, you can enhance the application using libraries like `tkinter`, `PyQt`, or `Kivy`.

*Given the scope, we'll skip GUI implementation in this guide, but feel free to explore it based on your needs.*

---

## **6. Testing and Troubleshooting**

### **6.1. Running the Script**

1. **Ensure Virtual Environment is Activated (if using one):**

   ```bash
   .\venv\Scripts\activate
   ```

2. **Navigate to Script Directory:**

   ```bash
   cd C:\Users\marve\Downloads\Modul1
   ```

3. **Run the Script:**

   ```bash
   python convert_and_merge.py
   ```

   *Or, if you've implemented command-line arguments:*

   ```bash
   python convert_and_merge.py -i "C:/Users/marve/Downloads/Modul1" -o "combined_document.pdf" -l "eng+ind" -e ".jpeg" -c -r "1-58"
   ```

### **6.2. Common Issues and Solutions**

1. **`tesseract` Not Recognized:**

   - **Issue:** The system can't find the `tesseract` command.
   - **Solution:** Ensure Tesseract is installed and added to the system's PATH. Verify by running `tesseract --version` in the command line.

2. **Incorrect Image Paths:**

   - **Issue:** The script can't find image files.
   - **Solution:** Ensure that image files are named correctly and placed in the specified directory. Verify the file extensions.

3. **Language Data Not Found:**

   - **Issue:** Tesseract reports missing language data for specified languages.
   - **Solution:** Confirm that the necessary `.traineddata` files (e.g., `eng.traineddata`, `ind.traineddata`) are present in Tesseract's `tessdata` directory.

4. **Permission Errors:**

   - **Issue:** The script lacks permissions to read/write files.
   - **Solution:** Run the script with appropriate permissions. On Windows, ensure that your user account has read/write access to the target directory.

5. **Large Files or Memory Issues:**

   - **Issue:** Processing large images or numerous files may consume significant memory.
   - **Solution:** Optimize images before processing (e.g., reduce resolution/resizing if possible). Monitor system resources during execution.

### **6.3. Verifying Output**

- **Individual PDFs:**
  - Open a few converted PDFs (e.g., `1.pdf`, `2.pdf`) to ensure that:
    - The PDF displays the image correctly.
    - The text is selectable/searchable, indicating successful OCR.

- **Merged PDF:**
  - Open `combined_document.pdf` and navigate through the pages to confirm that:
    - All pages are included in the correct order.
    - The searchable text layer functions as expected.

---

## **7. Final Thoughts and Best Practices**

1. **Backup Your Data:**
   - Before running bulk operations, especially those that modify or delete files, ensure you have backups.

2. **Handle Different Image Formats:**
   - Extend the script to handle multiple image formats (`.jpg`, `.png`, etc.) by adjusting the `extension` parameter or using pattern matching.

3. **Optimize OCR Settings:**
   - Depending on your images, you might need to adjust DPI settings or perform pre-processing (e.g., grayscale conversion, noise reduction) to improve OCR accuracy.

4. **Error Logging:**
   - Continuous logging helps in monitoring the application's performance and diagnosing issues.

5. **Scalability:**
   - For processing a large number of images or large-sized files, consider implementing multithreading or multiprocessing to speed up the process.

6. **User Feedback:**
   - Incorporate more detailed feedback, such as estimated time remaining or summaries of processed files.

7. **Packaging:**
   - For distribution, consider packaging the script using tools like `PyInstaller` to create an executable.

---

## **Complete Enhanced Script with Command-Line Arguments and Logging**

Here's the enhanced version of `convert_and_merge.py` incorporating command-line arguments and logging:

```python
# convert_and_merge.py

import os
import sys
import subprocess
from pathlib import Path
from typing import List
import argparse
import logging

import pytesseract
from PIL import Image
from PyPDF2 import PdfMerger
from tqdm import tqdm

def setup_logging():
    logging.basicConfig(
        filename='convert_and_merge.log',
        filemode='a',
        format='%(asctime)s - %(levelname)s - %(message)s',
        level=logging.INFO
    )

def parse_arguments():
    parser = argparse.ArgumentParser(description="Convert images to searchable PDF and merge them.")
    parser.add_argument(
        '-i', '--input_dir',
        type=str,
        default="C:/Users/marve/Downloads/Modul1",
        help='Directory containing input images.'
    )
    parser.add_argument(
        '-o', '--output_pdf',
        type=str,
        default=None,
        help='Filename for the merged output PDF. Defaults to "combined_document.pdf".'
    )
    parser.add_argument(
        '-l', '--languages',
        type=str,
        default="eng+ind",
        help='Languages for OCR separated by +, e.g., "eng+ind".'
    )
    parser.add_argument(
        '-e', '--extension',
        type=str,
        default=".jpeg",
        help='Image file extension to process, e.g., ".jpeg", ".jpg", ".png".'
    )
    parser.add_argument(
        '-c', '--cleanup',
        action='store_true',
        help='Cleanup individual PDFs after merging.'
    )
    parser.add_argument(
        '-r', '--range',
        type=str,
        default="1-58",
        help='Range of image numbers to process, e.g., "1-58".'
    )
    return parser.parse_args()

def convert_image_to_pdf(image_path: Path, output_pdf_path: Path, languages: str = "eng+ind"):
    """
    Converts an image to a searchable PDF using Tesseract OCR.

    Args:
        image_path (Path): Path to the input image.
        output_pdf_path (Path): Path to save the output PDF.
        languages (str, optional): Languages for Tesseract OCR. Defaults to "eng+ind".
    """
    try:
        if not image_path.exists():
            logging.warning(f"Image file {image_path} does not exist.")
            return

        command = [
            'tesseract',
            str(image_path),
            str(output_pdf_path.with_suffix('')),  # Tesseract appends .pdf
            '-l',
            languages,
            'pdf'
        ]

        subprocess.run(command, check=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
        logging.info(f"Converted {image_path} to {output_pdf_path}")
    except subprocess.CalledProcessError as e:
        logging.error(f"Error processing {image_path}: {e.stderr.decode().strip()}")
    except Exception as e:
        logging.error(f"Unexpected error processing {image_path}: {e}")

def merge_pdfs(pdf_paths: List[Path], output_path: Path):
    """
    Merges multiple PDFs into a single PDF.

    Args:
        pdf_paths (List[Path]): List of PDF file paths to merge.
        output_path (Path): Path to save the merged PDF.
    """
    merger = PdfMerger()

    try:
        for pdf in pdf_paths:
            if pdf.exists():
                merger.append(str(pdf))
                logging.info(f"Appended {pdf} to merger.")
            else:
                logging.warning(f"PDF file {pdf} does not exist and will be skipped.")

        merger.write(str(output_path))
        merger.close()
        logging.info(f"Merged PDF saved as {output_path}")
    except Exception as e:
        logging.error(f"Error merging PDFs: {e}")
        merger.close()

def main():
    """
    Main function to convert images to PDFs and merge them.
    """
    setup_logging()
    args = parse_arguments()

    # Define the directory containing images
    images_dir = Path(args.input_dir)

    # Define the output merged PDF path
    if args.output_pdf:
        merged_pdf_path = images_dir / args.output_pdf
    else:
        merged_pdf_path = images_dir / "combined_document.pdf"

    # Define languages for OCR
    languages = args.languages

    # Define the range of images
    try:
        start, end = map(int, args.range.split('-'))
        image_files = [images_dir / f"{i}{args.extension}" for i in range(start, end + 1)]
    except ValueError:
        print("Invalid range format. Use start-end, e.g., 1-58.")
        logging.error("Invalid range format provided.")
        sys.exit(1)

    # Define a list to store individual PDF paths
    individual_pdfs = []

    print("Starting conversion of images to PDFs...")
    logging.info("Starting conversion of images to PDFs.")
    for image_path in tqdm(image_files, desc="Converting Images", unit="image"):
        output_pdf = image_path.with_suffix('.pdf')
        convert_image_to_pdf(image_path, output_pdf, languages)
        if output_pdf.exists():
            individual_pdfs.append(output_pdf)

    print("Converting complete.\nStarting merging of PDFs...")
    logging.info("Conversion complete. Starting merging of PDFs.")

    # Sort PDFs based on numerical order
    def extract_number(pdf: Path) -> int:
        try:
            return int(pdf.stem)
        except ValueError:
            logging.warning(f"Non-numeric PDF filename: {pdf.name}. Assigning sorting key 0.")
            return 0  # Or handle differently

    individual_pdfs_sorted = sorted(individual_pdfs, key=extract_number)

    # Merge PDFs
    merge_pdfs(individual_pdfs_sorted, merged_pdf_path)

    print(f"Merged PDF saved as {merged_pdf_path}")
    logging.info(f"Merged PDF saved as {merged_pdf_path}")

    if args.cleanup:
        print("\nCleaning up individual PDF files.")
        logging.info("Starting cleanup of individual PDF files.")
        for pdf in individual_pdfs_sorted:
            try:
                pdf.unlink()
                print(f"Deleted {pdf}")
                logging.info(f"Deleted {pdf}")
            except Exception as e:
                print(f"Error deleting {pdf}: {e}")
                logging.error(f"Error deleting {pdf}: {e}")

    print("Process completed successfully!")
    logging.info("Process completed successfully.")

if __name__ == "__main__":
    main()
```

### **7.1. Running the Enhanced Script**

**Command-Line Usage:**

```bash
python convert_and_merge.py -i "C:/Users/marve/Downloads/Modul1" -o "merged_output.pdf" -l "eng+ind" -e ".jpeg" -c -r "1-58"
```

**Parameters:**

- `-i` or `--input_dir`: Path to the directory containing the images.
- `-o` or `--output_pdf`: Name for the merged PDF file.
- `-l` or `--languages`: Languages for OCR, separated by `+`.
- `-e` or `--extension`: Image file extension to process.
- `-c` or `--cleanup`: If set, the script will delete individual PDFs after merging.
- `-r` or `--range`: Range of image numbers to process (e.g., `1-58`).

**Example:**

```bash
python convert_and_merge.py -i "C:/Users/marve/Downloads/Modul1" -o "combined_document.pdf" -l "eng+ind" -e ".jpeg" -c -r "1-58"
```

### **7.2. Final Checks**

- **Log File:** Review `convert_and_merge.log` for detailed logs and errors.
- **Final PDF:** Open `combined_document.pdf` to ensure all pages are present and searchable.
- **Cleanup:** If the `-c` flag was used, verify that individual PDFs have been deleted.

---

## **8. Conclusion**

You've successfully built a Python application that automates the conversion of multiple images to searchable PDFs using Tesseract OCR and merges them into a single PDF. This script is modular, flexible, and can be enhanced further based on your needs.

### **Next Steps:**

1. **Advanced Error Handling:**
   - Implement retries for failed OCR conversions.
   - Handle different image orientations or formats.

2. **User Interface:**
   - Develop a GUI for non-technical users.

3. **Performance Optimization:**
   - Parallelize OCR processes for faster execution on multi-core systems.

4. **Packaging:**
   - Package the script into an executable using tools like `PyInstaller` for easy distribution.

5. **Integration:**
   - Integrate with cloud storage or other services for automated workflows.

Happy coding!