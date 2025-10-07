# **Legal Assistant & Document Generator Workflow – Easy Guide**
This workflow helps you **talk to a Telegram bot**, get legal answers, and generate legal documents automatically. It uses **AI**, **Telegram**, **Postgres**, **Odoo CRM**, and **Google Drive/Gotenberg**.



## **1. What This Workflow Does (Simple Version)**

Imagine this:

1. You **message a bot on Telegram**: “I need an NDA” or “What is a lease agreement?”
2. The bot **understands your message** using AI.
3. If it’s a **question**, the bot answers directly.
4. If it’s a **document request**, the bot:

   * Asks for missing information (name, case info, language)
   * Creates a legal document in **HTML**
   * Converts it to **PDF**
   * Sends it back to you on Telegram
5. All your messages are **remembered** in a database (Postgres)
6. Your request is **recorded in Odoo CRM** automatically for follow-up.

---

## **2. Step-by-Step Workflow Explanation**

### **Step 1: Telegram Trigger – “Listen to messages”**

**Node:** `Telegram Trigger1`

* Think of it as the bot’s **ears**.
* It listens to every message a user sends on Telegram.
* It gives the bot the **message text** and the **user chat ID**.

---

### **Step 2: AI Brain – “Decide what to do”**

**Node:** `AI Agent` + `Google Gemini Chat Model`

* This is the **smart brain**.
* It reads the user message and decides:

  * **General query?** (Flag = 2) → Just answer.
  * **Document request?** (Flag = 1) → Start document generation.

**Example:**

* User: “What is an affidavit?” → General query → Flag = 2
* User: “I want a lease agreement for my land” → Document request → Flag = 1

---

### **Step 3: Keep Memory – “Remember everything”**

**Node:** `Postgres Chat Memory`

* Stores user messages and previous conversations.
* Helps AI **remember context** if the user asks follow-up questions.

**Analogy:** Think of it as the bot’s **notebook**.

---

### **Step 4: Check CRM – “See if user exists in Odoo”**

**Nodes:** `Get many items` + `Code in JavaScript` + `If`

* **Get many items** → Pulls all existing leads (users) from Odoo.
* **JavaScript code** → Checks if this Telegram username is already in Odoo.
* **If** → Branches workflow:

  * Existing user → `Update an item`
  * New user → `Create an item`

**Why?** So the bot **doesn’t create duplicate records**.

---

### **Step 5a: If it’s a General Query (Flag = 2)**

**Nodes:** `Code in JavaScript2` → `Send a text message`

* Cleans the text for Telegram (removes symbols that break messages).
* Sends AI answer back to the user.
* Updates Odoo lead description with the query.

**Example:**

* User: “What is a lease agreement?”
* Bot reply: “A lease agreement is a contract between a landlord and tenant… Would you like to request another document or ask another legal query?”

---

### **Step 5b: If it’s a Document Request (Flag = 1)**

**Nodes:** `Code in JavaScript1` → `HTTP Request1` → `Send a document` → `Upload file in Google Drive (optional)`

1. **Code in JavaScript1** → Turns AI HTML output into a **file** for PDF conversion.
2. **HTTP Request1** → Uses **Gotenberg** to convert HTML to PDF.
3. **Send a document** → Sends the PDF to Telegram user.
4. **Upload file in Google Drive (optional)** → Saves document in Google Drive folder.

**Example:**

* User: “I want an NDA”
* Bot asks: Name, Parties, Date
* Bot generates HTML → converts to PDF → sends PDF to Telegram → stores it in Google Drive.

---

### **Step 6: CRM – Record the Request**

**Nodes:** `Create an item` / `Update an item`

* All user requests (questions or document requests) are **saved in Odoo CRM** automatically.
* Fields saved:

  * `contact_name` → Telegram username
  * `description` → User’s query
  * `email_from` → Bot email
  * `name` → “Telegram”

---

## **3. How AI Decides: Flags**

* **Flag = 2** → General legal query → Send text answer.
* **Flag = 1** → Legal document request → Generate HTML → Convert to PDF → Send.

---

## **4. HTML Document Rules**

* Generated documents are **pure HTML**, ready for PDF/Word conversion.
* Structure example:

```html
<!doctype html>
<html>
<head><meta charset="utf-8"><title>Document Title</title></head>
<body>
  <h1 style="text-align:center;">DOCUMENT TITLE</h1>
  <p>Content with user details...</p>
  <p>Signature: _____________________</p>
</body>
</html>
```

* Multilingual support included automatically.

---

## **5. Visual Workflow (Simple)**

**Flow:**

```
Telegram Message → AI Agent → Switch → [Flag 2 → Send Text / Flag 1 → Generate PDF] → Odoo CRM Update → Postgres Memory
```

**Analogy:**

1. You **talk** → Telegram Trigger
2. Bot **thinks** → AI Agent
3. Bot **remembers** → Postgres Memory
4. Bot **records you** → Odoo CRM
5. Bot **answers** → Text or PDF

---

## **6. Tools Needed**

* n8n (Workflow automation)
* Postgres (Memory storage)
* Google Gemini AI (AI responses)
* Telegram Bot (User interface)
* Odoo CRM (Lead management)
* Gotenberg (HTML → PDF conversion)
* Google Drive (Optional storage)

---

## **7. Tips for Using Workflow**

1. Make sure **Telegram webhook** is HTTPS.
2. **All AI responses** must follow JSON format: message, chat_id, reply, flag.
3. Ask users **politely for missing info** when creating documents.
4. Keep **HTML formatting** consistent for legal compliance.
5. Regularly **check Postgres** to ensure memory is working correctly.

---

 **Result:**

* User messages are answered instantly.
* Legal documents are generated automatically.
* All interactions are saved for future reference.


