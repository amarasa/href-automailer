# Automailer Project

A personal Mailchimp-like email marketing tool for internal use.

## Tech Stack

- **Frontend**: Next.js + Tailwind CSS + shadcn/ui
- **Database**: Supabase (href-cms project, tables prefixed with `mailer_`)
- **Email Sending**: Postmark SMTP
- **Hosting**: Vercel

## Core Features

### Client Management
- Unlimited clients
- Each client gets their own API endpoint for email collection forms
- Store: email, name, custom fields, tags

### Email Builder
- Visual email builder (Unlayer or similar)
- Clean, easy-to-use interface

### Campaigns
- One-off sends (MVP)
- Scheduling + send now
- Future: drip campaigns, automation sequences

### Analytics
- Advanced tracking
- Open rates, click rates, bounce tracking
- Unsubscribe handling

## Architecture

Built modularly for future expansion:
- AI content generation integration
- Additional automation features
- More campaign types

## Credentials

- **Postmark API Key**: `94ff1012-3e4b-492e-9953-b81c2e5ee211`
- **Supabase**: Using `href-cms` project
- **Table Prefix**: `mailer_`

## Development Standards

- TypeScript throughout
- Descriptive naming (files, functions, variables should be self-explanatory)
- Lighthouse targets: 92+ on all metrics (aiming for 100)
- WCAG AA accessibility compliance
- No emdashes in any content

## Volume Expectations

- Starting small: under 100 emails
- Designed to scale as needed
