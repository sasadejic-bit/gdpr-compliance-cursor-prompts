# PROMPT 4: Privacy Policy (GDPR Article 13 - Transparency Requirements)

**Time:** 15 minutes | **Risk:** 🟠 HIGH | **Generates:** `privacy_policy.html` + legal templates

```
Generate a GDPR-compliant privacy policy and legal documents.

OUTPUT FILES:
1. privacy_policy.html (full policy - publish on website)
2. privacy_policy.pdf (printable version)
3. dpa_template.docx (Data Processing Agreement)
4. cookie_policy.html (cookie consent)
5. terms_of_service.html (terms)

PRIVACY POLICY CONTENT (All 13 GDPR Article 13 elements):

1. Controller Identity
   - Company name: Handyman Service Marketplace
   - Address: [Your business address]
   - Email: privacy@handyman-app.com
   - Phone: [your phone]
   - DPA contact info

2. Processing Purposes
   - Service provision (bookings, payments)
   - Communication (support, updates)
   - Marketing (with consent)
   - Legal compliance (taxes, fraud)
   - Security (abuse prevention)
   - Analytics (aggregate only)

3. Legal Basis
   - Contract performance (bookings)
   - Consent (marketing)
   - Legal obligation (taxes, safety)
   - Legitimate interests (fraud prevention)
   - Data subject consent (optional processing)

4. Recipients (who gets data)
   - Contractors (for bookings)
   - Payment processors (Stripe, PayU)
   - Email service (Sendgrid, Firebase)
   - Analytics (Firebase Analytics)
   - Law enforcement (if legal order)
   - Accountant (tax records)

5. Retention Periods
   - Profile: until account deletion
   - Bookings: 7 years (tax)
   - Payments: 7 years (tax)
   - Messages: 1 year (then delete)
   - IP logs: 90 days (then delete)
   - Audit logs: 1 year (then delete)
   - Consent records: until withdrawn (then 3 years)

6. User Rights (Articles 15-22)
   - Right to access (Article 15)
   - Right to rectification (Article 16)
   - Right to erasure (Article 17)
   - Right to restrict (Article 18)
   - Right to data portability (Article 20)
   - Right to object (Article 21)
   - Right against automated decision-making (Article 22)
   - How to exercise: privacy@handyman-app.com

7. Cookies & Tracking
   - Essential cookies: [list]
   - Analytics cookies: [list]
   - Marketing cookies: [list]
   - Cookie opt-out link
   - Do-Not-Track support

8. International Data Transfers
   - If transferring to non-EU:
   - Use Standard Contractual Clauses (SCC)
   - Adequacy decisions
   - Privacy Shield status

9. Data Security
   - Encryption in transit (HTTPS)
   - Encryption at rest (Firestore)
   - Access controls (Firebase Auth)
   - Regular security audits
   - Bug bounty program

10. Data Breach Notification
    - We notify within 72 hours
    - You'll be notified if personal data exposed
    - Report to authorities if high risk
    - Contact: privacy@handyman-app.com

11. Automated Decision-Making
    - No fully automated decisions affecting users
    - For contractor matching: human review available
    - Users can request explanation

12. Complaint Process
    - Contact us: privacy@handyman-app.com
    - Or complaint to supervisory authority (data protection authority)
    - [List EU DPA contact info]

13. Policy Updates
    - We may update this policy
    - Material changes: email notification
    - Continued use = acceptance
    - Version history included

REQUIREMENTS:
1. Plain language (not legal jargon)
2. Hyperlinked sections (easy navigation)
3. Printable PDF version
4. Mobile-friendly HTML
5. Last updated date
6. Version history
7. 2-3 page document (concise)
8. DPA contact info prominent
9. Cookie consent banner code
10. Accessibility (WCAG 2.1 AA)

INCLUDE:
- Contact form for data subject requests
- DPA contact form
- Cookie preference center
- Language options (at least English + local)

PRODUCTION-READY HTML + LEGAL DOCUMENTS.
```

---

## Next Steps

Publish `privacy_policy.html` at: `https://your-domain.com/privacy`
Make DPA available at: `https://your-domain.com/dpa`
