# 1. Vulnerability Scanning

## 1.1 OpenVAS (Greenbone Vulnerability Manager)

* Create a new Kali Linux VM with:
  * ≥ 60 GB storage
  * ≥ 8 GB RAM
  * ≥ 4 processor cores (my personal recommendation)
* Configure a network adapter that places the VM on the same subnet as your target machine (for my case, VMNet5)
<img width="863" height="678" alt="image" src="https://github.com/user-attachments/assets/505db678-0b3c-4a92-becf-36a066448602" />

* IP address: 192.168.2.90
* Subnet mask: /24 (or 255.255.255.0)
* Default gateway: 192.168.2.1
  
* ping your target Windows machine from your Kali machine: `ping <TARGET_IP>`
* From your target machine, ping your Kali machine: `ping <KALI_IP>`
* Run the following command to install OpenVAS:
```
sudo apt update
sudo apt upgrade -y
sudo apt dist-upgrade -y
```
* Run the command `sudo apt install openvas` to install Greenbone Vulnerability Manager
* Use `gvm-setup` to start OpenVAS on your Kali machine. Watch carefully for the password to your admin account:
<img width="641" height="109" alt="image" src="https://github.com/user-attachments/assets/f18179d7-f0ee-4676-8e11-7799f991563c" />

* Run `gvm-check-setup` to check OpenVAS's status.
* You should get the prompt: "It seems like your GVM-version.number installation is OK." for a good installation:
<img width="623" height="71" alt="image" src="https://github.com/user-attachments/assets/59b1d940-ac67-4ae4-a141-38afee8e17a3" />

* Enter your credentials from `gvm-setup` to access OpenVAS:

* Now that you're in the dashboard, click on the wand icon, then **_Task Wizard_**:
<img width="550" height="196" alt="image" src="https://github.com/user-attachments/assets/d36e3acc-af67-458c-b989-d50777515a42" />

* Enter the IP address of your chosen machine, then click **_Start Scan_**.
<img width="660" height="605" alt="image" src="https://github.com/user-attachments/assets/673a7bbf-468e-4cd1-a6b8-e61f0206239a" />

* You should see your vulnerability scan's progress as it completes:
<img width="940" height="99" alt="image" src="https://github.com/user-attachments/assets/c3fb1581-a14f-45a5-88ea-1e8ab32f50f7" />

* Navigate to **_Scans → Reports_** to view the vulnerability scan in more detail.
* Click on the “Date” column to view your report, then the download button to access multiple report formats:
<img width="940" height="101" alt="image" src="https://github.com/user-attachments/assets/bd885288-bc6d-4ace-9ff3-a82267c01a5a" />
<img width="940" height="387" alt="image" src="https://github.com/user-attachments/assets/6f17a3bd-d2b0-43c2-b4b8-c0e9390e2d12" />

* Select **_PDF_** as the report format (or whatever file type you prefer):
<img width="634" height="409" alt="image" src="https://github.com/user-attachments/assets/08d5173e-86df-444e-9291-5924579d13e6" />

* You can now view the report of your vulnerability scan as a PDF.
<img width="635" height="364" alt="image" src="https://github.com/user-attachments/assets/b95e217c-0f33-48b4-a959-aac661962753" />

* Link to my report: [OpenVAS Scan](https://github.com/encryptidhh/host-network-sec-remediation/blob/main/URSA-Scan-1-OpenVAS.pdf)

## 1.2 Nmap (Network Mapper)
* Run a sample Nmap scan on your chosen target. Example syntax:
`nmap -vv -A -T4 YOUR_MACHINE_IP > nmap-scan.txt`
* Viewing these scan results (on my machine) showed 3 open ports, all of which pose a significant risk if they are exposed to outside actors:
<img width="813" height="80" alt="image" src="https://github.com/user-attachments/assets/273c8bb4-ed5a-4ef6-b994-9e72db7407bd" />

* You can also run:
`nmap -vv -A -T4 YOUR_MACHINE_IP -p1-65535 > nmap.scan.txt` for a more comprehensive scan.

# 2. Closing Open Ports with Windows Firewall
* Sign in to your target Windows 10 Enterprise machine as an Administrator:
* Run **Windows Defender Firewall with Advanced Security_** with elevated permissions:
<img width="940" height="535" alt="image" src="https://github.com/user-attachments/assets/6473aaaa-10a9-404f-bc9d-16a063cbc6a5" />

*Click on **_Inbound Rules_** (we want to prevent external inbound traffic):
<img width="940" height="228" alt="image" src="https://github.com/user-attachments/assets/32bb45f1-2d50-46a6-8fd5-3a5c8613689d" />

* We will add two firewall rules:
  * One to block TCP/135 and TCP/139
  * One to block TCP/445 if the connection is insecure (doesn't use SMB version 3+)

* Click on **_New Rule_** to open the **_New Inbound Rule Wizard_**. Set the following configuration:
  * TCP rule
  * Specific local ports: 135, 139
* Click **_Next_**, then select the "Block the connection" radio button:
<img width="881" height="428" alt="image" src="https://github.com/user-attachments/assets/74a30cf8-8f11-40aa-8e8b-fae6e7d3cd70" />

* Click **_Next_** through the rest of the rule configuration, then set a name for the rule within the "Name" tab:
<img width="890" height="296" alt="image" src="https://github.com/user-attachments/assets/63ea5ba9-1496-4b1a-a2a9-1b99611a0b00" />

* Click on "New Rule" again to set a new firewall rule.
* Within "Protocol and Ports," set the following configurations:
  * Protocol: TCP
  * Port: 445

* Set **_Action_** to "Allow the connection if it is secure":
<img width="889" height="446" alt="image" src="https://github.com/user-attachments/assets/a44bb60f-8f21-4baa-bb52-a5a9d41ddb49" />

* Add users within your AD domain that will have access to the machine via SMB:
<img width="881" height="321" alt="image" src="https://github.com/user-attachments/assets/db5c7c53-5939-445e-b6af-61e4cc3b0af5" />

* Click **_Next_** to complete the rule configuration, and set a name for your rule.
* You should see both rules within Windows Defender Firewall:
<img width="940" height="210" alt="image" src="https://github.com/user-attachments/assets/3695e397-4071-4627-852e-0379466304b3" />

* To further verify network settings, run the same Nmap scan from your Kali Linux machine. No open ports should be shown (or at least, not the _same_
 open ports).

# 3. PolicyAnalyzer and LGPO

## 3.1 Viewing compliance with PolicyAnalyzer
* Run `winver` on the target machine to see the system version - take a note of this for reference.
<img width="375" height="349" alt="image" src="https://github.com/user-attachments/assets/600331cd-9d24-422d-b1d9-62c159cb69e2" />

* Go to Microsoft's official site to install PolicyAnalyzer: https://www.microsoft.com/en-us/download/details.aspx?id=55319
<img width="568" height="251" alt="image" src="https://github.com/user-attachments/assets/bf7898fa-f055-453c-a6c5-9d672d11f989" />
 
* Click **_Download_**, select your Windows version, LGPO.zip, and PolicyAnalyzer.zip, then click **_Download_** again to install the files.
<img width="780" height="625" alt="image" src="https://github.com/user-attachments/assets/5d795a03-42c7-47e4-accf-99a776439e4f" />

* Transfer the files from your host machine to your virtual machine - skip this step if you installed the files on your VM directly.
* Click **_Extract All.._** over each zipped folder, then extract them to the current directory you're working in.
<img width="639" height="456" alt="image" src="https://github.com/user-attachments/assets/3cfb2da6-053f-49ee-b6e3-55a6d0662502" />

* Click on the PolicyAnalyzer_40 folder, then create a desktop shortcut for PolicyAnalyzer.exe (right-click, then **_Send to... → Create desktop shortcut_**)
* Change your VM's resolution settings to at least 1400 x 900 (to view the PolicyAnalyzer window better):
<img width="565" height="424" alt="image" src="https://github.com/user-attachments/assets/dbef274d-21d2-4a98-a414-24f907abc512" />

* Click on the PolicyAnalyzer shortcut to launch the PolicyAnalyzer.exe window:
<img width="601" height="507" alt="image" src="https://github.com/user-attachments/assets/ae4f5874-651f-477a-b182-bda7f997dbed" />

* Load the rules for the **_Policy Rule sets in..._** field:
  1. Click on **_Downloads_** (or your chosen folder)
  2. Double-click **_Windows 10-v22H2-Security-Baseline_**
  3. Click **_Documentation_**
  4. Click **_Select Folder_**

* Adjust the resolution of your display again - this makes the User Account Control window easier to view (you'll be prompted with the UAC because you'll need elevated permission later).
* Click the checkbox for MSFT-Win10-22H2 to select it.
* Click **_Compare to Effective State..._** to compare your machine's Policies to Microsoft's recommended states:
<img width="940" height="301" alt="image" src="https://github.com/user-attachments/assets/cbaf2a90-aa04-4018-b2af-a9caa4f611fa" />

## 3.2 Enforcing compliance with LGPO

* Create a snapshot of your current machine.
* Open Microsoft PowerShell as an Administrator. Type in relevant admin credentials if necessary.
* Within PowerShell, run the following command to move the LGPO folder to the C:\ directory:
```
mv -Path "C:\Users\<USER.NAME>\<FOLDER>\LGPO_30" -Destination "C:\"
```
* Confirm that the Windows 10-v22H2-Security-Baseline folder and LGPO.exe are within the LGPO_30 folder:
<img width="741" height="178" alt="image" src="https://github.com/user-attachments/assets/964c826c-23b2-43ce-9df1-e757a263e0a4" />

* If either item is not present, add them using File Explorer or the `Copy-Item` cmdlet.
* Navigate into the LGPO_30 directory using `cd`, then run the following command to enforce secure Group Policy:
```
.\LGPO.exe /g "Windows-10-v22H2-Security-Baseline"
```
* Confirm the command runs without any issues:
<img width="940" height="323" alt="image" src="https://github.com/user-attachments/assets/50f2949d-93f8-4c4f-b393-c4b58ae1859a" />

* Congratulations - you have now used LGPO.exe to apply Secure Group Policy.
