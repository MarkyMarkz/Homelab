# Create a container
<img width="945" height="711" alt="image" src="https://github.com/user-attachments/assets/d0cc3b1a-0a0c-41b2-a3c0-217be0217304" /> <br>
Then: apt update && apt full-upgrade -y

<br>

## Download Splunk
wget -O splunk-10.6.0.5-86587d4e3b27-linux-amd64.deb https://download.splunk.com/products/splunk/releases/10.6.0.5/linux/splunk-10.6.0.5-86587d4e3b27-linux-amd64.deb

<br>

## Splunk error with creation of logs
<img width="429" height="361" alt="image" src="https://github.com/user-attachments/assets/6d276e63-6577-4288-b4e8-600c95fa446d" /><br>
The log files were owned by root not letting Splunk create its log files. 
I had to use chown -R splunk:splunk /opt/splunk to change the ownership to Splunk.

<br>

## Installing Splunk
<img width="945" height="101" alt="image" src="https://github.com/user-attachments/assets/ad218eb9-7314-4758-9b2b-1686355d220b" /><br>
dpkg -i splunk-10.6.0.5-86587d4e3b27-linux-amd64.deb

<br>

## Starting splunk + creation of admin account
<img width="622" height="145" alt="image" src="https://github.com/user-attachments/assets/20d5d930-957f-47db-b880-1470ecb32a6d" /><br>
su - splunk -c "/opt/splunk/bin/splunk start --accept-license --answer-yes"

<br>

## Splunk GUI
<img width="626" height="31" alt="image" src="https://github.com/user-attachments/assets/99281023-ce8e-4c05-adcf-e85f65ff80e9" /><br>
<img width="945" height="172" alt="image" src="https://github.com/user-attachments/assets/c6a770ff-efca-48ba-bd32-d37fdbe937a2" /><br>

<br>

