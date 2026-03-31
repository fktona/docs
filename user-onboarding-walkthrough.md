# Vela UI walkthrough: from app setup to live alerts

This guide is a step-by-step runbook for end users who need to:

- create an app
- get an API key
- define event schemas
- add integration destinations (Slack, Discord, Email)
- create notification rules
- test the full flow

Use it as onboarding instructions for customers, support, and internal demos.

## 0) Prerequisites

- You can sign in to the Vela dashboard.
- You know which team channel/email should receive alerts.
- You have a sample event payload ready (JSON).

Recommended sample event:

```json
{
  "eventName": "build.failed",
  "severity": "high",
  "service": "api-gateway",
  "environment": "production",
  "errorCode": "DEPLOY_502",
  "message": "Deployment failed after health checks",
  "customerId": "cust_123"
}
```

---

## 1) Create app + get API key

1. Open Dashboard.
2. Click **Create New App**.
3. Fill:
   - **App Name**: `Production Alerts`
   - **Slug**: `production-alerts` (auto-generated; edit if needed)
4. Click **Create App**.
5. On the API key reveal screen:
   - click **Copy key**
   - store it in a password manager or secret manager
   - click **Open app**

Important:

- The full API key is shown once at creation.
- If lost, go to `Settings` and regenerate.

---

## 2) Define schema in Schema Builder

Route: `Apps > [your app] > Schema Builder`

1. Go to **Schema Builder**.
2. In the event list, create/select event name `build.failed`.
3. Add fields in the editor:
   - `severity` (string)
   - `service` (string)
   - `environment` (string)
   - `errorCode` (string)
   - `message` (string)
   - `customerId` (string)
4. Save schema.
5. Confirm the JSON preview matches expected shape.

Tip: define schema before rules so condition pickers are easier and safer.

---

## 3) Add integration destinations (Slack, Discord, Email)

Route: `Apps > [your app] > Integrations`

In **Destinations for this app**, click **Add destination**.

### Slack

1. **Channel type**: `Slack (webhook)`
2. **Label**: `#incident-alerts`
3. **Webhook URL**: paste Slack incoming webhook URL
4. Click **Save destination**
5. Click **Send test** on the destination row

### Discord

1. **Channel type**: `Discord (webhook)`
2. **Label**: `#ops-discord`
3. **Webhook URL**: paste Discord webhook URL
4. Click **Save destination**
5. Click **Send test**

### Email

1. **Channel type**: `Email`
2. **Label**: `On-call email`
3. **Email**: `oncall@company.com`
4. Click **Save destination**
5. Click **Send test**

Expected result: each destination shows healthy status and receives a test message.

---

## 4) Create notification rules

Route: `Apps > [your app] > Notifications`

1. Click **New Rule**.
2. In Rule Builder:
   - **Rule Name**: `High severity build failures`
   - **Event**: `build.failed`
3. Under **IF (Conditions)**, add:
   - `severity equals high`
   - `environment equals production`
4. Under **THEN (Actions)**:
   - Add action and select Slack destination
   - Add action and select Discord or Email destination
5. Ensure rule toggle is **Enabled**.
6. Click **Save Rule**.

Recommended starter rules:

- `build.failed` + `severity=high` -> Slack + Email
- `auth.failed` + `environment=production` -> Discord
- `payment.failed` + `region=us-east-1` -> Slack

---

## 5) Send a real event with API key

Use the app API key copied during app creation.

Example request:

```bash
curl -X POST "https://your-api-host/v1/events" \
  -H "Authorization: Bearer YOUR_APP_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "eventName": "build.failed",
    "severity": "high",
    "service": "api-gateway",
    "environment": "production",
    "errorCode": "DEPLOY_502",
    "message": "Deployment failed after health checks",
    "customerId": "cust_123"
  }'
```

Then verify:

1. Event appears in **Events** feed.
2. Matching rule trigger count increases.
3. Slack/Discord/Email receive alert.

---

## 6) Manage keys and secrets in Settings

Route: `Apps > [your app] > Settings`

### Rotate app API key

Use this when the current API key may be leaked or as part of scheduled rotation.

1. Open **Danger Zone**.
2. Click **Regenerate API Key**.
3. Copy the newly revealed key immediately.
4. Update server-side environments/secrets.
5. Redeploy affected services.

API equivalent:

```bash
curl -X POST "http://localhost:7750/v1/apps/APP_ID/keys/rotate" \
  -H "accept: application/json" \
  -H "Authorization: Bearer YOUR_JWT"
```

### Create client secret(s)

Use client secrets for server-to-server auth where separate credentials are needed.

1. In **Client Secrets**, enter a label (example: `Production server`).
2. Click **Create client secret**.
3. Copy and store the revealed `clientSecret` immediately (shown once).
4. Repeat as needed for staging/production/workers.

API equivalent:

```bash
curl -X POST "http://localhost:7750/v1/v1/client-secrets" \
  -H "accept: */*" \
  -H "Authorization: Bearer YOUR_JWT" \
  -H "Content-Type: application/json" \
  -d '{
    "label": "Production server"
  }'
```

### View or delete client secrets

In the same **Client Secrets** card, you can:

- view all key prefixes and metadata (`Created`, `Last used`)
- delete secrets you no longer need

API list equivalent:

```bash
curl -X GET "http://localhost:7750/v1/v1/client-secrets" \
  -H "accept: */*" \
  -H "Authorization: Bearer YOUR_JWT"
```

---

## 7) UI walkthrough checklist (quick run)

- [ ] Create app
- [ ] Copy/store API key
- [ ] Create schema for event name
- [ ] Add Slack destination + test
- [ ] Add Discord destination + test
- [ ] Add Email destination + test
- [ ] Create enabled rule with IF + THEN
- [ ] Send sample event via API
- [ ] Confirm event ingest + notifications
- [ ] Rotate API key in Settings
- [ ] Create at least one client secret
- [ ] Confirm client secret appears in list

---

## 8) "Paper part" (offline worksheet)

Use this before clicking through UI so setup is clean and consistent.

### App details

- App name:
- Slug:
- Owner:
- Environment(s):

### API key handling

- Secret manager path:
- Who can access key:
- Rotation cadence:
- Last rotation date:
- Next rotation date:

### Client secret handling

- Secret labels to create:
- Service mapped to each label:
- Storage location:
- Revocation owner:
- Revocation SLA:

### Schema plan

- Event name(s):
- Required fields:
- Optional fields:
- Field types:

### Destinations

- Slack webhook + channel label:
- Discord webhook + channel label:
- Email destination(s):

### Rules plan

- Rule name:
- Event:
- IF conditions:
- THEN actions:
- Escalation path:

---

## 9) Troubleshooting

- **No destination in action dropdown**  
  Add destination first in `Integrations`, then return to `Notifications`.

- **Rule saves but no alert sent**  
  Check event name and condition values exactly match payload.

- **API key not visible in settings**  
  Full key is only shown at creation/rotation; use regenerate when required.

- **Webhook test fails**  
  Re-check URL format and provider permissions, then retry test.

- **API key rotation succeeded but ingest fails**  
  Ensure all services were updated with the new key and redeployed.

- **Client secret created but not saved anywhere**  
  Create a new one immediately and store it in your secret manager; full secret is one-time reveal.

- **Delete client secret fails**  
  Verify JWT/account permissions and retry. If still failing, inspect API error message from Settings.

