# PROMPT 9: Flutter Privacy & Data Management Screen

**Time:** 20 minutes | **Risk:** 🟡 MEDIUM | **Generates:** `lib/screens/privacyScreen.dart` (400 lines)

```
Create Flutter privacy policy and data management screen.

FILE: lib/screens/privacyScreen.dart

COMPONENTS:

1. Privacy Policy Display
   - Full policy with expandable sections
   - Scroll through all sections
   - Links to external documents
   - Print button

2. Data Management Section
   - "View Your Data" button → calls dataExport API
   - Shows: last export date, next available export
   - Download button → generates and downloads ZIP

3. Account Deletion Section
   - "Delete My Account" button
   - Confirmation dialog (7-day grace period)
   - Timer showing when deletion completes
   - "Cancel Deletion" button (if within 7 days)

4. DPA Links
   - Link to Data Processing Agreement
   - Link to data protection authority
   - Contact form for privacy questions

5. Consent Management
   - View current consents
   - Withdraw consent button for each
   - Confirmation required

FEATURES:
- Dark mode support
- Offline support (cache policies)
- Loading states
- Error handling
- Success messages
- Accessible (WCAG 2.1)

PRODUCTION-READY FLUTTER PRIVACY SCREEN.
```
