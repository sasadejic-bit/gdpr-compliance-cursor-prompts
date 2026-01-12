# PROMPT 3: Account Deletion (GDPR Article 17 - Right to Erasure)

**Time:** 30 minutes | **Risk:** 🔴 CRITICAL | **Generates:** `functions/src/functions/accountDeletion.js` (350 lines)

```
Create a complete account deletion system with 7-day grace period (GDPR right to erasure).

FILE: functions/src/functions/accountDeletion.js

EXPORT FUNCTIONS:

1. exports.scheduleAccountDeletion = functions.https.onCall(async (data, context) => {
   - User requests permanent deletion
   - Creates document in /users/{uid}/deletion with:
     * requestedAt: now
     * scheduledFor: now + 7 days
     * status: 'pending'
     * cancelUrl: link to cancel
   - Sends email: "Your account will be deleted in 7 days. Click here to cancel."
   - Returns: { deletionScheduledFor, cancelUrl, days: 7 }
   - Rate limit: 1 request per day

2. exports.cancelAccountDeletion = functions.https.onCall(async (data, context) => {
   - User wants to cancel deletion
   - Requirements: Must be within 7-day grace period
   - Deletes deletion request
   - Sends confirmation email
   - Logs cancellation to audit_logs
   - Returns: { success: true, message: 'Deletion cancelled' }

3. exports.permanentAccountDeletion = functions.pubsub.schedule('every 1 hour').onRun(async (context) => {
   - Cloud Scheduler: checks every hour
   - Finds all /users/{uid}/deletion with scheduledFor <= now
   - For each deletion:
     a) Anonymize user data:
        - /users/{uid}/reviews: set name='Deleted User', rating stays
        - /users/{uid}/messages: delete all messages
        - /users/{uid}/bookings: archive (don't delete - needed for disputes)
     b) Keep for legal/tax (7 years):
        - Copy /users/{uid}/bookings to /archived_bookings/{uid}
        - Copy payment records to /archived_payments/{uid}
     c) Delete everything else:
        - /users/{uid}/profile
        - /users/{uid}/consents
        - /users/{uid}/preferences
        - /users/{uid}/notifications
        - User Firebase Auth account
        - User Cloud Storage files
        - User sessions
     d) Log to audit_logs: { action: 'account_deleted', uid, timestamp }
   - Send final email: "Your account and data have been deleted"
   - Update admin dashboard

4. exports.deleteUserAccountPermanently = functions.https.onCall(async (data, context) => {
   - Admin-only function (check user.isAdmin)
   - Immediate deletion without grace period (for abuse/banned users)
   - Same anonymization as above
   - Log with admin ID who triggered it
   - Return: { success, deletionId, timestamp }

DATA RETENTION POLICY (BUILT-IN):

Delete immediately:
- Profile data (name, email, phone, address)
- Preferences (language, settings, location)
- Consent records
- Session tokens
- Notifications
- Messages (all)

Anonymize (keep for reputation):
- Reviews: keep rating/text, remove author name
- Bookings: anonymize contractor info, keep dates for stats

Archive 7 years (legal/tax):
- All payment transactions
- Invoice records
- Booking history (dates, amounts)
- Account creation date

NEVER DELETE (compliance):
- Audit logs (6-month minimum, usually 1 year)
- Tax records (7 years - legal requirement)
- Fraud investigation records (7 years)

REQUIREMENTS:
1. 7-day grace period (user can cancel)
2. Complete data deletion after period
3. No recovery after 7 days
4. All references deleted (reviews, messages, profiles)
5. User can request status via API
6. Email confirmation at each step
7. Audit trail of all deletions
8. GDPR compliant (within 30 days)
9. Error handling: If deletion fails, retry daily
10. Admin override for abuse cases

IMPORTS:
const admin = require('firebase-admin');
const functions = require('firebase-functions');

PRODUCTION-READY WITH ERROR HANDLING, RETRIES, LOGGING.
```

---

## Next: Paste into Cursor

Save to: `functions/src/functions/accountDeletion.js`
