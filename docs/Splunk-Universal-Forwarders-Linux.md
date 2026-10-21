# Splunk Universal Forwarders Linux
For this example, I’ll install splunk forwarder for AdGuard.
Now that the Splunk server is set up it needs data to be useful. We do this by sending all the logs from the computers in the network to the Splunk Server using Splunk Universal Forwarders.<br>

<img width="945" height="226" alt="image" src="https://github.com/user-attachments/assets/9e619c0b-49fe-4cf9-982b-bdd91948dfc3" /><br>

Send data to the receiving server:

<img width="945" height="202" alt="image" src="https://github.com/user-attachments/assets/13eede72-64ff-4f08-89f5-90680f6e4f8b" /><br>

Set the port to 9997:

<img width="945" height="253" alt="image" src="https://github.com/user-attachments/assets/71f331c8-1cf8-4d52-95ed-65404bb30097" /><br>

This is the port the forwarders will use to send data to, aka to the server.

## Then to add these forwarders we first need to install them on my endpoints (AdGuard)
<img width="945" height="297" alt="image" src="https://github.com/user-attachments/assets/02d40569-9ca4-47f8-9666-d53c42a954e8" /><br>
