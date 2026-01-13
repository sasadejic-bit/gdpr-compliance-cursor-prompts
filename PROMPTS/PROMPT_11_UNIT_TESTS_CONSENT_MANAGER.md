# PROMPT 11: Unit Tests - Consent Manager

**Stage:** Post-Implementation Verification  
**Time to implement:** 30 minutes  
**Generates:** `functions/src/services/__tests__/consentManager.test.js`  
**Risk Level:** 🟠 HIGH (Must pass all tests before production)  
**Result:** Complete test coverage for consent tracking

---

## Copy-Paste This Entire Prompt into Cursor

```
Create comprehensive unit tests for the Consent Manager service to verify GDPR compliance.

CONTEXT:
- Backend: Node.js + Firebase Cloud Functions
- Testing Framework: Jest with Firebase Emulator
- Service: functions/src/services/consentManager.js (already generated)
- Project ID: handyman-32058
- Coverage Goal: >95% code coverage

FILE TO CREATE:
functions/src/services/__tests__/consentManager.test.js

REQUIREMENTS:

1. TEST SUITE: recordConsent() function
   
   Test Cases:
   a) recordConsent_ValidInput_Success
      - Given: Valid userId, consentType, granted=true
      - When: recordConsent() called
      - Then: Returns { success: true, timestamp, recordId }
      - And: Data saved to /users/{userId}/consents/{consentType}
      - And: Audit log created with action 'consent_recorded'
      - And: Timestamp is ISO8601 format
      - And: recordId is valid UUID
   
   b) recordConsent_InvalidConsentType_Failure
      - Given: Invalid consentType (not in enum)
      - When: recordConsent() called
      - Then: Throws error with message 'Invalid consentType'
      - And: No data saved to Firestore
      - And: Error logged to audit trail
   
   c) recordConsent_EmptyUserId_Failure
      - Given: userId is empty string
      - When: recordConsent() called
      - Then: Throws error with message 'userId cannot be empty'
      - And: No data saved
   
   d) recordConsent_FirestoreFailure_ErrorHandling
      - Given: Firestore connection fails (mock error)
      - When: recordConsent() called
      - Then: Returns { success: false, error: 'Failed to save consent' }
      - And: Error logged to audit trail with severity 'high'
      - And: Error message does not expose sensitive data
   
   e) recordConsent_RateLimiting_Exceeded
      - Given: User has already recorded 10 consent changes in past hour
      - When: recordConsent() called for 11th time
      - Then: Throws error with message 'Rate limit exceeded'
      - And: Logs 'rate_limit_exceeded' to audit trail
   
   f) recordConsent_WithIP_Captured
      - Given: context contains ip='203.0.113.45'
      - When: recordConsent() called
      - Then: IP address stored in consent record
      - And: IP address logged in audit trail

2. TEST SUITE: checkConsent() function
   
   Test Cases:
   a) checkConsent_ConsentGranted_ReturnsTrue
      - Given: User previously granted 'marketing' consent
      - When: checkConsent(userId, 'marketing') called
      - Then: Returns true
      - And: Query logged to audit trail
   
   b) checkConsent_ConsentDenied_ReturnsFalse
      - Given: User previously denied 'marketing' consent
      - When: checkConsent(userId, 'marketing') called
      - Then: Returns false
   
   c) checkConsent_NeverRecorded_ReturnsFalse
      - Given: No consent record exists for user/type
      - When: checkConsent(userId, 'analytics') called
      - Then: Returns false
      - And: Does not throw error
   
   d) checkConsent_CachingWorks_No2ndQuery
      - Given: User calls checkConsent() twice within 5 minutes
      - When: Both calls made
      - Then: First call queries Firestore
      - And: Second call returns cached result (no new query)
      - And: Firestore query count = 1
   
   e) checkConsent_CacheExpires_NewQuery
      - Given: Cache TTL is 5 minutes
      - When: Query made after 6 minutes
      - Then: New Firestore query executed
      - And: Cache updated with new result

3. TEST SUITE: withdrawConsent() function
   
   Test Cases:
   a) withdrawConsent_ValidInput_Success
      - Given: Valid userId, consentType
      - When: withdrawConsent() called
      - Then: Returns { success: true, timestamp, withdrawalId }
      - And: /users/{userId}/consents/{consentType}.granted = false
      - And: withdrawnAt timestamp set
      - And: Audit log created with action 'consent_withdrawn'
      - And: withdrawalId is valid UUID
   
   b) withdrawConsent_UpdatesAuditTrail_Complete
      - Given: User withdraws 'marketing' consent
      - When: withdrawConsent() called
      - Then: Audit trail shows:
         - Original grant date
         - New withdrawn date
         - Reason: 'user_request'
   
   c) withdrawConsent_AlreadyWithdrawn_Idempotent
      - Given: User already withdrew consent
      - When: withdrawConsent() called again
      - Then: Returns { success: true }
      - And: Timestamp updated to new withdrawal time
      - And: Does not throw error
   
   d) withdrawConsent_EmptyConsentType_Failure
      - Given: consentType is empty
      - When: withdrawConsent() called
      - Then: Throws error 'consentType cannot be empty'

4. TEST SUITE: getConsentHistory() function
   
   Test Cases:
   a) getConsentHistory_MultipleLegacyRecords_ReturnsPaginated
      - Given: User has 100 consent records
      - When: getConsentHistory(userId, 50) called
      - Then: Returns first 50 records
      - And: Records sorted by date (newest first)
      - And: Each record includes: date, type, status, IP, version
   
   b) getConsentHistory_DefaultLimit_50Records
      - Given: No limit specified
      - When: getConsentHistory(userId) called
      - Then: Returns max 50 records (default)
      - And: Does not return more than 50
   
   c) getConsentHistory_MaxLimit_Enforced
      - Given: Limit requested = 1000
      - When: getConsentHistory(userId, 1000) called
      - Then: Returns max 50 records (enforced maximum)
      - And: Does not exceed 50
   
   d) getConsentHistory_EmptyHistory_ReturnsEmptyArray
      - Given: User has no consent records
      - When: getConsentHistory(userId) called
      - Then: Returns []
      - And: Does not throw error

5. TEST SUITE: cleanupExpiredConsents() Cloud Scheduler function
   
   Test Cases:
   a) cleanupExpiredConsents_Archives2YearOldRecords
      - Given: Consent record dated 2+ years ago
      - When: cleanupExpiredConsents() triggered
      - Then: Record moved from /audit_logs to /archive_logs
      - And: Original record deleted from audit_logs
   
   b) cleanupExpiredConsents_KeepsRecentRecords
      - Given: Consent record dated 1 year ago
      - When: cleanupExpiredConsents() triggered
      - Then: Record stays in /audit_logs
      - And: Record not archived
   
   c) cleanupExpiredConsents_RetryOnFailure
      - Given: Firestore fails on first attempt
      - When: cleanupExpiredConsents() triggered
      - Then: Retries up to 3 times
      - And: Eventually succeeds or logs final error
   
   d) cleanupExpiredConsents_SendsSummaryEmail
      - Given: Cleanup job completes
      - When: cleanupExpiredConsents() triggered
      - Then: Summary email sent
      - And: Email includes count of archived records

6. TEST SUITE: Error Handling & Security
   
   Test Cases:
   a) AllFunctions_NoSensitiveDataInLogs
      - Given: Operations complete successfully
      - When: Error occurs
      - Then: Error logs do not contain passwords
      - And: Error logs do not contain full user records
      - And: Error logs do not contain consent values (only types)
   
   b) AllFunctions_InputValidation
      - Given: Various malicious inputs (SQL injection, XSS, etc.)
      - When: Functions called with malicious input
      - Then: All inputs sanitized
      - And: Functions do not execute malicious code
      - And: Errors returned safely
   
   c) AllFunctions_AuditLogging_Complete
      - Given: All function calls
      - When: Functions execute
      - Then: Every action logged to /audit_logs
      - And: Logs include timestamp, userId, action, result
      - And: Logs are immutable (cannot be edited/deleted)

7. TEST SUITE: GDPR Compliance
   
   Test Cases:
   a) GDPRArticle13_ConsentRecorded_WithMetadata
      - Given: User provides consent
      - When: recordConsent() called
      - Then: Consent recorded with:
         - Timestamp of consent (Article 13.2.a)
         - IP address (Article 13.2.b - transparency)
         - User Agent (for audit trail)
         - Consent type (for granularity)
   
   b) GDPRArticle15_DataAccessComplete
      - Given: User requests data access
      - When: getConsentHistory() called
      - Then: Returns complete history (Article 15.3)
      - And: Includes all metadata
      - And: No data withheld
   
   c) GDPRArticle17_DeletionRemovesConsent
      - Given: User requests account deletion
      - When: Account deletion triggered
      - Then: All consent records deleted
      - And: Archive records deleted
      - And: Audit trail marked as 'deleted_user'

TEST SETUP:

Imports:
const admin = require('firebase-admin');
const { initializeTestEnvironment } = require('@firebase/rules-unit-testing');
const consentManager = require('../consentManager');

Test Environment:
- Use Firebase Emulator for unit tests
- Mock Firestore instance for each test
- Reset state between tests
- Use test data fixtures

Test Data Fixtures:
- testUserId: 'test-user-001'
- testConsents: [
    { type: 'marketing', granted: true, date: new Date() },
    { type: 'analytics', granted: false, date: new Date() }
  ]
- testContext: { ip: '203.0.113.45', userAgent: 'Mozilla/5.0...' }

ASSERTIONS:

For each test, verify:
1. Function returns expected result
2. Firestore data matches expectation
3. Audit logs created correctly
4. No errors thrown (or expected errors thrown)
5. Performance acceptable (<500ms)
6. No sensitive data in logs or errors
7. GDPR requirements met

COVERAGE REQUIREMENTS:

✓ Statement coverage: >95%
✓ Branch coverage: >90%
✓ Function coverage: 100%
✓ Line coverage: >95%

REPORT OUTPUT:

After tests run, generate coverage report:
```
npm test -- --coverage
```

Expected output:
- Coverage summary for consentManager.js
- Any uncovered branches highlighted
- All tests passing (no failures)
- Total test count: 35+ tests

PRODUCTION-READY UNIT TESTS WITH COMPREHENSIVE COVERAGE, ERROR HANDLING, AND GDPR VALIDATION.
```

---

## After Cursor Generates Code

### 1. Save the test file
```bash
cp <generated-file> functions/src/services/__tests__/consentManager.test.js
```

### 2. Run the tests
```bash
cd functions

# Run all tests
npm test

# Run with coverage report
npm test -- --coverage

# Run specific test suite
npm test -- --testNamePattern="recordConsent"
```

### 3. Check coverage
```bash
# View coverage summary
cat coverage/coverage-summary.json

# Coverage should show:
# - Statements: >95%
# - Branches: >90%
# - Functions: 100%
# - Lines: >95%
```

### 4. Fix any failing tests
```bash
# If tests fail, review the error
# Update consentManager.js if needed
# Re-run tests until all pass
npm test -- --watch
```

### 5. Commit to Git
```bash
git add functions/src/services/__tests__/
git commit -m "Add comprehensive unit tests for Consent Manager - PROMPT_11"
```

---

## Success Criteria

✅ All 35+ tests pass  
✅ Coverage >95%  
✅ No console errors  
✅ No warnings  
✅ Performance <500ms per test  
✅ GDPR requirements verified in tests  

---

## Next Step

After all tests pass → Use PROMPT_12 (Integration Tests)
