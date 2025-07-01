# EDR-with-LimaCharlie

## Objective

The main objective of this lab was to explore EDR, using LimaCharlie, to detect an attack. From detecting the attack, I then went on to creatre a blocking rule and finally into threat hunting with YARA rules.
  
### Skills Learned

- Set up and deploy a sensor
- Create a malware and deploy it to the vulnerable system
- Detect an attack and visualize the steps of the attack
- How to craft a blocking rule in LimaCharlie
- Implement and test YARA Rules
- Automate Yara scans

### Tools Used

- LimaCharlie EDR and SIEM
- VMs
- Malware
  

### Steps

- Deploy the Vms to be used in this project, I used a Windows box and an Ubuntu box. The Windows box was configured to be vulnerable by turning off Windows Defender via group policy.
- Once the VMs were ready, I deployed a LimaCharlie sensor to the Windows Vm and configured LimaCharlie to collect Sysmon logs.

![image](https://github.com/user-attachments/assets/93d08a1d-7cc6-409d-be41-2bad77d4a88d)

- In LimaCharlie I then enabled Sigma rules via their built in add-ons
- Over in the Ubuntu VM, I set up Sliver and genetrated a payload using http

![image](https://github.com/user-attachments/assets/4c63506d-4edf-4722-adfc-2b1906c6ae2f)

- Using python3 http.server:8080 to create a web server, I downloaded the malware onto the Windows VM and executed the payload
- Once I had a live C2 session I escalated to System and dumped the lsass.exe file to the Ubuntu VM
- I was able to observe the file execution and the lsass.exe dump in LimaCharlie and checked the hash of the malware in Virus Total

  ![image](https://github.com/user-attachments/assets/85aab07b-ec82-4864-8aaa-d11dfb05900f)

- Knowing that dumping the lsass.exe is captured as a sensitive process, I isolated the event in LimaCharlie, so that I could use it to make a D&R Rule alerting anytime the lsass.exe was accessed.

![image](https://github.com/user-attachments/assets/6cd91259-6fa9-4c4b-b056-a26db7360841)

![image](https://github.com/user-attachments/assets/97671aed-858d-4df5-b809-616d45e55e3e)

- In order to test the functionality of the rule, I then dumped the lsass.exe file again, and checked for detections in LimaCharlie

![image](https://github.com/user-attachments/assets/f95b2acf-5574-4b51-a89a-9ce7e044ed03)

- Going a step further and setting up a D&R Rule to block an attack, I performed an attack that is commonly associated with ransomware, being an attempt to delete volume shadow copies.
- Sigma Rules detect an attempt to delete colume shadow copies by default, so no need to make a rule to detect this attack, but I used the detection to shape a D&R Rule that will kill the process attempting to delete the shadow copies, in this case an admin shell

![image](https://github.com/user-attachments/assets/68c75329-84b4-4649-b146-f7e61b07da12)

-As you can see here, when I try to execute a command in the C2 shell, it kills the connection, blocking a remote shell command execution

![image](https://github.com/user-attachments/assets/0bd4813d-ac29-4049-8a5d-aeedc215d45d)

- The next step in this project is to create a YARA rule to identify Sliver, in order to automate our scans

  ![image](https://github.com/user-attachments/assets/b7d01866-1d60-46a7-9e18-9394049c4fea)

- After executing a manual scan using the YARA Rules on the downloads folder, we get a couple detections

  ![image](https://github.com/user-attachments/assets/b0cbfea4-236e-4b01-ab88-a9f49bbd66e5)

- Finally to test the automation of the YARA Rules, we execute the malware again and get another detection

  ![image](https://github.com/user-attachments/assets/1d73c4ff-f232-4750-9206-4220d852c2d3)
