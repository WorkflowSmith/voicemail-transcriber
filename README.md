# Voicemail Transcriber for Jira & JSM

**Voicemail Transcriber** is an enterprise-grade Atlassian Marketplace application that automatically transcribes inbound Twilio voicemails into Jira internal comments. 

Built exclusively on the Atlassian Forge serverless framework, the application utilizes a strict Bring Your Own Key (BYOK) architecture. By requiring custom API credentials for Twilio and a chosen AI provider (Azure OpenAI, Deepgram, Groq, or OpenAI), it ensures strict data sovereignty. The architecture relies on no external databases, intercepts no webhooks, and processes all audio purely in-memory to maintain zero data retention.
