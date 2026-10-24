# Creating Alerts in Splunk

### Search for an event you want to monitor in your network, and save it as an alert:
<img width="1033" height="183" alt="image" src="https://github.com/user-attachments/assets/8313f407-3dcc-4a1c-a5ea-5077ac8b9945" /><br>

<br>

## Alert setup
Fill information about what and when and then add trigger actions. In this example I’m using triggered alerts (sends the alert to an overview of triggered alerts in splunk) and discord webhooks, which are a simple way to post alerts from the splunk server into Discord using “internet magic”<br><br>

<img width="684" height="937" alt="image" src="https://github.com/user-attachments/assets/e50ee928-450c-42b3-9013-54e41c83dfa9" /><br>

<br>

## Discord Webhooks
After creating the webhook on discord you can copy the URL and paste it into URL box in splunk/script.<br>
<img width="945" height="686" alt="image" src="https://github.com/user-attachments/assets/59390683-57a6-4aba-a1d9-7bf284e2e347" /><br>

<br>

## Use the webhook in a script instead of splunk's webhook trigger action
Splunk’s webhook action did not work for me, so I had to create a .py script instead. There I placed the webhook URL instead of using splunk’s webhook trigger action.<br>

### Make a folder for the script in the splunk console and create the .py script
```
mkdir -p /opt/splunk/etc/apps/search/bin/scripts
```
```
nano /opt/splunk/etc/apps/search/bin/scripts/discord_alert.py
```

<br>

### Script:
<img width="945" height="266" alt="image" src="https://github.com/user-attachments/assets/8c22c4bd-5d3e-40ea-8491-446e7b3ef082" /><br>

<br>

### And then make the .py script executable 
<img width="945" height="35" alt="image" src="https://github.com/user-attachments/assets/5f3aadb5-34c0-43ec-b629-76b49a2b9f93" /><br>

```
chmod +x /opt/splunk/etc/apps/search/bin/scripts/discord_alert.py
```
<br>

## Testing if the alert works
The Proxmox: Failed Web UI Login (Brute Force) goes off if someone has failed logging in 5 times in a row. So let's try it!<br>
<img width="936" height="477" alt="image" src="https://github.com/user-attachments/assets/7c2b80f0-67a2-4367-be18-77c2a3019868" /><br>
Now I have failed logging into Proxmox VE multiple times.<br>

<br>

## What the results shows:

### Triggered Alerts
<img width="1117" height="204" alt="image" src="https://github.com/user-attachments/assets/24c7811c-1214-4160-afca-e9c9d6a3c4e7" /><br>

<br>

### Discord
<img width="1114" height="227" alt="image" src="https://github.com/user-attachments/assets/3692f7f7-a0bd-4604-b257-2aea1fca9418" /><br>

<br>


