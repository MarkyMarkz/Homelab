# Active alerts in my homelab
Each alert runs as a scheduled search on a cron interval (typically every 5 minutes). When the search executes, it checks the defined trigger condition, for example, whether the number of matching results is greater than zero, or whether a threshold (such as 3+ failed login attempts) is met.<br>

### If the condition is satisfied, Splunk fires the configured trigger actions I have chosen:
- Add to Triggered Alerts (logs the event in Splunk’s Activity)
- Run a script (sends an alert to discord with information)
<br>
<img width="842" height="763" alt="image" src="https://github.com/user-attachments/assets/5662c100-74b7-4413-91c0-6f8b8d9bb8bf" /><br>

<br>

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

## Windows User Added to Privileged Group
Alerts when a user account is added to the local Administrators group.

```
index=main host=DESKTOP-4OJMHUV sourcetype=WinEventLog:Security EventCode=4732 earliest=-5m
| rex field=_raw "(?s)Gruppe:.*?Gruppenavn:\s+(?<group_name>\S+)"
| rex field=_raw "(?s)Emne:.*?Kontonavn:\s+(?<actor_account>\S+)"
| rex field=_raw "(?s)Medlem:.*?Sikkerhets-ID:\s+(?<member_sid>\S+)"
| where group_name="Administratorer"
| table _time, group_name, actor_account, member_sid
```

<br>

### After creating a new user I added it to the local Administrators group
<img width="810" height="147" alt="image" src="https://github.com/user-attachments/assets/fd2d61ba-ea59-4134-991f-9cb7fe0d4b04" /><br>

```
net localgroup Administratorer testbruker /add
```

<br>

### Alert
<img width="892" height="360" alt="image" src="https://github.com/user-attachments/assets/0926a70a-179b-4900-a5b2-07491164ac0a" /><br>

### To remove the test account/s privileges and deletion

### Command for removal of account/s privileges:
<img width="841" height="56" alt="image" src="https://github.com/user-attachments/assets/d36ba93b-b6b0-4b12-9959-c959a24025bc" /><br>

```
net localgroup Administratorer testbruker /delete
```

### Command for deletion of account:
<img width="592" height="70" alt="image" src="https://github.com/user-attachments/assets/4c5cbdab-2fe0-4cbe-a719-632c3fa6d8bd" /><br>

```
net user testbruker /delete
```

### To see all accounts with privileges:
<img width="945" height="245" alt="image" src="https://github.com/user-attachments/assets/ee2f5d87-ef33-422e-b9e8-e100956fe380" /><br>

```
net localgroup Administratorer
```

<br>






































