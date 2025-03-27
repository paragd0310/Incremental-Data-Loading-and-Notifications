# Bootcamp Project 5 - Scenario 2 Documentation

![image](https://github.com/user-attachments/assets/b62a9391-6e44-43d0-9359-5bc1d958794c)


## Title: Time-Based Notification Using Azure Data Factory (Scenario 2)

###  Role: Data Engineer
Implement a time-sensitive notification system in Azure Data Factory that alerts the team (via Slack) when the pipeline succeeds or fails **only between 7 AM and 9 AM UTC**.

---

##  Tools & Services Used:
- **Azure Data Factory**
- **Slack (with Incoming Webhooks)**
- **GitHub (ADF version control)**

---

##  Objective:
To create a pipeline in ADF that:
1. Retrieves the current UTC time.
2. Sends a success message to Slack only between 7 AM and 9 AM UTC.
3. Sends a fallback (skipped) message outside this time window.
4. All changes tracked using GitHub (feature branch workflow).

---

##  Pipeline Structure
**Pipeline Name:** `TimeBased_Notification_Pipeline`

###  Activities:
1. **Set Variable** (`currentTime`)
   - Expression: `@utcNow()`

2. **If Condition** (`CheckIfWithinTimeWindow`)
   - Expression:
     ```json
     @and(greaterOrEquals(formatDateTime(utcNow(),'HH'),'07'), less(formatDateTime(utcNow(),'HH'),'09'))
     ```

3. **True Path**: Web Activity (`SendSlackNotification`)
   - URL: Slack webhook URL (channel: `#adf-alerts`)
   - Method: `POST`
   - Body:
     ```json
     {
       "text": " ADF pipeline ran successfully between 7–9 AM!"
     }
     ```

4. **False Path** (Optional but used here): Web Activity (`SendFallbackNotification`)
   - URL: same Slack webhook
   - Body:
     ```json
     {
       "text": "ADF pipeline skipped notification (outside 7–9 AM window)."
     }
     ```

---

##  Slack Webhook Setup
- Created Slack app `ADF Notifier`
- Enabled **Incoming Webhooks** in [https://api.slack.com/apps](https://api.slack.com/apps)
- Target Channel: `#adf-alerts`
- Used the webhook in Web Activity URL field

---

## Git Integration
- GitHub Repo: `Incremental-Data-Loading-and-Notifications`
- Branch: `main`
- Steps:
  1. Connected GitHub from ADF Studio
  2. Switched to `main` branch
  3. Saved pipeline
  4. Pushed commit: `Adding pipeline: TimeBased_Notification_Pipeline`
  5. Published ARM template to `adf_publish`

---

##  Testing & Debug
### Problem Faced:
- No message appeared in Slack on first test
- **Issue:** Web Activity Body was plain text instead of JSON
- **Fix:** Changed to valid JSON body:
  ```json
  {
    "text": " ADF pipeline ran successfully between 7–9 AM!"
  }
  ```

### Second issue:
- Message went to Slack but only fallback message appeared
- **Why?** Time was outside 7–9 AM UTC
- **Fix/Validation:** Temporarily replaced condition with `@equals(1, 1)` to force success branch

---

##  Final Outcome
- Slack messages now show:
  - Success alert between 7–9 AM UTC
  - Skipped alert outside the time window
- All activities validated and saved to GitHub via proper strategy
- Pipeline published and ready for production use

---

##  Deliverables
- Pipeline: `TimeBased_Notification_Pipeline`
- GitHub Repo: `Incremental-Data-Loading-and-Notifications`
- Branch: `main`
- Slack Channel: `#adf-alerts`


