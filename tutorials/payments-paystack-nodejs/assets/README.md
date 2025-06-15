# 💸 Accepting Payments with Paystack + Sending SMS with Africa’s Talking (Node.js)

Let’s turn clicks into cash and send confirmations that matter.

In this guide, you’ll build a simple but powerful **"Pay and Notify"** system using **Paystack** to receive payments and **Africa’s Talking** to notify the payer via SMS.

---

## 🧾 What You’ll Build

A clean Node.js server that:
1. Opens a Paystack payment popup (frontend)
2. Listens for payment confirmation (backend)
3. Sends an SMS confirmation to the payer

---

## 🧰 What You’ll Need

- A [Paystack account](https://paystack.com)
- A [Africa’s Talking account](https://africastalking.com)
- Node.js installed
- A public tunnel (e.g., [ngrok](https://ngrok.com))
- Your brain and good vibes

---

## 🔧 Project Setup

Create a folder:

```bash
mkdir pay-and-notify && cd pay-and-notify
npm init -y
npm install express africastalking dotenv cors body-parser



🔐 Add Your Secrets
Inside .env

AT_USERNAME=sandbox
AT_API_KEY=your_africastalking_api_key
PSTK_SECRET_KEY=your_paystack_secret_key
RECIPIENT=+254712345678


⚙️ Build the Server
Paste this into index.js:
import express from 'express';
import bodyParser from 'body-parser';
import cors from 'cors';
import dotenv from 'dotenv';
import africastalking from 'africastalking';

dotenv.config();

const app = express();
const port = process.env.PORT || 5000;

app.use(cors());
app.use(bodyParser.json());

// Africa’s Talking setup
const at = africastalking({
  apiKey: process.env.AT_API_KEY,
  username: process.env.AT_USERNAME,
});

const sms = at.SMS;

// Paystack webhook listener (mocking this locally)
app.post('/payment-callback', async (req, res) => {
  const { status, data } = req.body;

  if (status === 'success') {
    const phone = process.env.RECIPIENT;
    const message = `Payment of KES ${data.amount / 100} confirmed. Ref: ${data.reference}`;

    try {
      await sms.send({
        to: [phone],
        message,
      });
      console.log('✅ SMS sent');
    } catch (err) {
      console.error('❌ SMS failed:', err);
    }
  }

  res.sendStatus(200);
});

// Launch server
app.listen(port, () => {
  console.log(`🚀 Server ready at http://localhost:${port}`);
});


🌍 Make It Public
In another terminal:

npx ngrok http 5000


Copy the HTTPS URL, e.g., https://secure-tunnel.ngrok.io

💳 Frontend Payment Button (HTML)
Save this as pay.html:

<!DOCTYPE html>
<html>
  <head>
    <title>Pay and Notify</title>
    <script src="https://js.paystack.co/v1/inline.js"></script>
  </head>
  <body>
    <button onclick="pay()">Pay Now</button>

    <script>
      function pay() {
        var handler = PaystackPop.setup({
          key: 'pk_test_xxxxx', // replace with your Paystack public key
          email: 'customer@example.com',
          amount: 10000, // KES 100
          currency: 'KES',
          callback: function(response) {
            fetch('https://secure-tunnel.ngrok.io/payment-callback', {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify({
                status: 'success',
                data: {
                  amount: 10000,
                  reference: response.reference,
                }
              })
            });
            alert('Payment successful. SMS will follow.');
          }
        });
        handler.openIframe();
      }
    </script>
  </body>
</html>


Open in browser → click → pay → receive SMS. Boom.


🔍 ##Test it##

Replace all fake keys with your actual test keys

Add your phone number in .env for SMS

Use test card: 4084 0840 8408 4081 (expires any date, 3 digits)

💡 ##Use Case Ideas##
Send receipts for school fees

Confirm e-commerce purchases

Issue ticket confirmations

Notify on donations

🧠 ##Final Notes to take into account ##

Sandbox SMS works with verified numbers only.

For real Paystack payments, switch to live keys.

Secure your webhook route in production.
