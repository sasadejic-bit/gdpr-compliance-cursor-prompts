# PROMPT 10: Admin Compliance Dashboard

**Time:** 25 minutes | **Risk:** 🟡 MEDIUM | **Generates:** `functions/src/functions/complianceDashboard.js` (350 lines)

```
Create admin dashboard for GDPR compliance monitoring.

FILE: functions/src/functions/complianceDashboard.js

EXPORT: exports.getComplianceDashboard = functions.https.onCall(async (data, context) => {

RETURNS:

1. Consent Statistics
   - Total users: X
   - Users with consent: Y (percentage)
   - Marketing consent: Z%
   - Analytics consent: A%
   - Withdrawn consents (last 30 days): B

2. Data Requests
   - Pending data exports: X
   - Completed today: Y
   - Average time to fulfill: Z hours
   - Oldest pending request: date

3. Deletion Requests
   - Scheduled deletions: X
   - Deletions in grace period: Y
   - Completed deletions (last 30 days): Z
   - Cancellations: W

4. Audit Log Summary
   - Total events (last 30 days): X
   - Events by type (chart)
   - Errors/failures: Y
   - Admin actions: Z

5. Compliance Scorecard
   - Consent tracking: ✓ 100%
   - Data export ready: ✓ 100%
   - Account deletion working: ✓ 100%
   - Audit logs complete: ✓ 100%
   - HTTPS enforced: ✓ 100%
   - Overall: ✓ 98% GDPR Compliant

6. Alerts
   - Data export failures: X
   - Deletion failures: Y
   - Consent withdrawal errors: Z
   - System warnings: ...

7. Recent Actions
   - Last 10 admin accesses
   - Last 10 user data requests
   - Last 10 deletion requests

API:
- GET /api/dashboard → returns dashboard data
- GET /api/alerts → returns current alerts
- GET /api/audit-summary → returns audit log summary

SECURITY:
- Admin-only (check user.isAdmin)
- All access logged to audit_logs
- Request rate limited
- Data anonymized where needed

PRODUCTION-READY COMPLIANCE DASHBOARD.
```
