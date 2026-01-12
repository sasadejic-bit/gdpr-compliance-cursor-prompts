# PROMPT 8: Flutter Consent Checkboxes UI

**Time:** 20 minutes | **Risk:** 🟡 MEDIUM | **Generates:** `lib/screens/consentScreen.dart` (300 lines)

```
Create Flutter consent checkbox screen for GDPR compliance.

FILE: lib/screens/consentScreen.dart

COMPONENTS:

1. consentScreen()
   - Displays all 4 consent types:
     * Terms of Service (REQUIRED - checkbox disabled if unchecked)
     * Privacy Policy (REQUIRED)
     * Marketing Communications (OPTIONAL)
     * Analytics (OPTIONAL)

2. Each Checkbox:
   - Clear label
   - "Learn more" expandable section
   - Links to full documents
   - Timestamp when checked
   - User agent captured

3. Submit Button
   - Disabled until Terms + Privacy checked
   - Calls backend consentManager.recordConsent()
   - Shows loading spinner
   - Error handling with retry

4. Privacy Policy Section
   - Links to:
     * Full privacy policy
     * Data Processing Agreement
     * Cookie policy
   - Opens in WebView or external browser

FEATURES:
- Responsive design (mobile-first)
- Accessible (WCAG 2.1)
- Error handling
- Loading states
- Success confirmation
- "Already accepted?" checkbox to skip

PRODUCTION-READY FLUTTER CONSENT UI.
```
