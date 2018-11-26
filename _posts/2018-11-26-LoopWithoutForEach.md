---
title: "Looping pipeline parameters without a foreach"
categories: ["Powershell","Remoting"]
---
# Looping pipeline parameters without a foreach

I was looking into the `begin`, `process` and `end` sections, was never really when begin and end ran, these are normally used in cmdlets.

```powershell
Function Test-Demo
 {
  Param ($Param1)
  Begin{ write-host "Starting"}
  Process{ write-host "processing" $_ for $Param1}
  End{write-host "Ending"}
 }

Echo Testing1, Testing2 | Test-Demo Sample
```

but then I realised that this wasnt a cmdlet, so I added `[CmdLetBinding()]` and the request broke.

This lead me to an example about handing pipeline variables as a cmdlet.

but I found it wasnt actually doing much other than proving that you could get a value from the pipeline and the pass it out.

even in the example they are doing a foreach after.

```powershell
function Test-Pipe {
[CmdLetBinding()]
param(
[parameter(ValueFromPipeline=$true)]
$Data
)
begin {}
process {
write-host "booya $_"
}
end {}
}

1..5 | Test-Pipe
```