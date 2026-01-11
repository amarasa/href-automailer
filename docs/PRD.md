# Automailer PRD

## Problem Statement

**What:** No centralized system for managing email marketing across multiple client projects. Currently not doing email marketing due to lack of tooling.

**Who:** Angelo (solo user) managing email lists and campaigns for multiple client websites.

**Why it matters:**
- **Control/Ownership**: Want to own subscriber data and have full control over the email system rather than depending on third-party platforms
- **Cost**: Avoid recurring fees from Mailchimp/alternatives as volume grows across clients
- **Learning**: Build expertise with a production email system using modern stack

**Current state:** Manual/none. Email marketing capabilities don't exist, limiting client offerings and lead nurturing.

---

## Goals & Success Metrics

| Goal | Metric | Target |
|------|--------|--------|
| Reliability | Email delivery success rate | 99%+ |
| Reliability | Zero unhandled bounces/complaints | 0 errors ignored |
| Time savings | Time to create and send a campaign | < 15 minutes |
| Analytics | Track opens, clicks, unsubscribes | 100% of sends tracked |
| Multi-client | Separate client data/endpoints | Complete isolation |

---

## User Stories

### P0 (Must Have for v1)

| ID | Story |
|----|-------|
| US-01 | As a user, I can create and manage multiple clients so that each client's email data is separate |
| US-02 | As a user, I can add subscribers to a client's list via API endpoint so that forms on client sites can collect emails |
| US-03 | As a user, I can create an email campaign with a visual builder so that I don't need to write HTML |
| US-04 | As a user, I can send a campaign immediately or schedule it so that I control timing |
| US-05 | As a user, I can view campaign analytics (opens, clicks) so that I understand performance |
| US-06 | As a subscriber, I can unsubscribe via link so that I stop receiving emails |
| US-07 | As a user, I can manage subscriber data (email, name, custom fields, tags) so that I can personalize and segment |

### P1 (Should Have)

| ID | Story |
|----|-------|
| US-08 | As a user, I can create drip/automation sequences so that subscribers receive timed follow-ups |
| US-09 | As a user, I can segment subscribers by tags/fields so that I send targeted campaigns |
| US-10 | As a user, I can A/B test subject lines so that I optimize open rates |

### P2 (Nice to Have)

| ID | Story |
|----|-------|
| US-11 | As a user, I can generate email content with AI so that I speed up copywriting |
| US-12 | As a user, I can duplicate campaigns across clients so that I reuse templates |

---

## Requirements

### Client Management
- Create, edit, delete clients
- Each client has unique API key for subscriber collection
- Client data is isolated (subscribers, campaigns, analytics)

### Subscriber Management
- API endpoint per client for adding subscribers
- Store: email (required), name (optional), custom fields (dynamic), tags
- Validate email format
- Handle duplicates (update existing vs reject)
- Soft delete for unsubscribes (retain for compliance, exclude from sends)

### Email Builder
- Visual drag-and-drop editor
- Mobile-responsive output
- Save as templates
- Preview before send

### Campaigns
- Select client and subscriber list/segment
- Compose using visual builder or templates
- Schedule for future or send immediately
- Track status: draft, scheduled, sending, sent

### Analytics
- Open tracking (pixel)
- Click tracking (link wrapping)
- Unsubscribe tracking
- Bounce handling (via Postmark webhooks)
- Per-campaign and aggregate views

### Email Delivery
- Integration with Postmark SMTP
- Handle rate limits
- Queue management for large sends

---

## Out of Scope (v1)

| Item | Reason |
|------|--------|
| Public signup pages | API endpoints only; client sites handle forms |
| Multi-user access | Solo user; no auth/permissions system needed |
| White-labeling | Internal tool; no branding customization |
| Billing/usage limits | Personal use; no monetization |

---

## Open Questions

| Question | Impact | Owner |
|----------|--------|-------|
| Unlayer vs custom builder? | Build time vs flexibility | Angelo |
| Postmark webhook setup for bounces? | Bounce handling implementation | Technical |
| Subscriber import from CSV? | Migration path for existing lists | P1 feature decision |

---

## Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Postmark rate limits | Medium | Delayed sends | Queue with backoff, batch sends |
| Email deliverability issues | Medium | Emails go to spam | Proper SPF/DKIM setup, warm-up sending |
| Visual builder complexity | High | Extended build time | Consider Unlayer or similar library |
| Scope creep (drips, AI, etc.) | Medium | Never ships | Strict P0 focus for v1 |
