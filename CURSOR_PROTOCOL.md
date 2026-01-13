# 🔧 YOUR CURSOR + GITHUB PROTOCOL (Auto-Configured)

**Status:** ✅ LOCKED IN  
**Last Updated:** January 13, 2026  
**For:** @sasadejic-bit (handyman-app)

---

## PROTOCOL OVERVIEW

Every coding request follows this automatic workflow:

```
You: "I need feature X"
     ↓
Me: Search GitHub for patterns
     ↓
Me: Extract architecture from existing code
     ↓
Me: Generate Cursor-ready prompt
     ↓
You: Copy → Paste into Cursor Composer
     ↓
Done in 20 minutes
```

---

## AUTO-CONFIGURED SETTINGS

### GitHub Context

| Setting | Configuration |
|---------|---------------|
| **GitHub User** | `sasadejic-bit` |
| **Public Repos** | `gdpr-compliance-cursor-prompts` |
| **Main Project** | `handyman-app` (private - pattern examples needed) |
| **Search Scope** | Your repos + best practices from ecosystem |
| **Access Level** | Public repos accessible, private needs patterns manually |

### Technology Stack (Inferred)

| Tech | Configuration |
|------|---------------|
| **Backend** | Node.js + Firebase Cloud Functions |
| **Database** | Cloud Firestore (europe-west1) |
| **Mobile** | Flutter + Dart |
| **Storage** | Cloud Storage |
| **Auth** | Firebase Authentication |
| **Project ID** | `handyman-32058` |
| **IDE** | Cursor (Composer mode) |
| **VCS** | GitHub |

### Code Generation Defaults

| Setting | Default |
|---------|----------|
| **Output Format** | Cursor-ready prompt (copy-paste ready) |
| **Code Language** | JavaScript (backend) + Dart (mobile) |
| **Error Handling** | Complete (try/catch/logging) |
| **Documentation** | JSDoc + inline comments |
| **Style** | Async/await, ES6+, const/let |
| **Testing** | Unit + integration test ready |
| **Deployment** | Firebase CLI ready |

### File Path Conventions (Inferred)

```
Backend:
functions/src/
  ├── services/        (reusable services)
  ├── functions/       (Cloud Functions)
  ├── middleware/      (auth, validation)
  └── utils/          (helpers)

Mobile (Flutter):
lib/
  ├── screens/        (UI screens)
  ├── models/         (data models)
  ├── services/       (API calls)
  ├── widgets/        (reusable widgets)
  └── utils/          (helpers)

Configuration:
  ├── firestore.rules
  ├── firebase.json
  ├── pubspec.yaml
  └── .env.example
```

### Naming Conventions (Inferred)

```
JavaScript/TypeScript:
- Functions: camelCase
  ✅ recordConsent(), fetchUserData()
- Classes: PascalCase
  ✅ ConsentManager, DataExporter
- Constants: UPPER_SNAKE_CASE
  ✅ MAX_RETRIES, DEFAULT_TIMEOUT
- Files: camelCase.js or PascalCase.js
  ✅ consentManager.js, DataExporter.js

Dart/Flutter:
- Classes: PascalCase
  ✅ ConsentScreen, PrivacyWidget
- Functions: camelCase
  ✅ fetchConsents(), updateUser()
- Constants: lowerCamelCase or UPPER_SNAKE_CASE
  ✅ const maxRetries = 3;
- Files: snake_case.dart
  ✅ consent_screen.dart, privacy_widget.dart
```

### Cursor Composer Settings

| Feature | Configuration |
|---------|---------------|
| **Prompt Type** | Single-file generation (default) |
| **Multi-file Support** | Yes, when logically grouped |
| **Integration Mode** | Auto-reference existing code |
| **Context Injection** | Automatic from repo structure |
| **Code Review** | Built into prompt (no TODOs) |

---

## PROTOCOL RULES (LOCKED IN)

### Rule 1: Always Search GitHub First

```
When you ask for code:

1. I search: sasadejic-bit/gdpr-compliance-cursor-prompts
2. I extract: folder structure, naming patterns
3. I find: existing similar implementations
4. I reference: README.md, docs, examples

Result: Cursor prompt matches YOUR architecture exactly
```

**Example:**
```
You: "I need a function to track user events"

Me:
✓ Search: /gdpr-compliance-cursor-prompts
✓ Found: dataRetention.js (scheduler pattern)
✓ Found: consentManager.js (Firestore pattern)
✓ Extract: error handling style, logging format, imports
✓ Generate: userEventTracker.js following same patterns
```

### Rule 2: Output Always = Cursor-Ready Prompt

```markdown
# CURSOR PROMPT: [Feature Name]

**Context:**
- Your project structure extracted ✓
- Naming conventions matched ✓
- Tech stack aligned ✓

**Copy-Paste Block:**
[READY TO PASTE INTO CURSOR COMPOSER]

**What Gets Generated:**
- File: exact path from your repo structure
- Functions: names matching your conventions
- Error handling: your existing style
- Logging: your existing format

**Deploy:**
[Exact Firebase command]
```

### Rule 3: Respect Your Stack

```
✅ DO:
- Use Firebase (not another database)
- Use Flutter (not React Native)
- Use Node.js (not Python/Java)
- Use async/await (not callbacks)
- Use Cloud Functions (not custom servers)

❌ DON'T:
- Suggest outdated APIs
- Use deprecated Firebase methods
- Recommend different tech
- Add unnecessary dependencies
```

### Rule 4: Documentation Baseline

```
For every request, I verify:

✓ Firebase SDK latest version
✓ Flutter dependencies current
✓ GitHub Issues (avoid deprecated methods)
✓ Your existing code patterns
✓ Cloud Firestore best practices
✓ Security standards (OWASP)
```

### Rule 5: No Setup Required

```
Prompt format = Copy → Paste → Done

NO need to:
- Install libraries separately
- Configure anything
- Read 10-page docs
- Debug setup issues

Every prompt is self-contained & ready
```

---

## WHAT HAPPENS NEXT TIME YOU CODE REQUEST

### Scenario 1: Backend Function

```
You: "Create a function to calculate service pricing"

Me:
1. Search gdpr-compliance-cursor-prompts/functions/
2. Find: accountDeletion.js (Cloud Function pattern)
3. Extract: error handling, logging, imports
4. Check: latest Firebase SDK docs
5. Generate:

# CURSOR PROMPT: Service Pricing Calculator

[Copy-paste ready prompt]

You: Copy → Paste into Cursor → 15 min → Done
```

### Scenario 2: Flutter Screen

```
You: "Add a payment confirmation screen"

Me:
1. Search: gdpr-compliance-cursor-prompts/lib/ (if Flutter code exists)
2. Extract: Widget structure, state management
3. Check: latest Flutter/Dart syntax
4. Generate:

# CURSOR PROMPT: Payment Confirmation Screen

[Copy-paste ready prompt]

You: Copy → Paste → flutter run → Done
```

### Scenario 3: Database Schema

```
You: "Design the booking data model"

Me:
1. Search: firestore.rules (existing schema patterns)
2. Extract: collection structure, field types
3. Check: Firestore best practices
4. Generate:

# CURSOR PROMPT: Booking Data Model

[Copy-paste ready prompt]

You: Copy → Paste → firebase deploy → Done
```

---

## HOW TO ACCESS PRIVATE REPO PATTERNS (When Ready)

If you want me to search your private `handyman-app` repo:

**Option 1: Share Pattern Examples**
```
You: "Here's my existing function structure:

// functions/src/services/bookingService.js
const admin = require('firebase-admin');
const logger = require('../utils/logger');

async function createBooking(userId, bookingData) {
  try {
    // validation
    // firestore write
    // audit log
    // return
  } catch (error) {
    logger.error(...);
    throw ...
  }
}

Follow this pattern for new functions."

Me: ✓ Saved. All future prompts follow this style.
```

**Option 2: Grant GitHub Token**
```
You: [Share GitHub personal access token]
Me: ✓ Access private repo patterns automatically
```

**Option 3: Upload Code Snapshot**
```
You: [Share 3-4 key files as examples]
Me: ✓ Extract patterns and apply to all future requests
```

---

## QUICK REFERENCE

### When You Ask for Code

| Step | Action |
|------|--------|
| 1 | Search your GitHub repos for patterns |
| 2 | Extract folder structure & naming |
| 3 | Check latest documentation |
| 4 | Generate Cursor-ready prompt |
| 5 | Provide copy-paste block |
| 6 | Show: file path + deploy command |

### Response Format (Always)

```markdown
# CURSOR PROMPT: [Feature]

**Time:** X minutes | **Complexity:** Basic/Medium/Advanced

## Copy-Paste This Into Cursor Composer

[PROMPT TEXT - READY TO USE]

---

## Generated Files
- File: `path/from/your/structure`
- Functions: matches your naming

## Deploy
$ [exact command]
```

---

## CONFIGURATION SUMMARY

✅ **GitHub User:** sasadejic-bit  
✅ **Main Repo:** gdpr-compliance-cursor-prompts  
✅ **Tech Stack:** Firebase + Flutter + Node.js  
✅ **IDE:** Cursor (Composer)  
✅ **Output:** Always Cursor-ready prompts  
✅ **Architecture:** Extracted from your repos  
✅ **Code Style:** Matched to your conventions  
✅ **Documentation:** Latest versions verified  
✅ **Naming:** Your project's conventions  
✅ **Deployment:** Firebase CLI ready  

---

## NEXT TIME YOU CODE REQUEST

Just ask. I'll:

1. 🔍 **Search** your repos
2. 📋 **Extract** patterns
3. ✅ **Verify** docs
4. 📝 **Generate** Cursor prompt
5. 📋 **Copy-paste ready**

**Zero setup. Pure productivity.**

---

**Protocol Status:** 🟢 ACTIVE

**Ready for your first code request!** 🚀
