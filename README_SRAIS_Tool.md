# SRAIS Tool

Scaling Responsible AI in Startups - a digital self-assessment tool built for ACAII-ACTS AI Institute.

## What This Is

The SRAIS Tool is a single self-contained HTML file that turns the SRAIS questionnaire into a guided, multi-stage digital assessment. It walks an innovator through five stages of AI solution development and scores each stage on two independent dimensions:

- RAI (Responsible AI) - how well the solution follows responsible AI principles at that stage
- SAI (Scalability) - how ready the solution is to scale at that stage

At the end of every stage, the tool shows a subtotal for that stage and a cumulative percentage of the grand total so far. The percentage is expected to be low early on and rise stage by stage as more of the questionnaire is completed.

The five stages, in order:

1. Problem Definition, Objectives, KPIs and Predictors (Features)
2. Data Collection
3. Model Development
4. Solution Deployment
5. Solution Scaling

Grand totals used for the percentage calculation: RAI = 540 points, SAI = 565 points. These are the totals stated in the original SRAIS Tool questionnaire document.

## Files

- ACAII_SRAIS_Tool.html - the tool itself. This is the only file needed to run the assessment.
- ACAII_SRAIS_AppsScript.js - backend script that receives submissions and writes them to a Google Sheet.

## Data note

The source questionnaire (SRAIS Tool.docx) has some inconsistencies in a few scoring cells (a handful of blank scores, and one multi-select question whose sub-items were reconstructed from the question text). The stated grand totals of 540 (RAI) and 565 (SAI) are used as the fixed denominators throughout, as given in the source document. A manual review of the underlying question bank against the original document is recommended before relying on this for scoring decisions.

## How to Use the Tool

1. Open ACAII_SRAIS_Tool.html in a browser (works locally, no installation needed)
2. Enter your access code (see Access Codes below) - this fills in the innovator and solution name automatically
3. Confirm the respondent details
4. Answer every question in a stage - all questions are mandatory, and the Next button stays disabled until the stage is complete. Response options do not show their score, so answers are not influenced by which option is worth more.
5. Review the stage subtotal and cumulative percentage shown at the bottom of each stage
6. After the final stage, review the full scorecard
7. Either download the responses as an Excel file, or submit them directly if the backend has been connected (see below)

## Access Codes

Innovators request their own access code from the "Request Access" link on the access screen (name, solution name, contact email). A code is generated and shown to them immediately, but it is created inactive - it will not work until an admin approves it. This means the signup form is open to anyone, but no one can actually start the assessment on a self-requested code until an admin has reviewed the request.

An admin can also add a code directly (Admin > Access Codes > Add a Code Directly), which is active immediately with no approval step needed - useful for handing a code to an innovator in person.

Access codes require the Apps Script backend to be deployed and connected (see below) - until then, both the access-code check and the request-access form tell the user the backend is not active yet.

## Admin Dashboard

Click "Admin" on the access screen and enter the admin password. The dashboard has four tabs:

- Responses - every submission (innovator, solution, respondent, date, final RAI % and SAI %), with a View button to see that submission's full question-by-question answers, and a Delete button to permanently remove a submission.
- Access Codes - every code that has been requested or added, whether it is active or still pending approval, with an Approve / Deactivate button per row. This is also where new codes are added directly.
- Access Log - every access-code attempt, valid and invalid, most recent first, with the code used and the timestamp. This is the record of who tried to log in and when.
- Analytics - total responses, average/lowest/highest RAI % across all submissions, and a per-innovator bar breakdown of RAI % and SAI %.

The admin password is set once as a Script Property (see setup steps below) and is not stored anywhere in the HTML file. This is a lightweight access gate suitable for keeping out casual or unauthorised viewing - it is not a substitute for real authentication if the responses are highly sensitive, since the password is sent as a plain request parameter. The same caveat applies to deleting a submission: there is no undo, so use it carefully.

## How to Deploy on GitHub Pages

1. Push this file (and the rest of the ACAII folder, or just this file on its own) to a GitHub repository
2. In the repository settings, go to Pages and set the source to the main branch, root folder
3. GitHub will publish the tool at `https://your-username.github.io/repository-name/ACAII_SRAIS_Tool.html`
4. Share that link with respondents

No build step and no server are required - it is a static HTML file.

## How to Connect Response Storage (Excel via Google Sheets)

GitHub Pages only serves static files, so it cannot write to a spreadsheet directly. The tool instead posts each submission to a small Google Apps Script Web App, which writes the response into a Google Sheet. A Google Sheet can be downloaded as a .xlsx file at any time (File > Download > Microsoft Excel), so this gives you a live, centrally stored, Excel-compatible record of every submission.

Setup steps:

1. Go to sheets.google.com and create a new spreadsheet, e.g. "ACAII SRAIS Responses"
2. Open Extensions > Apps Script
3. Paste in the contents of ACAII_SRAIS_AppsScript.js and save
4. Run the `setupSpreadsheet` function once (creates the Submissions, Response Detail, Access Codes, and Access Log sheets)
5. Set the admin password: Project Settings > Script properties > add a property with key `ADMIN_KEY` and your chosen password as the value
6. Deploy the script: Deploy > New deployment > Web app, execute as yourself, access set to Anyone
7. Copy the deployment URL
8. Open ACAII_SRAIS_Tool.html, find the line `const APPS_SCRIPT_URL = "";` near the end of the file, and paste the URL between the quotes
9. Re-publish the updated HTML file to GitHub Pages
10. From here, innovators can request their own codes and admins approve them from the dashboard - no need to add codes manually in the sheet unless you want to hand one out directly

Until this is configured, the "Submit Responses" button will tell the user to use "Download as Excel" instead, so the tool is usable even before the backend is set up.

## Editing the Question Bank

The full question bank is embedded in ACAII_SRAIS_Tool.html as a single JSON object (`SRAIS_DATA`) near the start of the script section. Each stage lists its RAI questions and SAI questions separately, each with a principle, question text, a type (single-select or multi-select), and a list of response options with their scores. Editing a question means editing this JSON directly in the HTML file.
