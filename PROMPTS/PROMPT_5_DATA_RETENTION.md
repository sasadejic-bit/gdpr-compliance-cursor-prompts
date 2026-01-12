# PROMPT 5: Data Retention & Automated Cleanup (GDPR Article 5 - Data Minimization)

**Time:** 25 minutes | **Risk:** 🟠 HIGH | **Generates:** `functions/src/functions/dataRetention.js` (300 lines)

```
Create automated data retention and cleanup jobs.

FILE: functions/src/functions/dataRetention.js

EXPORT FUNCTIONS:

1. exports.deleteOldMessages = functions.pubsub.schedule('every day 03:00').onRun(async (context) => {
   - Find all /messages with createdAt < (now - 1 year)
   - Delete collection
   - Log to audit_logs
   - Send summary email
   - Error handling: retry next day if fails

2. exports.deleteOldIpLogs = functions.pubsub.schedule('every week').onRun(async (context) => {
   - Find all /audit_logs with ipAddress field
   - If older than 90 days: delete ipAddress field (anonymize)
   - Keep action/timestamp/other fields
   - Maintains audit trail, removes personal data

3. exports.archiveOldBookings = functions.pubsub.schedule('every month').onRun(async (context) => {
   - Find /bookings with completedAt < (now - 2 years)
   - Copy to /archived_bookings/{bookingId}
   - Delete from active collection
   - Keep for stats/history

4. exports.archivePaymentRecords = functions.pubsub.schedule('every month').onRun(async (context) => {
   - NEVER DELETE payment records
   - Move to /archived_payments after 3 years
   - Keep for 7 years total (tax requirement)
   - Include: date, amount, payer, payee, invoice

5. exports.deleteAuditLogsByRetention = functions.pubsub.schedule('every month').onRun(async (context) => {
   - Keep non-sensitive audit logs for 1 year
   - Sensitive logs (failed login, deleted account): 6 months
   - But: keep everything for ongoing disputes/investigations
   - Never delete if: litigation pending, dispute open, fraud investigation

RETENTION SCHEDULE:

Delete After 1 Year:
- /messages (complete deletion)
- /notifications (old)
- /temporary_tokens
- /session_logs (if not auth-related)

Anonymize After 90 Days:
- IP addresses in all logs
- Device identifiers
- Precise geolocation (keep country only)

Archive After 2 Years:
- /bookings (move to /archived_bookings)
- /reviews (older ones)

NEVER DELETE (Keep 7 Years):
- Payment transactions
- Invoice records
- Tax documents
- Fraud investigation logs
- Dispute records

NEVER DELETE (Keep Indefinitely):
- Account creation records (anonymized)
- Deletion audit logs
- Security incident logs
- User preference audit trail

REQUIREMENTS:
1. Run on schedule (Cloud Scheduler)
2. Atomicity: all-or-nothing deletes
3. Logging: every deletion logged
4. Notifications: summary emails
5. Error recovery: retry failed deletes
6. Compliance: GDPR article 5(1)(e) - storage limitation
7. Exceptions: don't delete if litigation/dispute pending
8. Performance: process in batches (500 docs at a time)
9. Verify: check count deleted matches expected
10. Security: no sensitive data in logs

IMPORTS:
const admin = require('firebase-admin');
const functions = require('firebase-functions');

PRODUCTION-READY WITH ERROR HANDLING, BATCHING, LOGGING.
```

---

## Deploy Command

```bash
firebase deploy --only functions:deleteOldMessages,functions:deleteOldIpLogs,functions:archiveOldBookings,functions:deleteAuditLogsByRetention
```
