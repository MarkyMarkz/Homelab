# Splunk Universal Forwarder for Linux
For this example, I’ll install splunk forwarder for AdGuard.
Now that the Splunk server is set up it needs data to be useful. We do this by sending all the logs from the computers in the network to the Splunk Server using Splunk Universal Forwarders.<br>

<img width="945" height="226" alt="image" src="https://github.com/user-attachments/assets/9e619c0b-49fe-4cf9-982b-bdd91948dfc3" /><br>

Send data to the receiving server:

<img width="945" height="202" alt="image" src="https://github.com/user-attachments/assets/13eede72-64ff-4f08-89f5-90680f6e4f8b" /><br>

Set the port to 9997:

<img width="945" height="253" alt="image" src="https://github.com/user-attachments/assets/71f331c8-1cf8-4d52-95ed-65404bb30097" /><br>

This is the port the forwarders will use to send data to, aka to the server.

<br>

## Then to add these forwarders we first need to install them on my endpoints (AdGuard)
<img width="945" height="297" alt="image" src="https://github.com/user-attachments/assets/02d40569-9ca4-47f8-9666-d53c42a954e8" /><br>

From last time I installed Splunk, I ran into a problem where the program couldn’t write its own logs. This happened because dpkg -i creates some of the subdirectories under /opt/splunk/var/log/ (such as splunk, introspection, watchdog, and client_events) as root during installation, even after running chown -R splunk:splunk /opt/splunk beforehand. Since Splunk doesn’t run as root, it couldn’t write to these directories and failed to start properly.

<br>

Fix: run chown -R splunk:splunk /opt/splunk again, after installation is complete, to make sure every file and folder including the ones created during install is owned by the splunk user before starting the service for the first time.
<img width="642" height="88" alt="image" src="https://github.com/user-attachments/assets/9e6c5019-2592-4d93-9635-de61f7c45a87" /><br>

<br>

## Setting up the forwarder account and connect it to the Splunk server
su - splunkfwd -c "/opt/splunkforwarder/bin/splunk start --accept-license --answer-yes"
<img width="673" height="139" alt="image" src="https://github.com/user-attachments/assets/e811e15c-7900-4cc4-9029-45ce4ce1d37d" /><br>

<br>

And now we can finally connect it to the Splunk Server:<br>
su - splunkfwd -c "/opt/splunkforwarder/bin/splunk add forward-server 192.168.0.145:9997"
<img width="945" height="138" alt="image" src="https://github.com/user-attachments/assets/2b8e1efc-80dc-418c-b4c0-edc4e0a2b20a" /><br>

Now that they are connected, we must choose what logs we would like to send from AdGuard to the Splunk Server. I will send AdGuard’s querylog.

<br>

## Locating the logs and forwarding them to splunk
The first step is to find out where AdGuard is saving its logs:<br>
find / -iname "querylog*" 2>/dev/null<br>
<img width="697" height="88" alt="image" src="https://github.com/user-attachments/assets/7e829ce7-b151-4e3f-9e5d-194de10e1086" /><br>

<br>

### And then we can choose to monitor it:
su - splunkfwd -c "/opt/splunkforwarder/bin/splunk add monitor /opt/AdGuardHome/data/querylog.json -index main -sourcetype adguard"

<br>

### This gave me an error message:
<img width="945" height="57" alt="image" src="https://github.com/user-attachments/assets/b81f221c-2f0e-4d17-b3f6-9b9c9d7442b6" /><br>

<br>

To fix this we also need to give splunkfwd reading rights without needing to open all the files. In the snapshot you can see that the querylog.json is owned by root and its only root that can read or write that file, which means splunkwfd cannot read it.
<img width="788" height="585" alt="image" src="https://github.com/user-attachments/assets/27c716cf-4ffb-4d05-9494-aace0b48f4b6" /><br>

<br>

### To change this I used ACL (Access Control List) to let splunkfwd read current and future files:
apt install -y acl<br>
setfacl -R -m u:splunkfwd:rX /opt/AdGuardHome/data<br>
setfacl -d -m u:splunkfwd:rX /opt/AdGuardHome/data<br>
<img width="664" height="148" alt="image" src="https://github.com/user-attachments/assets/53b338f2-61b8-45f0-ba69-05deda06974f" /><br>

<br>

### Now splunkfwd can read the querylog.json
<img width="818" height="617" alt="image" src="https://github.com/user-attachments/assets/d24a05d2-b668-4037-a3cd-04c0f3228eef" /><br>
<img width="778" height="298" alt="image" src="https://github.com/user-attachments/assets/b978d03d-909f-4371-9f84-f1840f69a68e" /><br>

<br>

### Now let’s try to monitor the file again: 
su - splunkfwd -c "/opt/splunkforwarder/bin/splunk add monitor /opt/AdGuardHome/data/querylog.json -index main -sourcetype adguard"
<img width="945" height="71" alt="image" src="https://github.com/user-attachments/assets/6b08c451-3131-4192-8061-5639f1788630" /><br>
No error this time :) 

<br>

## Let’s see if we get anything on splunk
<img width="945" height="469" alt="image" src="https://github.com/user-attachments/assets/e00efaeb-96ed-4627-9141-9f078b538097" /><br>

<br>































