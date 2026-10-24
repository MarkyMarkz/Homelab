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

### I failed logging into my computer multiple times:
<img width="945" height="326" alt="image" src="https://github.com/user-attachments/assets/79927cf5-ce3f-49db-a0a4-ef26ff0e4965" /><br>

```
net use \\192.168.0.127\C$ /user:marks feilpassord12345
```

<br>

### Alert:
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

### Alert:
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

### After creating a new user I added it to the local Administrators group:
<img width="810" height="147" alt="image" src="https://github.com/user-attachments/assets/fd2d61ba-ea59-4134-991f-9cb7fe0d4b04" /><br>

```
net localgroup Administratorer testbruker /add
```

<br>

### Alert:
<img width="892" height="360" alt="image" src="https://github.com/user-attachments/assets/0926a70a-179b-4900-a5b2-07491164ac0a" /><br>

### To remove the test account/s privileges and deletion (cleanup):

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

## Windows Successful Login After Failure
Alerts when a successful Windows login follows 2 or more failed attempts within a short window.

```
index=main host=DESKTOP-4OJMHUV sourcetype=WinEventLog:Security (EventCode=4624 OR EventCode=4625) earliest=-5m
| rex field=_raw "(?s)Ny pålogging:.*?Kontonavn:\s+(?<success_account>\S+)"
| sort 0 _time
| streamstats window=5 count(eval(EventCode=4625)) as recent_failures
| where EventCode=4624 AND success_account!="SYSTEM" AND NOT match(success_account, "\$$") AND recent_failures>=2
| eval time_bucket=round(_time)
| dedup time_bucket, success_account
| table _time, success_account, recent_failures
```

<br>

### I locked my windows PC (WIN + L) and failed the login 2 times and then correctly:

### Alert:
<img width="894" height="214" alt="image" src="https://github.com/user-attachments/assets/32c036bf-62ac-4053-81eb-e09a3cd705e2" /><br>

### Splunk:
<img width="945" height="82" alt="image" src="https://github.com/user-attachments/assets/ee3d68d7-4289-45f4-8b90-6979824ef015" /><br>

<br>

## Proxmox: Failed Web UI Login (Brute Force)
Alert goes off if someone has failed logging in 5 times in a row.

```
index=main sourcetype=syslog earliest=-5m 
| rex field=_raw "authentication failure; rhost=(?<src_ip>\S+) user=(?<auth_user>\S+)" 
| where isnotnull(src_ip) 
| stats count by src_ip, auth_user 
| where count>=5
```

<br>

### Note: Check out Creating_splunk_alerts_for_discord.md for full walkthrough.

<br>

## AdGuard: Malware/Phishing Domain Blocked
Alerts when AdGuard blocks a domain flagged as malware or phishing.

```
index=main sourcetype=adguard earliest=-5m
| rename Result.Reason as reasonCode
| search reasonCode="4"
| stats count
| where count>0
```

<br>

## SSH Brute Force (AdGuard Container)
Alerts when 3 or more failed SSH login attempts occur from the same IP within 5 minutes.

```
index=main sourcetype=linux_secure earliest=-5m
| rex field=_raw "Failed password.*from (?<src_ip>\S+)"
| where isnotnull(src_ip)
| stats count by src_ip
| where count>=3
```

<br>

### Note: I’ll add these alerts to more containers.

<br>

## Splunk Forwarder Data Gap
Alerts when a forwarder host hasn't sent data to Splunk in over 30 minutes.

```
index=main earliest=-2h
| stats max(_time) as last_seen by host
| eval minutes_since = round((now()-last_seen)/60,1)
| where minutes_since > 30
| table host, minutes_since
```

<br>

### Alert:
<img width="876" height="182" alt="image" src="https://github.com/user-attachments/assets/54055518-8784-4943-b596-f92d9865909f" /><br>

<br>


