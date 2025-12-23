---
title: AI assisted Project Scoping and Development
deprecated: false
hidden: false
metadata:
  robots: index
---

This guide describes how to use AI to accelerate Avni project scoping and configuration development. The workflow has two main phases:

1. **Export scoping documents** – Convert Google Sheets scoping/SRS documents to CSV for AI consumption
2. **Generate bundle configuration** – Use AI with the exported CSVs and reference materials to produce production-ready Avni configurations

## Step 1: Export Scoping Documents to CSV

Use this Apps Script to export all sheets from Google Sheets files in a Drive folder as UTF‑8 CSV files.

### Setup

1. Create two folders in Google Drive:
   - One for **input** spreadsheets (source)
   - One for **CSV exports** (target)
2. Copy each folder's ID from its URL (`https://drive.google.com/drive/folders/<ID>`)
3. In any Google Sheet, open **Extensions → Apps Script**
4. Paste the script below and replace `SOURCE_FOLDER_ID` and `TARGET_FOLDER_ID` with your IDs

### Script

```javascript
// App Script to download all sheets as CSV for AI input
function exportAllSheetsFromFolderToCsv() {
  const SOURCE_FOLDER_ID = '1TPP3ZpOasdasdsdsaasa'; // Folder containing Google Sheets
  const TARGET_FOLDER_ID = '1Bcy10UHsKasdasdasdsd'; // Folder to store CSV exports

  const sourceFolder = DriveApp.getFolderById(SOURCE_FOLDER_ID);
  const targetFolder = DriveApp.getFolderById(TARGET_FOLDER_ID);

  // Only Google Sheets files in the source folder (not XLS/XLSX)
  const files = sourceFolder.getFilesByType(MimeType.GOOGLE_SHEETS);

  while (files.hasNext()) {
    const file = files.next();
    const ss = SpreadsheetApp.openById(file.getId());
    const sheets = ss.getSheets();

    sheets.forEach(sheet => {
      const data = sheet.getDataRange().getValues()
        .map(row => row.map(v => v.toString())
          .map(v => (v.includes('\n') || v.includes(',')) ? `"${v}"` : v)
          .join(','))
        .join('\n');

      const name = ss.getName() + ' - ' + sheet.getName() + '.csv';
      // Creates UTF‑8 text CSV in the target folder
      targetFolder.createFile(name, data, MimeType.PLAIN_TEXT);
    });
  }
}
```

### Run the Script

1. In the Apps Script editor, select `exportAllSheetsFromFolderToCsv` and click **Run**
2. Approve permissions when prompted (first time only)
3. Open the target Drive folder to find one `.csv` per sheet, named `<SpreadsheetName> - <SheetName>.csv`

## Step 2: Generate Bundle Configuration with AI

With the scoping CSVs ready, use AI to generate Avni bundle configurations. The **[Avni Bundle Configuration Guide](https://github.com/avniproject/avni-impl-bundles/blob/main/reference/BUNDLE_CONFIG_GUIDE.md)** in the `avni-impl-bundles` repository provides the complete reference for this process.

### What the Guide Covers

- **Analyze SRS Documents** – Map subject types, programs, and form fields from your CSVs
- **Create Concepts** – Define concept structures with proper data types and coded values
- **Create Forms** – Build forms with skip logic, validation rules, and decision rules
- **Configure Form Mappings** – Link forms to subject types and programs
- **Set Up User Groups & Privileges** – Define access control and permissions
- **Configure Report Cards & Dashboards** – Create standard and custom report cards
- **Write Rules** – Implement subject summary, eligibility, visit schedule, and validation rules
- **Validation & Troubleshooting** – Common errors and verification checklists

### Reference Materials

The [`avni-impl-bundles/reference`](https://github.com/avniproject/avni-impl-bundles/tree/main/reference) folder contains additional guidance and examples for AI-assisted configuration generation

