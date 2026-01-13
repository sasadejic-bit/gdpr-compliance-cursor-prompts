# PROMPT 13: Security & GDPR Compliance Audit

**Stage:** Pre-Production Verification  
**Time to implement:** 60 minutes  
**Generates:** `functions/scripts/security-compliance-audit.js` + `audit-report.json`  
**Risk Level:** 🔴 CRITICAL (Must pass 100% before production)  
**Result:** Complete security and compliance verification report

---

## Copy-Paste This Entire Prompt into Cursor

```
Create comprehensive security audit and GDPR compliance verification script.

CONTEXT:
- Project: GDPR Compliance Marketplace
- Backend: Node.js + Firebase Cloud Functions
- Database: Cloud Firestore
- Project ID: handyman-32058
- Region: europe-west1

FILE TO CREATE:
functions/scripts/security-compliance-audit.js

OUTPUT FILE:
audit-report.json (comprehensive audit results)

REQUIREMENTS:

1. SECURITY AUDIT SECTION
   
   a) Dependency Vulnerability Scan
      
      Task: Check all npm packages for known vulnerabilities
      
      Steps:
      1. Run npm audit on functions/package.json
      2. Parse npm audit output
      3. Categorize vulnerabilities:
         - Critical (fail if any)
         - High (allow max 3)
         - Medium (allow max 10)
         - Low (warn only)
      4. For each vulnerability, check:
         - Package name
         - Severity
         - CVE ID
         - Recommended fix version
      
      Report format:
      {
        "dependencies": {
          "total_packages": 45,
          "vulnerabilities_found": 5,
          "critical": 0,
          "high": 2,
          "medium": 3,
          "low": 0,
          "status": "PASS" // or "FAIL" if critical found
        }
      }
   
   b) Firestore Security Rules Validation
      
      Task: Verify security rules are correctly configured
      
      Checks:
      1. User data isolation
         - Users can only read their own /users/{uid}/* data
         - Verify: request.auth.uid == uid in rules
         - Test query: Non-owner accessing user data → DENIED
      
      2. Admin access control
         - Admins can access any data with logging
         - Verify: request.auth.token.admin == true
         - Test query: Admin accessing other user → ALLOWED + LOGGED
      
      3. Audit log immutability
         - Audit logs append-only (no delete/update)
         - Verify: delete/update == false in rules
         - Test: Attempt to delete audit log → DENIED
      
      4. Public data access
         - Public profiles readable (if publicProfile == true)
         - Private data not readable
         - Verify: correct field-level rules
      
      Report format:
      {
        "firestore_rules": {
          "user_isolation": "PASS",
          "admin_access_logging": "PASS",
          "audit_logs_immutable": "PASS",
          "public_data_control": "PASS",
          "status": "PASS"
        }
      }
   
   c) HTTPS & Security Headers Validation
      
      Task: Verify all traffic encrypted and secure headers present
      
      Checks:
      1. HTTP rejection
         - HTTP request returns 403 Forbidden
         - Test: curl http://function-url → 403
         - Verify: Strict-Transport-Security header present
      
      2. Security headers present
         - Content-Security-Policy (CSP)
         - X-Content-Type-Options: nosniff
         - X-Frame-Options: DENY
         - X-XSS-Protection: 1; mode=block
         - Test: HTTPS request shows all headers
      
      3. Certificate validation
         - SSL certificate valid
         - Not self-signed
         - Not expired
         - Test: openssl s_client check
      
      Report format:
      {
        "https_security": {
          "http_rejection": "PASS",
          "security_headers": {
            "content_security_policy": "PASS",
            "x_content_type_options": "PASS",
            "x_frame_options": "PASS",
            "x_xss_protection": "PASS"
          },
          "certificate_valid": "PASS",
          "status": "PASS"
        }
      }
   
   d) Input Validation Checks
      
      Task: Verify all inputs properly validated
      
      Checks:
      1. User ID validation
         - Empty strings rejected
         - Invalid characters rejected
         - Length limits enforced
         - Test inputs: "", "<script>", "' OR '1'=='1"
      
      2. Consent type validation
         - Only valid types accepted
         - Enum enforcement
         - Test invalid: "marketing-spam", "unknown"
      
      3. Rate limiting
         - Max 10 consent changes per hour per user
         - Test: Send 11 requests from same IP
         - 11th request rejected
      
      4. Payload size limits
         - Max request size enforced
         - Test: Send 100MB payload
         - Request rejected
      
      Report format:
      {
        "input_validation": {
          "user_id_validation": "PASS",
          "consent_type_enum": "PASS",
          "rate_limiting": "PASS",
          "payload_limits": "PASS",
          "status": "PASS"
        }
      }
   
   e) Data Encryption at Rest
      
      Task: Verify data encrypted when stored
      
      Checks:
      1. Firestore encryption enabled
         - Customer-managed encryption keys (CMEK) or Google-managed
         - Verify: gcloud firestore describe shows encryption
      
      2. Cloud Storage encryption
         - Exported data files encrypted
         - Bucket encryption enabled
      
      3. Backup encryption
         - Firestore backups encrypted
         - Keys managed securely
      
      Report format:
      {
        "encryption_at_rest": {
          "firestore_encryption": "PASS",
          "storage_encryption": "PASS",
          "backup_encryption": "PASS",
          "status": "PASS"
        }
      }

2. GDPR COMPLIANCE SECTION
   
   a) Article 13 - Information to be Provided
      
      Checklist (45 items total):
      
      [ ] Privacy Policy Available
      [ ] Identity of controller stated
      [ ] Contact info provided
      [ ] Processing purposes stated
      [ ] Legal basis documented
      [ ] Recipients listed
      [ ] Retention periods specified
      [ ] GDPR rights explained
      [ ] Complaint procedures documented
      [ ] Automated decision-making disclosed
      [ ] Profile assessment explained
      [ ] Legitimate interests stated
      [ ] Obligation vs. voluntary stated
      [ ] Data source disclosed
      [ ] Safeguards documented
      [ ] Data transfer mechanisms (SCC/BCR) documented
      [ ] Withdrawal mechanism explained
      [ ] Right to access documented
      [ ] Right to rectification documented
      [ ] Right to erasure documented
      [ ] Right to restriction documented
      [ ] Right to portability documented
      [ ] Right to object documented
      [ ] Right to not be profiled documented
      [ ] Supervisory authority listed
      [ ] DPA contact provided
      [ ] Third-party DPA links provided
      
      Verification Method:
      - Check privacyPolicy.html contains all required elements
      - Verify each element is clear and accessible
      - Test consent UI shows all required information
      
      Report format:
      {
        "article_13_compliance": {
          "items_total": 27,
          "items_present": 27,
          "missing_items": [],
          "status": "PASS" // or "FAIL" with missing items
        }
      }
   
   b) Article 15 - Right of Access
      
      Verification:
      1. User can download all personal data
      2. Data includes:
         - Profile information
         - All bookings
         - All messages
         - Preference settings
         - Consent history
         - Audit trail (own activities)
      3. Data in accessible format (JSON + CSV)
      4. Data complete and accurate
      5. Accessible within 30 days of request
      
      Test Scenario:
      - Create test user with various data
      - Request data export
      - Verify all data included
      - Check format accessibility
      
      Report format:
      {
        "article_15_right_access": {
          "export_available": "PASS",
          "data_completeness": "PASS",
          "data_accuracy": "PASS",
          "format_accessibility": "PASS",
          "status": "PASS"
        }
      }
   
   c) Article 17 - Right to be Forgotten
      
      Verification:
      1. User can request account deletion
      2. 7-day grace period enforced
      3. User can cancel within grace period
      4. Permanent deletion after grace period
      5. All user data deleted (except legally required)
      6. Reviews anonymized (not deleted)
      7. Payment records archived (7-year legal hold)
      8. User cannot log in after deletion
      9. No personal data recoverable
      
      Test Scenario:
      - Schedule account deletion
      - Verify 7-day wait enforced
      - Cancel deletion, verify restoration
      - Schedule again, wait 7 days
      - Verify permanent deletion
      - Confirm no data recovery
      
      Report format:
      {
        "article_17_right_erasure": {
          "deletion_requestable": "PASS",
          "grace_period_7days": "PASS",
          "cancellation_available": "PASS",
          "permanent_deletion": "PASS",
          "review_anonymized": "PASS",
          "payment_archived": "PASS",
          "no_data_recovery": "PASS",
          "status": "PASS"
        }
      }
   
   d) Article 32 - Security of Processing
      
      Verification:
      1. Encryption in transit (HTTPS)
      2. Encryption at rest (Firestore)
      3. Access controls (Firestore rules)
      4. Audit logging (complete)
      5. Incident response plan (documented)
      6. Data minimization (only needed data collected)
      7. Pseudonymization (where applicable)
      8. Regular backups (configured)
      9. Recovery procedures (documented)
      10. Employee training (documented)
      
      Report format:
      {
        "article_32_security": {
          "encryption_transit": "PASS",
          "encryption_rest": "PASS",
          "access_controls": "PASS",
          "audit_logging": "PASS",
          "incident_response": "PASS",
          "data_minimization": "PASS",
          "pseudonymization": "PASS",
          "backups": "PASS",
          "recovery_procedures": "PASS",
          "staff_training": "PASS",
          "status": "PASS"
        }
      }
   
   e) Articles 44-50 - International Transfers
      
      Verification:
      1. Data stored in EU (europe-west1 region)
      2. Standard Contractual Clauses (SCC) signed
      3. Data Protection Agreement (DPA) with Google Cloud
      4. Processor agreement in place
      5. Sub-processor list documented
      6. Transfer assessment completed
      7. No transfers outside EU/EEA
      8. SCC documentation accessible
      
      Test:
      - Verify Firebase project region is europe-west1
      - Check SCC document exists
      - Verify DPA signed
      - Check processor list up to date
      
      Report format:
      {
        "article_44_50_transfers": {
          "data_location_eu": "PASS",
          "scc_signed": "PASS",
          "dpa_in_place": "PASS",
          "processor_agreement": "PASS",
          "subprocessor_list": "PASS",
          "transfer_assessment": "PASS",
          "status": "PASS"
        }
      }

3. AUDIT TRAIL INTEGRITY
   
   Verification:
   - All user actions logged
   - No audit log entries missing
   - Logs immutable (cannot be deleted)
   - Logs include timestamp, userId, action, result
   - Logs accessible to user and admin
   - No sensitive data in logs
   - Audit logs retained for 7 years
   
   Report format:
   {
     "audit_trail_integrity": {
       "all_actions_logged": "PASS",
       "logs_immutable": "PASS",
       "logs_complete": "PASS",
       "sensitive_data_removed": "PASS",
       "retention_7years": "PASS",
       "status": "PASS"
     }
   }

4. INCIDENT RESPONSE
   
   Verification:
   - Incident response plan documented
   - Data breach notification procedure
   - Supervisory authority reporting (72 hours)
   - Affected user notification (within reasonable time)
   - Breach assessment procedure
   - DPA notification template
   
   Report format:
   {
     "incident_response": {
       "plan_documented": "PASS",
       "notification_procedure": "PASS",
       "72hour_reporting": "PASS",
       "user_notification": "PASS",
       "assessment_procedure": "PASS",
       "status": "PASS"
     }
   }

5. FINAL REPORT SUMMARY
   
   Report Structure:
   ```json
   {
     "audit_date": "2026-01-13T10:35:00Z",
     "project_id": "handyman-32058",
     "audit_version": "1.0",
     
     "security_audit": { ... },
     "gdpr_compliance": { ... },
     "audit_trail_integrity": { ... },
     "incident_response": { ... },
     
     "overall_status": "PASS" or "FAIL",
     "pass_percentage": 98.5,
     "items_total": 67,
     "items_passed": 66,
     "items_failed": 0,
     "items_warnings": 1,
     
     "failures": [],
     "warnings": [],
     "recommendations": [],
     
     "production_ready": true,
     "launch_approved": true
   }
   ```

OUTPUT:

1. Save full report to: audit-report.json
2. Print summary to console
3. Create compliance certificate (if all pass)
4. Generate PDF report (optional)

RUNNING THE AUDIT:

```bash
node functions/scripts/security-compliance-audit.js
```

Expected Output:
- Full report in audit-report.json
- Summary printed to console
- All checks pass (100%)
- Status: PRODUCTION READY
- Approval: LAUNCH AUTHORIZED

PRODUCTION-READY SECURITY AND GDPR COMPLIANCE AUDIT WITH COMPREHENSIVE VERIFICATION.
```

---

## After Cursor Generates Code

### 1. Save the script file
```bash
cp <generated-file> functions/scripts/security-compliance-audit.js
```

### 2. Run the security audit
```bash
cd functions

# Make script executable
chmod +x scripts/security-compliance-audit.js

# Run the audit
node scripts/security-compliance-audit.js
```

### 3. Review the audit report
```bash
# View the complete report
cat audit-report.json | jq '.'

# Check overall status
cat audit-report.json | jq '.overall_status'

# View any failures
cat audit-report.json | jq '.failures'

# View recommendations
cat audit-report.json | jq '.recommendations'
```

### 4. Fix any issues
```bash
# If any checks fail:
# 1. Review the failure description
# 2. Fix the underlying issue
# 3. Run audit again
node scripts/security-compliance-audit.js

# Repeat until all pass
```

### 5. Save the clean report
```bash
# Copy successful report
cp audit-report.json audit-report-FINAL.json

# Commit to Git
git add audit-report-FINAL.json
git commit -m "Final security and GDPR compliance audit - APPROVED - PROMPT_13"
```

---

## Success Criteria

✅ All security checks pass  
✅ All GDPR compliance checks pass  
✅ Overall status: PASS  
✅ Pass percentage: 100%  
✅ Production ready: true  
✅ Launch approved: true  
✅ No critical vulnerabilities  
✅ No compliance gaps  
✅ Audit report signed and dated  
✅ Ready for regulatory audits  

---

## Next Step

After audit passes → Use PROMPT_14 (Production Deployment Verification)
