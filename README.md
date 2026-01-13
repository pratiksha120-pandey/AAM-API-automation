# Postman + Newman + GitHub CI + Email Report – FOLLOW EXACTLY

This is a **recipe-style setup guide**.
Follow steps **in order**.
Do **not** skip steps.
Do **not** change commands or names.
If anything looks different → **STOP** and contact Archita.

---

## RULES (MANDATORY)
- Use **Command Prompt** only (unless mentioned)
- File and folder names must match exactly
- Verification steps are compulsory
- No experimentation or extra setup

---

## PHASE 0: SYSTEM CHECK

### Step 0.1 – Verify Node.js
1. Press `Windows + R`
2. Type `cmd`
3. Press Enter
4. Type:


node -v

5. Press Enter
6. Version must appear
7. Type:


npm -v

8. Press Enter
9. Version must appear

If versions do NOT appear → STOP.

---

## PHASE 1: INSTALL POSTMAN

### Step 1.1 – Download Postman
1. Open Google Chrome
2. Go to:


https://www.postman.com/downloads/

3. Click **Download for Windows**
4. Wait for download to complete

---

### Step 1.2 – Install & Login
1. Press `Ctrl + J`
2. Double-click downloaded file
3. Wait for installation to finish
4. Postman opens automatically
5. Click **Sign In**
6. Login using assigned email
7. Wait for dashboard to load

---

## PHASE 2: CREATE COLLECTION & TEST

### Step 2.1 – Create Workspace
1. Click **Workspaces**
2. Click **Create Workspace**
3. Name:


QA-Automation

4. Click **Create**

---

### Step 2.2 – Create Collection
1. Click **Collections**
2. Click **New Collection**
3. Name:


Sample-API-Tests

4. Click **Save**

---

### Step 2.3 – Add API Request
1. Click **Add a request**
2. Request name:


Health-Check

3. Method: **GET**
4. URL:


https://postman-echo.com/get

5. Click **Send**
6. Status code must be **200**

---

### Step 2.4 – Add Test Script
1. Click **Tests** tab
2. Paste exactly:
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});


Click Save

PHASE 3: EXPORT POSTMAN FILES
Step 3.1 – Export Collection

Click three dots next to collection

Click Export

Select Collection v2.1

Click Export

Save as:

sample-api-tests.json

PHASE 4: INSTALL NEWMAN
Step 4.1 – Install Newman

Open Command Prompt

Type:

npm install -g newman


Press Enter

Wait for completion

Step 4.2 – Verify Newman

Type:

newman -v


Press Enter

Version must appear

PHASE 5: LOCAL PROJECT STRUCTURE
Step 5.1 – Create Folder

Go to Documents

Create folder:

postman-ci

Step 5.2 – Create Subfolders

Inside postman-ci, create:

collections
reports

Step 5.3 – Move Files

Move sample-api-tests.json → collections

PHASE 6: RUN NEWMAN LOCALLY
Step 6.1 – Run Command

Open Command Prompt inside postman-ci

Run:

newman run collections/sample-api-tests.json -r html --reporter-html-export reports/report.html


Press Enter

Step 6.2 – Verify Report

Open reports folder

Double-click report.html

Report must open in browser

If not → STOP.

PHASE 7: GITHUB REPOSITORY
Step 7.1 – Create Repo

Go to GitHub

Click New Repository

Name:

postman-newman-ci


Click Create Repository

Step 7.2 – Clone Repo

Copy repo URL

Open Command Prompt

Navigate to Documents

Run:

git clone <repo-url>

Step 7.3 – Copy Files

Move into cloned repo:

collections/
reports/

PHASE 8: EMAIL SETUP (GMAIL APP PASSWORD)
Step 8.1 – Enable 2-Step Verification

Open Gmail account

Go to Google Account → Security

Enable 2-Step Verification

Step 8.2 – Create App Password

In Security → App passwords

Select:

App: Mail

Device: Other

Name:

GitHub-Newman


Click Generate

Copy password (save securely)

PHASE 9: ADD GITHUB SECRETS
Step 9.1 – Open Repo Settings

Open GitHub repo

Click Settings

Click Secrets and variables → Actions

Step 9.2 – Add Secrets

Add exactly these:

Name	Value
EMAIL_USERNAME	sender gmail
EMAIL_PASSWORD	app password
EMAIL_TO	recipient email

Click Save after each.

PHASE 10: GITHUB ACTIONS CI + EMAIL
Step 10.1 – Create Workflow File

Inside repo, create:

.github/workflows/newman.yml

Step 10.2 – Paste Workflow

Paste EXACTLY:

name: Postman CI with Email

on: [push]

jobs:
  postman-tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18

      - name: Install Newman
        run: npm install -g newman

      - name: Run Newman Tests
        run: |
          newman run collections/sample-api-tests.json \
          -r html \
          --reporter-html-export reports/report.html

      - name: Send Email with Report
        uses: dawidd6/action-send-mail@v3
        with:
          server_address: smtp.gmail.com
          server_port: 465
          username: ${{ secrets.EMAIL_USERNAME }}
          password: ${{ secrets.EMAIL_PASSWORD }}
          subject: "Postman CI Report"
          body: "Postman Newman execution completed. Report attached."
          to: ${{ secrets.EMAIL_TO }}
          from: ${{ secrets.EMAIL_USERNAME }}
          attachments: reports/report.html

PHASE 11: RUN PIPELINE
Step 11.1 – Commit & Push

Open Command Prompt in repo

Run:

git add .


Run:

git commit -m "Add Postman CI with email report"


Run:

git push

Step 11.2 – Verify

Open GitHub repo

Click Actions

Workflow must be green

Check inbox for email

Download and open attached report

SUCCESS CRITERIA

Setup is successful ONLY if:

Newman runs locally

CI passes on GitHub

Email received with report attachment

If all true → SETUP COMPLETE.