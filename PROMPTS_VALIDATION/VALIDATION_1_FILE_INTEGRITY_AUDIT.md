# VALIDATION 1: File Integrity & Codebase Audit

**Stage:** Pre-Production Validation  
**Purpose:** Verify all 15 modules exist with complete logic  
**Time:** 30 minutes  
**Risk Level:** 🔴 CRITICAL (Must verify before integration testing)  
**Output:** Comprehensive file audit report  

---

## Copy-Paste This Entire Prompt into Cursor

```
Perform comprehensive file integrity audit on all GDPR compliance modules.

CONTEXT:
- Project: GDPR Marketplace (handyman-32058)
- All 15 modules generated but not yet validated
- Need to verify: file existence, code logic, completeness
- Environment: Node.js + Firebase + Flutter

FILE TO CREATE:
functions/scripts/validate-file-integrity.js

REQUIREMENTS:

1. FILE EXISTENCE AUDIT
   
   Check each file exists and report:
   
   Backend Files (Node.js):
   - functions/src/services/consentManager.js
   - functions/src/functions/dataExportAPI.js
   - functions/src/functions/accountDeletion.js
   - functions/src/functions/dataRetention.js
   - functions/src/middleware/httpsEnforcement.js
   - functions/src/functions/adminDashboard.js
   - functions/src/__tests__/gdpr.integration.test.js
   - functions/scripts/security-compliance-audit.js
   - functions/scripts/deploy-to-production.sh
   - functions/scripts/monitoring-dashboard.js
   
   Configuration Files:
   - firestore.rules
   - monitoring-config.json
   - .env.production
   
   Web Files:
   - website/privacy-policy.html
   - web/admin-dashboard.html
   
   Flutter Files:
   - flutter/lib/screens/consent_screen.dart
   - flutter/lib/screens/privacy_screen.dart
   
   For each file:
   - Check file exists
   - Report file size (KB)
   - Report lines of code
   - Check for common errors (incomplete brackets, syntax errors)
   
   Output format:
   {
     "file_audits": [
       {
         "file": "functions/src/services/consentManager.js",
         "exists": true,
         "size_kb": 12.5,
         "lines": 250,
         "syntax_valid": true,
         "status": "PASS"
       }
     ]
   }

2. CODE LOGIC AUDIT - Per Module
   
   a) consentManager.js - MUST CONTAIN:
      ✓ recordConsent() function
      ✓ checkConsent() function
      ✓ withdrawConsent() function
      ✓ getConsentHistory() function
      ✓ Firestore reference (admin.firestore())
      ✓ Audit logging (audit_logs collection)
      ✓ Timestamp handling (admin.firestore.Timestamp)
      ✓ Error handling (try-catch blocks)
      ✓ Input validation
      ✓ Return statements for all functions
      
      Check:
      - All 4 functions exported
      - All functions have parameters defined
      - All functions have return statements
      - Error handling present (try-catch)
      - No TODO comments
      
      Report: PASS/FAIL with details
   
   b) dataExportAPI.js - MUST CONTAIN:
      ✓ exportUserData() function
      ✓ generateZipFile() or similar
      ✓ createSecureDownloadLink() function
      ✓ 24-hour link expiry logic
      ✓ Multiple file format support (JSON, CSV)
      ✓ Firestore queries for all user data
      ✓ Complete error handling
      ✓ Audit logging for exports
      
      Check:
      - Exports all required functions
      - ZIP/archiver mentioned
      - 24-hour expiry logic present
      - Multiple formats handled
      - Error handling present
      
      Report: PASS/FAIL with details
   
   c) accountDeletion.js - MUST CONTAIN:
      ✓ scheduleAccountDeletion() function
      ✓ cancelAccountDeletion() function
      ✓ permanentAccountDeletion() function (Cloud Scheduler)
      ✓ deleteUserAccountPermanently() function
      ✓ 7-day grace period logic
      ✓ Data anonymization for reviews
      ✓ Payment record archival (7-year hold)
      ✓ Complete audit trail
      
      Check:
      - 7-day period calculated (604800 seconds or similar)
      - Grace period enforcement logic
      - Cancellation during grace period allowed
      - Permanent deletion after grace period
      - Payment preservation logic
      
      Report: PASS/FAIL with details
   
   d) dataRetention.js - MUST CONTAIN:
      ✓ cleanupExpiredMessages() - 1 year
      ✓ cleanupOldBookings() - 2 years
      ✓ archivePaymentRecords() - 7 years
      ✓ cleanupIPLogs() - 90 days
      ✓ Cloud Scheduler setup
      ✓ Retention period constants
      ✓ Batch processing for large datasets
      
      Check:
      - All cleanup functions present
      - Correct time calculations (1 year = 31536000s, etc.)
      - Archive vs delete logic correct
      - Batch operations for performance
      - Error handling for each cleanup job
      
      Report: PASS/FAIL with details
   
   e) privacy-policy.html - MUST CONTAIN:
      ✓ DOCTYPE declaration
      ✓ All 13 GDPR Article 13 elements
      ✓ Controller identity
      ✓ Processing purposes
      ✓ Legal basis
      ✓ Recipients
      ✓ Retention periods
      ✓ User rights (Articles 15-22)
      ✓ Complaint procedure
      ✓ DPA contact
      ✓ Supervisory authority contact
      ✓ Plain language (grade 8 reading level)
      
      Check:
      - HTML structure valid
      - All required sections present
      - Contact information accurate
      - Rights fully explained
      - Accessible language used
      
      Report: PASS/FAIL with details
   
   f) firestore.rules - MUST CONTAIN:
      ✓ User isolation rules (/users/{uid}/)
      ✓ Admin access rules
      ✓ Admin logging (audit_logs)
      ✓ Audit log immutability (no delete)
      ✓ Deleted user blocking
      ✓ Field-level security
      ✓ Processor access control
      
      Check:
      - match /users/{uid}/* rule present
      - request.auth.uid == uid validation
      - Admin role checking
      - Audit log append-only enforcement
      - Rule syntax valid
      
      Report: PASS/FAIL with details
   
   g) httpsEnforcement.js - MUST CONTAIN:
      ✓ HTTP rejection (403 Forbidden)
      ✓ Middleware function
      ✓ Security headers:
         - Content-Security-Policy
         - X-Content-Type-Options: nosniff
         - X-Frame-Options: DENY
         - X-XSS-Protection: 1; mode=block
         - Strict-Transport-Security
      ✓ HTTPS URL enforcement
      ✓ Certificate validation
      ✓ Audit logging for HTTP attempts
      
      Check:
      - HTTP rejection logic
      - All security headers set
      - HSTS preload enabled
      - Error handling for certificate issues
      - Logging of security violations
      
      Report: PASS/FAIL with details
   
   h) consent_screen.dart - MUST CONTAIN:
      ✓ Flutter Widget class
      ✓ Consent checkboxes (marketing, analytics, etc.)
      ✓ Privacy policy link
      ✓ Terms acceptance checkbox
      ✓ Submit button
      ✓ Error handling
      ✓ Form validation
      ✓ Accessibility attributes
      
      Check:
      - Valid Dart syntax
      - Flutter widgets imported
      - Form elements present
      - Error states handled
      - Navigation logic included
      
      Report: PASS/FAIL with details
   
   i) privacy_screen.dart - MUST CONTAIN:
      ✓ Flutter Widget class
      ✓ Privacy policy display
      ✓ Scrollable sections
      ✓ Download data button
      ✓ Delete account button
      ✓ DPA/contact links
      ✓ Navigation between sections
      ✓ Error handling
      
      Check:
      - Valid Dart syntax
      - All buttons functional
      - Navigation between sections
      - Link launching (url_launcher)
      - Data passing between screens
      
      Report: PASS/FAIL with details
   
   j) adminDashboard.js - MUST CONTAIN:
      ✓ API endpoints (GET/POST)
      ✓ Consent statistics endpoint
      ✓ Data export requests endpoint
      ✓ Account deletion requests endpoint
      ✓ Audit log viewer endpoint
      ✓ Alert summary endpoint
      ✓ Admin authentication
      ✓ Real-time metrics
      
      Check:
      - Express app setup
      - At least 5 endpoints
      - Authentication middleware
      - Data aggregation logic
      - Error handling
      
      Report: PASS/FAIL with details
   
   k) admin-dashboard.html - MUST CONTAIN:
      ✓ Dashboard layout
      ✓ Consent statistics display
      ✓ Request queue displays
      ✓ Alert section
      ✓ Real-time update capability
      ✓ Export functionality
      ✓ Responsive design
      ✓ CSS styling
      ✓ JavaScript interaction
      
      Check:
      - HTML structure valid
      - CSS for styling
      - JavaScript for interactivity
      - Responsive layout
      - Data binding/templating
      
      Report: PASS/FAIL with details
   
   l) gdpr.integration.test.js - MUST CONTAIN:
      ✓ 50+ test cases
      ✓ Jest test framework
      ✓ Describe blocks for organization
      ✓ It blocks for individual tests
      ✓ Expect assertions
      ✓ Mock Firestore
      ✓ Complete GDPR workflows
      ✓ Error scenarios
      
      Check:
      - Test framework setup
      - At least 50 test cases
      - Full coverage of workflows
      - Error path testing
      - Performance testing
      
      Report: PASS/FAIL with details
   
   m) security-compliance-audit.js - MUST CONTAIN:
      ✓ Dependency vulnerability scanning
      ✓ Firestore rules validation
      ✓ HTTPS enforcement check
      ✓ GDPR Article compliance checks
      ✓ Security headers validation
      ✓ Audit trail verification
      ✓ Comprehensive reporting
      
      Check:
      - All audit categories present
      - Security checking logic
      - Compliance verification
      - Report generation
      - Error handling
      
      Report: PASS/FAIL with details
   
   n) deploy-to-production.sh - MUST CONTAIN:
      ✓ Pre-deployment checks
      ✓ Tests execution
      ✓ Security audit execution
      ✓ Pre-deployment backup
      ✓ Firebase deployment commands
      ✓ Post-deployment verification
      ✓ Rollback capability
      ✓ Error handling
      
      Check:
      - Bash syntax valid
      - All safety checks present
      - Firebase CLI commands correct
      - Error handling (if statements)
      - Logging and reporting
      
      Report: PASS/FAIL with details
   
   o) monitoring-dashboard.js - MUST CONTAIN:
      ✓ Real-time health monitoring
      ✓ GDPR compliance tracking
      ✓ Performance metrics
      ✓ Error tracking
      ✓ Alert generation
      ✓ Report generation
      ✓ Dashboard creation
      
      Check:
      - Metrics collection logic
      - Alert thresholds
      - Report generation
      - Real-time updates
      - Error handling
      
      Report: PASS/FAIL with details

3. COMPLETENESS CHECK
   
   For each module, verify:
   - No TODO comments
   - No placeholder code
   - No console.log for debugging
   - All functions have JSDoc comments
   - All error cases handled
   - No hardcoded credentials
   - All imports present
   - All exports present
   
   Report format:
   {
     "completeness": {
       "missing_todos": false,  // true if TODOs found
       "has_placeholders": false,
       "debug_logging_found": false,
       "jsdoc_complete": true,
       "error_handling": true,
       "credentials_exposed": false,
       "imports_complete": true,
       "exports_complete": true,
       "status": "PASS"
     }
   }

4. FINAL REPORT GENERATION
   
   Generate comprehensive report:
   
   {
     "audit_date": "2026-01-13T11:15:00Z",
     "total_files_checked": 15,
     "files_exist": 15,
     "files_valid_syntax": 15,
     "modules_complete": 15,
     "overall_status": "PASS" or "FAIL",
     
     "file_audit_summary": {
       "backend_files": { count: 10, pass: 10 },
       "config_files": { count: 3, pass: 3 },
       "frontend_files": { count: 2, pass: 2 }
     },
     
     "module_audit_summary": {
       "consentManager": "PASS",
       "dataExportAPI": "PASS",
       "accountDeletion": "PASS",
       "dataRetention": "PASS",
       "privacyPolicy": "PASS",
       "firestoreRules": "PASS",
       "httpsEnforcement": "PASS",
       "flutterConsentUI": "PASS",
       "flutterPrivacyScreen": "PASS",
       "adminDashboard": "PASS",
       "integrationTests": "PASS",
       "securityAudit": "PASS",
       "deployment": "PASS",
       "monitoring": "PASS"
     },
     
     "issues_found": [],
     "missing_items": [],
     "recommendations": [],
     
     "next_steps": [
       "If all PASS: Proceed to VALIDATION_2 (Cross-Module Integration)",
       "If any FAIL: Fix issues immediately before integration testing"
     ]
   }

OUTPUT:
- Console: Summary of audit results
- File: audit-integrity-report.json (full detailed report)

RUN COMMAND:
node functions/scripts/validate-file-integrity.js

EXPECTED RESULT:
✓ All 15 files exist
✓ All files have valid syntax
✓ All modules complete
✓ No TODOs or placeholders
✓ All functions exported
✓ Error handling present
✓ Overall Status: PASS
✓ Next: Integration testing

PRODUCTION-READY FILE INTEGRITY AUDIT WITH COMPREHENSIVE VALIDATION.
```

---

## After Cursor Generates Code

### 1. Save the script
```bash
cp <generated-file> functions/scripts/validate-file-integrity.js
chmod +x functions/scripts/validate-file-integrity.js
```

### 2. Run the audit
```bash
node functions/scripts/validate-file-integrity.js
```

### 3. Review the report
```bash
cat audit-integrity-report.json | jq '.'

# Check overall status
cat audit-integrity-report.json | jq '.overall_status'
# Should show: "PASS"

# Check any issues
cat audit-integrity-report.json | jq '.issues_found'
# Should be: []
```

### 4. If any failures, fix immediately
```bash
# Review failures
cat audit-integrity-report.json | jq '.module_audit_summary | .[] | select(. == "FAIL")'

# Fix the module(s)
# Re-run audit
node functions/scripts/validate-file-integrity.js

# Repeat until all PASS
```

### 5. Commit
```bash
git add functions/scripts/validate-file-integrity.js audit-integrity-report.json
git commit -m "chore(validation): file integrity audit - all modules verified and complete"
```

---

## Success Criteria

✅ All 15 files exist  
✅ All files valid syntax  
✅ All modules complete  
✅ No TODOs or placeholders  
✅ All functions exported  
✅ Error handling present  
✅ Documentation complete  
✅ Overall Status: PASS  
✅ Ready for integration testing  

---

## Next Step

After file integrity audit passes → Use VALIDATION_2 (Cross-Module Integration)
