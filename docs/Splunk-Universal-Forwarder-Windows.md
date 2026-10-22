# Splunk Universal Forwarder for Windows

### Download Splunk Universal Forwarder for Windows:
<img width="945" height="445" alt="image" src="https://github.com/user-attachments/assets/ab0486c8-b2d3-46d2-82d7-fa8dd3902601" /><br>
<img width="945" height="35" alt="image" src="https://github.com/user-attachments/assets/78f38ef9-ebc4-47b0-89c7-f935c0f2752f" /><br>

<br>

## Follow the installation guide:
<img width="797" height="610" alt="image" src="https://github.com/user-attachments/assets/3b783816-2738-406c-ae94-8431f94809b0" /><br>
<img width="794" height="616" alt="image" src="https://github.com/user-attachments/assets/10baeaed-6243-4169-8ba3-11d844f2971b" /><br>

<br>

## Then check if the forwarder is running towards the splunk server
<img width="945" height="278" alt="image" src="https://github.com/user-attachments/assets/03048784-2a67-4203-9f4b-c44b86815831" /><br>

## Forwarding the logs to be monitored
Now that the forwarder is connected to the Splunk server, we can start sending Windows Security, System, and Application logs to it.<br>

Unlike file-based logs, Windows Event Log sources can’t be added with a simple CLI command (splunk add monitor); they require a WinEventLog input stanza instead. The Universal Forwarder has no web interface of its own, so this has to be configured directly in inputs.conf, located at:<br>
C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf<br>

<img width="663" height="478" alt="image" src="https://github.com/user-attachments/assets/e2eef22c-c8bc-4d3a-bf50-f1b0cfc0d1b6" /><br>

Each stanza enables (disabled = 0) collection for one Windows event log channel and routes it to the main index on the Splunk server.<br>

### After creating the file, the SplunkForwarder service needs to be restarted:
Restart-Service -Name "SplunkForwarder"
<img width="898" height="217" alt="image" src="https://github.com/user-attachments/assets/520feb14-94d2-452f-98ec-a9e3ef5b19c6" /><br>

## Result on the splunk server
<img width="945" height="446" alt="image" src="https://github.com/user-attachments/assets/045351a2-a8c6-4de1-b41b-af3007b35ae1" /><br>








