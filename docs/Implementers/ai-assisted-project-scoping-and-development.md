---
title: AI assisted Project Scoping and Development
deprecated: false
hidden: false
metadata:
  robots: index
---
App Script to download multiple Sheets of GoogleSheets document as CSV, in-order to use as input for AI System.

<br />

```javascript AppScripts to download all sheets as CSV
function exportAllSheetsFromFolderToCsv() {
  const SOURCE_FOLDER_ID = '1TPP3ZpOasdasdsdsaasa'; //org scoping documentation folder
  const TARGET_FOLDER_ID = '1Bcy10UHsKasdasdasdsd'; //Sheet CSV Exports folder

  const sourceFolder = DriveApp.getFolderById(SOURCE_FOLDER_ID);
  const targetFolder = DriveApp.getFolderById(TARGET_FOLDER_ID); // [web:30]

  const files = sourceFolder.getFilesByType(MimeType.GOOGLE_SHEETS); // [web:69][web:83] NOT XLS files but Google sheets file

  while (files.hasNext()) {
    const file = files.next();
    const ss = SpreadsheetApp.openById(file.getId()); // [web:56][web:58]
    const sheets = ss.getSheets();

    sheets.forEach(sheet => {
      const data = sheet.getDataRange().getValues()
        .map(row => row.map(v => v.toString())
          .map(v => (v.includes('\n') || v.includes(',')) ? `"${v}"` : v)
          .join(','))
        .join('\n');

      const name = ss.getName() + ' - ' + sheet.getName() + '.csv';
      targetFolder.createFile(name, data, MimeType.PLAIN_TEXT); // UTF‑8 text CSV [web:30][web:43]
    });
  }
}


```
