# LetsDefend Write-Up: SOC140 – Phishing Mail Detected – Suspicious Task Scheduler

| | |
|---|---|
| **Platform** | LetsDefend |
| **Event ID** | 82 |
| **Rule** | SOC140 - Phishing Mail Detected - Suspicious Task Scheduler |
| **Level** | Security Analyst |
| **Verdict** | True Positive (phishing with a password-protected attachment) |

## 1. Alert Details

| Field | Value |
|---|---|
| Event time | Mar 21, 2021, 12:26 PM |
| SMTP address | 189.162.189.159 |
| Sender | aaronluo@cmail.carleton.ca |
| Recipient | mark@letsdefend.io |
| Subject | COVID19 Vaccine |
| Device action | Blocked |


![Alert details](images/alert-details.png)

## 2. Step-by-Step Investigation

### Step 1 – Read the alert details
I read the alert SOC140 (Event ID 82) and recorded the time, SMTP address, sender, recipient, subject and device action

### Step 2 – Parse the email
I Answered the playbook questions: when it was sent, the SMTP address, the sender and the recipient. Then opened the email body:

> Hey, did you read breaking news about Covid-19. Open it now!
> password: infected

The mail has one attachment.

![Email body and attachment](images/email-body.png)

### Step 3 – Judge the content
The mail is suspicious for these reasons:

1. **Urgency.** "Open it now!" pressures the recipient to act without thinking.
2. **Topical lure.** COVID-19 news was a common social-engineering theme in 2021.
3. **Password for the attachment.** The password is given in the body, a common way to stop mail gateways and AV from scanning the contents.
4. **Generic message.** No greeting or context, from an external sender to a corporate user.

### Step 4 – Analyze the attachment


### Step 5 – Check if the mail was delivered
The device action is **Blocked**, so the mail was most likely not delivered or executed. The Email Security search shows "Final Action: Unknown" for this message.

