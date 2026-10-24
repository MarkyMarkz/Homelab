# Active alerts in my homelab
Each alert runs as a scheduled search on a cron interval (typically every 5 minutes). When the search executes, it checks the defined trigger condition, for example, whether the number of matching results is greater than zero, or whether a threshold (such as 3+ failed login attempts) is met.<br>

### If the condition is satisfied, Splunk fires the configured trigger actions I have chosen:
- Add to Triggered Alerts (logs the event in Splunk’s Activity)
- Run a script (sends an alert to discord with information)
<br>
<img width="842" height="763" alt="image" src="https://github.com/user-attachments/assets/5662c100-74b7-4413-91c0-6f8b8d9bb8bf" /><br>

## Windows Brute Force
Alerts when 3 or more failed Windows logon attempts occur for the same account within 5 minutes.

```
index=main host=DESKTOP-4OJMHUV sourcetype=WinEventLog:Security EventCode=4625 earliest=-5m
| rex field=_raw "(?s)mislyktes for:.*?Kontonavn:\s+(?<target_account>\S+)"
| rex field=_raw "Adresse til kildenettverk:\s+(?<src_ip>\S+)"
| where target_account!="-"
| stats count by target_account, src_ip
| where count>=3
```

<br>

### I failed logging into my computer multiple times
<img width="945" height="326" alt="image" src="https://github.com/user-attachments/assets/79927cf5-ce3f-49db-a0a4-ef26ff0e4965" /><br>

```
net use \\192.168.0.127\C$ /user:marks feilpassord12345
```

<br>

### Alert
<img width="885" height="219" alt="image" src="https://github.com/user-attachments/assets/8f4b438c-23d6-4b2f-ae61-96d22113ca29" /><br>

<br><br>

## Windows New User Created
Alerts when a new local user account is created on this machine.

```
index=main host=DESKTOP-4OJMHUV sourcetype=WinEventLog:Security EventCode=4720 earliest=-5m
| rex field=_raw "(?s)Ny konto:.*?Kontonavn:\s+(?<new_account>\S+)"
| rex field=_raw "(?s)Emne:.*?Kontonavn:\s+(?<creator_account>\S+)"
| table _time, new_account, creator_account
```

<br>

### Here I created a new user:
<img width="673" height="51" alt="image" src="https://github.com/user-attachments/assets/d1d5f949-7c78-43e0-896a-64d77bd0f756" /><br>

```
net user testbruker Test1234! /add
```

<br>

### Alert
<img width="885" height="201" alt="image" src="https://github.com/user-attachments/assets/4239a86d-152c-4e77-b814-71d2778f42d4" /><br>

<br>

































