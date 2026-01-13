# 🧪 GDPR TESTING CONCEPT - Marketplace App
## Hands-On Test Procedures for Uber/Booking/Facebook-Style Service Marketplace

**Your App:** Service booking marketplace (craftspeople/contractors like TaskRabbit + Uber + Booking.com)  
**Testing Focus:** GDPR compliance + real user flows  
**Time Required:** 2-3 hours for complete test suite  
**Success Criteria:** All tests pass = Production ready  

---

## 🎯 TEST STRATEGY OVERVIEW

### What You're Testing
```
✓ GDPR Features (10 functions from Cursor prompts)
✓ User Flows (signup → booking → payment → review)
✓ Data Management (consent, storage, export, deletion)
✓ Security (HTTPS, validation, logging)
✓ Integration (backend functions ↔ Flutter UI)
```

### Test Environment
```
🔧 Local: Firebase Emulator (offline testing)
🔧 Staging: Firebase staging project (near-production)
🔧 Production: Real Firebase project (live users)
```

### Success = All Tests Pass
```
✅ User creates account with consent
✅ Data saved correctly to Firestore
✅ Privacy settings accessible
✅ Data export works
✅ Account deletion works
✅ Audit logs record everything
✅ HTTPS enforced
✅ Flutter UI shows consent screens
```

---

## 📋 PRE-TESTING CHECKLIST

### Before You Start Testing

- [ ] All 10 Cursor prompts generated ✓
- [ ] Code reviewed for errors
- [ ] Files saved in correct paths
- [ ] Firebase emulator installed
- [ ] Flutter app updated
- [ ] Git repository ready
- [ ] Postman/REST client ready (for API testing)
- [ ] Test data prepared

### Check These First

```bash
# 1. Firebase emulator ready?
firebase emulators:start
# Should show: ✔ All emulators ready! It is now safe to connect your app.

# 2. Flutter app ready?
flutter doctor
# Should show: No issues found!

# 3. Code files present?
ls functions/src/services/
ls lib/screens/
# Should list all generated files

# 4. Dependencies installed?
cd functions
npm install
cd ../
flutter pub get
# Should complete without errors
```

---

## 🚀 PHASE 1: UNIT TESTING (Backend Functions)
### Test each GDPR function individually

**Duration:** 45 minutes  
**Tools:** Firebase Emulator + Postman/cURL  
**Success:** All functions respond correctly  

### Test 1: Consent Manager (PROMPT 1)

**What to test:**
```
✓ Record new consent
✓ Check existing consent
✓ Withdraw consent
✓ Retrieve consent history
```

**Steps:**

1️⃣ **Start Firebase Emulator**
```bash
firebase emulators:start
```
Wait for: `✔ All emulators ready!`

2️⃣ **Open Postman (or use cURL)**

Create new HTTP request:
```
POST http://localhost:5001/handyman-32058/us-central1/recordConsent
Content-Type: application/json

{
  "userId": "test-user-123",
  "consentType": "marketing",
  "granted": true,
  "context": "signup_flow"
}
```

3️⃣ **Check Response**
```json
{
  "success": true,
  "timestamp": "2026-01-13T08:30:00Z",
  "consentId": "consent-abc123"
}
```

✅ **Test Passed If:**
- Response code is 200
- `success: true`
- Timestamp is current
- No errors in console

4️⃣ **Check Firestore (Emulator UI)**

Go to: `http://localhost:4000` (Firestore emulator)

Navigate to:
```
Collection: users
  Document: test-user-123
    Collection: consents
      Document: consent-abc123
        Data: {
          type: "marketing",
          granted: true,
          timestamp: 1673612400000,
          ip: "127.0.0.1",
          context: "signup_flow"
        }
```

✅ **Test Passed If:**
- Document exists
- All fields present
- Timestamp is recent
- IP address recorded

5️⃣ **Test Consent Check**

```
GET http://localhost:5001/handyman-32058/us-central1/checkConsent?userId=test-user-123&consentType=marketing
```

Expect response:
```json
{
  "granted": true,
  "timestamp": "2026-01-13T08:30:00Z"
}
```

✅ **Test Passed If:**
- Returns `granted: true`
- Matches recorded consent

6️⃣ **Test Consent Withdrawal**

```
POST http://localhost:5001/handyman-32058/us-central1/withdrawConsent
Content-Type: application/json

{
  "userId": "test-user-123",
  "consentType": "marketing"
}
```

Expect:
```json
{
  "success": true,
  "timestamp": "2026-01-13T08:31:00Z",
  "previousConsent": true
}
```

✅ **Test Passed If:**
- Withdrawal succeeds
- Can't find marketing consent after withdrawal
- Audit log records withdrawal

**Checklist for Test 1:**
- [ ] Record consent (200 response)
- [ ] Data in Firestore (check emulator)
- [ ] Check consent (returns correct value)
- [ ] Withdraw consent (successful)
- [ ] Can't find withdrawn consent
- [ ] No errors in console

---

### Test 2: Data Export (PROMPT 2)

**What to test:**
```
✓ User can request data export
✓ All their data included
✓ Generated as ZIP
✓ Download link works
✓ Expires after 24 hours
```

**Steps:**

1️⃣ **Request Data Export**

```
POST http://localhost:5001/handyman-32058/us-central1/requestDataExport
Content-Type: application/json

{
  "userId": "test-user-123",
  "email": "user@example.com"
}
```

2️⃣ **Check Response**

Expect:
```json
{
  "success": true,
  "exportId": "export-xyz789",
  "downloadLink": "https://storage.googleapis.com/...",
  "expiresAt": "2026-01-14T08:30:00Z",
  "size": "2.5MB"
}
```

3️⃣ **Check Firestore**

Go to emulator: `http://localhost:4000`

Navigate to:
```
Collection: data_exports
  Document: export-xyz789
    Data: {
      userId: "test-user-123",
      status: "completed",
      createdAt: 1673612400000,
      expiresAt: 1673698800000,
      size: 2621440
    }
```

4️⃣ **Verify Files Included**

Expected ZIP contents:
```
data-export/
├── account.json
│   └── { name, email, phone, created, updated, deleted }
├── bookings.json
│   └── [ { id, service, date, price, status, review } ]
├── messages.json
│   └── [ { id, from, to, content, timestamp } ]
├── reviews.json
│   └── [ { id, rating, content, timestamp } ]
├── payments.json
│   └── [ { id, amount, date, method, status } ]
└── metadata.json
    └── { exportDate, version, format }
```

✅ **Test Passed If:**
- Export created successfully
- All data files present
- ZIP structure correct
- Data matches what's in Firestore
- Expiration date is 24 hours later

**Checklist for Test 2:**
- [ ] Request export (200 response)
- [ ] Export created in Firestore
- [ ] Download link valid
- [ ] ZIP contains all files
- [ ] All user data included
- [ ] Expires after 24 hours

---

### Test 3: Account Deletion (PROMPT 3)

**What to test:**
```
✓ User requests deletion
✓ 7-day grace period starts
✓ Data still accessible during grace period
✓ After 7 days, account permanently deleted
✓ Reviews anonymized (no name)
✓ Payment records archived (kept 7 years)
```

**Steps:**

1️⃣ **Create Test User with Data**

First, create some test data:
```json
User: test-user-123
Bookings: 3 completed
Reviews: 2 reviews left
Messages: 5 conversations
Payments: $150 total
```

2️⃣ **Request Account Deletion**

```
POST http://localhost:5001/handyman-32058/us-central1/requestAccountDeletion
Content-Type: application/json

{
  "userId": "test-user-123",
  "reason": "user_request"
}
```

3️⃣ **Check Response**

Expect:
```json
{
  "success": true,
  "status": "pending_deletion",
  "deletionDate": "2026-01-20T08:30:00Z",
  "gracePeriod": 7,
  "canCancel": true
}
```

4️⃣ **Verify During Grace Period (Day 1)**

```
GET http://localhost:5001/handyman-32058/us-central1/getUserData?userId=test-user-123
```

Expect: User data still accessible (grace period active)

5️⃣ **Verify After Grace Period (Simulate Day 8)**

In Firestore emulator, check user document:
- User profile deleted
- Reviews anonymized (name removed)
- Payment records archived
- Messages deleted
- Audit log shows deletion

✅ **Test Passed If:**
- Deletion request succeeds
- Grace period enforced (7 days)
- Data accessible during grace period
- Permanent deletion after period
- Reviews properly anonymized
- Payments properly archived

**Checklist for Test 3:**
- [ ] Request deletion (200 response)
- [ ] Status is "pending_deletion"
- [ ] Deletion date is 7 days later
- [ ] Can cancel deletion
- [ ] User data still accessible (grace period)
- [ ] Audit log records deletion request

---

### Test 4: Firestore Rules (PROMPT 6)

**What to test:**
```
✓ User can read own data
✓ User cannot read other users' data
✓ Admin can access with logging
✓ Deleted users blocked from access
✓ Public data (reviews) readable
```

**Steps:**

1️⃣ **Test User Data Access (Own Data)**

As user: `test-user-123`

```javascript
// Should succeed
db.collection('users')
  .doc('test-user-123')
  .get()
// ✅ Returns data
```

2️⃣ **Test User Data Access (Other User)**

As user: `test-user-123` trying to access `test-user-456`

```javascript
// Should fail
db.collection('users')
  .doc('test-user-456')
  .get()
// ❌ Permission denied
```

3️⃣ **Test Public Reviews**

Anyone (no auth required)

```javascript
// Should succeed
db.collection('reviews')
  .where('public', '==', true)
  .get()
// ✅ Returns public reviews
```

4️⃣ **Test Admin Access**

As admin user:

```javascript
// Should succeed + log
db.collection('users')
  .doc('test-user-123')
  .get()
// ✅ Returns data + logs in audit_logs
```

✅ **Test Passed If:**
- Users can read own data
- Users cannot read other users' data
- Public data readable to all
- Admin access succeeds with logging
- Deleted users blocked

**Checklist for Test 4:**
- [ ] User reads own data (success)
- [ ] User reads other's data (denied)
- [ ] Public data accessible
- [ ] Admin access logged
- [ ] Deleted users blocked

---

## 🎨 PHASE 2: FLUTTER UI TESTING
### Test user-facing GDPR screens

**Duration:** 30 minutes  
**Tools:** Flutter emulator/device  
**Success:** All screens functional + data flows to backend  

### Test 5: Consent Screen (PROMPT 8)

**What to test:**
```
✓ Privacy policy shown
✓ Consent checkbox works
✓ Can't proceed without consent
✓ Saved to backend
```

**Steps:**

1️⃣ **Launch App**

```bash
flutter run
```

2️⃣ **Go to Signup Screen**

Tap: "Create Account"

3️⃣ **Fill Form**

```
Name: Test User
Email: test@example.com
Phone: +38123456789 (Balkans format)
Password: TestPass123!
```

4️⃣ **Check Consent Section**

Should show:
```
☐ I accept the Privacy Policy
☐ I accept the Terms of Service
☐ I want to receive marketing emails

[Read Privacy Policy] [Read Terms]
```

5️⃣ **Try to Submit Without Consent**

Tap: "Create Account" (without checking boxes)

Expect: Error message
```
"Please accept Privacy Policy to continue"
```

✅ **Test Passed If:**
- Consent form displayed
- Cannot proceed without acceptance
- Links work (navigate to policies)
- Acceptance saved

**Checklist for Test 5:**
- [ ] Consent screen appears
- [ ] All checkboxes visible
- [ ] Links clickable (policies load)
- [ ] Can't submit without consent
- [ ] Consent saved to backend

---

### Test 6: Privacy Settings Screen (PROMPT 9)

**What to test:**
```
✓ User can view privacy settings
✓ Can change consent preferences
✓ Can download data
✓ Can delete account
✓ Changes saved to backend
```

**Steps:**

1️⃣ **Log In**

```
Email: test@example.com
Password: TestPass123!
```

2️⃣ **Open Privacy Settings**

Tap: Menu → Settings → Privacy

3️⃣ **Check Privacy Settings Screen**

Should show:
```
📋 PRIVACY SETTINGS

 Consent Preferences
  ☑ Privacy Policy (Required)
  ☑ Terms of Service (Required)
  ☑ Marketing Emails (Changeable)
  [View Details] [Withdraw]

 Data Management
  [Download My Data] (2.5 MB)
  [View Data]
  [Request Deletion]

 Account & Legal
  [View Full Privacy Policy]
  [View DPA]
  [Contact DPO]
```

4️⃣ **Test Marketing Consent Toggle**

Tap: "Withdraw" next to "Marketing Emails"

Expect:
```
✓ Checkbox unchecked
✓ Message: "Preference updated"
✓ Backend receives withdrawal
✓ Audit log records it
```

5️⃣ **Test Download Data**

Tap: "Download My Data"

Expect:
```
⏳ Processing...
(Wait 5-30 seconds)
✓ Download started
✓ File: handyman-data-20260113.zip
✓ Contains all your data
```

6️⃣ **Test Delete Account**

Tap: "Request Deletion"

Expect:
```
⚠️ WARNING:
Your account will be permanently deleted in 7 days.
You can cancel during this period.

[Delete Account] [Cancel]
```

Tap: "Delete Account"

Expect:
```
✓ Deletion scheduled
✓ 7-day grace period shown
✓ You have until: Jan 20, 2026
✓ Email sent for confirmation
```

✅ **Test Passed If:**
- All options visible and clickable
- Consent changes saved
- Data download works
- Deletion flow works
- Backend receives all updates

**Checklist for Test 6:**
- [ ] Privacy screen loads
- [ ] All sections visible
- [ ] Consent toggles work
- [ ] Download data button works
- [ ] Delete account button works
- [ ] Changes saved to backend

---

## 🔄 PHASE 3: END-TO-END USER FLOW TESTING
### Test complete user journeys

**Duration:** 45 minutes  
**Tools:** Flutter app + Firebase emulator  
**Success:** All flows work from signup to deletion  

### Test 7: Complete User Lifecycle

**User Story:**
```
John (contractor) signs up for the marketplace
→ Accepts consent
→ Creates service profile
→ Gets bookings
→ Leaves reviews
→ Later requests data export
→ Eventually deletes account
```

**Steps:**

1️⃣ **Step 1: Signup with Consent (Day 1)**

```
App → "Sign Up"
Name: John Smith
Email: john@example.com
Phone: +38765123456
Type: Service Provider (Craftsman)
Password: SecurePass123!

☑ Privacy Policy
☑ Terms of Service
☑ Marketing Emails

→ "Create Account"
```

✅ **Check:**
- Account created
- Consent stored in Firestore
- Email verification sent
- Audit log records signup + consent

2️⃣ **Step 2: Complete Profile (Day 1)**

```
Name: John Smith
Service: Plumbing
Experience: 10 years
Rate: $30/hour
Service Area: Maldives + nearby atolls

→ "Save Profile"
```

✅ **Check:**
- Profile saved
- Service searchable
- Photos uploaded

3️⃣ **Step 3: Get Booking (Day 5)**

```
Customer: "Need pipe repair"
Date: Jan 15, 2026
Location: Malé
Time: 10:00 AM
Price: $80

Status: ACCEPTED → COMPLETED → REVIEWED
```

✅ **Check:**
- Booking created
- Payment processed (PayU integration)
- Review stored
- Data recorded

4️⃣ **Step 4: Request Data Export (Day 20)**

```
Settings → Privacy → Download My Data
→ "Download My Data"
```

✅ **Check:**
- Export generated
- Contains:
  - Account info
  - All bookings (with that customer review)
  - Messages
  - Payments
  - Profile info
- ZIP downloadable

5️⃣ **Step 5: Request Account Deletion (Day 25)**

```
Settings → Privacy → Request Deletion
→ "Delete Account"
→ Confirm deletion
```

✅ **Check:**
- Deletion scheduled
- Grace period: 7 days (until Feb 1)
- Email confirmation sent
- Can still log in during grace period

6️⃣ **Step 6: Permanent Deletion (Day 32 - After Grace Period)**

```
(System automatically runs scheduled deletion)
```

✅ **Check:**
- Account no longer exists
- Cannot log in
- Profile removed from search
- Review anonymized (no name)
- Payment records archived
- Messages deleted
- Consent records deleted
- Audit log records deletion

**Final Verification:**
```
Firestore Collections Check:
✓ /users/john-123 → DELETED
✓ /reviews → Anonymized
✓ /audit_logs → Shows deletion
✓ /archived_payments → Contains John's payment records (7-year retention)
```

**Checklist for Test 7:**
- [ ] Signup with consent successful
- [ ] Profile creation works
- [ ] Booking flow works
- [ ] Payment processed
- [ ] Review stored
- [ ] Data export works (contains all data)
- [ ] Deletion request succeeds
- [ ] Grace period enforced
- [ ] Permanent deletion after grace period
- [ ] Data properly anonymized/archived
- [ ] All audit logs recorded

---

## 🔐 PHASE 4: SECURITY & COMPLIANCE TESTING
### Verify GDPR security requirements

**Duration:** 30 minutes  
**Tools:** Browser DevTools + Postman  
**Success:** All security measures in place  

### Test 8: HTTPS Enforcement (PROMPT 5)

**What to test:**
```
✓ HTTP requests rejected
✓ HTTPS required
✓ Security headers present
✓ No data in transit unencrypted
```

**Steps:**

1️⃣ **Test HTTPS Only**

```bash
# Try HTTP (should fail)
curl -i http://api.example.com/auth/login
# Expected: 403 Forbidden or 301 Redirect to HTTPS

# Try HTTPS (should work)
curl -i https://api.example.com/auth/login
# Expected: 200 OK or 400 (invalid request)
```

2️⃣ **Check Security Headers**

```bash
curl -i https://api.example.com/health
```

Expect headers:
```
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self'
```

3️⃣ **Check Certificate**

```bash
openssl s_client -connect api.example.com:443
# Should show valid certificate
# Verify: Subject CN matches domain
# Verify: Not expired
```

✅ **Test Passed If:**
- HTTP rejected or redirected
- HTTPS required
- All security headers present
- Certificate valid

**Checklist for Test 8:**
- [ ] HTTP requests rejected
- [ ] HTTPS required
- [ ] Strict-Transport-Security header present
- [ ] No mixed content
- [ ] Certificate valid

---

### Test 9: Data Validation & Injection Prevention

**What to test:**
```
✓ Invalid inputs rejected
✓ No SQL injection possible
✓ No XSS attacks possible
✓ No CSRF attacks possible
```

**Steps:**

1️⃣ **Test Email Validation**

```
Invalid email: "test@"
Expected: Error "Invalid email"

Invalid email: "test@.com"
Expected: Error "Invalid email"

Valid email: "test@example.com"
Expected: Accepted
```

2️⃣ **Test SQL Injection Prevention**

```
Input: " OR 1=1 --"
In: Consent type field
Expected: Treated as literal string (not SQL)
Result: No consent found for type " OR 1=1 --"
```

3️⃣ **Test XSS Prevention**

```
Input: "<script>alert('xss')</script>"
In: Review comment field
Expected: Stored as literal text
When displayed: Shows as text, not executed
```

4️⃣ **Test CSRF Protection**

```
Attempt: POST without CSRF token
Expected: 403 Forbidden

With valid CSRF token:
Expected: 200 OK
```

✅ **Test Passed If:**
- Invalid inputs rejected
- No injection vulnerabilities
- Data properly sanitized
- CSRF protected

**Checklist for Test 9:**
- [ ] Email validation works
- [ ] SQL injection prevented
- [ ] XSS prevented
- [ ] CSRF tokens enforced
- [ ] Data sanitized

---

### Test 10: Audit Logging (PROMPT 1-10)

**What to test:**
```
✓ All actions logged
✓ Logs contain: who, what, when, why
✓ Logs immutable (can't be deleted)
✓ Logs include timestamps & IPs
```

**Steps:**

1️⃣ **Create Various Actions**

```
1. Sign up → Check audit log
2. Accept consent → Check audit log
3. Withdraw consent → Check audit log
4. Request data export → Check audit log
5. Request account deletion → Check audit log
6. Admin access user data → Check audit log
```

2️⃣ **Verify Audit Log Entries**

Firestore: `/audit_logs` collection

Expect entries like:

```json
{
  "id": "audit-123",
  "userId": "test-user-123",
  "action": "consent_recorded",
  "details": {
    "consentType": "marketing",
    "granted": true,
    "context": "signup_flow"
  },
  "timestamp": 1673612400000,
  "ip": "192.168.1.100",
  "userAgent": "Mozilla/5.0...",
  "status": "success"
}
```

3️⃣ **Verify Immutability**

```bash
# Try to delete audit log entry
# Expected: Firestore rules deny deletion
# Status: Permission denied

# Only admins can read, never delete
```

✅ **Test Passed If:**
- All actions logged
- Logs contain complete info
- Logs can't be deleted
- Logs have timestamps + IPs

**Checklist for Test 10:**
- [ ] All actions logged
- [ ] Logs contain action details
- [ ] Timestamps recorded
- [ ] IPs recorded
- [ ] Logs immutable
- [ ] Admin can view logs

---

## ✅ PHASE 5: COMPLIANCE VERIFICATION
### Verify GDPR compliance checklist

**Duration:** 20 minutes  
**Tools:** Checklist document  
**Success:** All items checked  

### Compliance Checklist

**Legal Requirements:**
- [ ] Privacy Policy published and accessible
- [ ] Privacy Policy includes all 13 GDPR Article 13 elements
- [ ] Terms of Service updated with GDPR clause
- [ ] Data Protection Agreement (DPA) in place
- [ ] Incident response plan documented
- [ ] Withdrawal consent option visible
- [ ] Contact info for DPO provided

**Technical Requirements:**
- [ ] Consent recorded for each user
- [ ] Consent timestamp & IP logged
- [ ] Data export works (all user data included)
- [ ] Data export format: JSON + CSV
- [ ] Account deletion functional
- [ ] 7-day grace period enforced
- [ ] Data anonymization working (reviews)
- [ ] Data retention policies implemented
  - [ ] Messages: 1 year
  - [ ] Bookings: 2 years
  - [ ] Payments: 7 years (archived, not deleted)
  - [ ] IPs: 90 days
- [ ] Firestore rules enforced
  - [ ] User data isolation
  - [ ] Admin access logging
  - [ ] Public data accessible
- [ ] HTTPS enforced (no HTTP)
- [ ] Security headers present
- [ ] All actions audit logged
- [ ] Audit logs immutable

**Operational Requirements:**
- [ ] Monitoring dashboard live
- [ ] Alert system configured
- [ ] Backup procedures documented
- [ ] Support team trained
- [ ] Incident response procedure tested
- [ ] User notification procedure ready

**User Communication:**
- [ ] Privacy policy prominently displayed
- [ ] Consent clear and affirmative
- [ ] Settings accessible after signup
- [ ] Easy withdrawal option
- [ ] Deletion request obvious
- [ ] Help/support for privacy questions

**Testing Coverage:**
- [ ] Unit tests pass (backend functions)
- [ ] Integration tests pass (UI + backend)
- [ ] End-to-end user flows tested
- [ ] Security testing completed
- [ ] Edge cases tested
- [ ] Error handling verified

---

## 📊 TEST RESULTS SUMMARY

### Create a Test Report

```markdown
# GDPR Testing Report
## Date: January 13, 2026

### Phase 1: Unit Testing
✅ Test 1: Consent Manager - PASSED
✅ Test 2: Data Export - PASSED
✅ Test 3: Account Deletion - PASSED
✅ Test 4: Firestore Rules - PASSED

### Phase 2: Flutter UI Testing
✅ Test 5: Consent Screen - PASSED
✅ Test 6: Privacy Settings - PASSED

### Phase 3: End-to-End Testing
✅ Test 7: Complete User Lifecycle - PASSED

### Phase 4: Security Testing
✅ Test 8: HTTPS Enforcement - PASSED
✅ Test 9: Data Validation - PASSED
✅ Test 10: Audit Logging - PASSED

### Phase 5: Compliance
✅ Legal Requirements: 7/7 PASSED
✅ Technical Requirements: 20/20 PASSED
✅ Operational Requirements: 6/6 PASSED
✅ User Communication: 6/6 PASSED
✅ Testing Coverage: 6/6 PASSED

## Overall Result: ✅ READY FOR PRODUCTION

## Issues Found: 0 Critical, 0 Major

## Approved By: [Your Name]
Date: January 13, 2026
Signature: ___________
```

---

## 🚨 TROUBLESHOOTING COMMON ISSUES

### Issue: "Function not found" error
```
Problem: Cursor generated code doesn't match expected path
Solution:
1. Check file path matches prompt (e.g., functions/src/services/consentManager.js)
2. Check imports in index.js
3. Restart Firebase emulator: firebase emulators:stop && firebase emulators:start
```

### Issue: "CORS error" when testing
```
Problem: Cross-origin request blocked
Solution:
1. Emulator runs on localhost (no CORS)
2. If testing from different port, add CORS header
3. In production, CORS should be configured correctly
```

### Issue: "Firestore rules deny access"
```
Problem: Rules too restrictive during testing
Solution:
1. Check user is authenticated
2. Check user ID matches document ID
3. For testing, temporarily allow access (remember to restrict again)
4. Verify rules logic is correct
```

### Issue: "Flutter can't connect to emulator"
```
Problem: App can't reach Firebase functions on localhost
Solution:
1. Make sure emulator is running: firebase emulators:start
2. Use correct emulator URL in Flutter app
3. On Android emulator, use 10.0.2.2 instead of localhost
4. Check firewall isn't blocking port 5001
```

### Issue: "Data not appearing in Firestore"
```
Problem: Write operation succeeded but no data
Solution:
1. Check Firestore rules allow write
2. Check document path matches query
3. Check data structure matches what you're looking for
4. Refresh emulator UI (Ctrl+R)
5. Check function error logs
```

---

## 🎯 NEXT STEPS AFTER TESTING

### If All Tests Pass ✅

1. **Create Summary Report**
   - Document all test results
   - List any minor issues found
   - Get approval for production

2. **Deploy to Staging**
   ```bash
   firebase deploy --only functions --project handyman-staging
   firebase deploy --only firestore:rules --project handyman-staging
   ```

3. **Test in Staging (24 hours)**
   - Invite beta users
   - Monitor for issues
   - Check logs

4. **Deploy to Production**
   ```bash
   firebase deploy --only functions --project handyman-32058
   firebase deploy --only firestore:rules --project handyman-32058
   ```

5. **Monitor First 24 Hours**
   - Watch error logs
   - Check data exports
   - Verify audit logs
   - Ensure no violations

### If Tests Fail ❌

1. **Identify Issue**
   - Which test failed?
   - What was expected vs actual?
   - Is it code or config?

2. **Fix**
   - Ask Cursor to fix the issue
   - Or fix manually
   - Commit changes

3. **Re-run Test**
   - Run just the failing test
   - Verify fix works
   - Run full suite again

4. **Document**
   - What was the issue?
   - How did you fix it?
   - Prevent future occurrences

---

## 📱 MARKETPLACE-SPECIFIC TEST SCENARIOS
### Because your app is like Uber/Booking/Facebook

### Scenario 1: Service Provider Perspective

```
John (Plumber) signs up
  → Provides consent
  → Creates service profile (Plumbing)
  → Sets rate ($30/hour)
  → Gets bookings from customers
  → Leaves reviews

Later: Requests data export
  → Gets ZIP with all bookings
  → Gets ZIP with all reviews left on him
  → Gets ZIP with all messages

Even later: Deletes account
  → 7-day grace period
  → Reviews anonymized ("Anonymous Plumber")
  → Payment records archived
  → Account gone
```

✅ **Test:**
- [ ] Service provider signup with consent
- [ ] Profile creation + search visibility
- [ ] Receives bookings
- [ ] Reviews stored correctly
- [ ] Data export contains all info
- [ ] Account deletion anonymizes reviews

### Scenario 2: Customer Perspective

```
Sarah (Customer) signs up
  → Provides consent
  → Searches for plumber
  → Books John for pipe repair
  → Payment processed ($80 PayU)
  → Leaves review ("Great service!")

Later: Requests data export
  → Gets all bookings
  → Gets all reviews she left
  → Gets all messages with providers

Even later: Deletes account
  → Her reviews become "Anonymous"
  → Her bookings archived (history)
  → Her messages deleted
```

✅ **Test:**
- [ ] Customer signup with consent
- [ ] Search functionality
- [ ] Booking flow
- [ ] Payment integration (PayU)
- [ ] Review creation
- [ ] Data export includes all customer data
- [ ] Account deletion anonymizes reviews

### Scenario 3: Messages/Chat

```
Customer messages Provider
Provider responds
Messages stored in Firestore

1-year retention:
  → After 1 year: Messages deleted automatically

If customer deletes account:
  → Messages deleted immediately

If provider deletes account:
  → Messages deleted from provider side
  → Customer still sees (but message shows "Provider deleted account")
```

✅ **Test:**
- [ ] Messages stored
- [ ] 1-year retention policy works
- [ ] Messages deleted on account deletion
- [ ] Proper user experience ("User deleted account")

### Scenario 4: Reviews & Ratings

```
Customer leaves review on Provider
Review stored with:
  - Customer name
  - Rating (1-5 stars)
  - Comment
  - Date
  - Photos

If customer deletes account:
  → Review becomes: "Anonymous - 5 stars - Great!"
  → Provider still sees review (valuable)
  → Customer name removed (privacy respected)

If provider deletes account:
  → Review deleted from provider's profile
  → Customer's review history shows "(Service deleted)"
```

✅ **Test:**
- [ ] Reviews stored with full data
- [ ] Customer deletion anonymizes review
- [ ] Provider deletion removes review from profile
- [ ] Data export includes all reviews

### Scenario 5: Payment Records

```
Payment flow:
  1. Customer books service ($80)
  2. Payment charged to PayU card
  3. Payment stored in Firestore
  4. Status: "completed"
  5. Money goes to provider

7-year archival:
  → Payment kept for 7 years (tax compliance)
  → After 7 years: deleted

Account deletion:
  → Payment still kept (legal requirement)
  → Associated with account ID (not visible to anyone)
  → Not deleted until 7 years pass
```

✅ **Test:**
- [ ] Payment recorded correctly
- [ ] Stored in Firestore
- [ ] 7-year retention enforced
- [ ] Not deleted even if account deleted
- [ ] Audit log records transaction

---

## 🏆 SUCCESS METRICS

### You're ready for production when:

✅ **All 10 Tests Pass**
- Unit tests (4/4)
- UI tests (2/2)
- E2E tests (1/1)
- Security tests (3/3)

✅ **Zero Critical Issues**
- No data leaks
- No GDPR violations
- No security vulnerabilities
- No unhandled errors

✅ **Complete Compliance**
- 45/45 checklist items checked
- Legal review completed
- Technical audit passed
- Operational readiness confirmed

✅ **Team Ready**
- Support trained
- Monitoring configured
- Incident plan ready
- Documentation complete

✅ **User Experience Good**
- Consent flows smooth
- Privacy settings intuitive
- Data export works
- Account deletion clear

### Estimated Timeline

```
Today (Jan 13): Read this document (1 hour)
Jan 13-14: Phase 1 (Unit testing) - 2 hours
Jan 14: Phase 2 (UI testing) - 1 hour
Jan 14: Phase 3 (E2E testing) - 1.5 hours
Jan 14-15: Phase 4 (Security) - 1.5 hours
Jan 15: Phase 5 (Compliance) - 1 hour
Jan 16: Fix any issues + documentation - 2 hours
Jan 17: Final review + approval - 1 hour

Total: 11 hours
Start: Jan 13
Done: Jan 17
Production launch: Jan 20 (after staging)
```

---

## 🎉 YOU'RE DONE!

When all tests pass, you have:

✅ **95%+ GDPR Compliance**
✅ **Production-Ready Code**
✅ **Secure Infrastructure**
✅ **Complete Audit Trail**
✅ **User Trust**
✅ **Legal Protection**
✅ **Ready to Scale**

**Next:** Deploy to production and monitor! 🚀

---

**Test Status:** Ready to begin  
**Owner:** You + Cursor  
**Timeline:** 11 hours total  
**Success Rate:** 95%+ (if you follow this guide)  
**Production Readiness:** High  

**Let's test! 🧪**
