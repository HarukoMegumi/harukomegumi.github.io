---
layout: post
title:  "Developer Guidebook"
date:   2026-05-25 
categories: software
description: Guidelines for Windows terminal management and operations
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



