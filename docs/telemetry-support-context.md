# Telemetry Support Context

Customers who embed the Screenpipe CLI can attach a stable, non-PII support
identity to Screenpipe's existing telemetry pipeline. This lets Screenpipe support
filter Sentry errors and PostHog events by customer, deployment, or host app.

```bash
export DAIMONION_SUPPORT_ID="spcust_acme_123"
export DAIMONION_CUSTOMER_ID="acme"
export DAIMONION_DEPLOYMENT_ID="prod-laptop-fleet-01"
export DAIMONION_EMBEDDER="acme-agent"
export DAIMONION_EMBEDDER_VERSION="2026.6.4"

npx screenpipe record
```

## Environment Variables

| Variable | Purpose |
| --- | --- |
| `DAIMONION_SUPPORT_ID` | Stable support ID for this customer/user/deployment. Used as the CLI PostHog `distinct_id` when `DAIMONION_ANALYTICS_ID` is not set, and attached to Sentry/PostHog as `screenpipe_support_id`. |
| `DAIMONION_CUSTOMER_ID` | Customer or organization key, attached as `screenpipe_customer_id`. |
| `DAIMONION_DEPLOYMENT_ID` | Fleet, endpoint group, device, or environment key, attached as `screenpipe_deployment_id`. |
| `DAIMONION_EMBEDDER` | Name of the app embedding or launching Screenpipe, attached as `screenpipe_embedder`. |
| `DAIMONION_EMBEDDER_VERSION` | Version of the embedding app, attached as `screenpipe_embedder_version`. |

Aliases are also accepted for easier integration with existing deployments:

| Canonical variable | Accepted aliases |
| --- | --- |
| `DAIMONION_SUPPORT_ID` | `DAIMONION_TELEMETRY_ID` |
| `DAIMONION_CUSTOMER_ID` | `DAIMONION_ORG_ID`, `DAIMONION_TELEMETRY_CUSTOMER_ID` |
| `DAIMONION_DEPLOYMENT_ID` | `DAIMONION_TELEMETRY_DEPLOYMENT_ID` |
| `DAIMONION_EMBEDDER` | `DAIMONION_HOST_APP`, `DAIMONION_TELEMETRY_HOST_APP` |
| `DAIMONION_EMBEDDER_VERSION` | `DAIMONION_HOST_VERSION`, `DAIMONION_TELEMETRY_HOST_VERSION` |

`DAIMONION_ANALYTICS_ID` remains the highest-priority explicit PostHog
`distinct_id`. Use it only when you intentionally want to control the exact
person identity. Otherwise prefer `DAIMONION_SUPPORT_ID`.

## Privacy Boundary

These variables should contain opaque IDs, not emails or names. Screenpipe
telemetry still follows the normal telemetry settings: `--disable-telemetry`
turns it off, and telemetry does not include screen content, audio, transcripts,
or file contents.

The JavaScript/Swift SDK does not send first-party Screenpipe telemetry on its
own. If an SDK host wants Screenpipe support correlation, it should set the same
environment variables when launching the Screenpipe CLI/engine, and may also add
the same fields to the host application's own Sentry/PostHog reports.
