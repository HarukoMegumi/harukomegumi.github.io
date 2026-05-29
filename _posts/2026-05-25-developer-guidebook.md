---
layout: post
title:  "Developer Guidebook"
date:   2026-05-25 
categories: software
description: Guidelines for Windows terminal and powershell automation
---

# Terminal Guide
  
## Session  
**Issue#1**   
Each time a cmd/powershell/bash terminal is opened, either directly through system or through an IDE, a shell session is created. The session is a "parent" process under which "child" process tool commmands are run. The session loads configuration files or environment variables (PATHs) to build its environment which tool commands also inherit. Since each session works in static environment, any new tool installed during an active shell session, the changes made to the variables are not automatically loaded by that session and so its child processes tool commands also don't know the existence of the new tool installed. 

**Solution#1**   
Close the terminal and reopen it, it will initialize a session along with all newly installed tool information   

<!-- Placing two images side by side -->    
<div style="display: flex; justify-content: space-between;">
  <div style="flex: 1; margin-right: 10px;">
    <img src="/assets/Before.png" alt="Before" style="width: 100%;">
    <p style="text-align: center;">Before reopening terminal</p>
  </div>
  <div style="flex: 1; margin-left: 10px;">
    <img src="/assets/After.png" alt="After" style="width: 100%;">
    <p style="text-align: center;">After reopening terminal</p>
  </div>     
</div>


# Automation  

## Cron-job with Powershell Script  
**Issue#1**  
You want to schedule a task to check and append newly run commands from `C:\Users\USERNAME\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt` into a different
location's text file at 1 hour interval, it won't update the text file if no new command appears in ConsoleHost_history.txt   

**Solution#1**    
Windows Task Scheduler is a built-in tool that allows users to automate tasks on their Windows machines. To schedule a task that checks and appends newly run commands from `C:\Users\USERNAME\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt` into a different location's text file at 1 hour interval, you can follow these steps:

1. Open Task Scheduler: Press `Win + R`, type `taskschd.msc`, and press Enter.
2. Create a new task: In the Task Scheduler window, click on "Create Task" in the right-hand pane.
3. General settings: In the "General" tab,  
   - Provide a name for your task (e.g., "Backup PS History")  
   - Check "Run whether user is logged on or not"  
   - Check "Run with highest privileges".
4. Trigger settings: Go to the "Triggers" tab,  
   - click "New"  
   - Change "Begin the task" to "On a schedule".  
   - Under Settings, select Daily (leave it to recur every 1 days).  
   - Under Advanced settings, check the box for "Repeat task every" and select "1 hour"  
   - Change the dropdown next to "for a duration of": to "Indefinitely".  
   - Click "OK" to save the trigger.
5. Action settings: Go to the "Actions" tab,  
   - click "New..."
   - Action: "Start a program"
   - Program/script: powershell.exe
   - Add arguments: `-NoProfile -ExecutionPolicy Bypass -File "E:\Scripts\BackupHistory.ps1"`
   - Click OK.  
This will set up the task to run the specified PowerShell script every hour. Make sure to replace "E:\Scripts\BackupHistory.ps1" with the actual path where you saved your PowerShell script. 
6. Conditions settings: Uncheck "Start the task only if the computer is on AC power" (so it runs on laptops using battery too).   
7. Click "OK" to save the task. You may be prompted to enter your user credentials to allow the task to run with the specified privileges. Provide the necessary credentials and click "OK". Usually PC's `USERNAME` is the user name and the password is the same as your MS account password.  
8. Open powershell and run the following command:

```
Set-ExecutionPolicy RemoteSigned -Scope LocalMachine -Force
```  
Running this once ensures that your automated backup task can trigger and execute seamlessly without being blocked by Windows security.  
9. Write and save the following powershell script to backup the history file in "My PowerShell History Backup.txt":    

```
# Define paths
$SourceFile = "$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt"
$BackupFile = "E:\Backups\My PowerShell History Backup.txt"

# Check if source file exists
if (Test-Path $SourceFile) {
    # If backup doesn't exist yet, create it and copy everything
    if (-not (Test-Path $BackupFile)) {
        New-Item -ItemType File -Path $BackupFile -Force
        Copy-Item -Path $SourceFile -Destination $BackupFile -Force
        Exit
    }

    # Read all content from both files
    $SourceContent = Get-Content -Path $SourceFile
    $BackupContent = Get-Content -Path $BackupFile

    $SourceCount = $SourceContent.Count
    $BackupCount = $BackupContent.Count

    # Only proceed if the source has more lines than the backup
    if ($SourceCount -gt $BackupCount) {
        # Extract only the newly added lines
        $NewLines = $SourceContent[$BackupCount..($SourceCount - 1)]

        # Append new lines to the backup file
        Out-File -FilePath $BackupFile -InputObject $NewLines -Append -Encoding utf8
    }
}
```
<!--Special format for post date and update date-->
*Published on: 2026-05-25*   
*Last updated on: 2026-05-30*
