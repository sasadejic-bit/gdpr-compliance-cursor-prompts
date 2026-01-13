# PROMPT 12: Integration & E2E Tests - All GDPR Modules

**Stage:** Post-Implementation Verification  
**Time to implement:** 45 minutes  
**Generates:** `functions/src/__tests__/gdpr.integration.test.js`  
**Risk Level:** 🔴 CRITICAL (Must pass all tests before production)  
**Result:** Complete end-to-end GDPR workflow testing

---

## Copy-Paste This Entire Prompt into Cursor

```
Create comprehensive integration and end-to-end tests for all GDPR compliance modules working together.

CONTEXT:
- Backend: Node.js + Firebase Cloud Functions
- Testing Framework: Jest + Firebase Emulator + Supertest
- Project ID: handyman-32058
- Scope: Test all 10 modules working together
- Goal: Verify complete GDPR user journey

FILE TO CREATE:
functions/src/__tests__/gdpr.integration.test.js

REQUIREMENTS:

1. TEST SUITE: User Registration & Consent Flow
   
   Scenario: User signs up and grants marketing consent
   
   Test Cases:
   a) UserRegistration_ConsentRecorded_AuditTrailCreated
      - Given: New user signs up (userId: 'integration-test-001')
      - When: Consent given for 'terms', 'marketing', 'analytics'
      - Then: All 3 consents recorded to /users/{userId}/consents/
      - And: 3 audit logs created in /audit_logs
      - And: Each audit log includes timestamp, ip, userAgent
      - And: checkConsent() returns true for all 3 types
      - And: getConsentHistory() returns all 3 records
      - Expected duration: <1 second
   
   b) UserConsent_WithdrawalFlow_UpdatesEverywhere
      - Given: User previously granted marketing consent
      - When: User withdraws marketing consent
      - Then: /users/{userId}/consents/marketing.granted = false
      - And: Withdrawal logged to /audit_logs
      - And: checkConsent('marketing') returns false
      - And: getConsentHistory() shows withdrawal date
      - And: Both grant and withdrawal visible in history
      - Expected duration: <1 second

2. TEST SUITE: Data Export (Article 15)
   
   Scenario: User requests all their personal data
   
   Test Cases:
   a) DataExport_ReturnsAllUserData_InMultipleFormats
      - Given: User has bookings (5), messages (20), preferences (3), consents (4)
      - When: dataExportAPI.exportUserData(userId) called
      - Then: Returns ZIP file containing:
         - user_profile.json (profile data)
         - user_bookings.csv (booking history)
         - user_messages.json (all messages)
         - user_preferences.json (all settings)
         - user_consents.json (consent history)
         - user_audit_trail.json (all audit logs for this user)
      - And: Each file includes timestamps
      - And: CSV files are properly formatted (headers, quotes)
      - And: JSON files are valid JSON
      - And: No data is missing
      - Expected duration: <3 seconds
   
   b) DataExport_DownloadLink_24HourExpiry
      - Given: exportUserData() returned download URL
      - When: Download URL accessed within 24 hours
      - Then: ZIP file downloads successfully
      - And: File size >1KB (contains data)
      - And: File integrity verified (checksum)
      
      When: Download URL accessed after 24 hours
      - Then: Returns 403 Forbidden
      - And: Link cannot be used again
      - And: Security log shows 'expired_download_attempt'
   
   c) DataExport_AuditTrail_Logged
      - Given: User requests data export
      - When: exportUserData() called
      - Then: Audit log created with action 'data_export_requested'
      - And: Log includes requestedAt, expiresAt timestamps
      - And: Log includes download URL (hashed)
      - And: Log is accessible to user and admin

3. TEST SUITE: Account Deletion (Article 17)
   
   Scenario: User requests permanent account deletion with 7-day grace period
   
   Test Cases:
   a) AccountDeletion_7DayGracePeriod_UserCanCancel
      - Given: User requests account deletion
      - When: scheduleAccountDeletion(userId) called
      - Then: Returns { success: true, willDeleteAt: timestamp }
      - And: Deletion scheduled for 7 days from now
      - And: User account marked as 'scheduled_for_deletion'
      - And: Audit log created: action 'deletion_scheduled'
      
      When: User logs in within 7 days
      - Then: User can still access their account
      - And: Warning shown: "Account will be deleted on [date]"
      - And: Cancel deletion button available
      
      When: User clicks cancel
      - Then: cancelAccountDeletion(userId) executed
      - And: Deletion cancelled
      - And: Account restored to normal
      - And: Audit log created: action 'deletion_cancelled'
   
   b) AccountDeletion_PermanentRemoval_After7Days
      - Given: 7 days have passed since deletion scheduled
      - When: permanentAccountDeletion() Cloud Scheduler triggered
      - Then: User account completely deleted
      - And: /users/{userId} collection deleted
      - And: All user messages deleted
      - And: All user bookings deleted
      - And: All user consents deleted
      - But: Reviews anonymized (not deleted)
      - And: Payment records archived for 7 years
      - And: Audit log shows: action 'account_permanently_deleted'
   
   c) AccountDeletion_DataLost_NoRecovery
      - Given: Account permanently deleted
      - When: User attempts to log in
      - Then: Login fails (user not found)
      - And: User cannot access any data
      - And: Data cannot be recovered (no backups exposed)

4. TEST SUITE: Firestore Security Rules
   
   Scenario: Verify data isolation and access control
   
   Test Cases:
   a) SecurityRules_UserIsolation_CannotAccessOtherUserData
      - Given: Two users: user-A and user-B
      - When: User-A authenticated
      - Then: Can read /users/{user-A}/profile
      - And: Cannot read /users/{user-B}/profile
      - And: Query returns permission denied
      - And: Attempt logged to audit trail
   
   b) SecurityRules_AdminAccess_WithLogging
      - Given: Admin user authenticated (admin token = true)
      - When: Admin accesses /users/{any-user}/profile
      - Then: Access granted
      - And: Audit log created: action 'admin_data_access'
      - And: Log includes admin_uid, accessed_uid, timestamp
   
   c) SecurityRules_AuditLogs_AppendOnly
      - Given: Audit log entry created
      - When: Attempt to delete audit log
      - Then: Delete fails (permission denied)
      - And: Attempt logged (cannot delete own denial)
      
      When: Attempt to update audit log
      - Then: Update fails (permission denied)
      - And: Logs remain immutable

5. TEST SUITE: Data Retention & Cleanup
   
   Scenario: Verify automatic data retention and cleanup
   
   Test Cases:
   a) DataRetention_MessageDeletion_After1Year
      - Given: Message created 13 months ago
      - When: dataRetention.cleanupExpiredMessages() triggered
      - Then: Message moved to /archive_messages
      - And: Original message deleted
      - And: Audit log shows: action 'message_archived'
      
      When: Message created 1 month ago
      - Then: Message NOT deleted
      - And: Message still accessible to user
   
   b) DataRetention_BookingArchival_After2Years
      - Given: Booking completed 25 months ago
      - When: dataRetention.cleanupOldBookings() triggered
      - Then: Booking moved to /archive_bookings
      - And: User cannot access archived booking
      - And: Admin can still access for compliance
   
   c) DataRetention_PaymentArchival_7YearHold
      - Given: Payment transaction completed 7 years ago
      - When: dataRetention.cleanupExpiredData() triggered
      - Then: Payment record NOT deleted
      - And: Payment archived but retained for legal
      - And: Access restricted to admin+compliance team

6. TEST SUITE: HTTPS Enforcement
   
   Scenario: Verify all traffic encrypted and secure
   
   Test Cases:
   a) HTTPSEnforcement_HTTP_Rejected
      - Given: HTTP request to API (not HTTPS)
      - When: HTTP request sent
      - Then: Response: 403 Forbidden
      - And: Response header: Strict-Transport-Security
      - And: Audit log: action 'http_request_rejected'
   
   b) HTTPSEnforcement_SecurityHeaders_Present
      - Given: HTTPS request to API
      - When: Request processed
      - Then: Response includes headers:
         - Content-Security-Policy
         - X-Content-Type-Options: nosniff
         - X-Frame-Options: DENY
         - X-XSS-Protection: 1; mode=block
      - And: Certificate valid and not expired

7. TEST SUITE: Error Handling & Recovery
   
   Scenario: Verify graceful error handling
   
   Test Cases:
   a) ErrorHandling_FirestoreDown_GracefulFailure
      - Given: Firestore temporarily unavailable
      - When: recordConsent() called
      - Then: Returns { success: false, error: 'Service temporarily unavailable' }
      - And: Error does not expose internal details
      - And: User sees user-friendly message
      - And: Error logged to audit trail
      - And: Retry mechanism triggered automatically
   
   b) ErrorHandling_NetworkTimeout_Retried
      - Given: Network timeout on first attempt
      - When: Function called
      - Then: Automatically retried (up to 3 times)
      - And: Eventually succeeds or fails gracefully
      - And: User informed of status
   
   c) ErrorHandling_RateLimiting_Enforced
      - Given: User makes 100 consent changes
      - When: Each change processed
      - Then: First 10 succeed
      - And: Remaining 90 rejected with 'Rate limit exceeded'
      - And: Rate limit resets after 1 hour

8. TEST SUITE: Audit Trail Integrity
   
   Scenario: Verify audit logging for compliance
   
   Test Cases:
   a) AuditTrail_AllActionsLogged_Complete
      - Given: User performs these actions:
         1. Grant consent
         2. Check consent
         3. Withdraw consent
         4. Request data export
         5. Schedule account deletion
      - When: All actions completed
      - Then: Audit log contains 5+ entries
      - And: Each entry includes:
         - Timestamp (ISO8601)
         - Action name
         - userId
         - Result (success/failure)
         - IP address
         - User Agent
      - And: No entries missing
   
   b) AuditTrail_Immutable_CannotTamper
      - Given: Audit log entries created
      - When: Attempt to modify audit log
      - Then: Firestore rules deny access
      - And: Audit log entries unchanged
      - And: Tampering attempt logged separately
   
   c) AuditTrail_Accessible_ToUser
      - Given: User requests their audit trail
      - When: getAuditTrail(userId) called
      - Then: Returns all audit entries for that user
      - And: User can see all their activity
      - And: Consistent with GDPR Article 15 (data access)

9. TEST SUITE: Complete GDPR User Journey
   
   Scenario: End-to-end workflow simulating real user
   
   Test Case: CompleteGDPRJourney_AllFeaturesWorking
      
      Step 1: User Registration (time: 0s)
      - New user signs up
      - Consent form shown
      - User grants marketing, analytics, denies cookies
      - 3 consent records created
      
      Step 2: User Activity (time: 1s - 1 week later)
      - User creates bookings (5 total)
      - User sends messages (20 total)
      - User updates preferences
      - Data stored in Firestore
      
      Step 3: Data Access Request (time: 1 week)
      - User requests all their data (Article 15)
      - Export generated with all bookings, messages, consents
      - Download link provided (24-hour expiry)
      - User downloads data successfully
      
      Step 4: Consent Withdrawal (time: 1 week + 1 day)
      - User withdraws marketing consent
      - Audit trail updated
      - Marketing communications stopped
      - Withdrawal logged
      
      Step 5: Account Deletion Request (time: 2 weeks)
      - User requests account deletion
      - 7-day grace period starts
      - User notified of pending deletion
      - Audit trail shows deletion scheduled
      
      Step 6: Deletion Cancellation (time: 2 weeks + 3 days)
      - User changes mind
      - Cancels deletion
      - Account restored
      - Audit trail updated
      
      Step 7: Final Account Deletion (time: 3 weeks)
      - 7 days have passed
      - Cloud Scheduler runs permanentAccountDeletion()
      - All user data deleted
      - User cannot log in
      - Audit trail preserved for legal compliance
      
      Verification:
      - All steps completed successfully
      - Total time <30 seconds
      - All audit trails consistent
      - No data loss except intended deletion
      - All GDPR requirements met

10. TEST SUITE: Performance & Load
    
    Test Cases:
    a) Performance_ConsentOperations_SubSecond
       - recordConsent() completes in <500ms
       - checkConsent() completes in <100ms (cached)
       - withdrawConsent() completes in <500ms
       - getConsentHistory() completes in <1s
    
    b) Performance_DataExport_Under3Seconds
       - Export 100 records completes in <3s
       - Export 1000 records completes in <5s
       - Concurrent exports don't slow each other
    
    c) LoadTesting_ConcurrentUsers_Scalable
       - 10 concurrent users succeed
       - 100 concurrent users succeed
       - 1000 concurrent users handled (with queueing)
       - No data loss under load

TEST SETUP:

Imports:
const admin = require('firebase-admin');
const { initializeTestEnvironment } = require('@firebase/rules-unit-testing');
const request = require('supertest');
const app = require('../app'); // Express app

Test Environment:
- Firebase Emulator running
- Firestore emulator initialized
- Cloud Functions emulator running
- All 10 modules deployed to emulator

Test Data:
- Multiple test users with varying data
- Bookings from different dates
- Messages spanning weeks
- Consent history with grants and withdrawals

ASSERTIONS:

For integration tests:
1. All modules work together seamlessly
2. Data flows correctly between modules
3. Audit trail captures all activities
4. Errors handled gracefully
5. Performance acceptable
6. GDPR requirements met
7. Security rules enforced

COVERAGE REQUIREMENTS:

✓ All 10 GDPR modules tested
✓ All user journeys covered
✓ All error paths tested
✓ All security scenarios tested
✓ GDPR Articles 13, 15, 17, 32, 44-50 verified
✓ Integration between modules confirmed

REPORT OUTPUT:

After tests run:
```
npm test -- --testPathPattern="gdpr.integration"
```

Expected output:
- 50+ integration tests
- All tests passing
- Complete GDPR journey verified
- Performance metrics shown
- No failures or warnings

PRODUCTION-READY INTEGRATION TESTS WITH COMPLETE GDPR USER JOURNEY VERIFICATION.
```

---

## After Cursor Generates Code

### 1. Save the test file
```bash
cp <generated-file> functions/src/__tests__/gdpr.integration.test.js
```

### 2. Run integration tests
```bash
cd functions

# Start Firebase Emulator (if not already running)
firebase emulators:start &

# Wait for emulator to be ready
sleep 10

# Run integration tests
npm test -- --testPathPattern="gdpr.integration"
```

### 3. Check results
```bash
# View full test output
npm test -- --testPathPattern="gdpr.integration" --verbose

# Check performance metrics
npm test -- --testPathPattern="gdpr.integration" --logHeapUsage
```

### 4. Fix any failing tests
```bash
# Run in watch mode for debugging
npm test -- --testPathPattern="gdpr.integration" --watch

# Run specific test suite
npm test -- --testNamePattern="AccountDeletion_7DayGracePeriod"
```

### 5. Commit to Git
```bash
git add functions/src/__tests__/
git commit -m "Add comprehensive integration tests for all GDPR modules - PROMPT_12"
```

---

## Success Criteria

✅ All 50+ integration tests pass  
✅ Complete GDPR journey succeeds  
✅ All modules communicate correctly  
✅ Performance <3s for complex operations  
✅ No security issues  
✅ Audit trail complete and immutable  ✅ Ready for production deployment  

---

## Next Step

After all integration tests pass → Use PROMPT_13 (Security & Compliance Verification)
