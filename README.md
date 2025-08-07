# Task 2: WhatsApp-Driven Google Drive Assistant (n8n workflow)

This project is an n8n workflow that listens to WhatsApp messages (via Twilio) and performs Google Drive actions. It can list files, move files, perform safe deletions, and summarize documents using OpenAI's GPT-4o.

## Features
- **File Listing:** `LIST /path/to/folder`
- **File Moving:** `MOVE /source/file.pdf /destination/folder`
- **Safe Deletion:** `DELETE /path/to/file.pdf` (prompts for confirmation)
- **AI Summarization:** `SUMMARY /path/to/folder` (summarizes text, PDF, and docx files)
- **Help Command:** Any unrecognized command returns a help message.
- **Audit Logging:** All actions are logged to a Google Sheet.

## Tools & Services Used
- **n8n:** Workflow automation engine (run via Docker).
- **Twilio:** For the WhatsApp API Sandbox.
- **Google Drive API:** For file operations. OAuth2 is used for secure authentication.
- **OpenAI API:** For document summarization with GPT-4o.
- **Google Sheets API:** For audit logging.

## Setup & Deployment

1.  **Run n8n via Docker:**
    It's recommended to run n8n using Docker to have a clean environment.
    ```bash
    docker run -it --rm --name n8n -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n
    ```
    Access n8n at `http://localhost:5678`.

2.  **Import the Workflow:**
    - In the n8n UI, go to "Workflows" and click "Import from File".
    - Select the `workflow.json` file from this repository.

3.  **Configure Credentials:**
    The workflow requires several credentials. In n8n, go to the "Credentials" section on the left sidebar and add a new credential for each of the following:
    - **Twilio:** Add your Account SID and Auth Token.
    - **Google OAuth:** This is the most complex step. You need to create a project in the Google Cloud Platform, enable the Drive API and Sheets API, and create OAuth 2.0 credentials. Use the `helper_scripts/setup_gcp_oauth.sh` guide for pointers.
    - **OpenAI:** Add your API key.

4.  **Configure Nodes:**
    - **Twilio Trigger:** After saving your Twilio credentials, n8n will generate a webhook URL. Copy this URL and paste it into your Twilio WhatsApp Sandbox settings under "WHEN A MESSAGE COMES IN".
    - **Google Drive & Sheets Nodes:** Open each Google Drive and Google Sheets node in the workflow and select the Google OAuth credential you created.
    - **OpenAI Node:** Select the OpenAI credential you created.
    - **Log to Sheet Node:** In this node, you'll need to enter the **Sheet ID** from the URL of the Google Sheet you want to use for logging.

5.  **Create `.env` file:**
    While the secrets are stored in n8n's credentials store, the `.env.sample` file lists the information you will need to gather.

6.  **Activate the Workflow:**
    - Save the workflow.
    - Toggle the "Active" switch in the top right corner.

You can now send commands from your WhatsApp-enabled phone to the Twilio Sandbox number.

## Command Syntax
- `LIST /folder`
- `SUMMARY /folder`
- `MOVE /source/file.txt /destination/folder`
- `DELETE /file.txt`
- `DELETE /file.txt CONFIRM`

## License
This project is licensed under the Apache License 2.0. See the `LICENSE` file for details.
