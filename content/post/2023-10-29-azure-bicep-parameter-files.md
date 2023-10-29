---
title: "Azure Bicep Parameter Files"
date: 2023-10-29
author: Matt Allford
draft: false
url: /2023/azure-bicep-parameter-files/
categories:
- '2023'
- October

tags:
- Azure
- Bicep
- IaC
- Infrastructure as Code
---

# Azure bicep: Zero to Hero Course

As a shameless plug, if you like the content you see here on Azure Bicep and are looking to further expand your skills, I've create an **Azure Bicep: Zero to Hero** course, which can be found [here](https://learn.mattallford.com/p/azure-bicep-from-zero-to-hero).

If you prefer to consume video content, similar content that is in this blog post is in the video below. If you prefer to read, please skip past the video and read on!

{{< youtube AMOj5-puoGI >}}


# Introduction

Until now, if you've been authoring Azure Bicep templates to deploy and manage your Azure resources using Infrastructure as Code, and you use parameter files to define values for Bicep parameters, you've needed to use JSON based parameter files.

As I'm sure you can appreciate, JSON isn't enjoyable for humans to read or write, but now you can author parameter files using the native Bicep language!

This comes with several benefits, including but not limited to:

- Real time intellisense and validation as you create your parameter files
- Ability to use functions and expressions in the Bicep parameter file
- Ability to read in environment variables dynamically as parameter values in the parameter file

There will no doubt be more benefits and features to come in future as this continues to receive development.

The GitHub repository being used for examples in this blog post, and in the video above, can be found [here](https://github.com/mattallford/bicep-parameter-file).

> **_NOTE:_** The repository includes a `.bicepparam` file already as an example. If you want to follow along, just delete the exiting bicepparam file from the repository after you clone it down.

# Requirements

You will need to be using version 0.18.4 or newer of Bicep to take advantage of this functionality. Also, you'll want to make sure your Visual Studio Code extension is also running that same version, or newer.

# Manually Create Your First Bicep Parameter File

Bicep parameter files have an extension of `.bicepparam`, and at the time of writing, you need to associate your bicep parameter file with a specific bicep template. This allows for real-time validation against the parameters defined in the Bicep template. I can see examples in the future as to where this may be relaced, especially if concepts such as nesting paramter files or similar patterns get introduced.

I've cloned down the repository referenced above, and then in the root directory you can create a new `.bicepparam` file. I'll name mine `parameters.bicepparam`:

[![](/post/images/azure-bicep-parameter-files-01.png)](/post/images/azure-bicep-parameter-files-01.png)

You'll notice the icon in Visual Studio Code is the Bicep icon, but instead of being blue like a bicep template, it is purple, and enclosed in square brackets. This is the icon for a Bicep parameter file.

You'll also notice even though we've just created an empty file, there is a "problem" with the file that needs some attention. This is because at the time of writing, there's a requirement to specify which bicep file the parameter file is associated with. You do this with a `using` keyword, and providing the path to the bicep file:

`using 'deploy-environment.bicep'`

The Bicep parameter file is still showing a warning. If you now hover over the `deploy-environment.bicep` which is underlined with red, you'll see the issue now is there are parameter files declared in the Bicep file which don't have a default value, and aren't being declared in the parameter file.

To define a parameter in a bicepparam file, use the `param` keyword, followed by the name of the parameter, followed by an equals sign, followed by the value:

```
using 'deploy-environment.bicep'

param environmentCode = 'dev'
```

In my example, `dev` is a valid value for the environmentCode parameter, because in the Bicep file I am defining some allowed values on that parameter:

```
@description('Optional. Enter the code for the environment being deployed.')
@allowed([
  'dev'
  'test'
  'prod'
])
param environmentCode string = 'dev'
```

If I were to change the value to something other than one of the allowed values, the real time intellisense will let me know there is an issue with the value I have provided in the bicepparam file:

[![](/post/images/azure-bicep-parameter-files-02.png)](/post/images/azure-bicep-parameter-files-02.png)

Another thing you can do is reference one parameter as the value for another. If we expand the bicepparam file to define parameters and values for all of the parameters of our bicep file, notice below with one of the tags, I'm referencing the value of `environmentCode`, which itself is another parameter:

```
using 'deploy-environment.bicep'

param environmentCode = 'dev'
param location = 'australiaeast'
param tags = {
  environment: environmentCode
  deployedWith: 'IaC'
}
param deployStorage = false
```


# Generating Bicep Parameter Files

Manually creating a `.bicepparam` file like we did above is all well and good, but more often than not it will be more efficient and useful to generate the parameter file automatically from the bicep template in question. There are a couple of ways to achieve this.

> **_NOTE:_** It's up to you, but you may want to delete the existing `.bicepparam` file each time you follow along with the exercises!

## Generate With Visual Studio Code

If you right click on a Bicep file, in my example this is the `deploy-environment.bicep` file, there is an option to generate a parameters file:

[![](/post/images/azure-bicep-parameter-files-03.png)](/post/images/azure-bicep-parameter-files-03.png)

This option has been here for a while and used to create a JSON based parameter file, but what's new is that when you click on this now, you'll get asked to select the output format - either json or bicepparam.

After selecting bicepparam, you'll be asked if you want all parameters to be added to the bicepparam file, or just required parameters (therefore not defining those that have default values in the Bicep template in question). 

[![](/post/images/azure-bicep-parameter-files-04.png)](/post/images/azure-bicep-parameter-files-04.png)

[![](/post/images/azure-bicep-parameter-files-05.png)](/post/images/azure-bicep-parameter-files-05.png)

You'll now have a `.bicepparam` file generated automatically in the same directory as the Bicep template, with the same name as the Bicep template. The `using` statement in the param file will be linked to the Bicep file you generated the bicepparam file from, and either required parameters or all parameters will be defined, depending on which option you selected when generating them. If you chose to generate the bicepparam file will all parameters, those that have default values in the Bicep template will be filled out automatically with the default value in the bicepparam file.

## Generate With the Bicep CLI

You can also use the Bicep Command Line Interface (CLI) to generate a `.bicepparam` file.

> **_NOTE:_** The Azure CLI also contains a version of the Bicep CLI (az bicep ...), but as of the time of writing, using the Azure CLI to generate bicep parameter files does not work. It's probable this is resolved by the time you are reading this, as the fix is believed to be coming in the next release.

To use the Bicep CLI to generate a bicep parameter file, run the following command:

```
bicep generate-params .\deploy-environment.bicep --output-format bicepparam
```

Similar to when generating with Visual Studio Code, you can use the `--include-params` argument to determine if the bicepparm file should include all parameters, or just required ones:

```
bicep generate-params .\deploy-environment.bicep --output-format bicepparam --include-params all
```

## Decompiling JSON Parameter Files to Bicepparam

At the end of the day, Bicep is just a domain specific language which builds JSON files, which are then submitted to Azure Resource Manager. For a long time we've been able to take JSON based ARM templates and "decompile" them in to bicep templates, which provides a good starting point if you were already invested in ARM templates, to start moving across to Bicep. That same functionality exists where you can take a JSON based parameter file, and decompile it to a bicepparam file.

Consider you had a JSON based parameter file that looks like the below. You can copy and paste this in to a new file in the directory you've been working in, maybe name it `deploy-environment.parameters.json`:

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value": "australiaeast"
    },
    "deployStorage": {
      "value": false
    },
    "environmentCode": {
      "value": "dev"
    }
  }
}
```

### Visual Studio Code
You can use Visual Studio Code to decompile the JSON file in to a bicep param file:

- Right click on the JSON file and select **Decompile into Bicepparams**
- Optionally choose to link the bicepparam file to a bicep file
- The .bicepparam file will now be generated in the directory you are working in
- If you don't select the Bicep template to associate the bicepparam file with, the `using` keyword in the .bicepparam file won't have any value and you'll need to populate this manually


### Bicep CLI
You can also use the Bicep CLI to decompile the JSON file in to a bicep param file. Provide the path of the JSON file, and then using the `--bicep-file` argument, provide the path to the bicep file to associate the bicepparam file with.

```
bicep decompile-params .\deploy-environment.parameters.json --bicep-file .\deploy-environment.bicep
```


# Using Expressions

<intro>

It's possible to use an expression in a bicepparam file to calculate a parameter default value at deployment time.

Head back on over to the deploy-environment.bicep file, remove the variable named `storageAccountName`, and create a new parameter with the same name:
```
param storageAccountName string
```

Now over in the bicepparam file, we need to define the value for this new parameter. But this time, instead of using a hard coded string, try using an expression such as the following:

```
param storageAccountName = 'st${uniqueString('myuniquestring')}'
```

Your .bicepparam file might now look something like this:

```
using './deploy-environment.bicep'

param environmentCode = 'dev'
param location = 'australiaeast'
param tags = {}
param deployStorage = false
param storageAccountName = 'st${uniqueString('myuniquestring')}'
```

Additionally, it is possible to use a conditional statement here as well. Until now in the bicepparam file, the `deployStorage` parameter has been set to `false`. Let's imagine you want to moke it conditional based on the value of another parameter. 

That's easily done by using the same syntax we'd use in a .bicep file for specifying a condition:

```
param deployStorage = environmentCode == 'dev' ? false : true
```

In this example, if the value being provided for the `environmentCode` parameter is dev, then `deployStorage` will be set to false, otherwise the value will default to true.


# Deploying Bicep With Bicepparam Files

At this point in time, all our work has been in Visual Studio code, and we're yet to deploy the bicep template to Azure with our new .bicepparam file, so let's walk through a few ways of achieving that.

> **_NOTE:_** In my terminal, I ran`az login` to authenticate to Azure with the Azure CLI, and `Connect-AzAccount` to authenticate to Azure with the Azure PowerShell module.

## Azure CLI
This is going to look awfully familiar to you if you've used Azure CLI to deploy Bicep files with JSON based parameter files, which is a good thing! Then only thing that needs to be changed is to ensure you provide the path to the .bicepparam file with the `-p` argument.

In this example, I'm doing a deployment to the subscription scope, because my bicep template is deploying a resource group, followed by a storage account, and to deploy a resource group we need to scope the deployment to the subscription.

Try running the following command, assuming the names of the .bicep and .bicepparam files you are working with match the example below:

```
az deployment sub create -f .\deploy-environment.bicep -p .\deploy-environment.bicepparam -l australiaeast
```

If, like me, you left the value to `environmentCode` to `dev`, then due to the conditional logic for deploying a storage account, a storage account won't get deployed for the dev enviornment, so the result in Azure should simply be an empty resource group named `rg-dev-001`:

[![](/post/images/azure-bicep-parameter-files-06.png)](/post/images/azure-bicep-parameter-files-06.png)


## Azure PowerShell
Very similarly to Azure CLI, the Azure PowerShell module already knows how to deal with .bicepparam files, meaning it's a very similar command to always, just ensure you specify the .bicepparam file with the `-TemplateParameterFile` parameter:

```
New-AzSubscriptionDeployment -Location australiaeast -TemplateFile .\deploy-environment.bicep -TemplateParameterFile .\deploy-environment.bicepparam
```

You'll note again I'm doing a deployment to the subscription scope, but the same method applies if you were doing a deployment to the resource group scope.

## Visual Studio Code

The great thing about the Bicep extension in VS Code is we can do all this from VS Code itself as well.

This time I want to do something a little different, and let's create a new .bicepparam file, but this time for a production environment:

- Create a new bicepparam file named `deploy-environment.prod.bicepparam`
- Change the value of `param environmentCode` from `dev` to `prod` and save the file
- Right click on the bicep file > Deploy Bicep File
- Give the deployment a name (I just go with the default when playing around with a new feature)
- Confirm the subscription and location for the deployment
- Select a parameter file to use for the deployment, note you can choose a bicepparam file here, and in this case I'll select my newly created `deploy-environment.prod.bicepparam` file

In this deployment, the parameter value for `deployStorage` will be set to true, because we are doing a deployment to an environment other than dev. This means this deployment will create a new resource group for production, and also deploy a storage account:

[![](/post/images/azure-bicep-parameter-files-07.png)](/post/images/azure-bicep-parameter-files-07.png)


# Read Environment Variables in Bicepparam Files

One great feature the team built in to .bicepparam files, is the ability to read in environment variable values, and inject those as the values of bicep parameters at deployment time. This opens up some great use cases when deploying Bicep templates from CI/CD pipelines, allowing you to make your .bicepparam files more generic and flexible, and setting environment variables on the agent doing the deployment.

To read environment variables in a bicep param file, you use the `readEnvironmentVariable` expression. For example, if you wanted to read in the value for the `environmentCode` parameter from an environment variable on the machine called `envCode`, you'd use the following syntax in the .bicepparam file:


```
param environmentCode = readEnvironmentVariable('envCode')
```

# Wrap-Up
It's clear that the introduction of .bicepparam files marks a significant enhancement in the Azure Bicep ecosystem. These native Bicep parameter files not only streamline the authoring experience but also expand the possibilities for how we manage and deploy infrastructure configurations. By leveraging real-time IntelliSense, utilizing expressions, and dynamically reading environment variables, we can achieve a more efficient, error-resistant, and simplified IaC practice.

The ability to generate, decompile, and deploy using .bicepparam files, whether through Visual Studio Code, Azure PowerShell, or the Bicep CLI, offers versatility to suit various workflows. 