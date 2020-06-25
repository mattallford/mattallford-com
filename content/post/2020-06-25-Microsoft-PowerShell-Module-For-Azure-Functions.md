---
author: Matt Allford
title: "Microsoft PowerShell Module for Azure Functions"
date: 2020-06-25T13:50:43+10:00
draft: false
type: post
url: /2020/microsoft-powershell-module-for-azure-functions/
categories:
- '2020'
- June
tags:
- Microsoft
- Azure
- Functions
- Serverless
- PowerShell
---


# Introduction

Microsoft recently released a PowerShell module named `Az.Functions`, providing cmdlets to manage the Azure Functions Service. According to the [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.Functions/1.0.0), version 1.0.0 was released on the 19th of May, followed by 1.0.1 on the 23rd of June (2 days ago).

`Az.Functions` is now included as part of the wider `Az` module, so if you install the entire `Az` PowerShell module, you'll automatically receive `Az.Functions`.

In this post, we'll take a 101 look at some of the cmdlets that are included in this initial release. We'll deploy a new function app, modify some of the settings using the `Update` cmdlets and then clean up by deleting it at the end.

If you prefer, I also did a short video clip of the content that is similar to this blog post, so if you prefer consuming video content please check it out below!

{{< youtube JPbyhFdclwM >}}

# Installing and Reviewing Az.Functions

As mentioned in the introduction, the module is now located on the PowerShell Gallery, so installation is a simple as firing up your PowerShell session and running one of the following.

If you just want the Az.Functions Module, run the following (note that it will also pull down Az.Accounts as that is a required module):

```powershell
Install-Module -Name Az.Functions
```

Or alternatively, run the following to install the entire Az PowerShell library:

```powershell
Install-Module -Name Az
```

Following the installation, let's take a look at the cmdlets that are provided in Az.Functions:

```PowerShell
PS C:\> Get-Command -Module Az.Functions

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Get-AzFunctionApp                                  1.0.1      Az.Functions
Function        Get-AzFunctionAppAvailableLocation                 1.0.1      Az.Functions
Function        Get-AzFunctionAppPlan                              1.0.1      Az.Functions
Function        Get-AzFunctionAppSetting                           1.0.1      Az.Functions
Function        New-AzFunctionApp                                  1.0.1      Az.Functions
Function        New-AzFunctionAppPlan                              1.0.1      Az.Functions
Function        Remove-AzFunctionApp                               1.0.1      Az.Functions
Function        Remove-AzFunctionAppPlan                           1.0.1      Az.Functions
Function        Remove-AzFunctionAppSetting                        1.0.1      Az.Functions
Function        Restart-AzFunctionApp                              1.0.1      Az.Functions
Function        Start-AzFunctionApp                                1.0.1      Az.Functions
Function        Stop-AzFunctionApp                                 1.0.1      Az.Functions
Function        Update-AzFunctionApp                               1.0.1      Az.Functions
Function        Update-AzFunctionAppPlan                           1.0.1      Az.Functions
Function        Update-AzFunctionAppSetting                        1.0.1      Az.Functions
```

There's a good range of cmdlets provided to Get, Remove and Update Functions Apps, as well as create new function apps.

# Deploy a New Function App

`Get-AzFunctionAppAvailableLocation` allows you to specify the type of function app you want to deploy, and the cmdlet will return a list of regions that support the function app you are deploying.

```PowerShell
PS C:\> Get-AzFunctionAppAvailableLocation -PlanType Consumption -OSType Windows

Name
----
Central US
North Europe
West Europe
Southeast Asia
East Asia
West US
East US
Japan West
Japan East
East US 2
North Central US
South Central US
Brazil South
Australia East
Australia Southeast
East Asia (Stage)
Central India
West India
South India
Canada Central
Canada East
West Central US
West US 2
UK West
UK South
East US 2 EUAP
Central US EUAP
Korea Central
France Central
Australia Central 2
Australia Central
South Africa North
Switzerland North
Germany West Central
Norway East
```

Let's go ahead and deploy a new function app directly from PowerShell. To save repatition of typing out the same names for resources, you can assign them to variables similar to the below. As per any resource in Azure, function apps need to be placed in to a resource group. The other pre-requisite of a function app is to have an Azure Storage Account, so we'll create variables for both of those items below.

```powershell
$ResourceGroupName = "poshfuncdemo"
$FunctionAppName = "mafunctionapp01"
$Location = "Australia East"
$storageAccountName = "poshfuncdemostg01"
```

Next, you need to log in to your Azure Account that has a valid subscription that the resources can be deployed to. That can be done with the following cmdlet. After running this, you will be asked to open a web browser to a particular URL and submit a code that will authenticate your PowerShell session.

```powershell
Connect-AzAccount
```

Next up, you'll need to deploy the resource group and storage account (assuming you aren't placing the function app in an existing resource group and attaching it to an existing storage account):
```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName -Location $location -SkuName Standard_LRS
```

Lastly, we can use `New-AzFunctionApp` to deploy the function app. If you take a look at the help for `New-AzFunctionApp` like in the block below, under the syntax you can see the mandatory parameters by observing the parameters not enclosed in **[ ]** brackets.


```powershell
PS C:\> get-help New-AzFunctionApp

NAME
    New-AzFunctionApp

SYNOPSIS
    Creates a function app.


SYNTAX
    New-AzFunctionApp -ResourceGroupName <String> -Name <String> -StorageAccountName <String> -Location
    <String> -Runtime <String> [-SubscriptionId <String>] [-ApplicationInsightsName <String>]
    [-ApplicationInsightsKey <String>] [-OSType <String>] [-RuntimeVersion <String>] [-FunctionsVersion
    <String>] [-DisableApplicationInsights] [-PassThru] [-Tag <Hashtable>] [-AppSetting <Hashtable>]
    [-IdentityType <ManagedServiceIdentityType>] [-IdentityID <String[]>] [-DefaultProfile <PSObject>]
    [-NoWait] [-AsJob] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>] [-HttpPipelinePrepend
    <SendAsyncStep[]>] [-Proxy <Uri>] [-ProxyCredential <PSCredential>] [-ProxyUseDefaultCredentials] [-WhatIf]
    [-Confirm] [<CommonParameters>]
```

We need a name for the function app, a resource group name, a location, a storage account and a runtime. Luckily (or was it good planning?!) we have all of these defined in variables, except for the runtime, which I'm going to specify as being PowerShell. You of course can select another support language runtime depending on the requirements of your function app.

```powershell
New-AzFunctionApp -Name $FunctionAppName -ResourceGroupName $resourceGroupName -Location $Location -StorageAccount $storageAccountName -Runtime PowerShell
```

We get some verbose output to let us know that some other selections were made for us as we didn't specify them, such as the Functions Version, OS Runtime and the runtime version.

```
VERBOSE: FunctionsVersion not specified. Setting default FunctionsVersion to '3'.
VERBOSE: OSType for PowerShell is 'Windows'.
VERBOSE: RuntimeVersion not specified. Setting default runtime version for PowerShell to '6.2'.
```

# Function App Configuration

Function App settings are a common configuration of function apps where name value key pairs can be stored, which are then exposed to the function at runtime as environment variables. You can use `Get-AzFunctionAppSetting` to view the current Application Settings, the below are the default directly after provisioning a new function app.

```powershell
PS C:\> Get-AzFunctionApp | Get-AzFunctionAppSetting

Name                           Value
----                           -----
WEBSITE_NODE_DEFAULT_VERSION   ~12
WEBSITE_CONTENTSHARE           mafunctionapp01
AzureWebJobsStorage            DefaultEndpointsProtocol=https;AccountName=poshfuncdemostg01;AccountKey=uvUukz0p…
APPINSIGHTS_INSTRUMENTATIONKEY e279e3f0-c55c-4c26-897f-1492a03a4376
WEBSITE_CONTENTAZUREFILECONNE… DefaultEndpointsProtocol=https;AccountName=poshfuncdemostg01;AccountKey=uvUukz0p…
FUNCTIONS_WORKER_RUNTIME       powershell
FUNCTIONS_EXTENSION_VERSION    ~3
AzureWebJobsDashboard          DefaultEndpointsProtocol=https;AccountName=poshfuncdemostg01;AccountKey=uvUukz0p…
```

There isn't a cmdlet to create a new application setting, but we can use `Update-AzFunctionAppSetting` to achieve this by specifying a name for the key value pair which isn't already in use. You can see the application setting named `NewAppSetting` is now stored in the configuration:

```powershell
PS C:\> Update-AzFunctionAppSetting -name $FunctionAppName -ResourceGroupName $resourceGroupName  -AppSetting @{"NewAppSetting" = "value123"}

Name                           Value
----                           -----
WEBSITE_NODE_DEFAULT_VERSION   ~12
AzureWebJobsStorage            DefaultEndpointsProtocol=https;AccountName=poshfuncdemostg01;AccountKey=uvUukz0p…
AzureWebJobsDashboard          DefaultEndpointsProtocol=https;AccountName=poshfuncdemostg01;AccountKey=uvUukz0p…
WEBSITE_CONTENTSHARE           mafunctionapp01
FUNCTIONS_WORKER_RUNTIME       powershell
NewAppSetting                  value123
WEBSITE_CONTENTAZUREFILECONNE… DefaultEndpointsProtocol=https;AccountName=poshfuncdemostg01;AccountKey=uvUukz0p…
FUNCTIONS_EXTENSION_VERSION    ~3
APPINSIGHTS_INSTRUMENTATIONKEY e279e3f0-c55c-4c26-897f-1492a03a4376```
```

You can update the value of an existing app setting by using the same cmdlet and modifying the value. Let's change the value of our application setting `NewAppSetting`:

```powershell
PS C:\> Update-AzFunctionAppSetting -name $FunctionAppName -ResourceGroupName $resourceGroupName  -AppSetting @{"NewAppSetting" = "MyNewValue"}

Name                           Value
----                           -----
WEBSITE_NODE_DEFAULT_VERSION   ~12
WEBSITE_CONTENTAZUREFILECONNE… DefaultEndpointsProtocol=https;AccountName=poshfuncdemostg01;AccountKey=uvUukz0p…
AzureWebJobsStorage            DefaultEndpointsProtocol=https;AccountName=poshfuncdemostg01;AccountKey=uvUukz0p…
APPINSIGHTS_INSTRUMENTATIONKEY e279e3f0-c55c-4c26-897f-1492a03a4376
WEBSITE_CONTENTSHARE           mafunctionapp01
NewAppSetting                  MyNewValue
FUNCTIONS_WORKER_RUNTIME       powershell
FUNCTIONS_EXTENSION_VERSION    ~3
AzureWebJobsDashboard          DefaultEndpointsProtocol=https;AccountName=poshfuncdemostg01;AccountKey=uvUukz0p…
```

A common use case is to configure a managed identity for the function app, allowing you to use Azure Role Based Access Control to delegate access for the function app to other Azure resources within your subscription. This can be achieved with `Update-AzFunctionApp` and setting the `-IdentityType` to `SystemAssigned`:

```powershell
Update-AzFunctionApp -Name $FunctionAppName -ResourceGroupName $ResourceGroupName -IdentityType SystemAssigned
```

Finally, the cmdlets for `Stop-AzFunctionApp`, `Start-AzFunctionApp` and `Restart-AzFunctionApp` do exactly what you think they will do! The stop and restart actions will prompt you by default to confirm as they are disruptive actions, you can of course use the `-Confirm:$false` parameter to supress the prompt if desired.

# Cleaning Up

To remove a function app you can use `Remove-AzFunctionApp`. You can either pipe to this command from `Get-AzFunctionApp`, or run `Remove-AzFunctionApp` standalone:

```powershell
PS C:\> Remove-AzFunctionApp -Name $FunctionAppName -ResourceGroupName $ResourceGroupName

Deleting function app
Delete function app 'mafunctionapp01'? This operation cannot be undone. Are you sure?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
```

If you are following along, you may also want to remove the resource group which will also remove the storage account we provisioned at the start of the article.

```powershell
PS C:\> Remove-AzResourceGroup -Name $ResourceGroupName

Confirm
Are you sure you want to remove resource group 'poshfuncdemo'
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
True
```

# Summary

There are currently several ways to deploy function apps in to Azure; Azure Portal, REST API, Azure Resource Manager Templates, using the Azure Functions core tools, third party IaC tools such as Terraform and probably a few others I'm forgetting. With that said, it is great to see Microsoft release the `Az.Functions` PowerShell module, brining the deployment and management capability to those who are familiar and comfortable with using PowerShell to deploy and manage infrastructure.

I think Microsoft have done a great job of the 1.0 release of the module and so far with my limited testing it is working well and I haven't had any unexpected behaviour.

If you followed along, you're now armed with the information you need to start using the `Az.Functions` PowerShell module to deploy and manage Azure Function Apps. Enjoy!