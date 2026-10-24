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
