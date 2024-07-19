# Crowdstrike_BSOD_Workaround
CrowdStrike BSOD Workaround using Windows Group Policy on safe Mode

This is Windows GPO tested on local and was working, be carefull when deploying it at big IT level desk supervision. I assume that if you read this, you have a good knowledge of Windows GPOs.

Configure a Group Policy Object (GPO) to run a script during Safe Mode to address issues caused by the problematic CrowdStrike driver file leading to Blue Screen of Death (BSOD) errors. Here’s a step-by-step guide on how to achieve this:

# Create a Script to delete Crowdstrike files and for forcing safe mode
First File : 
```powershell
$driverPath = "C:\Windows\System32\drivers\C-00000291*.sys"
$driverFiles = Get-ChildItem -Path $driverPath -ErrorAction SilentlyContinue

foreach ($file in $driverFiles) {
    try {
        Remove-Item -Path $file.FullName -Force
        Write-Output "Deleted: $($file.FullName)"
    } catch {
        Write-Output "Failed to delete: $($file.FullName)"
    }
}
# Reset Safe Mode Boot
bcdedit /deletevalue {current} safeboot
```

Second File : 
```powershell
bcdedit /set {current} safeboot minimal
Restart-Computer
```


# GPO creation and configuration
After finishing the Script file, create a GPO for the appropriate Organizational Unit and name it as you want.\\

Afterwards naviagte to the GPO script policies : Policies.Windows Settings.Scripts(Startup/Shutdown) and add a new startup file by browsing and choosing the first file we wrote earlier.\\

Following the same steps as before add the second file to the GPO script policies.\\

The last step is deploying the GPO. (This GPO will force computers into safe mode using script 2 and then the first script wil delete the files causing the issue)\\


#HOPE THIS HELPS PEOPLE! GIVE THIS SOLUTION A FEEDBACK

