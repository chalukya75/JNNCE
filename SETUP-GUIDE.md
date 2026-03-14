# AI Voice Receptionist - Complete Setup Guide

## Overview
This system creates an AI voice receptionist using Vapi (frontend voice AI) and N8N (backend automations). The receptionist can:
- Look up clients in CRM
- Create new client records
- Check appointment availability
- Book, update, and delete appointments
- Answer questions from a knowledge base
- Transfer calls to specialized departments
- Log call summaries automatically

## Architecture
- **Frontend**: Vapi handles voice interactions and AI decision-making
- **Backend**: N8N workflows handle data operations (no AI in backend)
- **Connection**: MCP Server in N8N allows Vapi to call specific workflows as tools
- **Storage**: Google Sheets for CRM and appointments, Google Calendar for scheduling

---

## Setup Instructions

### Part 1: Google Sheets Setup

Create a Google Sheet with THREE tabs:

#### Tab 1: "Clients"
Columns:
- Email
- Name
- Phone
- Created Date

#### Tab 2: "Appointment Log"
Columns:
- Event ID
- Email
- Type
- Date
- Notes

#### Tab 3: "Call Log"
Columns:
- Timestamp
- Call ID
- Summary
- Outcome
- Duration

### Part 2: N8N Setup

#### Step 1: Import Workflows
Import all 9 JSON workflow files into your N8N instance:
1. `1-mcp-server-trigger.json` - Main MCP server
2. `2-client-lookup.json` - Client lookup tool
3. `3-new-client-crm.json` - New client creation
4. `4-check-availability.json` - Availability checker
5. `5-book-event.json` - Book appointments
6. `6-lookup-appointment.json` - Find appointments
7. `7-update-appointment.json` - Update appointments
8. `8-delete-appointment.json` - Delete appointments
9. `9-end-of-call-report.json` - Call logging

#### Step 2: Configure Credentials
For each workflow, update these credential IDs:
- Google Sheets OAuth2 credential
- Google Calendar OAuth2 credential

Replace `YOUR_GOOGLE_SHEETS_CREDENTIAL_ID` and `YOUR_GOOGLE_CALENDAR_CREDENTIAL_ID` with your actual credential IDs.

#### Step 3: Update Google Sheets References
In each workflow that uses Google Sheets, update the sheet name/ID to match your Google Sheet.

#### Step 4: Activate MCP Server
1. Open workflow #1 (MCP Server Trigger)
2. Activate the workflow
3. Copy the Production URL from the MCP Server Trigger node
4. Save this URL - you'll need it for Vapi

#### Step 5: Get N8N API Key
1. Go to Settings → N8N API
2. Create a new API key
3. Save this key - you'll need it for Vapi

#### Step 6: Activate Call Logger
1. Open workflow #9 (End of Call Report)
2. Activate the workflow
3. Copy the Production webhook URL
4. Save this URL - you'll need it for Vapi

### Part 3: Vapi Setup

#### Step 1: Create Assistant
1. Go to Vapi dashboard
2. Create a new assistant
3. Name it (e.g., "AI Receptionist - Kylie")

#### Step 2: Configure Model
- Model: OpenAI GPT-4o
- First Message: "Hi, this is Kylie with [Your Business]. Can I please have your email so I can look you up in the system?"

#### Step 3: Add System Prompt
Copy the system prompt from `vapi-system-prompt.txt` (included in this package) and paste it into the System Prompt field in Vapi.

#### Step 4: Connect N8N Tool
1. In Vapi, go to Tools
2. Click "Create Tool"
3. Select "MCP" as integration type
4. Configure:
   - **Tool Name**: n8n
   - **Server URL**: [Your MCP Server Production URL from N8N]
   - **Headers**:
     - Name: `Authorization`
     - Value: `Bearer [YOUR_N8N_API_KEY]`
   - **Connection Type**: Server Sent Events

#### Step 5: Add Knowledge Base
1. Create a PDF with your business information (hours, services, policies, FAQs)
2. In Vapi, go to More → Files
3. Upload your PDF
4. In your assistant settings, under Model, add the file to "Files" section

#### Step 6: Configure End of Call Report
1. In Vapi assistant settings, go to Advanced → Messaging
2. Server Settings:
   - **Server URL**: [Your webhook URL from N8N workflow #9]
3. Server Messages:
   - Enable only "End of Call Report"
4. Analysis:
   - **Summary Prompt**: "You are an expert notetaker. Your job is to summarize the transcript in two to three sentences."
   - **Structured Data**: Add a property called "outcome" with enum values:
     - "appointment_booked"
     - "appointment_rescheduled"
     - "appointment_cancelled"
     - "customer_inquiry"
     - "transferred_to_support"
     - "transferred_to_sales"
     - "general_question"

#### Step 7: Create Phone Number
1. In Vapi, go to Phone Numbers
2. Create a new phone number (free Vapi number or import your own)
3. Assign it to your assistant as an Inbound number

#### Step 8: Add Handoff Tool (Optional)
If you want to transfer calls to other departments:
1. Create additional Vapi assistants for each department (e.g., "Customer Support Agent", "Sales Agent")
2. In your main assistant, go to Tools
3. Create a "Handoff" tool
4. Add destinations for each specialized assistant
5. Configure when to handoff based on customer intent

---

## Testing

### Test Scenario 1: New Client Booking
1. Call the phone number
2. Provide a new email address
3. Give your name and phone number
4. Request to book an appointment
5. Choose a time
6. Confirm booking
7. Check Google Sheets to verify:
   - Client added to "Clients" tab
   - Appointment in "Appointment Log" tab
   - Event in Google Calendar
   - Call summary in "Call Log" tab

### Test Scenario 2: Existing Client Updates
1. Call again with the same email
2. Request to change the appointment
3. Choose a new time
4. Verify updates in Google Sheets and Calendar

### Test Scenario 3: Knowledge Base
1. Call and ask about business hours or services
2. Verify the AI responds with information from your PDF

---

## Customization Tips

### Modify Appointment Duration
In the system prompt, find the line about appointment duration (currently 1 hour) and change it to your preferred length.

### Change Business Hours
Update the availability check times in the system prompt to match your business hours.

### Add More CRM Fields
Modify the Google Sheets structure and update workflows #2 and #3 to collect additional information.

### Change AI Personality
Edit the system prompt to adjust Kylie's personality, tone, or communication style.

### Add More Tools
Create additional N8N workflows for new features and connect them to the MCP Server.

---

## Important Notes

⚠️ **No AI in Backend**: Keep all N8N workflows simple and deterministic. Don't add AI agents in N8N - let Vapi handle all reasoning.

⚠️ **Lowercase Emails**: The system converts emails to lowercase for consistency. Make sure your CRM does the same.

⚠️ **Time Formats**: Use ISO 8601 format (YYYY-MM-DDTHH:mm:ss) for all datetime values.

⚠️ **Testing**: Always test in Vapi's interface first before using with a real phone number.

⚠️ **Latency**: Keep tools simple (1-2 nodes) to minimize call latency and silence.

---

## Troubleshooting

### Issue: Vapi can't call N8N tools
- Verify MCP Server workflow is activated
- Check Authorization header has correct Bearer token
- Ensure Server URL is the Production URL, not Test URL

### Issue: Events not appearing in Calendar
- Verify Google Calendar credentials are configured
- Check that calendar ID is "primary" or correct calendar ID
- Ensure time formats are valid ISO 8601

### Issue: Client lookup not working
- Confirm Google Sheets credentials are set up
- Verify sheet name matches exactly (case-sensitive)
- Check that email column name is exact

### Issue: AI not following instructions
- Refine system prompt with more specific examples
- Add phrases like "You must..." or "Before you do X, always do Y"
- Test iteratively and update prompt based on failures

---

## Cost Estimates

### Vapi Costs
- Free tier: 10 phone numbers, limited minutes
- Paid: ~$0.05-0.12 per minute depending on model and features

### N8N Costs
- Self-hosted: Free (just server costs)
- N8N Cloud: Starts at $20/month

### Google APIs
- Sheets API: Free for reasonable use
- Calendar API: Free for reasonable use

---

## Next Steps

1. Import all workflows into N8N
2. Configure credentials
3. Activate workflows
4. Set up Vapi assistant
5. Connect N8N to Vapi via MCP
6. Test thoroughly
7. Assign phone number
8. Go live!

For detailed system prompt and workflow explanations, see accompanying documentation files.
