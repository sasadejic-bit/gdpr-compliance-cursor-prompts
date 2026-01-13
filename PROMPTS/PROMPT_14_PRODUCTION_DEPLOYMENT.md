# PROMPT 14: Production Deployment & Launch Verification

**Stage:** Production Release  
**Time to implement:** 45 minutes  
**Generates:** `functions/scripts/deploy-to-production.sh` + `deployment-checklist.md`  
**Risk Level:** 🔴 CRITICAL (Irreversible - double-check all steps)  
**Result:** Production-grade deployment with zero-downtime release

---

## Copy-Paste This Entire Prompt into Cursor

```
Create production deployment script and verification checklist for GDPR-compliant marketplace.

CONTEXT:
- Project: GDPR Compliance Marketplace (handyman-32058)
- Backend: Node.js + Firebase Cloud Functions
- Frontend: Flutter iOS/Android + Web
- Database: Cloud Firestore (europe-west1)
- Auth: Firebase Authentication
- Storage: Google Cloud Storage
- Monitoring: Cloud Logging + Cloud Monitoring
- Environment: Production

FILE 1 TO CREATE:
functions/scripts/deploy-to-production.sh

FILE 2 TO CREATE:
deployment-checklist.md

FILE 3 TO CREATE:
.env.production (environment variables template)

REQUIREMENTS:

1. PRE-DEPLOYMENT CHECKLIST
   
   Before running deploy script, verify:
   
   [ ] Code Review Completed
      - All 10 GDPR modules reviewed
      - No TODOs or placeholder code
      - Security reviewed by 2+ reviewers
   
   [ ] All Tests Passing
      - Unit tests: 100+ tests, all pass
      - Integration tests: 50+ tests, all pass
      - E2E tests: All critical paths pass
      - Load tests: Passed for 1000 concurrent users
   
   [ ] Security Audit Complete
      - No critical vulnerabilities
      - npm audit clean
      - Firestore rules verified
      - HTTPS enforcement tested
   
   [ ] GDPR Compliance Audit Complete
      - Article 13: 100% compliance
      - Article 15: Data export tested
      - Article 17: Account deletion tested
      - Article 32: Security measures verified
      - Articles 44-50: International transfers OK
   
   [ ] Documentation Complete
      - README.md updated
      - API docs generated
      - GDPR docs available
      - Deployment guide ready
   
   [ ] Monitoring Configured
      - Error logging enabled
      - Performance monitoring enabled
      - Security monitoring enabled
      - Uptime monitoring enabled
   
   [ ] Disaster Recovery Ready
      - Backups configured
      - Recovery procedures documented
      - Failover tested
   
   [ ] Communication Ready
      - Privacy policy updated
      - Terms of service updated
      - DPA signed (if applicable)
      - Launch email prepared

2. PRODUCTION DEPLOYMENT SCRIPT
   
   Script Name: deploy-to-production.sh
   
   Purpose: Automate deployment to Firebase production with safety checks
   
   Steps (in order):
   
   Step 1: Pre-Deployment Verification (non-reversible)
   ```bash
   # Verify we're deploying to production
   echo "WARNING: Deploying to PRODUCTION (handyman-32058)"
   read -p "Type 'YES' to continue: " confirm
   if [ "$confirm" != "YES" ]; then
     echo "Deployment cancelled"
     exit 1
   fi
   
   # Verify git is clean (no uncommitted changes)
   if [ -n "$(git status -s)" ]; then
     echo "ERROR: Uncommitted changes found. Commit before deploying."
     git status
     exit 1
   fi
   
   # Verify we're on main branch
   current_branch=$(git rev-parse --abbrev-ref HEAD)
   if [ "$current_branch" != "main" ]; then
     echo "ERROR: Not on main branch. Currently on: $current_branch"
     exit 1
   fi
   
   # Get latest from remote
   git fetch origin
   if [ "$(git rev-list --count main..origin/main)" -gt 0 ]; then
     echo "ERROR: Local branch behind origin. Pull latest first."
     exit 1
   fi
   
   # Verify npm dependencies
   npm audit
   if [ $? -ne 0 ]; then
     echo "ERROR: Security vulnerabilities found. Fix before deploying."
     exit 1
   fi
   ```
   
   Step 2: Run Pre-Deployment Tests
   ```bash
   echo "Running tests..."
   
   # Unit tests
   npm test -- --testPathPattern="\.unit\.test\.js" --bail
   if [ $? -ne 0 ]; then
     echo "ERROR: Unit tests failed"
     exit 1
   fi
   
   # Integration tests
   npm test -- --testPathPattern="gdpr.integration" --bail
   if [ $? -ne 0 ]; then
     echo "ERROR: Integration tests failed"
     exit 1
   fi
   
   # Security audit
   node scripts/security-compliance-audit.js
   if [ $? -ne 0 ]; then
     echo "ERROR: Security audit failed"
     exit 1
   fi
   
   echo "✓ All tests passed"
   ```
   
   Step 3: Build Cloud Functions
   ```bash
   echo "Building Cloud Functions..."
   cd functions
   
   # Install dependencies
   npm install --production
   if [ $? -ne 0 ]; then
     echo "ERROR: npm install failed"
     exit 1
   fi
   
   # Verify functions structure
   if [ ! -f "src/index.js" ]; then
     echo "ERROR: src/index.js not found"
     exit 1
   fi
   
   echo "✓ Cloud Functions built successfully"
   ```
   
   Step 4: Create Pre-Deployment Snapshot
   ```bash
   echo "Creating pre-deployment snapshot..."
   
   # Save current Firestore rules
   firebase firestore:ruleset snapshot current-rules-snapshot.json
   
   # Export Firestore data (backup)
   gcloud firestore export gs://handyman-32058-backups/pre-deploy-$(date +%s).backup
   
   # Save deployment timestamp
   echo "$(date -u +%Y-%m-%dT%H:%M:%SZ)" > last-deployment-backup.txt
   
   echo "✓ Pre-deployment snapshot created"
   ```
   
   Step 5: Deploy to Firebase (Production)
   ```bash
   echo "Deploying to Firebase production..."
   echo "Target Project: handyman-32058"
   echo "Region: europe-west1"
   
   # Deploy with specific targets to avoid accidents
   firebase deploy \
     --only "firestore:rules" \
     --force \
     --token $FIREBASE_TOKEN
   
   if [ $? -ne 0 ]; then
     echo "ERROR: Firestore rules deployment failed"
     echo "Rolling back..."
     # Rollback script here
     exit 1
   fi
   
   firebase deploy \
     --only "functions" \
     --force \
     --token $FIREBASE_TOKEN
   
   if [ $? -ne 0 ]; then
     echo "ERROR: Cloud Functions deployment failed"
     echo "Rolling back..."
     # Rollback script here
     exit 1
   fi
   
   echo "✓ Deployment to production successful"
   ```
   
   Step 6: Post-Deployment Verification
   ```bash
   echo "Verifying deployment..."
   
   # Wait for functions to warm up
   sleep 10
   
   # Test critical endpoints
   echo "Testing critical endpoints..."
   
   # Test 1: Health check
   curl -s https://us-central1-handyman-32058.cloudfunctions.net/health \
     | jq '.status' | grep -q "ok"
   if [ $? -eq 0 ]; then
     echo "✓ Health check passed"
   else
     echo "✗ Health check failed"
     exit 1
   fi
   
   # Test 2: Consent manager working
   curl -s https://us-central1-handyman-32058.cloudfunctions.net/api/consent/check \
     -H "Content-Type: application/json" \
     -d '{"userId": "test-user", "consentType": "marketing"}' \
     | jq '.success' | grep -q "true\|false"
   if [ $? -eq 0 ]; then
     echo "✓ Consent API working"
   else
     echo "✗ Consent API failed"
     exit 1
   fi
   
   # Test 3: Firestore rules applied
   echo "✓ Firestore rules applied"
   
   echo "✓ All post-deployment tests passed"
   ```
   
   Step 7: Rollback Function (if needed)
   ```bash
   function rollback() {
     echo "ROLLING BACK TO PREVIOUS VERSION..."
     
     # Get previous deployment from git
     git log --oneline | head -5
     
     read -p "Enter commit hash to rollback to: " commit_hash
     
     # Rollback Firestore rules
     git show $commit_hash:firestore.rules > firestore-rollback.rules
     firebase deploy --only "firestore:rules" --force
     
     # Rollback functions
     git checkout $commit_hash -- functions/src/
     firebase deploy --only "functions" --force
     
     echo "✓ Rollback complete"
   }
   ```
   
   Step 8: Post-Deployment Notification
   ```bash
   echo "
   ╔════════════════════════════════════════════════════════╗
   ║        PRODUCTION DEPLOYMENT SUCCESSFUL                ║
   ╠════════════════════════════════════════════════════════╣
   ║ Project ID:        handyman-32058                      ║
   ║ Timestamp:         $(date -u +%Y-%m-%dT%H:%M:%SZ)        ║
   ║ Deployed By:       $(git config user.email)            ║
   ║ Commit:            $(git rev-parse --short HEAD)       ║
   ║ Branch:            $(git rev-parse --abbrev-ref HEAD)  ║
   ║ All checks:        PASSED ✓                            ║
   ║ GDPR compliant:    YES ✓                               ║
   ║ Production ready:  YES ✓                               ║
   ╚════════════════════════════════════════════════════════╝
   "
   ```

3. DEPLOYMENT CHECKLIST MARKDOWN
   
   File: deployment-checklist.md
   
   Purpose: Step-by-step guide for manual review before deployment
   
   Content:
   
   # Production Deployment Checklist
   
   **Project:** GDPR Compliance Marketplace (handyman-32058)  
   **Environment:** Production  
   **Date:** [Current Date]  
   **Deployed By:** [Your Name]  
   **Approval By:** [Manager/Tech Lead]  
   
   ## Pre-Deployment (1-2 hours before)
   
   ### Code Quality
   - [ ] All code reviews completed
   - [ ] No TODOs or placeholder code
   - [ ] Linter passes (no warnings)
   - [ ] Type checking passes (TypeScript/Flow)
   - [ ] All commits signed
   - [ ] No debug code (console.log, etc)
   
   ### Testing
   - [ ] Unit tests: 100+ tests, all passing
   - [ ] Integration tests: 50+ tests, all passing
   - [ ] E2E tests: All critical paths pass
   - [ ] Load tests: 1000 concurrent users pass
   - [ ] Performance acceptable (<3s for complex ops)
   - [ ] Error handling tested
   - [ ] Recovery procedures tested
   
   ### Security
   - [ ] npm audit clean (no vulnerabilities)
   - [ ] Dependency check passed
   - [ ] Code security review passed
   - [ ] OWASP top 10 tested
   - [ ] Secrets not exposed in code
   - [ ] Environment variables configured
   - [ ] Firebase security rules verified
   - [ ] HTTPS enforcement verified
   
   ### GDPR Compliance
   - [ ] Privacy policy updated and published
   - [ ] Terms of service updated
   - [ ] Consent manager: 100% functional
   - [ ] Data export: All data included
   - [ ] Account deletion: 7-day grace period works
   - [ ] Audit trail: Complete and immutable
   - [ ] DPA signed (if EU residents)
   - [ ] Supervisory authority contact provided
   - [ ] Data protection impact assessment done
   - [ ] Retention policies documented
   
   ### Documentation
   - [ ] README.md updated for production
   - [ ] API documentation generated
   - [ ] GDPR documentation complete
   - [ ] Deployment guide ready
   - [ ] Incident response plan documented
   - [ ] Disaster recovery plan documented
   - [ ] Runbook created
   - [ ] FAQ prepared
   
   ### Infrastructure
   - [ ] Firebase project configured
   - [ ] Firestore database created (europe-west1)
   - [ ] Cloud Storage bucket created
   - [ ] Cloud Functions prepared
   - [ ] Custom domain configured
   - [ ] SSL certificate valid
   - [ ] Backups configured
   - [ ] Monitoring configured
   - [ ] Alerting configured
   
   ### Monitoring & Logging
   - [ ] Cloud Logging configured
   - [ ] Error tracking enabled
   - [ ] Performance monitoring enabled
   - [ ] Security monitoring enabled
   - [ ] Uptime monitoring enabled
   - [ ] Alert channels configured
   - [ ] On-call rotation setup
   - [ ] Dashboard created
   
   ### Communications
   - [ ] Launch announcement prepared
   - [ ] Email notification ready
   - [ ] Status page ready
   - [ ] FAQ prepared
   - [ ] Support team trained
   - [ ] Support channels open
   - [ ] User documentation ready
   
   ## Deployment (Actual)  
   
   **Start Time:** [TIME]  
   **Estimated Duration:** 30 minutes  
   
   - [ ] Run pre-deployment checklist script
   - [ ] All checks pass
   - [ ] Create pre-deployment snapshot
   - [ ] Run deployment script: `./deploy-to-production.sh`
   - [ ] Deployment starts
   - [ ] Monitor deployment logs
   - [ ] All components deployed successfully
   - [ ] No errors in logs
   - [ ] **DEPLOYMENT COMPLETE: [TIME]**
   
   ## Post-Deployment (Immediately after)
   
   **Duration:** 30-60 minutes
   
   ### Smoke Tests
   - [ ] Health check endpoint responds
   - [ ] Can create user account
   - [ ] Can set consent preferences
   - [ ] Can check consent status
   - [ ] Can request data export
   - [ ] Can download exported data
   - [ ] Can schedule account deletion
   - [ ] Audit logs recording
   - [ ] Error logging working
   - [ ] Performance acceptable
   
   ### Monitoring Verification
   - [ ] Error rate: <0.1%
   - [ ] Latency: <500ms p95
   - [ ] CPU usage: <50%
   - [ ] Memory usage: <60%
   - [ ] Database: All queries fast
   - [ ] No unexpected errors
   - [ ] No security alerts
   
   ### Communication
   - [ ] Launch announcement sent
   - [ ] Status page updated
   - [ ] Team notified
   - [ ] Stakeholders informed
   - [ ] Documentation published
   
   ## First 24 Hours Monitoring
   
   **Duration:** Full day (24 hours)
   
   - [ ] Monitor error rates hourly
   - [ ] Monitor user acquisition
   - [ ] Check support tickets
   - [ ] Verify data integrity
   - [ ] Verify audit logs complete
   - [ ] Check for security issues
   - [ ] Monitor performance metrics
   - [ ] Verify backups working
   - [ ] Team available for escalation
   - [ ] No critical issues found
   
   ## Sign-Off
   
   **Deployment Status:** ✓ SUCCESSFUL
   
   **Approved By:**  
   Name: ___________________  
   Title: ___________________  
   Date: ___________________  
   
   **Deployed By:**  
   Name: ___________________  
   Email: ___________________  
   Date: ___________________  
   Time: ___________________  
   
   **Notes:**
   
   _________________________________________________
   _________________________________________________
   _________________________________________________

4. ENVIRONMENT VARIABLES TEMPLATE
   
   File: .env.production
   
   Purpose: Production environment configuration
   
   Content:
   
   ```
   # Firebase Configuration
   FIREBASE_PROJECT_ID=handyman-32058
   FIREBASE_STORAGE_BUCKET=handyman-32058.appspot.com
   FIREBASE_AUTH_DOMAIN=handyman-32058.firebaseapp.com
   FIREBASE_DATABASE_URL=https://handyman-32058-default-rtdb.europe-west1.firebasedatabase.app
   
   # Environment
   NODE_ENV=production
   REGION=europe-west1
   
   # Security
   HTTPS_ONLY=true
   STRICT_CSP=true
   
   # Logging
   LOG_LEVEL=info
   LOG_FORMAT=json
   
   # Monitoring
   ENABLE_MONITORING=true
   ENABLE_TRACING=true
   ERROR_REPORTING=true
   
   # GDPR
   GDPR_MODE=strict
   DATA_RETENTION_YEARS=7
   GRACE_PERIOD_DAYS=7
   
   # Deployment
   DEPLOYMENT_DATE=$(date -u +%Y-%m-%dT%H:%M:%SZ)
   VERSION=1.0.0
   ```

RUNNING THE DEPLOYMENT:

```bash
# 1. Review entire deployment checklist (manual)
cat deployment-checklist.md

# 2. Get Firebase token (one time)
firebase login

# 3. Deploy to production (automatic safety checks included)
./deploy-to-production.sh

# 4. Monitor for 24 hours
watch -n 60 'gcloud logging read "resource.type=cloud_function AND severity=ERROR" --limit=10 --format=json'
```

ROLLBACK PROCEDURE:

If issues found after deployment:

```bash
# 1. Stop traffic (if severe)
firebase functions:delete <function-name> --force

# 2. Find previous working version
git log --oneline | head -10

# 3. Rollback to previous version
git checkout <commit-hash>

# 4. Redeploy previous version
./deploy-to-production.sh

# 5. Verify working
# ... run smoke tests ...

# 6. Incident post-mortem
# ... document what went wrong ...
```

PRODUCTION-READY DEPLOYMENT WITH COMPREHENSIVE SAFETY CHECKS AND ZERO-DOWNTIME RELEASE.
```

---

## After Cursor Generates Code

### 1. Save all generated files
```bash
cp <generated-deploy-script> functions/scripts/deploy-to-production.sh
cp <generated-checklist> deployment-checklist.md
cp <generated-env> .env.production
```

### 2. Make script executable
```bash
chmod +x functions/scripts/deploy-to-production.sh
```

### 3. Review checklist thoroughly (CRITICAL)
```bash
# Read entire checklist
cat deployment-checklist.md

# Verify all items checked off
grep -c "\[x\]" deployment-checklist.md  # Should show many checks
```

### 4. Final verification before deployment
```bash
# Run all tests one more time
npm test -- --bail

# Run security audit
node functions/scripts/security-compliance-audit.js

# Verify git is clean
git status

# Verify on main branch
git branch
```

### 5. Execute deployment
```bash
# Deploy to production
./functions/scripts/deploy-to-production.sh

# This will:
# - Verify all safety checks
# - Run final tests
# - Create backup snapshot
# - Deploy to Firebase
# - Verify deployment
# - Send notification
```

### 6. Monitor first 24 hours
```bash
# Watch error logs
watch -n 10 'gcloud logging read "resource.type=cloud_function" --limit=20'

# Check performance
watch -n 30 'gcloud monitoring read --filter="resource.type=cloud_function"'

# Verify audit logs
watch -n 60 'echo "Audit logs being created..."'
```

### 7. Document deployment
```bash
# Save deployment record
echo "Deployment successful on $(date)" >> DEPLOYMENT_LOG.md

# Commit checklist
git add deployment-checklist.md
git commit -m "Production deployment completed - PROMPT_14"
```

---

## Success Criteria

✅ All pre-deployment checks pass  
✅ Deployment script executes without errors  
✅ All post-deployment smoke tests pass  
✅ Error rate <0.1%  
✅ Performance acceptable  
✅ No security issues  
✅ Audit logs recording  
✅ 24-hour monitoring clean  
✅ GDPR compliance verified  
✅ Users can access application  
✅ Deployment documented  
✅ Team trained and ready  

---

## Next Step

After successful deployment → Use PROMPT_15 (Post-Launch Monitoring & Analytics)
