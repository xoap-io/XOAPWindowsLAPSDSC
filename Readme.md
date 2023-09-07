# XOAPWindowsLAPSDSC

Windows Local Administrator Password Solution (Windows LAPS) is a Windows feature that automatically manages and backs up the password of a local administrator account on your Azure Active Directory-joined or Windows Server Active Directory-joined devices. You also can use Windows LAPS to automatically manage and back up the Directory Services Restore Mode (DSRM) account password on your Windows Server Active Directory domain controllers. An authorized administrator can retrieve the DSRM password and use it.

Windows LAPS is now available on the following OS platforms with the specified update or later installed:

- Windows 11 22H2 - April 11 2023 Update
- Windows 11 21H2 - April 11 2023 Update
- Windows 10 - April 11 2023 Update
- Windows Server 2022 - April 11 2023 Update
- Windows Server 2019 - April 11 2023 Update

All supported editions of the above platforms have been updated with Windows LAPS, including LTSC editions. The introduction of the Windows LAPS feature doesn't modify in any way whatsoever the standard Microsoft product lifecycle policies.
The Windows LAPS on-premises Active Directory scenarios are fully supported as of the above updates.

## Benefits of using Windows LAPS

Use Windows LAPS to regularly rotate and manage local administrator account passwords and get these benefits:

- Protection against pass-the-hash and lateral-traversal attacks
- Improved security for remote help desk scenarios
- Ability to sign in to and recover devices that are otherwise inaccessible
- A fine-grained security model (access control lists and optional password encryption) for securing passwords that are stored in Windows Server Active Directory
- Support for the Azure role-based access control model for securing passwords that are stored in Azure Active Directory

## Key Windows LAPS scenarios

You can use Windows LAPS for several primary scenarios:

- Back up local administrator account passwords to Azure Active Directory (for Azure Active Directory-joined devices)
- Back up local administrator account passwords to Windows Server Active Directory (for Windows Server Active Directory-joined clients and servers)
- Back up DSRM account passwords to Windows Server Active Directory (for Windows Server Active Directory domain controllers)
- Back up local administrator account passwords to Windows Server Active Directory by using legacy Microsoft LAPS

In each scenario, you can apply different policy settings.

## Requirements

There are a couple of requirements when it comes to installing and using Microsoft LAPS:

- It only works on Domain Joined devices
- Protects only one local admin account (recommend disabling any other accounts)
- We need to extend the Active Directory scheme
- Configuration is done with a Group Policy

LAPS Client needs to be installed on all client devices.

## GPO Settings

All current GPO Settings for Microsoft LAPS are described here:

[Policy Settings](https://learn.microsoft.com/en-us/windows-server/identity/laps/laps-management-policy-settings)

## Why should you use this module

- supports all 3 scenarios for Windows LAPS (Azure, Local AD, legacy Microsoft LAPS emulation mode)
- creates Local Administrator Account (GPO doesn't create it)

## Module Installation

To manually install the module, download the latest version and unzip the contents to the

"$env:ProgramFiles\WindowsPowerShell\Modules\XOAPWindowsLAPSDSC\x.x.x" directory.

Check the correct availability of the module by running the following command:

```powershell
Get-DscResource -Module XOAPWindowsLAPSDSC
```

If the module is installed correctly, the output should show the following DSC resources.

There are 3 Resources inside this module:

### AzureADWindowsLAPSSettings

```powershell
    AzureADWindowsLAPSSettings [String] #ResourceName
    {
        [DependsOn = [String[]]]
        [PsDscRunAsCredential = [PSCredential]]
        [AdministratorAccountName = [String]]
        [PasswordAgeDays = [Int32]]
        [PasswordLength = [Int32]]
        [PasswordComplexity = [Int32]]
        [PostAuthenticationResetDelay = [Int32]]
        [PostAuthenticationActions = [Int32]]
    }
```

### LocalADWindowsLAPSSettings

```powershell
    LocalADWindowsLAPSSettings [String] #ResourceName
    {   [DependsOn = [String[]]]
        [PsDscRunAsCredential = [PSCredential]]
        [AdministratorAccountName = [String]]
        [PasswordAgeDays = [Int32]]
        [PasswordLength = [Int32]]
        [PasswordComplexity = [Int32]]
        [PasswordExpirationProtectionEnabled = [Boolean]]
        [ADPasswordEncryptionEnabled = [Boolean]]
        [ADPasswordEncryptionPrincipal = [String]]
        [ADEncryptedPasswordHistorySize = [Int32]]
        [ADBackupDSRMPassword = [Boolean]]
        [PostAuthenticationResetDelay = [Int32]]
        [PostAuthenticationActions = [Int32]]
    }
```

### WindowsLAPSEmulationMode

```powershell
WindowsLAPSEmulationMode [String] #ResourceName
{
    [DependsOn = [String[]]]
    [PsDscRunAsCredential = [PSCredential]]
    [AdministratorAccountName = [String]]
    [PasswordAgeDays = [Int32]]
    [PasswordLength = [Int32]]
    [PasswordComplexity = [Int32]]
    [PasswordExpirationProtectionEnabled = [Boolean]]
    [AdministratorPasswordEnabled = [Boolean]]
}
```

## Default Settings

Default Settings defined in the module are:

### AzureADWindowsLAPSSettings

```powershell
    AdministratorAccountName = 'LAPSAdmin',
    PasswordAgeDays = '30',
    PasswordLength = '14',
    PasswordComplexity = '4',
    PostAuthenticationResetDelay = '24',
    PostAuthenticationActions = '3'
```

### LocalADWindowsLAPSSettings

```powershell
    AdministratorAccountName = 'LAPSAdmin',
    PasswordAgeDays = '30',
    PasswordLength = '14',
    PasswordComplexity = '4',
    PasswordExpirationProtectionEnabled = $true,
    ADPasswordEncryptionEnabled = $false,
    ADPasswordEncryptionPrincipal = '',
    ADEncryptedPasswordHistorySize = '0',
    ADBackupDSRMPassword = $false,
    PostAuthenticationResetDelay = '24',
    PostAuthenticationActions = '3'
```

### WindowsLAPSEmulationMode

```powershell
    $AdministratorAccountName = 'LAPSAdmin',
    PasswordAgeDays = '30',
    PasswordLength = '14',
    PasswordComplexity = '4',
    PasswordExpirationProtectionEnabled = $true,
    AdministratorPasswordEnabled = $true
```

For more info regarding each setting check Windows LAPS or for emulation mode Microsoft LAPS (legacy solution).

## Configuration examples for each Resource

```powershell
configuration TestConfigAzureAD
{
    Import-DscResource -ModuleName 'XOAPWindowsLAPSDSC' -ModuleVersion 'x.x.x'
    AzureADWindowsLAPSSettings 'TEST'
    {
        AdministratorAccountName = 'TestAdmin'
        PasswordAgeDays = '14'
        PasswordLength = '16'
        PasswordComplexity = '4'
        PostAuthenticationResetDelay = '2'
        PostAuthenticationActions = '1'
    }
}
```

```powershell
configuration 'TestConfigLocalAD'
{
Import-DscResource -ModuleName 'XOAPWindowsLAPSDSC' -ModuleVersion 'x.x.x'

    LocalADWindowsLAPSSettings 'TEST'
    {
        AdministratorAccountName = 'TestAdmin'
        PasswordAgeDays = '15'
        PasswordLength = '21'
        PasswordComplexity = '4'
        PostAuthenticationResetDelay = '2'
        PostAuthenticationActions = '1'
        PasswordExpirationProtectionEnabled = $true
        ADPasswordEncryptionEnabled = $true
        ADPasswordEncryptionPrincipal = 'Administrator'
        ADEncryptedPasswordHistorySize = '3'
        ADBackupDSRMPassword = $true
    }
}
```

```powershell
configuration 'TestConfigEmulationMode'
{
Import-DscResource -ModuleName 'XOAPWindowsLAPSDSC' -ModuleVersion 'x.x.x'

    WindowsLAPSEmulationMode 'TEST'
    {
        AdministratorAccountName = 'TestAdmin'
        PasswordAgeDays = '15'
        PasswordLength = '21'
        PasswordComplexity = '4'
        PasswordExpirationProtectionEnabled = $true
        AdministratorPasswordEnabled = $true
    }
}
```
