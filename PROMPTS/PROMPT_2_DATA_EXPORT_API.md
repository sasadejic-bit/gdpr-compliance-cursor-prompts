# PROMPT 2: Data Export API (GDPR Article 20 - Data Portability)

**Time:** 30 minutes | **Risk:** 🔴 CRITICAL | **Generates:** `functions/src/functions/dataExport.js` (400 lines)

```
Create a complete data export API that lets users download ALL their personal data in GDPR-compliant format.

FILE: functions/src/functions/dataExport.js

EXPORT FUNCTIONS:

1. exports.requestDataExport = functions.https.onCall(async (data, context) => {
   - Requires: Firebase Auth user
   - Creates request document in /users/{uid}/dataExports/{requestId}
   - Fields: { requestedAt, status: 'pending', expiresAt: now + 24h, format: 'zip' }
   - Sends confirmation email to user
   - Returns: { requestId, expiresAt, downloadUrl }
   - Rate limit: 1 request per day per user

2. exports.generateDataExport = functions.firestore.document('users/{uid}/dataExports/{docId}').onCreate(async (snap) => {
   - Triggered when new export request created
   - Gathers data from:
     * /users/{uid}/profile
     * /users/{uid}/bookings
     * /users/{uid}/reviews
     * /users/{uid}/messages
     * /users/{uid}/consents
     * /audit_logs for this user
   - Creates JSON files for each collection
   - Creates CSV export of bookings/messages
   - Generates README with explanation
   - Creates ZIP file in Cloud Storage
   - Uploads to: gs://bucket/exports/{uid}/{requestId}.zip
   - Updates document: { status: 'ready', downloadUrl, generatedAt }
   - Sends download link via email
   - Logs to audit trail

3. exports.downloadDataExport = functions.https.onCall(async (data, context) => {
   - Parameters: { requestId, exportId }
   - Verifies: User owns this export request
   - Checks: Export not expired (< 24h old)
   - Returns: { downloadUrl: signed URL valid for 1 hour }
   - Logs download attempt
   - Auto-deletes ZIP after 24 hours

4. exports.deleteExpiredExports = functions.pubsub.schedule('every 6 hours').onRun(async (context) => {
   - Finds all /dataExports with expiresAt < now
   - Deletes from Cloud Storage
   - Updates status: 'expired'
   - Logs cleanup action
   - Sends notification if user already downloaded

DATA STRUCTURE (included in ZIP):
data_export_{date}.zip
├── README.md (explanation of files)
├── profile.json (name, email, phone, preferences)
├── bookings.json (all bookings with dates)
├── bookings.csv (spreadsheet format)
├── reviews.json (all reviews written)
├── reviews.csv (spreadsheet format)
├── messages.json (all message history)
├── messages.csv (spreadsheet format)
└── consents.json (consent history with timestamps)

REQUIREMENTS:
1. All data must be included - no censoring/redaction
2. Files must be human-readable (JSON + CSV)
3. ZIP file encrypted with user's password (optional)
4. Include metadata: export date, data scope, field definitions
5. GDPR compliant: within 30 days of request
6. Error handling: If export fails, retry up to 3 times, email user
7. Logging: Every step logged to audit_logs
8. Security: Signed download URLs (1-hour expiry)
9. Privacy: Delete files after 24 hours
10. Performance: Handle large exports (100MB+) with streaming

IMPORTS:
const admin = require('firebase-admin');
const functions = require('firebase-functions');
const archiver = require('archiver');
const { Storage } = require('@google-cloud/storage');

PRODUCTION-READY WITH ERROR HANDLING, RETRIES, LOGGING.
```

---

## Next: Paste into Cursor

Save generated file to: `functions/src/functions/dataExport.js`
