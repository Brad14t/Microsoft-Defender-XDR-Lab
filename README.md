# Microsoft-Defender-XDR-Lab
Microsoft SC-200: Microsoft Security Analyst Interactive lab.

# Summary:

In this interactive lab, I learned how to use Microsoft Defender XDR and Microsoft Sentinel to investigate and mitigate a security incident in a hybrid environment. I began by selecting the most severe incident in Azure Sentinel and viewed detailed information, including resource IDs and malware details, using Microsoft Defender's XDR capabilities. After gathering incident details, I was guided through remediation steps, including using quick fix logic to block public access to Azure Storage accounts. I also explored deeper investigation methods by using Sentinel's "Investigate" feature, which provided a visual map of the affected components. This allowed me to trace and mitigate further malicious activities, including discovering linked malicious storage artifacts. By integrating playbooks, I automated the process of identifying and blocking new threat indicators. The lab also demonstrated how to track the attacker's entry point via suspicious Office app alerts and how to apply mitigation measures across the environment. Overall, I gained valuable hands-on experience in using Azure security tools to identify, analyze, and remediate threats efficiently.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Utilities:

- Microsoft Defender XDR
- Microsoft Sentinel
- Azure Environment
- Playbooks
- Quick Fix Logic
- Investigate Feature
- Alerts and Automation Rules

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Walkthrough:

Following along in an interactive lab to demonstrate Microsoft Defender XDR and Microsoft Sentinel working together to investigate a security incident in a hybrid environment

In the incidents section in Azure Sentinel, I select the most severe incident to investigate in Azure Defender due to its XDR capabilities.

![image](https://github.com/user-attachments/assets/776277a5-1400-40cd-b162-cd950c7b9774)

Once I selected the incident I can see further details in accordance with the incident. Under “Related entities” select Azure resources to show Resource ID and Subscription ID of the affected account.

![3](https://github.com/user-attachments/assets/def3a81e-759d-4a9e-8c48-3ec7d50b5f28)

There are further drop downs to provide vital information like the “File” and “Malware” drop down. File will provide us with the file name, directory and hash. Malware provides the name, category and file associated with that malware.
In Defender after receiving all information about this incident it will suggest an Action to take.

![image](https://github.com/user-attachments/assets/17221baa-7545-4f89-905e-ccd4684a7f2c)

Once you select Take Action the next screen will provide you with Mitigate the threat options and Prevent future attacks. I will click on one of the Prevent future attacks to see what defender recommends for remediation.

![image](https://github.com/user-attachments/assets/b98af3e8-08e2-42a3-894b-1ce1bdc6cccd)

Once I click on the recommended step of my choice it gives me a description of the what the problem is and a quick explanation on how to fix. There is also a quick fix logic available for some incidents. I’ll click on quick fix logic.

![image](https://github.com/user-attachments/assets/f8e30152-64fa-496e-ab9f-bc5e26aa6d46)

It provided me with a script to disallow public access to the Azure Storage accounts. 

![image](https://github.com/user-attachments/assets/4eacdbfb-05da-4960-9f82-56945c1f7874)

I want it to fix itself so I will select fix -> fix 1 resource.

![image](https://github.com/user-attachments/assets/47fb21ea-4a9e-427e-9aab-4f7039b0f840)

Make sure to see this notification in the top right of screen upon fixing the resource. 

![image](https://github.com/user-attachments/assets/b96e0813-85b9-426f-b0a9-7a203370107d)

But I need to make sure the attacker didn’t send any other malicious files. In Azure Sentinel I select Investigate on the associated incident. This will give me a deeper investigation.

![image](https://github.com/user-attachments/assets/ddde79bb-fa13-45b0-a1b0-3de32789d3ad)

Once I select Investigate, I am provided a “map” that shows all affected parts of the system from the incident. 

Circled in red we can see the incident, these are the file that was uploaded, the hash and the storage account.

![image](https://github.com/user-attachments/assets/ecdb607f-718c-4445-b822-25f1de52a4b9)

![image](https://github.com/user-attachments/assets/7607136a-8730-467a-97eb-409d70a97dc8)

If I follow the “map” it provides me with a Linked Malicious storage artifact, normally it wouldn’t be there unless you set a detection rule (Provided by Interactive lab). In this rule it allows the malicious IP to check blob storage for any other files associated with that IP. For this case it shows there is 2 additional files. 

![image](https://github.com/user-attachments/assets/634ee891-668b-465c-ac1a-e2a402b9e2d5)

When I click on the linked malicious storage artifacts it allows me to create a playbook with the new malicious hashes and use them as custom threat intelligence indicators.

![image](https://github.com/user-attachments/assets/0734537e-e6dc-4960-8980-bb7727527486)

Once I select View playbooks it takes me to all current playbooks.

When I select "run", the playbook will send Microsoft Defender the new hashes to look through the machines of the network.

This happens from the use of Microsoft Graph API in the playbook. It retrieves all data associated with that data. For this case it retrieves the data and looks for matching hashes. 

![image](https://github.com/user-attachments/assets/e4fa4de6-2338-48fa-9dcf-d51ed10561ee)

Make sure to get the notification that Playbook was triggered successfully.

![image](https://github.com/user-attachments/assets/dc6ba958-c225-406b-8719-6b898e6e7840)

I have now found the incident, and all associated malicious files. Next I want to see how the attacker gained access. 

I’ll head back to Azure Sentinel. And select a different incident.

I select the incident and take ownership (I will be the one investigating further)

![image](https://github.com/user-attachments/assets/9310d67b-24ec-4bd3-abc2-1e4edfdfe635)

Next I’ll investigate in Microsoft 365 Defender due to its deeper analysis of this incident.

I will go into the Alerts to see if any alerts were triggered by an office application.

![image](https://github.com/user-attachments/assets/4cbe2704-851f-4442-b43a-86a639780f79)

As we can see 3 alerts were generated when an Office application ran suspicious commands. I will select the alert I would like to further investigate.

![image](https://github.com/user-attachments/assets/0517858e-7652-4b36-a866-78ed9a714a0e)

Further investigation shows that a Microsoft Word was used to open an attached file (probably phishing). After opening the file a few suspicious PowerShell commands were run. After that a command.exe spawned a x.exe

![image](https://github.com/user-attachments/assets/6328194b-1bc2-4fe3-8068-36bf6fb2b18d)

Locating when x.exe ran we see the same IP associated in the attack.

![image](https://github.com/user-attachments/assets/85c3aee4-1e98-4c46-9935-6cbfc891f9bf)

At the very end of the alert it looks like the attacker scheduled tasks to perform hourly.
You can tell by the /sc this is a schtasks command, which schedules tasks.  Hourly is the choice to have it check by the hour.

![image](https://github.com/user-attachments/assets/a29f3e56-2fce-4689-8d6d-7b38f07ca405)

Back in Azure Sentinel I’ll select the investigate button to see the total affected “map”.

![image](https://github.com/user-attachments/assets/65d41d6b-fe51-4960-be5f-f88a3a93975e)

This map shows everything associated with the attacker. After finding all this information from investigating, the map grows pretty quicly.

![image](https://github.com/user-attachments/assets/ae8857ed-7d8e-4318-a677-a8e502cf8206)

Back in Azure Sentinel I can see what was done to fix this issue if I scroll down to the comments section. I can see that the incident triggered an automation rule playbook, in turn blocking the account.

![image](https://github.com/user-attachments/assets/07a83e3d-743f-4528-90bd-e6e86501eb56)

If you want to check what automation rule was used, continue scrolling in the comments section and select “automation rule”.
Once in Azure Sentinel Automation select the rule.

This will display what needs to happen for the rule to trigger, and when it does trigger, what actions need to happen.

![image](https://github.com/user-attachments/assets/b2c638b7-bbb0-45af-acdb-6009d25767ed)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Conclusion:

Furthered my education and understanding of Microsoft Defender XDR and Microsoft Sentinel for hybrid security incident detection and response.
Gathered incident details, including malware signatures and affected resources, and applied remediation steps.
Utilized automated playbooks and quick fixes to secure Azure resources and block further threats.
Investigated the attacker's entry point and used mapping and automation tools to streamline the incident response process.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Badges:

These are learning modules in Microsft Learn, with completion of this interactive lab and completion of all other modules included.

https://learn.microsoft.com/api/achievements/share/en-us/BradleySpooner-8327/KG5PC4ZB?sharingId=D3C166ED7E32C3B8

https://learn.microsoft.com/api/achievements/share/en-us/BradleySpooner-8327/FVZW26HX?sharingId=D3C166ED7E32C3B8






















