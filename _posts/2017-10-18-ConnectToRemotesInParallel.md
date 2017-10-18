---
title: "Execute Commands In Parallel on Remote Nodes"
categories: ["Powershell","Remoting"]
---
# Execute Commands In Parallel on Remote Nodes

Powershell allows you to create simultaneous remote sessions to exceute tasks on remote servers. This speeds up administration tasks when dealing with many servers at once.

For example, say we want to check that a particular file exists on our group of servers:

```powershell
#Create an array of our servers
$nodes = 
@(
    "MyServerName1",
    "MyServerName2",
    "MyServerName3",
    "MyServerName4"
)

try {
    #This creates a session to each server, using the credentials of the current user
    $pssessions = New-PSSession -ComputerName $nodes

    #This is a script block - the code that will execute on each remote node
    $myRemoteExecutionCode = {
        #Write the computer name to the console
        Write-Host $env:COMPUTERNAME

        #Test the file exists
        if(Test-Path "C:\Windows\notepad.exe") {
            #Write this message if it does...
            Write-Host "$($env:COMPUTERNAME) has notepad installed! :)"
        } else {
            #And this one if it doesn't...
            Write-Host "$($env:COMPUTERNAME) does not have notepad installed. :("
        }
    }
    
    #Run the command on the remote - it takes the sessions I want to run the command on and the command itself as inputs.
    Invoke-Command -Session $pssessions -ScriptBlock $myRemoteExecutionCode
}
catch {
    #If something goes wrong, like a session couldn't connect, we can see it here.
    Write-Host $_.Exception.Message
}
finally {
    #If the sessions are not null, we want to remove them.
    if($pssession -ne $null){
        Remove-PSSession -Session $pssession
    }
}
```

The output of the command is written back to your local console:
```
MyServerName4
MyServerName4 has notepad installed! :)
MyServerName1
MyServerName1 has notepad installed! :)
MyServerName2
MyServerName2 has notepad installed! :)
MyServerName3
MyServerName3 has notepad installed! :)
```
Notice the results are returned in a random order - this is because the command was sent to each server in parallel, and so the console dispalys in the order the servers returned in. Server 4 was fastest so returned first.

You can also pass local files as commands to run by using "FilePath" instead of ScriptBlock:
```powershell
#Create an array of our servers
$nodes = 
@(
    "MyServerName1",
    "MyServerName2",
    "MyServerName3",
    "MyServerName4"
)

try {
    #This creates a session to each server, using the credentials of the current user
    $pssessions = New-PSSession -ComputerName $nodes
    
    #Run the command on the remote - it takes the sessions I want to run the command on and the ps1 file with the code I want to execute.
    Invoke-Command -Session $pssessions -FilePath "C:\PoSHScripts\NotepadInstalled.ps1"
}
catch {
    #If something goes wrong, like a session couldn't connect, we can see it here.
    Write-Host $_.Exception.Message
}
finally {
    #If the sessions are not null, we want to remove them.
    if($pssession -ne $null){
        Remove-PSSession -Session $pssession
    }
}
```

