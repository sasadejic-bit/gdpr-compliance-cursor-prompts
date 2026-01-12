# ⚡ QUICK START (5 Minutes to First Code)

## Installation

### 1. Install Cursor (1 minute)

**Option A: Standalone Cursor IDE**
```bash
# Download from: https://cursor.sh/
# Install for your OS (Windows/Mac/Linux)
# Takes 2 minutes
```

**Option B: Use VS Code Extension**
```bash
# If you already have VS Code:
# Extensions > Search "Cursor" > Install
# Reload VS Code
```

### 2. Open Your Project (1 minute)

```bash
# In Cursor:
File > Open Folder

# Select your handyman app folder:
/path/to/handyman-app
```

### 3. Set Context (1 minute)

In Cursor chat, paste this (one time only):

```
I'm building a service marketplace mobile application with:
- Backend: Node.js + Firebase Cloud Functions
- Database: Cloud Firestore
- Mobile: Flutter + Dart
- Project ID: handyman-32058
- Region: europe-west1

Generate production-ready GDPR compliance code using all best practices.
```

**Cursor now understands your setup.**

---

## Using the First Prompt (20 Minutes)

### Step 1: Copy Prompt

```bash
# In this repository:
open PROMPTS/PROMPT_1_CONSENT_MANAGER.md

# Select everything after "## Copy-Paste This Entire Prompt into Cursor"
# Copy (Ctrl+C / Cmd+C)
```

### Step 2: Paste into Cursor

```
1. Click Cursor chat window
2. Paste (Ctrl+V / Cmd+V)
3. Press Enter

[Wait 30 seconds...]
```

### Step 3: Review Generated Code

Cursor generates:

```javascript
// functions/src/services/consentManager.js
// ~250 lines of production code
```

**Check:**
- ✅ No TODOs or placeholders
- ✅ All functions present
- ✅ Error handling included
- ✅ JSDoc comments added
- ✅ Ready to use

### Step 4: Save File

```bash
# Copy generated code
# Create file:
functions/src/services/consentManager.js

# Paste code
# Save (Ctrl+S)
```

### Step 5: Commit to Git

```bash
git add functions/src/services/consentManager.js
git commit -m "Add Consent Manager service for GDPR compliance"
```

**Total time: 20 minutes** ✅

---

## Full Implementation (5-7 Hours)

### Day 1: Backend (2.5 hours)

```
PROMPT 1 (Consent Manager)      → 20 min
PROMPT 2 (Data Export API)      → 30 min
PROMPT 3 (Account Deletion)     → 30 min
PROMPT 5 (Data Retention)       → 25 min
PROMPT 6 (Firestore Rules)      → 20 min
PROMPT 7 (HTTPS Enforcement)    → 15 min
                                 ━━━━━
                            Total: 2.5 hours
```

**Deploy:**
```bash
firebase deploy --only functions
firebase deploy --only firestore:rules
```

### Day 2: Frontend (1.5 hours)

```
PROMPT 8 (Flutter Consent UI)    → 20 min
PROMPT 9 (Flutter Privacy Screen) → 20 min
PROMPT 10 (Admin Dashboard)      → 25 min
                                  ━━━━━
                             Total: 1.5 hours
```

**Integrate:**
```bash
flutter pub get
flutter run
```

### Day 3: Legal (15 minutes)

```
PROMPT 4 (Privacy Policy)        → 15 min

# Publish to:
https://your-domain.com/privacy
```

### Day 3: Testing (1.5 hours)

```bash
# Test each function
firebase emulators:start

# Verify:
- Consent tracking works
- Data export generates ZIP
- Account deletion 7-day grace
- Audit logs recording
- HTTPS enforced

# Run smoke tests:
./smoke-tests.ps1
```

### Day 4: Deploy to Production

```bash
firebase deploy
firebase hosting:deploy
```

**Monitor first 24 hours.**

---

## Troubleshooting

### Cursor Can't Generate Code

**Problem:** "I don't think I can help with that"

**Solution:** Break into smaller prompts
```
"Split this into 3 files: [file1], [file2], [file3]"
```

### Code Has Errors

**Problem:** "ReferenceError: X is not defined"

**Solution:** Tell Cursor
```
"Fix this error: ReferenceError: X is not defined. 
Make sure all imports are included."
```

### Code Doesn't Match Your Setup

**Problem:** Generated code uses wrong Firebase region

**Solution:** Correct it
```
"Update all Firebase region references from us-central1 to europe-west1"
```

### Need to Customize

**Problem:** Generated code has hardcoded company name

**Solution:** Just edit it after generation
```javascript
// Change this:
const COMPANY_NAME = 'Cursor Generated';

// To this:
const COMPANY_NAME = 'Your Company';
```

---

## Success Checklist

### Before Starting
- [ ] Firebase project created
- [ ] Firestore database enabled
- [ ] Cloud Functions enabled
- [ ] Cloud Storage enabled
- [ ] Firebase CLI installed (`npm install -g firebase-tools`)
- [ ] Cursor installed
- [ ] Project folder opened in Cursor

### After Each Prompt
- [ ] Code generated without errors
- [ ] No TODOs or placeholders
- [ ] All imports present
- [ ] Error handling complete
- [ ] JSDoc comments added
- [ ] File saved to correct location
- [ ] Committed to Git

### After All 10 Prompts
- [ ] All functions deployed
- [ ] Firestore rules active
- [ ] Flutter screens added
- [ ] Privacy policy published
- [ ] Smoke tests passing
- [ ] Dashboard accessible
- [ ] Monitoring active

---

## Next Step

Start with PROMPT 1:

```bash
open PROMPTS/PROMPT_1_CONSENT_MANAGER.md
```

**You'll have complete code in 20 minutes.** ✅
