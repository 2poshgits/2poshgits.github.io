---
title: "Create an Interactive Remote Powershell Session"
categories: ["Powershell","Remoting"]
---

To create an interactive remote Powershell session, use the Enter-PSSession command:

```
Enter-PSSession -ComputerName "MyServer.MyDomain.Com"
```

Once in the session, the remote computer name will show at the prompt:

```
[MyServer.MyDomain.Com]: PS C:\Users\acarlm\Documents>
```

If you need to run the session as a different user, you can collect credentials via Get-Credential:

```
#prompt for credential - this will pop-up a box for users to enter credentials
$cred = Get-Credential
#create session
Enter-PSSession -ComputerName "MyServer.MyDomain.Com" -Credential $cred
```
When you have finished in your remote session, simply type "exit" at the prompt.
