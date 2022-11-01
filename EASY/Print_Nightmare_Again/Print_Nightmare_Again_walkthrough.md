# Print Nightmare Again Walkthrough
### Link
- https://tryhackme.com/room/printnightmarec2bn7l
---
**Question**
#### The user downloaded a zip file. What was the zip file saved as?

Open FullEventLogView and search the keyword **zip**. But , we need to search inside all events.

For that, go to Options --> Advanced Options --> on the first dropdown list , select **show events from all times** and click OK.

Now we can search the keyword **zip**
**Answer** 
#### levelup.zip

**Question**
#### What is the full path to the exploit the user executed?

The tough part... We need to navigate inside FullEventLogView and find the full path to the exploit. To help us, we know from the previous question, the name of the user is **bmurphy** so we can try to serach all events with this key word.

**Answer** 
#### C:\Users\bmurphy\Downloads\CVE-2021-1675-main\CVE-2021-1675.ps1

**Question**
#### What was the temp location the malicious DLL was saved to? 

Same pattern as above

**Answer** 
#### C:\Users\bmurphy\AppData\Local\Temp\3\nightmare.dll

**Question**
#### What was the full location the DLL loads from?

We know the name of the dll : **nightmare.dll** so we can use it as a key word.

**Answer** 
#### C:\Windows\System32\spool\drivers\x64\3\New\nightmare.dll

**Question**
#### What is the primary registry path associated with this attack?

Scroll down and you can find the event with the same PID **2600**

**Answer** 
#### HKLM\System\CurrentControlSet\Control\Print\Environments\Windows x64\Drivers\Version-3\THMPrinter\DriverVersion

**Question**
#### What was the PID for the process that would have been blocked from loading a non-Microsoft-signed binary?

See above

**Answer** 
#### 2600

**Question**
#### What is the username of the newly created local administrator account?

Scroll down and be patient ^^

**Answer** 
#### backup

**Question**
#### What is the password for this user?

For this question, we need to open the powershell history file located here : **C:\Users\bmurphy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ControlHost_History**

**Answer** 
#### ucGGDMyFHkqMRWwHtQ

**Question**
#### What two commands did the user execute to cover their tracks? (no space after the comma)

Inside the same file, you have the answer

**Answer** 
#### rmdir .\CVE-2021-1675-main\,del .\levelup.zip