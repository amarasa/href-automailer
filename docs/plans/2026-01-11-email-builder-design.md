# Email Builder Design

## Overview

Custom block-based email builder for Automailer. No third-party dependencies, full control, no branding issues.

## Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Editor style | Block-based | Clean, intuitive, hard to break layouts |
| Block richness | Rich (11 block types) | Full-featured for diverse email needs |
| Theming | Per-client defaults + overrides | Brand consistency with flexibility |
| Templates | Starters + save your own | Quick start, build library over time |
| Layout | Three-panel | Industry standard, everything visible |
| Preview | In-canvas only | No mobile toggle, test via real email |
| App responsiveness | None | Solo user, desktop only |
| Testing | Critical paths only | Email sending, tracking, webhooks |

## Builder Layout

```
┌─────────────────────────────────────────────────────────────────┐
│  Campaign Editor                               [Preview] [Save] │
├────────────┬──────────────────────────────┬─────────────────────┤
│  BLOCKS    │         CANVAS               │  BLOCK SETTINGS     │
│            │                              │                     │
│  □ Heading │   ┌──────────────────────┐   │  Typography         │
│  □ Text    │   │   [Your email]       │   │  Spacing            │
│  □ Image   │   └──────────────────────┘   │  Background         │
│  □ Button  │                              │  etc.               │
│  □ Divider │   [+ Add block here]         │                     │
│  ...       │                              │                     │
├────────────┴──────────────────────────────┴─────────────────────┤
│                        Client: Acme Corp  │ Draft               │
└─────────────────────────────────────────────────────────────────┘
```

## Block Types (V1)

| Block | Settings |
|-------|----------|
| Heading | Font, size, color, alignment (H1-H3) |
| Text | Font, size, color, line height, links (rich text) |
| Image | Upload/URL, alt text, width, link |
| Button | Text, URL, colors, border radius, size |
| Divider | Color, thickness, width %, margin |
| Spacer | Height in px |
| Columns | 2 or 3 columns, each holds other blocks |
| Social | Platform icons, URLs, icon style |
| Video | YouTube/Vimeo URL, auto thumbnail |
| Product | Image, title, price, description, CTA |
| Countdown | Target date/time, expired message |

## Theming

**Client theme settings:**
- Logo (uploaded image)
- Primary color
- Secondary color
- Text color
- Background color
- Font family
- Button style (rounded/square/pill)

**Override hierarchy:**
```
Client defaults → Email defaults → Block settings
```

## Templates

**Starter templates (built-in):**
- Blank
- Welcome
- Newsletter
- Announcement
- Product Launch
- Event Invite

**Saved templates:**
- Save any email as template
- Per-client (not shared)
- Appears in template picker

## Data Model

```sql
mailer_clients
├── id, name, api_key, theme (JSON), created_at

mailer_subscribers
├── id, client_id, email, name, custom_fields (JSON)
├── tags (array), status, created_at

mailer_templates
├── id, client_id, name, blocks (JSON), is_starter, created_at

mailer_campaigns
├── id, client_id, name, subject, from_name, from_email
├── blocks (JSON), status, scheduled_at, sent_at, created_at

mailer_sends
├── id, campaign_id, subscriber_id, status
├── sent_at, opened_at, clicked_at, postmark_message_id
```

## Email Rendering

- Each block type has a render function
- Output is table-based HTML with inline styles
- Self-contained, no external CSS
- Mobile-friendly by default (fluid widths, readable sizes)

## Send Flow

1. Click "Send Now" or schedule triggers
2. Campaign status → "sending"
3. Get active subscribers for client
4. For each subscriber:
   - Render HTML with personalization
   - Add tracking pixel
   - Wrap links for click tracking
   - Create mailer_sends record
   - Queue for Postmark
5. Batch send via Postmark API (500/batch)
6. Campaign status → "sent"

## Tracking

- **Opens:** 1x1 tracking pixel per send
- **Clicks:** Link wrapping with redirect
- **Bounces/Unsubs:** Postmark webhooks

## Analytics

Campaign view shows:
- Sent, Delivered, Opened, Clicked, Bounced, Unsubscribed
- Click breakdown by link
- Simple numbers and percentages, no charts

## Tech Stack

| Layer | Choice |
|-------|--------|
| Framework | Next.js 15 (App Router) |
| Styling | Tailwind + shadcn/ui |
| Database | Supabase (href-cms, mailer_ prefix) |
| Email API | Postmark |
| Drag & Drop | dnd-kit |
| Rich Text | Tiptap |
| Auth | None (Vercel protection) |

## App Routes

```
/                              → Dashboard
/clients                       → Client list
/clients/[id]                  → Client detail
/clients/[id]/campaigns/new    → New campaign
/clients/[id]/campaigns/[id]   → Campaign editor
/clients/[id]/campaigns/[id]/stats → Analytics
/api/subscribe/[api_key]       → Subscriber endpoint
/api/webhooks/postmark         → Webhook receiver
/api/track/o/[id]              → Open tracking
/api/track/c/[id]/[link]       → Click tracking
```

## Testing

Critical paths only:
- Subscriber API validation
- Email rendering output
- Postmark send logic
- Tracking pixel/redirect
- Webhook processing

~20-30 tests via Vitest.

## Out of Scope

- Desktop/mobile preview toggle
- App responsiveness
- ADA compliance
- SEO optimization
- Performance optimization
- Multi-user auth
- Fancy charts
