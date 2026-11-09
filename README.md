# 021_Bulk Messenger — hosted application starter

This version upgrades the static preview into a Node.js + PostgreSQL application with a private admin session, CSV import, campaign history and an optional official WhatsApp Cloud API template sender.

## Important
- The Netlify link currently hosts the static preview only. This application needs a Node.js host and a persistent PostgreSQL database.
- Do not upload real customer data to the static preview. Use this backend only after the business has a lawful contact list and documented WhatsApp opt-in.
- WhatsApp sending is **off by default** (`WHATSAPP_ENABLED=false`). When enabled, this app sends approved WhatsApp message templates only; it does not provide a free-text bulk sender.
- Configure the Meta webhook and verify webhook signatures using the Meta app secret before relying on webhook status updates in production. The included webhook endpoint is a starter and should be hardened with signature verification before production use.
- Use an authorised business administrator to own hosting, billing and Meta Business/WhatsApp account setup. Never share access tokens, passwords or secrets in chat.

## Local development

1. Install Node.js 20+ and PostgreSQL.
2. Create a PostgreSQL database.
3. Copy `.env.example` to `.env` and set `DATABASE_URL`, `SESSION_SECRET`, and `ADMIN_EMAIL`.
4. Run `npm install`.
5. Run `npm run hash-password`, then put the generated bcrypt hash in `ADMIN_PASSWORD_HASH` in `.env`.
6. Run `npm start` and open `http://localhost:3000`.

The app creates its tables at startup. Never commit `.env`.

## CSV format

```csv
phone,name,opted_in
+2348012345678,Example Customer,yes
+2348098765432,Another Customer,true
```

Numbers must be in international E.164 format. Rows without a valid phone or explicit `yes`, `true`, or `1` opt-in value are skipped. Keep evidence of consent and honour opt-outs; this CSV flag alone is not proof of consent.

## Hosting checklist

Deploy this folder to a Node.js host (for example, a paid Render web service) and attach a persistent PostgreSQL database. Set all required environment variables from `.env.example` in the host dashboard. Set `NODE_ENV=production`, use HTTPS, a long random `SESSION_SECRET`, and a strong admin password hash. Restrict access to the hosting project and database.

For WhatsApp sending, the business administrator must configure the official WhatsApp Business Platform Cloud API, phone number ID, access token, approved templates, webhook URL (`https://YOUR-DOMAIN/webhooks/whatsapp`) and verify token. Only then should the administrator set `WHATSAPP_ENABLED=true`. Do not paste tokens into source code or chat.

## Current limitations before production

1. Add Meta webhook request-signature verification using `WHATSAPP_APP_SECRET`.
2. Add explicit opt-out/suppression management and consent-evidence fields/workflow.
3. Move the campaign worker to a durable queue so campaigns resume safely after a server restart.
4. Add audit logs, backup/restore, monitoring, admin password rotation and a second-factor option.
5. Review privacy notices, retention periods and local legal requirements before importing customer information.
