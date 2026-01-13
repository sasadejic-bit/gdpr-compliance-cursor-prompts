# 🚨 CRITICAL: IMMEDIATE ACTION GET-WELL CHECKLIST
## 2-WEEK PLAN TO PRODUCTION READINESS (Jan 13-20)

**Status:** 🔴 RED - 5 CRITICAL BLOCKERS  
**Current Code:** 0% (Prompts only, no implementation)  
**Timeframe:** 2 weeks to launch  
**Owner:** You  
**Last Updated:** January 13, 2026

---

## 🚨 DO THIS RIGHT NOW (Today - Jan 13)

### STEP 1: Set Up Staging Firebase Project (30 min)

**Why:** Cannot test production code without staging environment

```bash
# Run this in your terminal:
firebase init staging

# Follow prompts:
# - Select project: handyman-staging (create new if needed)
# - Configure Firestore? Y
# - Configure Functions? Y
# - Configure Hosting? Y (optional)

# Verify it worked:
firebase projects:list
# Should show both handyman-32058 and handyman-staging
```

**Validation:** ✅ Run `firebase projects:list` and confirm staging project exists

---

### STEP 2: Generate PROMPT #1 - Consent Manager (30 min)

**Why:** First critical GDPR module - everything else depends on this

#### HOW TO USE CURSOR:

1. **Open Cursor IDE**
   - Download from https://cursor.sh/ if not installed
   - Open your project folder

2. **Copy the prompt below** (entire block)

3. **Paste into Cursor chat and wait**

---

## 📄 CURSOR PROMPT #1: CONSENT MANAGER
### COPY EVERYTHING BELOW THIS LINE

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

### END CURSOR PROMPT #1

---

**After Cursor generates code:**

```bash
# 1. Review the generated code
#    - Check for syntax errors
#    - Verify all 5 functions present
#    - Look for error handling

# 2. Save to correct location
cp <generated-file> functions/src/services/consentManager.js

# 3. Verify it compiles
cd functions
npm run build

# 4. Commit to Git
git add functions/src/services/consentManager.js
git commit -m "Add consentManager from Cursor - PROMPT_1"

# 5. Verify no syntax errors
node -c functions/src/services/consentManager.js
```

**Validation:** ✅ File saved, syntax valid, all functions present

---

### STEP 3: Quick Security Scan (20 min)

```bash
# Check for vulnerable dependencies
npm audit

# Note any results for BLOCKING ISSUE #3 (Jan 15-16)
# Don't fix yet, just note them
```

**Validation:** ✅ Know what vulnerabilities exist (if any)

---

## SCHEDULE FOR NEXT 2 WEEKS

### MONDAY (Jan 14)

#### MORNING (2 hours):
- [ ] Generate PROMPT #2 - Data Export API
- [ ] Generate PROMPT #3 - Account Deletion
- [ ] Review generated code

#### AFTERNOON (2 hours):
- [ ] Deploy functions to staging: `firebase deploy --only functions --project staging`
- [ ] Verify deployment in Firebase Console

---

### TUESDAY (Jan 15)

#### MORNING (2 hours):
- [ ] Generate PROMPT #4 - Privacy Policy
- [ ] Generate PROMPT #5 - Data Retention
- [ ] Review code

#### AFTERNOON (3 hours):
- [ ] Generate PROMPT #6 - Firestore Rules
- [ ] Deploy rules to staging: `firebase deploy --only firestore:rules --project staging`
- [ ] Validate rules with `firebase rules:test --project staging`

---

### WEDNESDAY (Jan 16)

#### MORNING (2 hours):
- [ ] Generate PROMPT #7 - HTTPS Enforcement
- [ ] Generate PROMPT #8 - Flutter Consent UI
- [ ] Review code

#### AFTERNOON (2 hours):
- [ ] Run full security scan: `npm audit`
- [ ] Fix any CRITICAL vulnerabilities

---

### THURSDAY (Jan 17)

#### MORNING (2 hours):
- [ ] Generate PROMPT #9 - Flutter Privacy Screen
- [ ] Generate PROMPT #10 - Admin Dashboard
- [ ] Deploy all to staging

#### AFTERNOON (3 hours):
- [ ] Security rules testing (10 test scenarios)
- [ ] Fix any security issues found

---

### FRIDAY (Jan 18)

#### MORNING (3 hours):
- [ ] Validate error handling
- [ ] Test all error scenarios
- [ ] Fix any error handling gaps

#### AFTERNOON (2 hours):
- [ ] Document rollback procedure
- [ ] Test rollback in staging

---

### SATURDAY (Jan 19)

#### MORNING (3 hours):
- [ ] GDPR compliance testing (all 45 points)
- [ ] Document compliance evidence
- [ ] Create compliance checklist

#### AFTERNOON (2 hours):
- [ ] Legal review (SCC + DPA)
- [ ] Final security audit

---

### SUNDAY (Jan 20)

#### MORNING (2 hours):
- [ ] Final validation of all modules
- [ ] Green light decision
- [ ] Prepare deployment procedure

#### AFTERNOON (1-2 hours):
- [ ] Deploy to production
- [ ] 24-hour monitoring begins

---

## 📋 THE 5 CRITICAL BLOCKERS

### BLOCKER #1: Code Not Generated (0%)
**Status:** Starting TODAY  
**Action:** Generate all 10 prompts using Cursor  
**Cursor Prompts:** Ready in `/PROMPTS/` directory  
**Time:** 4 hours total

### BLOCKER #2: Error Handling Unknown
**Status:** After code generated (Jan 14+)  
**Action:** Validate error handling in all modules  
**Time:** 5 hours

### BLOCKER #3: Security Vulnerabilities
**Status:** After code generated (Jan 15)  
**Action:** npm audit + penetration testing  
**Time:** 3 hours

### BLOCKER #4: GDPR Compliance Unverified
**Status:** After code deployed (Jan 17)  
**Action:** Test all GDPR features in staging  
**Time:** 4 hours

### BLOCKER #5: Staging Environment Missing
**Status:** Starting TODAY  
**Action:** Create handyman-staging Firebase project  
**Time:** 30 minutes

---

## 🗐️ COPY-PASTE READY PROMPTS

**All 10 prompts are available. Use them in this order:**

### PROMPT 1: Consent Manager (✅ DO FIRST)
**File:** PROMPTS/PROMPT_1_CONSENT_MANAGER.md  
**Time:** 20 min  
**Generates:** functions/src/services/consentManager.js  
**Status:** CRITICAL PATH - DO TODAY

**Quick Copy:**
```
Create a complete production-ready consent tracking service for GDPR compliance.
[... see full prompt above ...]
```

---

### PROMPT 2: Data Export API
**File:** PROMPTS/PROMPT_2_DATA_EXPORT_API.md  
**Time:** 20 min  
**Generates:** functions/src/functions/dataExportAPI.js

**Quick Start:**
```
Run: cat PROMPTS/PROMPT_2_DATA_EXPORT_API.md
Copy entire prompt
Paste into Cursor
Wait for generation
```

---

### PROMPT 3: Account Deletion
**File:** PROMPTS/PROMPT_3_ACCOUNT_DELETION.md  
**Time:** 20 min  
**Generates:** functions/src/functions/accountDeletion.js

---

### PROMPT 4: Privacy Policy
**File:** PROMPTS/PROMPT_4_PRIVACY_POLICY.md  
**Time:** 15 min  
**Generates:** public/privacyPolicy.html

---

### PROMPT 5: Data Retention
**File:** PROMPTS/PROMPT_5_DATA_RETENTION.md  
**Time:** 20 min  
**Generates:** functions/src/functions/dataRetention.js

---

### PROMPT 6: Firestore Rules (🟠 CRITICAL SECURITY)
**File:** PROMPTS/PROMPT_6_FIRESTORE_RULES.md  
**Time:** 20 min  
**Generates:** firestore.rules

---

### PROMPT 7: HTTPS Enforcement
**File:** PROMPTS/PROMPT_7_HTTPS_ENFORCEMENT.md  
**Time:** 15 min  
**Generates:** functions/src/functions/httpsEnforcement.js

---

### PROMPT 8: Flutter Consent UI
**File:** PROMPTS/PROMPT_8_FLUTTER_CONSENT_UI.md  
**Time:** 20 min  
**Generates:** lib/screens/consentUI.dart

---

### PROMPT 9: Flutter Privacy Screen
**File:** PROMPTS/PROMPT_9_FLUTTER_PRIVACY_SCREEN.md  
**Time:** 20 min  
**Generates:** lib/screens/privacyScreen.dart

---

### PROMPT 10: Admin Dashboard
**File:** PROMPTS/PROMPT_10_ADMIN_DASHBOARD.md  
**Time:** 20 min  
**Generates:** public/dashboard.html

---

## 🗣️ HOW TO USE EACH CURSOR PROMPT

### PATTERN FOR ALL PROMPTS:

```
1. Open Cursor IDE
2. Read the prompt file:
   cat PROMPTS/PROMPT_N_*.md
3. Copy the entire prompt (everything under "Copy-Paste This Entire Prompt into Cursor")
4. Paste into Cursor chat
5. Wait 30 seconds for code generation
6. Review generated code:
   - Check for syntax errors
   - Verify all functions present
   - Look for error handling
7. Copy generated code
8. Save to specified file path
9. Commit to Git:
   git add <new-file>
   git commit -m "Add <module> from Cursor - PROMPT_N"
```

---

## ✅ DAILY VALIDATION CHECKLIST

### END OF DAY (5 PM) - CONFIRM COMPLETED:

**MONDAY (Jan 13):**
- [ ] Staging Firebase project created
- [ ] PROMPT #1 (Consent Manager) generated
- [ ] Code compiles without errors
- [ ] Committed to Git
- [ ] npm audit run (results noted)

**TUESDAY (Jan 14):**
- [ ] PROMPT #2 (Data Export) generated
- [ ] PROMPT #3 (Account Deletion) generated
- [ ] Functions deployed to staging
- [ ] Deployment verified in Firebase Console

**WEDNESDAY (Jan 15):**
- [ ] PROMPT #4 (Privacy Policy) generated
- [ ] PROMPT #5 (Data Retention) generated
- [ ] PROMPT #6 (Firestore Rules) generated
- [ ] Rules validated and deployed
- [ ] Security vulnerabilities assessed

**THURSDAY (Jan 16):**
- [ ] PROMPT #7 (HTTPS) generated
- [ ] PROMPT #8 (Flutter UI) generated
- [ ] All code compiled and tested
- [ ] Security issues fixed

**FRIDAY (Jan 17):**
- [ ] PROMPT #9 (Privacy Screen) generated
- [ ] PROMPT #10 (Admin Dashboard) generated
- [ ] All 10 modules deployed
- [ ] Security rules tested (10 scenarios)

**SATURDAY (Jan 18):**
- [ ] Error handling validated
- [ ] Test scenarios passed
- [ ] Rollback plan documented

**SUNDAY (Jan 19):**
- [ ] GDPR compliance testing complete
- [ ] 45-point compliance checklist done
- [ ] Legal review passed
- [ ] Ready for production

---

## 📑 FILES TO TRACK

**After code generation, you should have:**

```
functions/
  ├─ src/
  └── services/
      └─ consentManager.js ✅ (PROMPT_1)
  ├─ src/
  └── functions/
      ├─ dataExportAPI.js ✅ (PROMPT_2)
      ├─ accountDeletion.js ✅ (PROMPT_3)
      ├─ dataRetention.js ✅ (PROMPT_5)
      └─ httpsEnforcement.js ✅ (PROMPT_7)

public/
  ├─ privacyPolicy.html ✅ (PROMPT_4)
  └─ dashboard.html ✅ (PROMPT_10)

firestore.rules ✅ (PROMPT_6)

lib/screens/ (Flutter)
  ├─ consentUI.dart ✅ (PROMPT_8)
  └─ privacyScreen.dart ✅ (PROMPT_9)
```

---

## 📞 SUPPORT

**If Cursor can't generate code:**
```
1. Try rephrasing the prompt
2. Provide more context about your setup
3. Break large prompts into smaller pieces
4. Copy generated errors back to Cursor for fixes
```

**If you get stuck:**
```
1. Check TECHNICAL_AUDIT_CRITICAL_ISSUES.md for detailed explanations
2. Review CURSOR_PROTOCOL.md for advanced usage
3. Check TESTING_CONCEPT_MARKETPLACE.md for test scenarios
```

---

## 💪 COMMITMENT CHECKLIST

Before starting, confirm:

- [ ] I have 2 weeks available (Jan 13-20)
- [ ] I can dedicate 10-15 hours over 2 weeks
- [ ] Cursor IDE is installed and working
- [ ] Firebase CLI is installed (`firebase --version`)
- [ ] Git is configured
- [ ] I understand the 5 critical blockers
- [ ] I'm ready to launch on Jan 20

**IF YOU CAN COMMIT TO ALL OF ABOVE:**

Start with Step 1 RIGHT NOW. You can do this! 🚀

---

**Next Step:** Set up staging Firebase project (30 min) → Then generate PROMPT #1

**Questions?** Review TECHNICAL_AUDIT_CRITICAL_ISSUES.md for detailed context.
