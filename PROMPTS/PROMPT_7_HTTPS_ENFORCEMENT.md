# PROMPT 7: HTTPS Enforcement & Security Headers

**Time:** 15 minutes | **Risk:** 🟡 MEDIUM | **Generates:** `functions/src/middleware/httpsEnforcement.js` (100 lines)

```
Create HTTPS enforcement and security headers middleware.

FILE: functions/src/middleware/httpsEnforcement.js

EXPORT:
1. httpsEnforcement middleware
2. securityHeaders middleware
3. certificateValidation function

REQUIREMENTS:

1. HTTPS Enforcement
   - Reject all HTTP requests with 403 Forbidden
   - Redirect HTTP to HTTPS (optional)
   - Log violations to audit_logs
   - Except: health check endpoint (for load balancer)

2. Security Headers
   - Strict-Transport-Security: max-age=31536000; includeSubDomains
   - X-Content-Type-Options: nosniff
   - X-Frame-Options: DENY
   - Content-Security-Policy: strict policy
   - X-XSS-Protection: 1; mode=block
   - Referrer-Policy: strict-origin-when-cross-origin

3. Certificate Validation
   - Verify SSL/TLS certificate on startup
   - Check expiration date (warn if < 30 days)
   - Validate certificate matches domain
   - Log certificate details

4. Logging
   - Every HTTP request attempt logged
   - SSL errors logged with details
   - Certificate warnings emailed

PRODUCTION-READY HTTPS ENFORCEMENT WITH HEADERS.
```
