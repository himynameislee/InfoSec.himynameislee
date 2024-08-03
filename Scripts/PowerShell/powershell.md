# Powershell

## Useful Powershell Commands

Commands I have used that made my life easier. Remember to read the command and replace anything in \<BETWEEN>.

### GroupMembers.ps1

Displays members of a specified Security Group within Active Directory.

<pre class="language-powershell"><code class="lang-powershell"><strong>Get-ADGroupMember "&#x3C;GROUPNAME>"
</strong></code></pre>

### NestedGroupMembers.ps1

Identifies members of a nested group within a Security Group.

```powershell
Get-ADGroupMember -Identity "<NESTED-GROUP-NAME>" -Recursive -server "<DOMAIN.LOCAL>"
```

### PSVersion

Displays which version of PowerShell you're using.

```powershell
$PSVersionTable
```

### GroupMembersExport.ps1

Export the users of a Security Group to a .csv file.

```powershell
Get-ADGroupMember -identity "<SECURITY GROUP>" | Select name, samAccountname | Export-csv -path "<FILE-PATH>"
```

### SimilarNamingConvention.ps1

Exports a .csv with Security Groups with a specified naming convention.

```powershell
Get-ADgroup -Filter 'name -like "<*GROUP NAME*>"' | sort name | select name |
Out-file "<FILE-PATH>"
```

### GetUsernameBasedOnAttribute.ps1

Finds and exports users based on an attribute within AD. This is useful when you are provided with something like an Employee ID number instead of a username. (I.e John Doe 123456)

```powershell
# Just put the attribute value in a .txt, you don't need a header
# Extremely useful when you're given a large list of users and something like an EID and you need to get their usernames

$usernames = Get-Content -Path "<FILEPATH>"

$results = @()

foreach ($username in $usernames) {

  $user = Get-ADUser -Filter { <ATTRIBUTE-NAME> -eq $username }

  if ($user) {
    $results += $user | Select-Object Name, SamAccountName, DistinguishedName
  }
}

$results | Export-Csv -Path "<FILE-PATH>"  -NoTypeInformation
```

### TerminateUser.ps1

Disables a user account and removes all Security Groups.

```powershell
#The username is only needed in the .txt, you do not need to add a header.

$userList = Get-Content "<FILE-PATH>"

foreach ($user in $userList) {
    
    Disable-ADAccount -Identity $user
    Write-Host "Disabled account for user $user" -ForegroundColor Cyan
    
    #Get a list of all security groups that the user is a member of (Ignore Domain Users so the error doesn't occur)
    $groups = Get-ADPrincipalGroupMembership $user | Where-Object {$_.Name -ne "Domain Users" -and $_.GroupCategory -eq "Security"}

    foreach ($group in $groups) {
        Remove-ADGroupMember -Identity $group -Members $user -Confirm:$false
        Write-Host "Removed $user from $group" -ForegroundColor Red
    }
}

Write-Host "All users have been terminated." -ForegroundColor Green
```

### BulkExtendUserAccounts.ps1

Put usernames into a .txt file and you can extend their expiration date.

```powershell
# Create a .txt file and add the usernames of the accounts you're going to extend. No need to add a header in it.
# Add the path of the text file and change the -dateTime to one day after the expiration date
# Update the description in Row 11 then fire the script 
# Spot check to make sure everything worked as intended.

Get-Content <FILE-PATH> | Set-ADAccountExpiration -DateTime "[EXPIRATION DATE]"  

Get-Content <FILE-PATH> | Get-ADUser  -Prop Description | Foreach {
$desc =  "[USERNAME DESCRIPTION]"
     Set-ADUser  $_.sAMAccountName -Description $desc
        Write-Host "The user $($_.sAMAccountName) has been extended & the description updated to $($desc)" -ForegroundColor Green
 }
```

### CopyADGroups.ps1

Mirror a user's Security Groups with another user. Useful for users with the same job title who don't have similar accesses.

```powershell
Get-ADUser -Identity <SOURCE-USER> -Properties memberof | Select-Object -ExpandProperty memberof | 
Add-ADGroupMember -Members <TARGET-USER>
```

### BulkRemoveUsersFromADGroup.ps1

Removes users from a group in bulk using a .csv

```powershell
Import-Csv -Path “<FILE-PATH>” | ForEach-Object {Remove-ADGroupMember -Identity “<SECURITY-GROUP>” -Members $_.’User-Name’ -Confirm:$false}

Write-Host "`n"
    		Write-Host "The accounts have been successfully removed."
```

### FindStringInTXTFile.ps1

Finds a specified string of text in a .txt file.

```powershell
##################################################################
### Line One: Enter the file path of where you want to search. ###
### Line Two: Enter the string of text you are trying to find. ###
##################################################################

$Path = "<FILE-EXPLORER-PATH>"
$Text = "<TEXT>"
$PathArray = @()

### Gets all the files in $Path that end in ".txt". ###

Get-ChildItem $Path -Filter "*.txt" |
Where-Object { $_.Attributes -ne "Directory"} |
ForEach-Object {
If (Get-Content $_.FullName | Select-String -Pattern $Text) {
$PathArray += $_.FullName
}
}

Write-Host "Contents of ArrayPath:"
$PathArray | ForEach-Object {$_}
```

### InactiveUsers.ps1

Find users who haven't logged in for >30 days.

```powershell
Import-Module ActiveDirectory


$time = (Get-Date).Adddays(-180)
$now=[datetime]::Now.ToFileTime()


$report = Get-ADUser -Filter {LastLogonTimeStamp -lt $time -and enabled -eq $false -and PasswordNeverExpires -eq $false -and (accountexpires -ge $now -or accountexpires -eq 0)} -Properties Name, SamAccountName, DistinguishedName, LastLogonTimeStamp, PasswordLastSet | 
Select-Object samaccountname, Name,@{Name="LastLogon"; Expression={[DateTime]::FromFileTime($_.lastLogonTimestamp).ToString('yyyy-MM-dd')}},
@{Name='PasswordLastSet';Expression={Get-Date $_.PasswordLastSet -Format 'yyyy-MM-dd'}} | sort-object -property LastLogon, PasswordLastSet -Descending
$report | export-csv <FILE-PATH>
```

### ExportOU.ps1

Simply export users from a specified OU.

```powershell
 Get-ADUser -Filter * -SearchBase "<OU-PATH>" -Properties * | Select-Object name, samAccountName, description | 
 Export-CSV -Path "<FILE-PATH>"
```

### PasswordExpirationsFromOU.ps1

Password expiration dates for users in an OU.

```powershell
Get-ADUser -SearchBase "<OU=***>" -filter {Enabled -eq $True -and PasswordNeverExpires -eq $False} -Properties "Name", "SamAccountName","msDS-UserPasswordExpiryTimeComputed" | 
  Select-Object -Property "Name" , "SamAccountName", @{Name="Password Expiry Date"; Expression={[datetime]::FromFileTime($_."msDS-UserPasswordExpiryTimeComputed")}} |

    Out-File <FILE-PATH>
```

### CheckUsernames.ps1

See if the usernames exist. Paste usernames into a .txt file. Extremely useful when a data leak occurs and you need to cross reference if the user is still in your organization.

<pre class="language-powershell"><code class="lang-powershell"># Define the path to the text file containing usernames or emails
<strong>$filePath = "&#x3C;FILE-PATH>"
</strong>
# Read the contents of the text file
$userList = Get-Content -Path $filePath

# Iterate through each user in the list
foreach ($user in $userList) {
    # Check if the user exists in Active Directory based on username or email
    $userExists = $false

    # Check by username
    $userObject = Get-ADUser -Filter {SamAccountName -eq $user} -ErrorAction SilentlyContinue
    if ($userObject) {
        $userExists = $true
    }

    # If the user does not exist by username, check by email
    if (-not $userExists) {
        $userObject = Get-ADUser -Filter {EmailAddress -eq $user} -ErrorAction SilentlyContinue
        if ($userObject) {
            $userExists = $true
        }
    }

    # Output the result
    if ($userExists) {
        Write-Host "User '$user' exists in Active Directory." -ForegroundColor Green
    } else {
        Write-Host "User '$user' does not exist in Active Directory." -ForegroundColor Red
    }
</code></pre>

### BulkDisableAccounts.ps1

Usernames in a .txt file will be disabled.

```powershell
$users=Get-Content <FILE-PATH>
ForEach ($user in $users)
{
Disable-ADAccount -Identity $user
write-host "User $($user) has been disabled" -ForegroundColor Red
}
```
