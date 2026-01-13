# VALIDATION 2: Cross-Module Integration Testing

**Stage:** Integration & Connectivity Verification  
**Purpose:** Verify all modules communicate correctly  
**Time:** 40 minutes  
**Risk Level:** 🔴 CRITICAL (Must verify module connectivity)  
**Output:** Integration test report with connectivity verification  

---

## Copy-Paste This Entire Prompt into Cursor

```
Create comprehensive cross-module integration tests to verify all modules communicate correctly.

CONTEXT:
- All 15 modules generated individually
- Need to verify: data flows between modules, no connectivity gaps
- Test: consent manager → firestore rules, dart UI → backend API
- Ensure: No circular dependencies, proper error propagation

FILE TO CREATE:
functions/src/__tests__/cross-module.integration.test.js

REQUIREMENTS:

1. MODULE DEPENDENCY VERIFICATION
   
   Check Dependencies:
   
   a) Consent Manager → Firestore Rules
      Test:
      - recordConsent() writes to /users/{userId}/consents/{type}
      - Firestore rules allow this write
      - Non-owner cannot read via rules
      - Admin can read with logging
      
      Assert:
      - recordConsent succeeds
      - Firestore rules enforced
      - Audit trail created
      - No permission errors
   
   b) Consent Manager → Admin Dashboard
      Test:
      - Admin can query all consents
      - Consent statistics accurate
      - Real-time updates work
      - Data aggregation correct
      
      Assert:
      - Admin queries return correct data
      - Statistics calculations accurate
      - No data loss
      - Performance acceptable
   
   c) Data Export API → Consent Manager
      Test:
      - exportUserData() retrieves all consents
      - Consent history included in export
      - Timestamps accurate
      - Format correct (JSON/CSV)
      
      Assert:
      - Export includes all consent data
      - No data missing
      - Formats valid
      - File generation succeeds
   
   d) Account Deletion → Data Retention
      Test:
      - scheduleAccountDeletion marks user
      - dataRetention respects marked users
      - Permanent deletion removes all data
      - Payment records preserved
      
      Assert:
      - Users marked for deletion
      - Data cleanup respects marks
      - Payment preservation verified
      - No premature deletion
   
   e) Account Deletion → Firestore Rules
      Test:
      - Deleted user cannot authenticate
      - Firestore rules block deleted user access
      - Audit logs retained
      - No orphaned data
      
      Assert:
      - Rules enforce deletion
      - No data access possible
      - Audit trail intact
      - Cleanup complete
   
   f) HTTPS Enforcement → All APIs
      Test:
      - HTTP requests rejected
      - All APIs require HTTPS
      - Security headers present
      - No information leakage
      
      Assert:
      - HTTP returns 403
      - HTTPS works
      - Headers correct
      - No exceptions
   
   g) Flutter Consent UI → Consent Manager API
      Test:
      - UI calls recordConsent()
      - Response correctly displayed
      - Errors handled gracefully
      - Loading states work
      
      Assert:
      - API calls succeed
      - Response formatting correct
      - Error messages displayed
      - State management works
   
   h) Flutter Privacy Screen → Data Export API
      Test:
      - Download button calls exportUserData()
      - File download works
      - Error handling functional
      - Link expiry enforced
      
      Assert:
      - Export initiated
      - File generated
      - Download link valid
      - Link expires after 24h
   
   i) Flutter Privacy Screen → Account Deletion
      Test:
      - Delete button calls scheduleAccountDeletion()
      - Grace period shown
      - Cancellation works
      - Confirmation dialog displays
      
      Assert:
      - Deletion scheduled
      - 7-day shown to user
      - Cancel button works
      - Confirmation received
   
   j) Admin Dashboard → Monitoring
      Test:
      - Dashboard shows real-time metrics
      - Monitoring data accurate
      - Alerts displayed
      - Export functionality works
      
      Assert:
      - Metrics current
      - Data consistent
      - Alerts firing
      - Exports complete

2. DATA FLOW VERIFICATION
   
   Test Complete Workflows:
   
   Workflow 1: User Registration → Consent Recording
   
   Steps:
   1. New user registers
   2. Consent form displayed (Flutter UI)
   3. User grants marketing + analytics
   4. consentManager.recordConsent() called
   5. Firestore rules allow write
   6. Audit log created
   7. Admin dashboard updated
   8. Monitoring tracks new consent
   
   Assertions:
   - Each step completes without error
   - Data flows correctly between modules
   - No data loss
   - All logging captured
   - Timestamps accurate
   - Performance acceptable
   
   Workflow 2: Data Export Request
   
   Steps:
   1. User clicks "Download My Data" (Flutter UI)
   2. dataExportAPI.exportUserData() called
   3. All user data retrieved (consents, bookings, messages, etc.)
   4. consentManager data included
   5. ZIP file created
   6. Secure download link generated (24-hour expiry)
   7. Admin dashboard shows export request
   8. Audit log shows export started
   9. User receives download link
   10. User downloads file
   11. Link expires after 24 hours
   12. Download link rejected (403 Forbidden)
   13. Monitoring tracks export completion
   14. Admin sees export in dashboard
   
   Assertions:
   - All data included
   - Format valid
   - Link works
   - Link expires
   - Auditing complete
   - Dashboard updated
   
   Workflow 3: Account Deletion
   
   Steps:
   1. User clicks "Delete Account" (Flutter UI)
   2. Confirmation shown with warning
   3. scheduleAccountDeletion() called
   4. Grace period starts (7 days)
   5. Admin dashboard shows scheduled deletion
   6. User receives confirmation email
   7. User can still log in (grace period)
   8. User can cancel deletion
   9. After 7 days, permanentAccountDeletion() runs
   10. All user data deleted
   11. Reviews anonymized
   12. Payment records archived (7 years)
   13. User cannot log in
   14. Firestore rules block access
   15. Audit trail preserved
   16. Monitoring tracks completion
   17. Admin sees deletion in dashboard
   
   Assertions:
   - Grace period enforced
   - Cancellation works
   - Permanent deletion complete
   - Payment preservation verified
   - Firestore rules enforce deletion
   - Audit trail intact
   - Monitoring accurate
   
   Workflow 4: Automated Data Retention
   
   Steps:
   1. Message created (timestamp T)
   2. After 1 year: dataRetention.cleanupExpiredMessages() runs
   3. Message moved to archive (not deleted)
   4. Booking created (timestamp T)
   5. After 2 years: dataRetention.cleanupOldBookings() runs
   6. Booking archived
   7. Payment created (timestamp T)
   8. After 7 years: still retained (legal requirement)
   9. IP logs created (timestamp T)
   10. After 90 days: dataRetention.cleanupIPLogs() runs
   11. IP logs deleted
   12. Admin dashboard shows cleanup status
   13. Monitoring tracks retention compliance
   
   Assertions:
   - Messages deleted after 1 year
   - Bookings deleted after 2 years
   - Payments retained 7 years
   - IP logs deleted 90 days
   - Retention policy followed
   - Compliance verified

3. ERROR PROPAGATION TESTING
   
   Test Error Handling:
   
   Scenario 1: Firestore Error
   - consentManager tries to write but Firestore fails
   - Error caught and logged
   - User gets friendly error message
   - Admin notified
   - Monitoring alerts
   - No data corruption
   
   Scenario 2: API Timeout
   - dataExportAPI times out
   - Export retried automatically
   - User shown "Please wait"
   - After max retries: user notified
   - Admin dashboard shows failure
   - No partial exports
   
   Scenario 3: Permission Denied
   - User tries to access other user's data
   - Firestore rules deny
   - API returns 403
   - Audit logs security event
   - No data leaked
   - Admin alerted
   
   Scenario 4: Invalid Input
   - User submits invalid consent type
   - consentManager validates
   - Rejects invalid type
   - Flutter UI shows error
   - No database write
   - Audit logs attempt

4. CIRCULAR DEPENDENCY CHECK
   
   Verify No Circular Dependencies:
   
   Check:
   - consentManager imports: admin, firestore only (no other modules)
   - dataExportAPI imports: admin, consentManager (one-way)
   - accountDeletion imports: admin, dataRetention (one-way)
   - dataRetention imports: admin only
   - httpsEnforcement imports: express only
   - adminDashboard imports: admin, all modules read-only
   - monitoring imports: admin, all modules read-only
   
   Assert:
   - No circular imports
   - Dependency tree valid
   - All dependencies declared
   - No implicit dependencies

5. TYPE & INTERFACE COMPATIBILITY
   
   Verify Data Types Match:
   
   consentManager Output:
   - recordConsent returns: { success: boolean, timestamp: Timestamp, data: {...} }
   - checkConsent returns: boolean
   - withdrawConsent returns: { success: boolean, timestamp: Timestamp }
   
   Verify All Consumers Accept These Types
   
   dataExportAPI Input:
   - Expects userId: string
   - Gets from auth context: string ✓
   - Expects options: { format: 'json'|'csv' }
   - Validates format ✓
   
   accountDeletion Input:
   - Expects userId: string
   - Gets from auth: string ✓
   - Expects reason: string (optional)
   - Handles omitted ✓
   
   Assert:
   - All type conversions explicit
   - No implicit type coercion
   - All data validated before use
   - Error messages helpful

6. PERFORMANCE & BOTTLENECK ANALYSIS
   
   Test Module Performance:
   
   consentManager.recordConsent():
   - Target: <500ms
   - Measure: Start to return
   - Assert: Passes target
   
   dataExportAPI.exportUserData():
   - Target: <3s for 1000 records
   - Measure: Query + ZIP + link generation
   - Assert: Passes target
   
   accountDeletion.permanentAccountDeletion():
   - Target: <5s for cascading deletes
   - Measure: All collections cleaned
   - Assert: Passes target
   
   dataRetention cleanup jobs:
   - Target: Batch process >1000/min
   - Measure: Records processed per minute
   - Assert: Achieves batch rate

TEST EXECUTION STRATEGY:

Test Framework: Jest + Supertest + Firebase Emulator

Test Organization:
- describe("Module Connectivity")
  - test("Consent Manager <-> Firestore Rules")
  - test("Consent Manager <-> Admin Dashboard")
  - ... (all 10 dependency tests)

- describe("Complete Workflows")
  - test("Workflow 1: Registration → Consent")
  - test("Workflow 2: Data Export")
  - test("Workflow 3: Account Deletion")
  - test("Workflow 4: Data Retention")

- describe("Error Propagation")
  - test("Firestore Error Handling")
  - test("API Timeout Handling")
  - test("Permission Denied")
  - test("Invalid Input")

- describe("Integration Validation")
  - test("No Circular Dependencies")
  - test("Type Compatibility")
  - test("Performance Benchmarks")

RUNNING TESTS:

```bash
cd functions
npm test -- --testPathPattern="cross-module.integration" --verbose
```

EXPECTED OUTPUT:

- All 25+ tests PASS
- No circular dependencies
- No type mismatches
- All workflows complete
- Error handling verified
- Performance targets met
- Data flows correct
- Integration: READY FOR PRODUCTION

PRODUCTION-READY CROSS-MODULE INTEGRATION TEST SUITE.
```

---

## After Cursor Generates Code

### 1. Save the test file
```bash
cp <generated-file> functions/src/__tests__/cross-module.integration.test.js
```

### 2. Run the integration tests
```bash
cd functions

# Start Firebase Emulator (if not running)
firebase emulators:start &

# Wait for emulator
sleep 5

# Run integration tests
npm test -- --testPathPattern="cross-module.integration" --verbose
```

### 3. Review test results
```bash
# Expected: ALL TESTS PASSING

# Check specific modules
npm test -- --testPathPattern="Module Connectivity" --verbose
npm test -- --testPathPattern="Complete Workflows" --verbose
npm test -- --testPathPattern="Error Propagation" --verbose
```

### 4. Analyze any failures
```bash
# If test fails, Cursor will show:
# - Which module failed
# - What data was expected
# - What was received
# - Where in the flow it broke

# Common issues:
# - consentManager not called by dataExportAPI
# - Type mismatch in return values
# - Firestore rules too restrictive
# - Circular dependency
```

### 5. Fix issues
```bash
# Review the failing test
grep -A 20 "test.*fails" functions/src/__tests__/cross-module.integration.test.js

# Check the implementation
grep -n "recordConsent" functions/src/functions/dataExportAPI.js

# Fix the issue
# Re-run tests
npm test -- --testPathPattern="cross-module.integration"

# Repeat until all pass
```

### 6. Commit
```bash
git add functions/src/__tests__/cross-module.integration.test.js
git commit -m "test(integration): cross-module connectivity and data flow verification - all tests passing"
```

---

## Success Criteria

✅ All 25+ tests passing  
✅ No circular dependencies  
✅ No type mismatches  
✅ All workflows complete  
✅ Error handling verified  
✅ Performance targets met  
✅ Data flows correct  
✅ Integration: READY FOR PRODUCTION  

---

## Next Step

After cross-module integration passes → Use VALIDATION_3 (Security Standards 2026)
