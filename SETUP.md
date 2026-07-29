# Setup & Configuration Guide
**Voicemail Transcriber for Jira & JSM**

Because this application utilizes a Bring Your Own Key (BYOK) architecture to ensure data privacy, linking an active Twilio account and a preferred AI transcription provider is required. 

## Step 1: Obtain Twilio Credentials
If Jira Service Management (JSM) is already being used for incident routing, a Twilio account is likely already active.

1. Log in to the [Twilio Console](https://console.twilio.com/).
2. On the main dashboard, scroll down to the **Account Info** section.
3. Copy the **Account SID** and the **Auth Token** (click the eye icon to reveal the token). Keep these secure.

## Step 2: Choose and Configure an AI Provider
Configure **one** of the following supported providers. Azure OpenAI is often utilized for strict enterprise compliance requirements.

### Option A: Azure OpenAI (Enterprise / SOC2 / HIPAA)
1. Log in to the Azure Portal and navigate to the **Azure OpenAI** resource.
2. Under *Resource Management*, click **Keys and Endpoint**.
3. Copy **Key 1** (This acts as the API Key).
4. Copy the **Endpoint** (It will look similar to `https://<resource-name>.openai.azure.com/`).

### Option B: Deepgram
1. Log in to the [Deepgram Console](https://console.deepgram.com/).
2. Navigate to **API Keys** on the left menu.
3. Click **Create a New API Key**, provide a name (e.g., "Jira Voicemail"), and copy the generated key.

### Option C: Groq or OpenAI
* **Groq:** Log in to the [GroqCloud Console](https://console.groq.com/keys), click **Create API Key**, and copy the result.
* **OpenAI:** Log in to the [OpenAI Platform](https://platform.openai.com/api-keys), click **Create new secret key**, and copy the result.

## Step 3: Configure the Jira Application
Once the credentials are obtained, they must be securely saved inside Jira.

1. Open the Jira instance and click the **Gear Icon (Settings)** in the top right.
2. Select **Apps** from the dropdown menu.
3. On the left sidebar, under the *Apps* section, click **Voicemail Transcriber Setup**.
4. **Target Project:** Enter the exact Project Key where the voicemails should appear (e.g., `IT` or `HELP`).
5. Paste the Twilio SID, Twilio Auth Token, and AI Provider API Key.
6. Click **Save Settings**. 

*(Note: Credentials are instantly encrypted at rest using Atlassian's native Vault storage).*

## Step 4: Route Twilio to Jira
The final step routes incoming phone calls to the Jira application.

1. On the Jira setup page (from Step 3), copy the generated **Webhook URL** located at the top of the page.
2. Return to the Twilio Console and navigate to **Phone Numbers** -> **Active Numbers**.
3. Click on the designated emergency or inbound phone number.
4. Scroll down to the **Voice Configuration** section.
5. Under the **"A Call Comes In"** dropdown, select **Webhook**.
6. Paste the copied Jira Webhook URL into the text box and ensure the method is set to **HTTP POST**.
7. Click **Save configuration**.

The setup is complete. Inbound calls to the configured Twilio number will now be automatically transcribed and attached to the target Jira project as secure internal comments.
