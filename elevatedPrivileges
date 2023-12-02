<#
    .SYNOPSIS
        This tool performs process lineage and adds user/domain fields.

    .DESCRIPTION
        This tool with execute two (1) PowerShell commands;
            Get-WmiObject -Class Win32_Process

    .NOTES
        This tool will assist with:
            Understanding what process executed what process  
            Troubleshooting processes
            Investigating anomalous processes 

            The following converts time to human readable format:
            '@{n='CreationDate';e={$_.ConvertToDateTime($_.CreationDate)}}'
            
    .EXAMPLE
        Format the information in a human readable table format.
            Get-ProcessLineage -Id 1337

        Sort objects by local port and format the information in a human reable format.
            Get-ProcessLineage -Id 1337 | Format-Table

        Sort objects by local port and export to Comma-Separated Value (CSV) file.
            Get-ProcessLineage -Id 1337 | Export-Csv example.csv
#>

function Get-ProcessLineage{
    [CmdletBinding()]
    param(
        [Parameter(Mandatory)] [int] $Id
    )

    $systemProcesses = Get-WmiObject -Class Win32_Process
    $selectProcess = $systemProcesses | Where-Object ProcessID -eq $Id
    $consolidatedInformation = @()

    while($null -ne $selectProcess.ProcessId){
        $consolidatedInformation += [PSCustomObject] @{
            'ProcessName' = $selectProcess.ProcessName
            'ProcessId' = $selectProcess.ProcessID
            'ProcessPath' = $selectProcess.ExecutablePath
            'UserName' = ($selectProcess.GetOwner()).User
            'DomainName' = ($selectProcess.GetOwner()).Domain
            'ProcessParentId' = $selectProcess.ParentProcessId
            'CreationDate' = ($selectProcess | Select-Object @{n='CreationDate';e={$_.ConvertToDateTime($_.CreationDate)}}).CreationDate
        }
        $selectProcess = $systemProcesses | Where-Object ProcessID -eq $selectProcess.ParentProcessId
    }
    return $consolidatedInformation
}
