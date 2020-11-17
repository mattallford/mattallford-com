---
author: Matt Allford
title: "Microsoft PowerShell SecretManagement Module"
date: 2020-09-23
draft: false
url: /2020/Microsoft-PowerShell-SecretManagement-Module/
categories:
- '2020'
- September
tags:
- DevOps
- PowerShell
- Secrets
- Credentials
- Microsoft
---

First things first, if you prefer to consume video content, the same content that is in this blog post is in the video below. If you prefer to read, please skip past the video and read on!

{{< youtube s-XJJYglWFg >}}

# Secrets Management

Microsoft have recently released an update to a PowerShell module they've been working on for a short while now which is everything to do with management of secrets.

[Version 3 of the Secret Management PowerShell module](https://devblogs.microsoft.com/powershell/secretmanagement-preview-3/) was released last week and it is a decent update from version 2, so much so that it includes breaking changes, as can be expected with preview releases. So with that said, given this is still in preview, please don't use this in any production capacity!

Historically we have had cmdlets to generate and manage credentials, and even export them as excrypted files such as XML files on to the file system. These can then be imported at script execution time, but it's a bit clunky and it doesn't bring any of the secret management benefits that a full blown secret manager / vault can provide.

The big idea here with this module is to create a set of cmdlets that can be used to manage secrets in PowerShell. That's what the module named SecretManagement is all about. The secrets are stored in a vault, and that can pretty much be any vault such as Azure Key Vault, Windows credential manager, Hashicorp Vault, so on and so forth. Microsoft have designed this in a way where the vault modules are extensible, so any vault store with an API / CLI can have a module written around it to be compatible with the SecretManagement module. Without a vault, the SecretManagement module and cmdlets will not really be of any use.

With the release of SecretManagement Preview 3, Microsoft also released preview 1 of SecretStore, which is a cross-platform local extension vault. Because it used the .NET cryptographic APIs, it works across all PowerShell supported platforms including Linux and Mac.

In this post, I'm going to explore the SecretManagement module working with a local vault provided by the SecretStore module. Strap in!

# Installing the Modules

Ok, to get going we need to install the SecretManagement and SecretStore modules which are available on the Powershell Gallery. We need to use the `-AllowPreRelease` parameter as these modules are still in preview.



```powershell
Install-Module Microsoft.PowerShell.SecretManagement -AllowPrerelease
Install-Module Microsoft.PowerShell.SecretStore -AllowPreRelease

```

Let's check out the commands that are available in the SecretManagement module

```powershell
Get-Command -Module Microsoft.PowerShell.SecretManagement | select name

Name
----
Get-Secret
Get-SecretInfo
Get-SecretVault
Register-SecretVault
Remove-Secret
Set-DefaultVault
Set-Secret
Test-SecretVault
Unregister-SecretVault
```

That's a handy set of cmdlets and as per all PowerShell cmdlets, it's very easy to determine what each cmdlet is going to do. If you're like me, you might be wondering how you add a new secret. To do that, you actually use `Set-Secret`, and we'll get to that in a moment.

Remeber that at the moment we don't currently have a vault installed or registered, so if we tried to do something like Get-Secret, it's going to fail:

```powershell
Get-Secret -name MySecret
Get-Secret: The secret MySecret was not found.
```

# Registering a Vault

Let's go ahead and register a vault using the SecretStore module. To do that, you need to use Register-SecretVault and provide it with the name of the Vault module (if the module is in one of your $env:PsModulePath folders), or the full path in the file system to the module. My SecretStore module is in one of the PsModulePath folders, so I just need to reference the name and I'm also going to set this as the default vault:

```powershell
Register-SecretVault -Name MyVault -ModuleName Microsoft.PowerShell.SecretStore -DefaultVault
```

# Working with Secrets

Now we have a vault registered, we can create a secret in the vault. Now, before we jump in to that, one quick sidebar. The SecretStore vault, by default, requires you to specify a password for accessing the vault. This has got nothing to do with the secrets themselves, but this is a password you need to set and enter to access the secrets. When you enter the password, by default the vault remains unlocked for 15 minutes in the same Powershell session, after which you'll be prompted to enter the password again.

With that out of the way, let's create a new secret by using Set-Secret. The example I'm going to use here is to store an Azure Function Key that I need to specify to trigger a function, but the secret can be any sort of password, passphrase, API key, etc.

```powershell
Set-Secret -Name FunctionKey -Vault MyVault
```

Because I didn't enter the secret when calling `Set-Secret`, which could be done with the `-Secret` or `-SecureStringSecret` parameter, I was prompted to enter the secret in to the console:

```PowerShell
cmdlet Set-Secret at command pipeline position 1
Supply values for the following parameters:
SecureStringSecret: ********************************************************
```

Now, immediately after that, I get another prompt telling me that my new vault requires a password, and because this is the first time interacting with the vault, I need to set a password:

```powershell
Vault MyVault requires a password.
Enter password:
********
```

If you were to add another secret straight away now, you would only get prompted for the value of the secret you are trying to set, you wouldn't get prompted for a vault password.

Alright, at this point we've got a new secret in the vault. We can use `Get-SecretInfo` to find and list metadata about one or more secrets:

```powershell
Get-SecretInfo

Name                Type VaultName
----                ---- ---------
FunctionKey SecureString MyVault
```

And we can retrieve the specific secret by using `Get-Secret`:

```powershell
Get-Secret -Name FunctionKey
System.Security.SecureString
```

If the platform / endpoint you're planning to use with the secret can work with a credential object, you can store the above in a variable and start working from there. There are times, like in my scenario, where I need the plain text of the secret to include in another command, and to do that you can use the `-AsPlainText` parameter:

```powershell
Get-Secret -Name FunctionKey -AsPlainText
nXTDj/6/3umxVTx9Rl5LOEjjgYCj8vVrgafAS20PcXVT1X4FjOlghg==
```

To put this in to a practical use case, I've got a function running up in Azure that is triggered via a HTTP request, but to trigger the function I need to specify an API key, which is the secret we just created. Let me try using Invoke-RestMethod to trigger my function without spefying the key:

```powershell
Invoke-RestMethod -Method get -Uri "https://pssecretfcn.azurewebsites.net/api/pssecretfcn"

Invoke-RestMethod: Response status code does not indicate success: 401 (Unauthorized).
```

And that failed because I'm not authorized to trigger the function. To successfully trigger it, I need to provide the function Key after `?code=` in the Uri. What I can do here is enclose the `Get-Secret` cmdlet to get the value of my secret in plain text and build that in to the URI. This time it works, and I get a cheesy response written back to me (yeah, I may have written that myself :)):

```powershell
Invoke-RestMethod -Method get -Uri "https://pssecretfcn.azurewebsites.net/api/pssecretfcn?code=$(Get-Secret -Name FunctionKey -Vault MyVault -AsPlainText)"


Congratulations, you triggered the function using the Function Key from the PowerShell Secret Management Module!
```

And really, that's pretty much it. If you want to remove a secret, that can be performed with `Remove-Secret`:

```powershell
Remove-Secret -Name FunctionKey

cmdlet Remove-Secret at command pipeline position 1
Supply values for the following parameters:
Vault: MyVault
```

The beauty of what we've gone through above, is that you can use the same cmdlets to interact with secrets in *any* vault, provided someone has written a PowerShell extention for that specific vault. This is one of the promises and benfits of PowerShell; abstract all the mess of the world in to easy to use commands for a consistent experience.

There are [examples in the SecretManagement GitHub Repo](https://github.com/PowerShell/SecretManagement/tree/master/ExtensionModules) of extensions for the Windows credential manager and Azure Key Vault and I encourage you to have a play with these.

There is also an extension for [Lastpass](https://github.com/TylerLeonhardt/SecretManagement.LastPass), written by [Tyler Leonhardt](https://twitter.com/TylerLeonhardt/).

# Working with the Vault

Finally, just to bring things to a close here, there are some cmdlets in the vault extension modules that can be used to manage the configuration of the vault itself. We can wee these cmdlets in our example by looking at the commands in the SecretStore module:

```powershell
Get-Command -Module Microsoft.Powershell.SecretStore | select name

Name
----
Get-SecretStoreConfiguration
Reset-SecretStore
Set-SecretStoreConfiguration
Unlock-SecretStore
Update-SecretStorePassword
```

The configuration of the secret store can get viewed with `Get-SecretStoreConfiguration`:

```powershell
Get-SecretStoreConfiguration

      Scope PasswordRequired PasswordTimeout DoNotPrompt
      ----- ---------------- --------------- -----------
CurrentUser             True             960       False
```

The configuration above is the default for the SecretStore Microsoft are shipping, which is relatively secure by default. You can set the vault to not require a password, and you can change the Password timeout, both of which can be achieved with `Set-SecretStoreConfiguration`.

The last cmdlet I want to highlight is `Unlock-SecretStore`. There may be times where you want to run a Powershell script or command unattended and have it retrieve a secret from the secret store, but by default the vault requires a password and will prompt you to enter a password to access it. If you're trying to run something unattended, this isn't really going to work. In this scenario, you can use `Unlock-SecretStore` before interacting with the vault to pass in the value of the password for the vault. This will ensure the vault is unlocked for the rest of the configured timeout, allowing your script or automation job to work with secrets. The only catch here is you need to provide the password to access the vault, but you can't store that in the vault, so it's the catch 22. To provide the vaule password with `Unlock-SecretStore`, you'd likely need to revert to using something like an excrypted XML file, or if your automation is in a pipeline or CICD tool, you can potentially use a secure variable to be passed in at runtime to unlock the vault.

# Summary

All in all I think this is a great project, and I've always felt a bit messy when dealing with storing credentials that I need to use for scheduled tasks or scripts. I'm really looking forward to the GA release of the Secret Management module and hopefully watching vendors and the community rally around to get extension modules written for a wide range of secret stores and vaults.

Further resources / reading:

[SecretManagement Preview 3 Microsoft Blog](https://devblogs.microsoft.com/powershell/secretmanagement-preview-3/)

[SecretManagement Github Repository](https://github.com/PowerShell/SecretManagement)

[SecretStore Github Repository](https://github.com/PowerShell/SecretStore)