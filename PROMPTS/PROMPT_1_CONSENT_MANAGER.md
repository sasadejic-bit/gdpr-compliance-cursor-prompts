# PROMPT 1: Consent Manager Service

**Time to implement:** 20 minutes  
**Generates:** `functions/src/services/consentManager.js` (250 lines)  
**Risk Level:** 🔴 CRITICAL  
**Result:** Complete consent tracking with audit logging

---

## Copy-Paste This Entire Prompt into Cursor

```
Create a complete production-ready consent tracking service for GDPR compliance.

CONTEXT:
- Backend: Node.js + Firebase Cloud Functions
- Database: Cloud Firestore
- Mobile: Flutter app calling this service
- Project ID: handyman-32058
- Region: europe-west1

FILE TO CREATE:
functions/src/services/consentManager.js

EXPORT THESE FUNCTIONS:

1. recordConsent(userId, consentType, granted, context)
   Parameters:
   - userId: string (from Firebase Auth)
   - consentType: 'marketing' | 'analytics' | 'cookies' | 'terms'
   - granted: boolean (true = consented, false = rejected)
   - context: { ip, userAgent, timestamp }
   
   MUST DO:
   - Save to Firestore collection: /users/{userId}/consents/{consentType}
   - Store: { granted, recordedAt, ip, userAgent, consentVersion }
   - Log to audit trail: /audit_logs with action 'consent_recorded'
   - Return: { success: true, timestamp: ISO8601, recordId: uuid }
   - Error handling: Try/catch with logging
   - Validate: userId not empty, consentType is valid enum
   - Rate limit: Max 10 consent changes per hour per user

2. checkConsent(userId, consentType)
   Parameters:
   - userId: string
   - consentType: string (as above)
   
   MUST DO:
   - Query Firestore: /users/{userId}/consents/{consentType}
   - Return: boolean (true if consent granted, false if denied/never given)
   - Cache result for 5 minutes (memory cache)
   - Log all queries to audit trail
   - Handle case where consent never recorded (return false)

3. withdrawConsent(userId, consentType)
   Parameters:
   - userId: string
   - consentType: string
   
   MUST DO:
   - Update Firestore: /users/{userId}/consents/{consentType}
   - Set: { granted: false, withdrawnAt: ISO8601, reason: 'user_request' }
   - Log to audit trail with action 'consent_withdrawn'
   - Stop using that consent type immediately
   - Return: { success: true, timestamp, withdrawalId: uuid }
   - Error handling: Full try/catch

4. getConsentHistory(userId, limit = 50)
   MUST DO:
   - Return all consent records for user (paginated)
   - Include: date, type, status (granted/withdrawn), IP, version
   - Sort by most recent first
   - Limit: max 50 records per request

5. Cloud Scheduler function: cleanupExpiredConsents()
   MUST DO:
   - Query all /audit_logs with action = 'consent_recorded'
   - If not updated in 2 years, archive to /archive_logs
   - Run daily at 2 AM UTC
   - Error recovery: retry up to 3 times
   - Send daily summary email

IMPORTS REQUIRED:
const admin = require('firebase-admin');
const functions = require('firebase-functions');
const { v4: uuidv4 } = require('uuid');

CODE REQUIREMENTS:
1. Complete error handling with proper error messages
2. Full JSDoc comments for each function
3. Input validation for all parameters
4. Logging to Firebase Console and /audit_logs
5. Type hints (jsdoc @param @returns)
6. No console.log() - use admin.firestore() logging
7. Performance: Firestore queries optimized with indexes
8. Security: No sensitive data in logs, only metadata
9. GDPR compliance: Every action logged with timestamp/IP
10. Return consistent format: { success: boolean, data: object, error: string }

PRODUCTION-READY CODE WITH ALL ERROR HANDLING, VALIDATION, AND LOGGING.
```

---

## What Cursor Will Generate

A complete `consentManager.js` file with:

✅ **recordConsent()** - Saves consent to Firestore + audit logs  
✅ **checkConsent()** - Retrieves consent status (cached)  
✅ **withdrawConsent()** - Revokes consent with logging  
✅ **getConsentHistory()** - Paginated consent history  
✅ **cleanupExpiredConsents()** - Cloud Scheduler job  
✅ **Error handling** - All cases covered  
✅ **Input validation** - Type checking + constraints  
✅ **Audit logging** - Every action recorded  
✅ **JSDoc comments** - Full documentation  
✅ **Production ready** - Deploy immediately  

---

## Next Steps

1. Copy prompt above
2. Open Cursor
3. Paste into chat
4. Wait 30 seconds for code
5. Save to: `functions/src/services/consentManager.js`
6. Run: `firebase deploy --only functions`

**Time investment: 20 minutes total**
