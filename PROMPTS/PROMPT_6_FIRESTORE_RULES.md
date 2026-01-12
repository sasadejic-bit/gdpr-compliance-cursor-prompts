# PROMPT 6: Firestore Security Rules (GDPR Data Isolation)

**Time:** 20 minutes | **Risk:** 🟠 HIGH | **Generates:** `firestore.rules` (150 lines)

```
Create Firestore security rules that enforce GDPR data isolation.

FILE: firestore.rules

REQUIREMENTS:

1. User Data Isolation
   - Users can ONLY read/write their own documents
   - /users/{uid}/profile: only accessible by {uid}
   - /users/{uid}/bookings: only accessible by {uid}
   - /users/{uid}/messages: only accessible by {uid}
   - Exception: contractors can read limited booking info

2. Admin Access with Logging
   - Admins can access any user data
   - Every admin access logged to /audit_logs
   - Log: admin_uid, accessed_uid, timestamp, action
   - Required for support/legal compliance

3. Processor Access Control
   - Payment processors: read-only on payment data
   - Email service: read-only on contact fields
   - Analytics: read-only on anonymized data
   - No write access for processors

4. Deleted User Blocking
   - When user deleted: their documents marked as 'deleted'
   - No access to deleted user data (even admins)
   - Exception: audits with 'investigation' flag

5. Audit Log Protection
   - /audit_logs: append-only (no delete/update)
   - Only admin can read (for compliance audits)
   - Immutable: timestamps locked

RULES:

rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Allow signing up and getting user data
    match /users/{uid} {
      allow read, write: if request.auth.uid == uid;
      allow read: if request.auth.uid == null && resource.data.publicProfile == true;
    }

    // Admin access with logging
    match /users/{uid}/{document=**} {
      allow read: if request.auth.token.admin == true;
      // Log admin access
    }

    // Audit logs: append-only
    match /audit_logs/{logId} {
      allow create: if request.auth != null;
      allow read: if request.auth.token.admin == true;
      allow update, delete: if false; // Never allow updates/deletes
    }

    // Public data
    match /contractors/{contractorId} {
      allow read: if true; // Public profiles
      allow write: if request.auth.uid == contractorId;
    }

    // Messages between users
    match /messages/{messageId} {
      allow read, write: if request.auth.uid in resource.data.participants;
    }
  }
}

REQUIREMENTS:
1. Zero trust: deny by default
2. User isolation: cannot access other users' data
3. Admin override: with audit logging
4. Processor boundaries: limited scope
5. Immutable audit logs: append-only
6. Performance: efficient indexing
7. GDPR: enforces data minimization
8. Security: no privilege escalation

PRODUCTION-READY FIRESTORE RULES WITH AUDIT LOGGING.
```

---

## Deploy

```bash
firebase deploy --only firestore:rules
```
