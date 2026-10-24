# Home SOC (Splunk_Dashboard)

<img width="1917" height="899" alt="image" src="https://github.com/user-attachments/assets/29590799-8755-47a0-9b8d-e57308193228" /><br>

<br>

### Total Log Events: 24h
<img width="945" height="192" alt="image" src="https://github.com/user-attachments/assets/7bf776b9-f98f-415c-9fb8-d50a53e0467a" /><br>

```
index=main sourcetype=adguard earliest=-24h | stats count as "Total DNS"
```

<br>

### Blocked by Filters
<img width="828" height="345" alt="image" src="https://github.com/user-attachments/assets/ac9dd1a0-da03-4af2-902b-03ed2860025c" /><br>

```
index=main sourcetype=adguard earliest=-24h 
| rename Result.IsFiltered as isFiltered 
| stats count(eval(isFiltered="true")) as blocked 
| fields blocked
```

<br>

### Blocked malware/phishing
<img width="403" height="335" alt="image" src="https://github.com/user-attachments/assets/3d6f49fc-16fb-45ae-8316-581a046871de" /><br>

```
index=main sourcetype=adguard earliest=-24h 
| rename Result.Reason as reasonCode 
| stats count(eval(reasonCode="4")) as malware 
| fields malware
```

<br>

### Blocked adult websites
<img width="416" height="347" alt="image" src="https://github.com/user-attachments/assets/be0f2e70-8b40-4572-8b77-1d0bc15fff8b" /><br>

```
index=main sourcetype=adguard earliest=-24h 
| rename Result.Reason as reasonCode 
| stats count(eval(reasonCode="5")) as adult 
| fields adult
```

<br>

### Traffic time chart (Allowed/Blocked)
<img width="945" height="104" alt="image" src="https://github.com/user-attachments/assets/4c0b8e76-9683-43fb-9b4c-4d69766c50cb" /><br>

```
index=main sourcetype=adguard earliest=-24h 
| rename Result.IsFiltered as isFiltered 
| eval status=if(isFiltered="true", "Blocked", "Allowed") 
| timechart span=30m count by status
```

<br>

### Top Blocked Domains
<img width="945" height="253" alt="image" src="https://github.com/user-attachments/assets/76249893-dda2-4bf1-91dd-2056bef78613" /><br>

```
index=main sourcetype=adguard earliest=-24h 
| rename Result.IsFiltered as isFiltered 
| search isFiltered="true" 
| top limit=10 QH showperc=false 
| rename QH as "Domains"
```

<br>

### Query-type
<img width="795" height="454" alt="image" src="https://github.com/user-attachments/assets/7a2d49f7-0d2d-498c-821b-d2fecd8339ef" /><br>

```
index=main sourcetype=adguard earliest=-24h 
| top limit=10 QT showperc=false 
| rename QT as "Query-type"
```

<br>

### Client IPs
<img width="834" height="428" alt="image" src="https://github.com/user-attachments/assets/e6eaae43-ef73-49c4-a201-57bfc22fe775" /><br>

```
index=main sourcetype=adguard earliest=-24h 
| top limit=10 IP showperc=false 
| rename IP as "Client IPs"
```

<br>

### Failed Server Logins
<img width="829" height="382" alt="image" src="https://github.com/user-attachments/assets/c9b69b3e-21f5-47fb-92cc-efee3461cf62" /><br>

```
index=main sourcetype=pve_proxy_access earliest=-7d 
| rex field=_raw "^(?<client_ip>\S+) \S+ \S+ \[(?<ts>[^\]]+)\] \"(?<method>\S+) (?<path>\S+) HTTP/[\d.]+\" (?<status>\d+)" 
| where path="/api2/json/access/ticket" AND status="401" 
| stats count 
| fields count
```

<br>

### Windows: Topp EventCodes
<img width="945" height="213" alt="image" src="https://github.com/user-attachments/assets/28759675-4db4-4d7d-88ea-50975c4e8a5f" /><br>

```
index=main host=DESKTOP-4OJMHUV sourcetype=WinEventLog* earliest=-7d 
| eval Log=case(sourcetype="WinEventLog:Security","Security", sourcetype="WinEventLog:System","System", sourcetype="WinEventLog:Application","Application", true(), sourcetype) 
| eval Label=case(EventCode="4907","4907 - Audit Policy Changed", EventCode="5379","5379 - Credential Read", EventCode="4798","4798 - User Group Query", EventCode="1001","1001 - System Error", EventCode="4624","4624 - Successful Login", EventCode="4672","4672 - Admin Login", EventCode="10019","10019 - DCOM Warning", EventCode="1094","1094 - Disk Check", EventCode="7023","7023 - Service Failed", EventCode="16","16 - Kernel Error", EventCode="10010","10010 - DCOM Registration Timeout", EventCode="16394","16394 - License Check (SPP)", EventCode="4799","4799 - Group Membership Query", EventCode="5058","5058 - Key File Operation", EventCode="5061","5061 - Cryptographic Operation", true(), EventCode)
| stats count by Label, Log 
| eventstats sum(count) as Total by Label 
| sort -Total, Label 
| streamstats dc(Label) as LabelRank 
| where LabelRank<=10 
| fields - Total, LabelRank 
| xyseries Label Log count
```

<br>

### Proxmox: Task Overview
<img width="847" height="391" alt="image" src="https://github.com/user-attachments/assets/43d0a00e-d3f3-4561-89e1-a727c9b1d536" /><br>

```
index=main sourcetype=pve_tasks earliest=-24h 
| rex field=_raw max_match=0 "UPID:pve:[^:]+:[^:]+:[^:]+:(?<task_type>[^:]+):[^:]*:root@pam:" 
| mvexpand task_type 
| eval Label=case(task_type="aptupdate","APT Package Update", task_type="vncproxy","VNC Console Access", task_type="vncshell","VNC Shell Session", task_type="vzcreate","Container Created", task_type="vzstart","Container Started", task_type="qmstart","VM Started", task_type="qmcreate","VM Created", task_type="download","Template Download", task_type="qmstop","VM Stopped", task_type="vzstop","Container Stopped", true(), task_type) 
| stats count by Label 
| sort -count 
| rename Label as "Task", count as "Count"
```

<br>




