---
title: "Looping pipeline parameters without a foreach"
categories: ["Powershell","CmdLet"]
---
# Looping pipeline parameters without a foreach

I was looking at the `begin`, `process` and `end` sections of a cmdlet; I was never clear myself when begin and end ran.

We use them a lot, but I may have been putting things in begin which should have been in process. I wanted to make sure I could give the right advice to my co-workers.

From a quick search it is clear to find documention around `begin` and `end` fire [once per pipeline](https://ss64.com/ps/syntax-function-input.html)

>**Begin**  
>This block is used to provide optional **one-time** pre-processing for the function.  
>The PowerShell runtime uses the code in this block one time for each instance of the function in the pipeline.

This is simlar for the end block:

>**End**  
>This block is used to provide optional one-time post-processing for the function.

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

I realised that this example wasnt a cmdlet, so I added `[CmdLetBinding()]` and that broke the code.

```powershell
#This one will error
Function Test-Demo
 {
    [CmdLetBinding()]
    Param ($Param1)
    begin { write-host "Starting" }
    process { write-host "processing" $_ for $Param1 }
    end { write-host "Ending" }
 }

Echo Testing1, Testing2 | Test-Demo Sample
```

This lead me to an example about handing pipeline variables with a cmdlet (which I cannot find). In the example they are doing a foreach after which didnt really explain what was going on with the pipeline variable during the process.

I thought, what happens if I applied the idea of the `$_` in the process section in the cmdlet:

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

I found this output `booya 1` through to `booya 5`. I also found I could use `$Data` in the same way, as the `Test-Pipe process` session was being called for each input value in the array.

Is it possible to fire the process once for an array rather than for each one?

Begin and End do not have access to the pipeline, and process only has the option of [one at a time processing](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_pipelines?view=powershell-6#one-at-a-time-processing)

Interesting, calling the function direct processes the input all at once.

```powershell
function Test-Pipe {
    [CmdLetBinding()]
    param(
        [parameter(ValueFromPipeline = $true, Mandatory = $true)]
        $Data
    )
    begin {
    }
    process {
        write-host "booya $Data"
    }
    end {
    }
}

1..5 | Test-Pipe

#output to console:
booya 1
booya 2
booya 3
booya 4
booya 5

Test-Pipe 1,2,3,4,5

#output to console:
booya 1 2 3 4 5
```

This doesnt stop you passing parameters:

```powershell
function Test-Pipe {
    [CmdLetBinding()]
    param(
        [parameter(ValueFromPipeline = $true, Mandatory = $true)]
        $Data,
        $OtherData
    )
    begin {        
    }
    process {
        write-host "booya $Data"
        write-host "other $OtherData"
    }
    end {
        
    }
}

1..5 | Test-Pipe -Other 1,2,3,4,5
```