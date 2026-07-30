# AI Voice Receptionist System - Complete N8N Workflows

## 📋 What's Included

This package contains everything you need to build an AI voice receptionist system that can:

✅ Look up clients in a CRM (Google Sheets)
✅ Create new client accounts automatically
✅ Check calendar availability in real-time,
✅ Book, update, and cancel appointments
✅ Answer questions from a knowledge base
✅ Transfer calls to specialized departments
✅ Log detailed call summaries automatically.

## 📦 Package Contents

### N8N Workflow Files (9 total)
1. **1-mcp-server-trigger.json** - Main MCP server that routes to all sub-workflows
2. **2-client-lookup.json** - Checks if a client exists in the CRM
3. **3-new-client-crm.json** - Creates new client entries
4. **4-check-availability.json** - Checks Google Calendar availability
5. **5-book-event.json** - Books appointments in Calendar and logs them
6. **6-lookup-appointment.json** - Finds existing appointments for a client
7. **7-update-appointment.json** - Reschedules appointments
8. **8-delete-appointment.json** - Cancels appointments
9. **9-end-of-call-report.json** - Logs call summaries to Google Sheets

### Documentation Files
- **SETUP-GUIDE.md** - Complete step-by-step setup instructions
- **VAPI-SYSTEM-PROMPT.txt** - Full system prompt for Vapi AI assistant
- **README.md** - This file

## 🚀 Quick Start

### Prerequisites
- N8N instance (cloud or self-hosted)
- Vapi account (free tier available)
- Google account (for Sheets and Calendar)
- Phone number (Vapi provides free test numbers)

### Installation Steps

1. **Import N8N Workflows**
   - Import all 9 JSON files into your N8N instance
   - Configure Google Sheets and Google Calendar credentials
   - Activate all workflows

2. **Create Google Sheet**
   - Create a Google Sheet with 3 tabs: "Clients", "Appointment Log", "Call Log"
   - See SETUP-GUIDE.md for exact column structure

3. **Set Up Vapi**
   - Create a new AI assistant in Vapi
   - Copy the system prompt from VAPI-SYSTEM-PROMPT.txt
   - Connect N8N via MCP Server tool
   - Add your business knowledge base PDF

4. **Connect Everything**
   - Get N8N MCP Server Production URL
   - Add it to Vapi with your N8N API key
   - Configure end-of-call webhook for logging
   - Assign a phone number

5. **Test**
   - Call your number and test all scenarios
   - Verify data flows to Google Sheets and Calendar

## 📖 Detailed Documentation

For complete setup instructions, troubleshooting, and customization options, see **SETUP-GUIDE.md**.

## 🎯 System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                         PHONE CALL                          │
│                             ↓                               │
│                    ┌────────────────┐                       │
│                    │  VAPI (Frontend)  │                    │
│                    │  - Voice AI       │                    │
│                    │  - GPT-4          │                    │
│                    │  - Decision Making│                    │
│                    └────────┬───────────┘                   │
│                             ↓                               │
│                    ┌────────────────┐                       │
│                    │  MCP Server    │                       │
│                    │  (N8N Workflow) │                      │
│                    └────────┬───────────┘                   │
│                             ↓                               │
│         ┌───────────────────┼───────────────────┐          │
│         ↓         ↓         ↓         ↓         ↓          │
│    ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐│
│    │Client  │ │New     │ │Check   │ │Book    │ │Update  ││
│    │Lookup  │ │Client  │ │Avail.  │ │Event   │ │Appt    ││
│    └────────┘ └────────┘ └────────┘ └────────┘ └────────┘│
│         ↓         ↓         ↓         ↓         ↓          │
│    ┌────────────────────────────────────────────────────┐  │
│    │         Google Sheets CRM & Appointment Log        │  │
│    └────────────────────────────────────────────────────┘  │
│         ↓                                   ↓               │
│    ┌──────────────────┐          ┌──────────────────┐     │
│    │ Google Calendar  │          │   Call Logs      │     │
│    └──────────────────┘          └──────────────────┘     │
└─────────────────────────────────────────────────────────────┘
```

## 🔑 Key Features

### 1. Smart CRM Integration
- Automatically looks up clients by email
- Creates new accounts on the fly
- Tracks all client interactions

### 2. Real-Time Calendar Management
- Checks availability before booking
- Syncs with Google Calendar instantly
- Handles double-booking prevention

### 3. Natural Conversation Flow
- AI handles all decision-making in frontend (Vapi)
- Backend workflows stay simple and fast (no AI)
- Minimal latency for great call experience

### 4. Comprehensive Logging
- Every call logged with summary
- Tracks outcomes (booked, cancelled, inquiry, etc.)
- Full conversation transcripts available

### 5. Flexible Handoffs
- Can transfer to human agents
- Supports multiple departments
- Graceful fallback options

## ⚙️ Customization Options

### Change Business Hours
Edit the system prompt to reflect your actual operating hours.

### Modify Appointment Duration
Default is 1 hour. Update the system prompt and workflows to change.

### Add Custom Services
Expand the knowledge base PDF and update appointment types.

### Add More CRM Fields
Modify Google Sheets structure and update workflows 2 & 3.

### Change AI Personality
Edit VAPI-SYSTEM-PROMPT.txt to adjust tone, style, and behavior.

### Add New Features
Create additional N8N workflows and connect to MCP Server.

## 💡 Best Practices

### ✅ DO:
- Keep backend workflows simple (1-3 nodes)
- Let Vapi handle all reasoning and decisions
- Test thoroughly before going live
- Use lowercase emails consistently
- Confirm details with callers before actions

### ❌ DON'T:
- Add AI agents in N8N workflows (causes latency)
- Make assumptions about data formats
- Skip credential configuration
- Forget to activate workflows
- Share sensitive appointment details

## 🐛 Troubleshooting

### Vapi Can't Connect to N8N
- ✓ Verify MCP Server workflow is activated
- ✓ Check Authorization header format: `Bearer YOUR_API_KEY`
- ✓ Ensure you're using Production URL, not Test URL

### Appointments Not Showing in Calendar
- ✓ Check Google Calendar credentials
- ✓ Verify calendar ID (usually "primary")
- ✓ Confirm time format is ISO 8601

### Client Lookup Fails
- ✓ Verify Google Sheets credentials
- ✓ Check sheet name matches exactly
- ✓ Ensure email is lowercase

### AI Not Following Instructions
- ✓ Refine system prompt with specific examples
- ✓ Add mandatory phrases like "You must..."
- ✓ Test and iterate based on failures

For more troubleshooting tips, see SETUP-GUIDE.md.

## 📊 Cost Estimates

### Vapi
- **Free Tier**: 10 phone numbers, limited minutes
- **Paid**: ~$0.05-0.12 per minute

### N8N
- **Self-hosted**: Free (server costs only)
- **N8N Cloud**: From $20/month

### Google APIs
- **Sheets & Calendar**: Free for normal use

## 🎓 Learning Resources

### Video Tutorial
This system is based on the complete tutorial video by [Creator Name]. Watch the full 30+ minute walkthrough for detailed explanations.

### Prerequisites
If you're new to voice AI or N8N, start with these concepts:
- Voice AI basics and Vapi platform
- N8N workflow fundamentals
- Google Sheets API integration
- Webhook configurations

## 📝 License & Usage

This is a complete template system that you can:
- ✅ Use for your own business
- ✅ Modify and customize
- ✅ Deploy for clients
- ✅ Learn from and build upon

## 🤝 Support & Community

For questions, troubleshooting, or to share your implementation:
- Join the free community (link in original video)
- Check the Plus community for advanced tutorials
- Review the SETUP-GUIDE.md for detailed instructions

## 🚀 Next Steps

1. Read through SETUP-GUIDE.md completely
2. Set up your Google Sheet with proper structure
3. Import all N8N workflows and configure credentials
4. Set up Vapi assistant with system prompt
5. Connect everything together
6. Test all scenarios thoroughly
7. Customize for your specific business
8. Deploy to production phone number

---

**Ready to build your AI receptionist?** Start with SETUP-GUIDE.md and follow the step-by-step instructions. You'll have a working system in under an hour!

**Questions?** Review the troubleshooting section or join the community for support.

**Want to customize?** The system prompt and workflows are fully documented and easy to modify for your specific needs.

Good luck! 🎉
