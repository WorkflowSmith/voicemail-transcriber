# Security & Privacy Architecture

**Voicemail Transcriber for JSM Operations**

Atlassian Marketplace applications frequently introduce third-party vulnerabilities by routing data through external vendor servers. **Voicemail Transcriber** utilizes a different approach.

Built exclusively on Atlassian’s Forge framework using a strict **Bring Your Own Key (BYOK)** architecture, the application relies on no external databases, intercepts no webhooks, and retains zero data.

## 1. Zero Data Retention

When a P1 incident occurs, audio left on emergency lines is highly confidential. The application processes this data purely in-memory.

* **No Database:** No backend database is maintained. Incident data never touches WorkflowSmith servers.
* **Ephemeral Processing:** Audio files fetched from Twilio are held in the Forge Lambda memory strictly long enough to be streamed to the configured AI provider and attached to the Jira issue.
* **Instant Destruction:** Once the Jira ticket is successfully created and the MP3 is attached, the memory is instantly freed and destroyed by Atlassian's serverless infrastructure.

## 2. Bring Your Own Key (BYOK) Security

To guarantee absolute data sovereignty, the application requires custom API credentials for both Twilio and the chosen AI provider (Azure OpenAI, Deepgram, Groq, or OpenAI).

* **Data Lineage:** Infrastructure audio travels directly from Atlassian’s AWS servers to the corporate AI account. Transcripts cannot be seen, monitored, or monetized by WorkflowSmith.
* **HIPAA/SOC2 Compatibility:** By utilizing **Azure OpenAI** as the provider within the app, transcripts are processed entirely under the organization's existing Microsoft enterprise compliance agreements.

## 3. Credential Encryption at Rest

API keys are never hardcoded or stored in plain text.

* The application utilizes Atlassian’s native `@forge/kvs` (Key-Value Store) to manage credentials.
* When keys are entered in the Jira Admin UI, they are instantly encrypted at rest using Atlassian’s `setSecret()` API.
* These keys are stored directly within the Atlassian tenant's secure vault and are completely inaccessible to the WorkflowSmith development team.

## 4. Architecture & Data Flow

Because the app runs 100% natively on **Atlassian Forge**, it inherits Atlassian’s enterprise-grade security, egress firewalls, and compliance certifications.

**The exact lifecycle of a voicemail:**

1. **Inbound Call:** The Twilio number receives a call and executes an Atlassian WebTrigger webhook natively hosted on the Jira domain.
2. **Secure Fetch:** The Atlassian Forge background worker securely fetches the MP3 from Twilio using the encrypted credentials over TLS 1.2+.
3. **In-Memory Transcription:** The worker streams the audio blob directly to the chosen AI provider.
4. **Internal Commenting:** The returned text is pushed into the Jira issue as a `sd.public.comment (internal: true)`. **The transcript is hidden from the customer portal by default** and is only visible to JSM agents.
5. **Secure Attachment:** The original MP3 is attached to the Jira issue via Jira's secure internal attachment API, and all temporary memory is wiped.

## 5. Network Egress & Wildcard Restrictions

To prevent data exfiltration, Atlassian Forge strictly enforces egress network rules. The application’s `manifest.yml` permits outbound traffic only to highly trusted, pre-approved domains:

* `*.twilio.com`
* `*.openai.azure.com`
* `api.openai.com`
* `api.deepgram.com`
* `api.groq.com`

Any attempt to route data to an unauthorized third-party server is hard-blocked at the Atlassian infrastructure level.
