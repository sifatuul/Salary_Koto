# 💰 স্যালারি কত! (Salary Koto!) - Smart Salary & Timesheet Tracker

A beautiful, glassmorphism-inspired web application designed to track work hours and automatically calculate your salary based on an hourly rate. Perfect for freelancers, part-time workers, and contractors. It uses Google Sheets as a free, serverless backend database.


<img width="1249" height="1079" alt="image" src="https://github.com/user-attachments/assets/c0b8500d-80f6-480a-852b-b597da21e294" />

## ✨ Features

* **Smart Time Conversion:** Automatically converts `HH:MM` time formats into decimal hours for accurate salary calculation.
* **Real-time Analytics:** Instantly displays Total Earnings, Total Time worked, and current Hourly Rate.
* **CSV Export:** Download your salary history as a `.csv` file with a single click.
* **Serverless Architecture:** Uses Google Apps Script and Google Sheets—no paid hosting or database required.
* **Admin Protection:** Secure your data! Adding or deleting entries requires an admin password.
* **Modern UI:** Responsive design featuring premium glassmorphism styling and smooth animations.

## 🛠️ Tech Stack

* **Frontend:** HTML5, CSS3 (Glassmorphism, Flexbox/Grid), Vanilla JavaScript.
* **Backend / Database:** Google Apps Script, Google Sheets.
* **Fonts:** 'Anek Bangla' & 'Montserrat' (Google Fonts).

---

## 🚀 Setup & Installation Guide

To make this app work, you need to set up your Google Sheet database and connect it to your frontend code.

### Step 1: Set up the Google Sheet (Database)

1. Go to [Google Sheets](https://sheets.google.com) and create a new blank spreadsheet.
2. Rename the first sheet at the bottom to `SalaryData` (case-sensitive).
3. In the first row, create the following headers exactly as written (A1 to E1):
`id` | `month` | `time` | `rate` | `timestamp`

### Step 2: Set up Google Apps Script (Backend)

1. On your Google Sheet, click on **Extensions > Apps Script**.
2. Delete any existing code in the editor and paste the following script:

```javascript
const SHEET_NAME = "SalaryData";
const ADMIN_PASSWORD = "your_admin_password"; // ⚠️ CHANGE THIS TO YOUR PREFERRED PASSWORD

function doGet(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);
  const data = sheet.getDataRange().getValues();
  const headers = data[0];
  const result = [];
  
  for (let i = 1; i < data.length; i++) {
    let obj = {};
    for (let j = 0; j < headers.length; j++) {
      obj[headers[j]] = data[i][j];
    }
    result.push(obj);
  }
  return ContentService.createTextOutput(JSON.stringify(result))
    .setMimeType(ContentService.MimeType.JSON);
}

function doPost(e) {
  try {
    const payload = JSON.parse(e.postData.contents);
    
    // Password Check
    if (payload.password !== ADMIN_PASSWORD) {
      return ContentService.createTextOutput(JSON.stringify({success: false, error: "Incorrect Admin Password!"}))
        .setMimeType(ContentService.MimeType.JSON);
    }

    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);

    // Add Entry
    if (payload.action === 'add') {
      const uniqueId = new Date().getTime().toString(); // Auto-generates a unique ID
      const timestamp = new Date().toLocaleString();
      
      sheet.appendRow([
        uniqueId,
        payload.month,
        payload.time,
        payload.rate,
        timestamp
      ]);
      
      return ContentService.createTextOutput(JSON.stringify({success: true}))
        .setMimeType(ContentService.MimeType.JSON);
    }

    // Delete Entry
    if (payload.action === 'delete') {
      const data = sheet.getDataRange().getValues();
      for (let i = 1; i < data.length; i++) {
        // id is in the first column (index 0)
        if (data[i][0] == payload.id) { 
          sheet.deleteRow(i + 1);
          return ContentService.createTextOutput(JSON.stringify({success: true}))
            .setMimeType(ContentService.MimeType.JSON);
        }
      }
      return ContentService.createTextOutput(JSON.stringify({success: false, error: "Entry not found"}))
        .setMimeType(ContentService.MimeType.JSON);
    }

  } catch(error) {
    return ContentService.createTextOutput(JSON.stringify({success: false, error: error.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

```

3. Change `"your_admin_password"` on line 2 to the password you want to use.
4. Click the **Save** icon (💾).

### Step 3: Deploy the Script

1. Click the blue **Deploy** button at the top right, then select **New deployment**.
2. Click the gear icon (⚙️) next to "Select type" and choose **Web app**.
3. Under *Description*, type "Initial Release".
4. Under *Execute as*, select **Me**.
5. Under *Who has access*, select **Anyone**.
6. Click **Deploy**. *(Note: Google will ask you to authorize the script. Click "Review permissions", select your Google account, click "Advanced", and click "Go to project" to allow it).*
7. Copy the generated **Web app URL**.

### Step 4: Connect Backend to Frontend

1. Open the `index.html` file in your project.
2. Scroll down to the `<script>` section at the bottom.
3. Replace the `SCRIPT_URL` variable with the exact Web app URL you just copied:
```javascript
const SCRIPT_URL = "YOUR_NEW_WEB_APP_URL_HERE"; 

```


4. Save the file.

## 🌐 Hosting

Upload your `index.html` file to any free static hosting provider:

* [GitHub Pages](https://pages.github.com/) (Recommended) 
* [Vercel](https://vercel.com/)
* [Netlify](https://www.netlify.com/)

## 📝 Usage Guide

1. **Add Time:** Enter the month (e.g., Jun 2026), your time in `HH:MM` format (e.g., 5:30 for 5 hours and 30 minutes), and your hourly rate.
2. **Save:** Click **Save Entry** and enter your admin password. The app will automatically convert your time to decimal format and calculate your earnings.
3. **Delete:** Click the red **Delete** button next to a mistake. You will need your admin password to confirm.
4. **Export:** Click **Export CSV** in the top right to download a spreadsheet copy of all your records.

---

*Developed by Sifatul Islam*
