## Set-LocalPasswordPolicy

The objective of this function is to adjust various settings related to the local password policy on a Windows system. 

The function offers a range of parameters to customize the policy settings. These include settings such as `PasswordComplexity`, `MinimumPasswordLength`, `MinimumPasswordAge`, `MaximumPasswordAge`, `PasswordHistorySize`, and several account lockout settings like `LockoutBadCount`, `ResetLockoutCount`, and `LockoutDuration`.

For instance, if you wanted to ensure password complexity, set a minimum password length of 8 characters, and specify that passwords must be at least 1 day old before they can be changed, you could do so using the command:

```
Set-LocalPasswordPolicy -PasswordComplexity:$true -MinimumPasswordLength 8 -MinimumPasswordAge 1
```

Setting lock out duration to 30 minutes

```
Set-LocalPasswordPolicy -PasswordComplexity:$true -LockoutDuration 30
```

## Under the hood

PowerShell doesn't have a dedicated cmdlet or .NET method to directly alter the security policy, I mean, if it did, you probably wouldn't be here. It leverages `secedit.exe`. So, how does it work? The function uses 'secedit.exe' to export the current local security policy to a temporary file. It then reads this file into a variable. The function manipulates the text of the exported policy file to adjust the settings based on the input parameters with some simple regex.

```
$sec_pol_cfg = Get-Content $outfile
```

```
Foreach ($key in $PSBoundParameters.Keys) {
    If (@([System.Management.Automation.Cmdlet]::CommonParameters,"Confirm") -contains $key) {
        Continue

    }
    [int]$value = $PSBoundParameters[$key]
    $sec_pol_cfg = $sec_pol_cfg -replace ("$key = \d+", "$key = $value")

}
```

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License
[MIT](https://choosealicense.com/licenses/mit/)