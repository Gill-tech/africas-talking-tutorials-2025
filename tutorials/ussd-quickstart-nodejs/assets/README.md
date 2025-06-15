# 📲 USSD Quickstart with Node.js and Africa's Talking

Build your first *tap-free* interface with Africa’s Talking USSD API — where users dial `*384*123#` and interact with your backend via simple text menus.

This guide shows how to set up, test, and handle basic USSD interactions in Node.js using a mock use-case: **"Checking Loan Balance or Requesting a Loan"**.

## 🚀 What You’ll Build

A basic USSD menu that gives users 2 options:

Welcome to FinLite!

1.Check Loan Balance
2.Request Loan


Each choice responds with a different message.

---

## 🧰 What You Need

✅ A free Africa’s Talking account → [Sign Up](https://account.africastalking.com)  
✅ Node.js v14+  
✅ [ngrok](https://ngrok.com/) account (for public URL to your localhost)  
✅ Sandbox short code (USSD → *384*123# in Kenya)

---

## 🛠 Step 1: Project Setup

### In your terminal:

```bash
mkdir ussd-server && cd ussd-server
npm init -y
npm install express body-parser



##Step 2: Create a USSD Handler (index.js)
Paste this in index.js:

import express from 'express';
import bodyParser from 'body-parser';

const app = express();
app.use(bodyParser.urlencoded({ extended: false }));
const port = process.env.PORT || 3000;

// USSD callback route
app.post('/ussd', (req, res) => {
  const { text, sessionId, phoneNumber } = req.body;

  let response = '';

  if (text === '') {
    response = `CON Welcome to FinLite!\n1. Check Loan Balance\n2. Request Loan`;
  } else if (text === '1') {
    response = `END Your loan balance is KES 4,230.`;
  } else if (text === '2') {
    response = `END You’ve requested a loan. Our team will contact you shortly.`;
  } else {
    response = `END Invalid option. Try again.`;
  }

  res.set('Content-Type', 'text/plain');
  res.send(response);
});

// Start server
app.listen(port, () => {
  console.log(`✅ USSD server running on http://localhost:${port}/ussd`);
});




##Step 3: Expose Your Server Using ngrok
Open a second terminal window and run:
npx ngrok http 3000

Copy the public URL it gives you, e.g., https://22ac-105-203-89-101.ngrok-free.app


##🔗 Step 4: Register Callback URL on Africa’s Talking
Go to: USSD Sandbox Settings

Under Shortcodes, click “+” to add a USSD service

Use these values:

Shortcode: *384*123#

Callback URL: https://your-ngrok-url.ngrok-free.app/ussd

Save & deploy


##Step 5: Test Your USSD App
Dial *384*123# from your registered number

Select:

1 to check loan balance

2 to request a loan

You should get a direct SMS-like response via USSD.


How USSD Works Behind the Scenes
Africa’s Talking sends a POST request with:

{
  "sessionId": "ATUasd123...",
  "phoneNumber": "+254712345678",
  "text": "1"
}

Your server replies with a text response:

Start with CON to continue the session

Start with END to terminate the session

##Pro Tips##
You can simulate advanced menus by branching based on text like 1*1, 1*2, etc.

Production shortcodes require approval; sandbox is perfect for testing logic.

Always test edge cases like invalid input or long sessions.