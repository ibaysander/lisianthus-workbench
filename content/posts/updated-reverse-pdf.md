---
title: 'Updated Reverse Pdf'
date: '2025-02-05T10:41:32+07:00'
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
## **1. Updated Workflow Overview**

Here's the enhanced workflow your Python application will follow:

1. **Identify and Process Images:**
   - Convert each image (`.jpeg`, `.jpg`, `.png`, etc.) to a searchable PDF using Tesseract OCR.
   - Extract text from each image and save it as a `.txt` file.

2. **Merge Outputs:**
   - Combine all individual PDFs into one comprehensive PDF.
   - Concatenate all individual text files into a single text document.

3. **Optional Cleanup:**
   - Delete individual PDFs and text files after merging, based on user preference.

---

## **2. Enhanced Python Script**

Below is the complete Python script, `convert_and_merge.py`, incorporating all the desired functionalities:

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
from PyPDF2 import PdfMerger
from tqdm import tqdm


def setup_logging():
    """
    Sets up logging to file with INFO level.
    """
    logging.basicConfig(
        filename='convert_and_merge.log',
        filemode='a',
        format='%(asctime)s - %(levelname)s - %(message)s',
        level=logging.INFO
    )


def parse_arguments():
    """
    Parses command-line arguments.

    Returns:
        argparse.Namespace: Parsed arguments.
    """
    parser = argparse.ArgumentParser(
        description="Convert images to searchable PDFs and text files using Tesseract OCR, then merge them."
    )
    parser.add_argument(
        '-i', '--input_dir',
        type=str,
        help='Directory containing input images.'
    )
    parser.add_argument(
        '-o', '--output_pdf',
        type=str,
        help='Filename for the merged output PDF. Defaults to "combined_document.pdf".'
    )
    parser.add_argument(
        '-t', '--output_txt',
        type=str,
        help='Filename for the merged output text file. Defaults to "combined_text.txt".'
    )
    parser.add_argument(
        '-l', '--languages',
        type=str,
        help='Languages for OCR separated by +, e.g., "eng+ind". Defaults to "eng+ind".'
    )
    parser.add_argument(
        '-e', '--extension',
        type=str,
        help='Image file extension to process, e.g., ".jpeg", ".jpg", ".png". Defaults to ".jpeg".'
    )
    parser.add_argument(
        '-c', '--cleanup',
        action='store_true',
        help='Cleanup individual PDFs and text files after merging.'
    )
    parser.add_argument(
        '-r', '--range',
        type=str,
        help='Range of image numbers to process, e.g., "1-58". Defaults to "1-58".'
    )
    return parser.parse_args()


def prompt_user_inputs(args):
    """
    Prompt user for inputs if they are not provided via command-line arguments.

    Args:
        args (argparse.Namespace): Parsed arguments.
    """
    if not args.input_dir:
        args.input_dir = input("Enter the directory containing the input images (e.g., C:/Users/Username/Images): ").strip()

    if not args.output_pdf:
        args.output_pdf = input("Enter the desired name for the merged output PDF (default: combined_document.pdf): ").strip()
        if not args.output_pdf:
            args.output_pdf = "combined_document.pdf"

    if not args.output_txt:
        args.output_txt = input("Enter the desired name for the merged output text file (default: combined_text.txt): ").strip()
        if not args.output_txt:
            args.output_txt = "combined_text.txt"

    if not args.languages:
        args.languages = input("Enter OCR languages separated by + (e.g., eng+ind) [default: eng+ind]: ").strip()
        if not args.languages:
            args.languages = "eng+ind"

    if not args.extension:
        args.extension = input("Enter the image file extension to process (e.g., .jpeg, .jpg, .png) [default: .jpeg]: ").strip()
        if not args.extension:
            args.extension = ".jpeg"

    if not args.range:
        args.range = input("Enter the range of image numbers to process (e.g., 1-58) [default: 1-58]: ").strip()
        if not args.range:
            args.range = "1-58"

    # Confirm cleanup option if not provided
    if not args.cleanup:
        cleanup_input = input("Do you want to cleanup individual PDFs and text files after merging? (y/n) [default: n]: ").strip().lower()
        args.cleanup = cleanup_input == 'y'


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


def extract_text_from_image(image_path: Path, output_txt_path: Path, languages: str = "eng+ind"):
    """
    Extracts text from an image using pytesseract and saves it to a text file.

    Args:
        image_path (Path): Path to the input image.
        output_txt_path (Path): Path to save the extracted text.
        languages (str, optional): Languages for OCR. Defaults to "eng+ind".
    """
    try:
        if not image_path.exists():
            logging.warning(f"Image file {image_path} does not exist.")
            return

        # Open the image using PIL
        from PIL import Image
        img = Image.open(image_path)

        # Use pytesseract to do OCR on the image
        text = pytesseract.image_to_string(img, lang=languages)

        # Write the extracted text to the txt file
        with open(output_txt_path, 'w', encoding='utf-8') as f:
            f.write(text)

        logging.info(f"Extracted text from {image_path} to {output_txt_path}")
    except Exception as e:
        logging.error(f"Error extracting text from {image_path}: {e}")


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


def merge_texts(txt_paths: List[Path], output_path: Path):
    """
    Merges multiple text files into a single text file.

    Args:
        txt_paths (List[Path]): List of text file paths to merge.
        output_path (Path): Path to save the merged text file.
    """
    try:
        with open(output_path, 'w', encoding='utf-8') as outfile:
            for txt in txt_paths:
                if txt.exists():
                    with open(txt, 'r', encoding='utf-8') as infile:
                        outfile.write(infile.read())
                        outfile.write("\n\n--- End of Page ---\n\n")  # Separator between pages
                    logging.info(f"Appended {txt} to {output_path}")
                else:
                    logging.warning(f"Text file {txt} does not exist and will be skipped.")
        logging.info(f"Merged text file saved as {output_path}")
    except Exception as e:
        logging.error(f"Error merging text files: {e}")


def main():
    """
    Main function to convert images to PDFs and text files, then merge them.
    """
    setup_logging()
    args = parse_arguments()

    # If any required arguments are missing, prompt the user
    if not (args.input_dir and args.output_pdf and args.output_txt and args.languages and args.extension and args.range):
        print("Some required arguments are missing. You will be prompted to enter them.")
        prompt_user_inputs(args)
    else:
        # Even if arguments are provided, confirm cleanup preference
        if not args.cleanup:
            cleanup_input = input("Do you want to cleanup individual PDFs and text files after merging? (y/n) [default: n]: ").strip().lower()
            args.cleanup = cleanup_input == 'y'

    # Validate and convert input directory to Path
    images_dir = Path(args.input_dir).expanduser().resolve()
    if not images_dir.is_dir():
        print(f"Error: The input directory '{images_dir}' does not exist or is not a directory.")
        logging.error(f"Invalid input directory: {images_dir}")
        sys.exit(1)

    # Define the output merged PDF and text file paths
    merged_pdf_path = images_dir / args.output_pdf
    merged_txt_path = images_dir / args.output_txt

    # Define languages for OCR
    languages = args.languages

    # Define the range of images
    try:
        start, end = map(int, args.range.split('-'))
        if start > end:
            print("Error: Start of range cannot be greater than end.")
            logging.error("Invalid range: start greater than end.")
            sys.exit(1)
        image_files = [images_dir / f"{i}{args.extension}" for i in range(start, end + 1)]
    except ValueError:
        print("Error: Invalid range format. Use start-end, e.g., 1-58.")
        logging.error("Invalid range format provided.")
        sys.exit(1)

    # Define lists to store individual PDF and text paths
    individual_pdfs = []
    individual_txts = []

    print("\nStarting conversion of images to PDFs and text files...")
    logging.info("Starting conversion of images to PDFs and text files.")
    for image_path in tqdm(image_files, desc="Processing Images", unit="image"):
        # Define output paths
        output_pdf = image_path.with_suffix('.pdf')
        output_txt = image_path.with_suffix('.txt')

        # Convert image to PDF
        convert_image_to_pdf(image_path, output_pdf, languages)
        if output_pdf.exists():
            individual_pdfs.append(output_pdf)
        else:
            logging.warning(f"Failed to create PDF for {image_path}")

        # Extract text from image to txt
        extract_text_from_image(image_path, output_txt, languages)
        if output_txt.exists():
            individual_txts.append(output_txt)
        else:
            logging.warning(f"Failed to create text file for {image_path}")

    print("\nConversion complete.\nStarting merging of PDFs and text files...")
    logging.info("Conversion complete. Starting merging of PDFs and text files.")

    # Sort PDFs and text files based on numerical order
    def extract_number(file: Path) -> int:
        try:
            return int(file.stem)
        except ValueError:
            logging.warning(f"Non-numeric filename: {file.name}. Assigning sorting key 0.")
            return 0  # Assign 0 or handle differently

    individual_pdfs_sorted = sorted(individual_pdfs, key=extract_number)
    individual_txts_sorted = sorted(individual_txts, key=extract_number)

    # Merge PDFs
    merge_pdfs(individual_pdfs_sorted, merged_pdf_path)

    # Merge Text Files
    merge_texts(individual_txts_sorted, merged_txt_path)

    print(f"\nMerged PDF saved as {merged_pdf_path}")
    print(f"Merged text file saved as {merged_txt_path}")
    logging.info(f"Merged PDF saved as {merged_pdf_path}")
    logging.info(f"Merged text file saved as {merged_txt_path}")

    if args.cleanup:
        print("\nCleaning up individual PDF and text files...")
        logging.info("Starting cleanup of individual PDF and text files.")
        for pdf in individual_pdfs_sorted:
            try:
                pdf.unlink()
                print(f"Deleted {pdf}")
                logging.info(f"Deleted {pdf}")
            except Exception as e:
                print(f"Error deleting {pdf}: {e}")
                logging.error(f"Error deleting {pdf}: {e}")
        for txt in individual_txts_sorted:
            try:
                txt.unlink()
                print(f"Deleted {txt}")
                logging.info(f"Deleted {txt}")
            except Exception as e:
                print(f"Error deleting {txt}: {e}")
                logging.error(f"Error deleting {txt}: {e}")

    print("\nProcess completed successfully!")
    logging.info("Process completed successfully.")


if __name__ == "__main__":
    main()
```

---

## **3. Detailed Explanation of Enhancements**

### **3.1. Dual Conversion: PDF and Text Files**

To meet your requirement of generating both searchable PDFs and accompanying text files, two key functions have been added:

1. **`convert_image_to_pdf`:**
   - Utilizes Tesseract via a subprocess call to convert each image into a searchable PDF.
   - Ensures that the OCR layer is embedded within the PDF for text selection.

2. **`extract_text_from_image`:**
   - Uses `pytesseract` to extract raw text from each image.
   - Saves the extracted text to a `.txt` file.
   - This provides a separate text file that can be referenced or used independently of the PDF.

### **3.2. Merging Functionality Expanded**

In addition to merging individual PDFs into a single PDF, the script also merges individual text files into a single comprehensive text document:

1. **`merge_pdfs`:**
   - Remains largely the same, appending each individual PDF in numerical order to ensure the final merged PDF maintains the correct sequence.

2. **`merge_texts`:**
   - Introduced to concatenate all individual text files into one.
   - Inserts a clear separator (`--- End of Page ---`) between each page's content for better readability.

### **3.3. Interactive Prompts for Enhanced User-Friendliness**

The script now intelligently prompts users for any missing information instead of relying solely on command-line arguments. This ensures that even users unfamiliar with command-line operations can effectively use the tool.

### **3.4. Logging for Better Monitoring and Debugging**

Comprehensive logging has been integrated:

- **Log File:** All operations, successes, warnings, and errors are logged to `convert_and_merge.log`.
- **Console Feedback:** Users receive real-time feedback on the conversion and merging processes.

### **3.5. Cleanup Functionality**

An optional cleanup feature allows users to delete individual PDFs and text files after successfully merging them, reducing clutter and saving disk space.
