# Powershell-MicrosoftGraphAPI
Easy way to programatically access the Microsoft Graph API

# More information
https://tech.xenit.se/querying-microsoft-graph-with-powershell-the-easy-way/

# Installation
Since the module is not published on Powershell gallery the easiest way is to clone the repo and install it in your module folder.

```powershell
Remove-Item "Powershell-MicrosoftGraph" -recurse -force -erroraction silentlycontinue
git clone 'https://github.com/Freakling/Powershell-MicrosoftGraph'

If($isWindows){
    Copy-item -Path "Powershell-MicrosoftGraph\MicrosoftGraph\" -Destination ($env:PSModulePath.Split(';')[-1]) -recurse -force 
}
ElseIf($isLinux){
    Copy-item -Path "./Powershell-MicrosoftGraph/MicrosoftGraph" -Destination ($env:PSModulePath.Split(':')[-1]) -recurse -force
}
ElseIf($isMacOS){
    # No idea how to do this. please PR if you know.
}
Else{
    #Not PSCore - Default to windows
    Copy-item -Path "Powershell-MicrosoftGraph\MicrosoftGraph\" -Destination ($env:PSModulePath.Split(';')[-1]) -recurse -force 
}
```

# Example

```powershell
#Get a token
$credential = New-Object System.Management.Automation.PSCredential($appID,(ConvertTo-SecureString $SPSecret -AsPlainText -Force))
$token = Get-MSGraphAuthToken -credential $credential -tenantID $TenantID

#Get all users
Invoke-MSGraphQuery -URI 'https://graph.microsoft.com/v1.0/users?$top=999' -token $token -recursive -tokenrefresh -credential $credential -tenantID $TenantID | select -ExpandProperty Value

#Get All groups
Invoke-MSGraphQuery -URI 'https://graph.microsoft.com/v1.0/groups' -token $token -recursive -tokenrefresh -credential $credential -tenantID $TenantID | select -ExpandProperty Value

#Get all users from a specific group
Invoke-MSGraphQuery -URI "https://graph.microsoft.com/v1.0/groups/$($Group.id)/members" -token $token -recursive -tokenrefresh -credential $credential -tenantID $TenantID | select -ExpandProperty Value | Where-Object{$_.'@odata.type' -like "*graph.user"}

#Get all Onedrive items for a user
Get-OneDriveItems -UserId $User.id -token $token -credential $credential -TenantID $TenantID
```
