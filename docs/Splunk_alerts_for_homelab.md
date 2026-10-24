# Active alerts in my homelab
Each alert runs as a scheduled search on a cron interval (typically every 5 minutes). When the search executes, it checks the defined trigger condition, for example, whether the number of matching results is greater than zero, or whether a threshold (such as 3+ failed login attempts) is met.<br>

### If the condition is satisfied, Splunk fires the configured trigger actions I have chosen:
- Adds to Triggered Alerts (logs the event in Splunk’s Activity)
- Runs a script (sends an alert to discord with information)
<br>
<img width="842" height="763" alt="image" src="https://github.com/user-attachments/assets/5662c100-74b7-4413-91c0-6f8b8d9bb8bf" />
