---
title: "Azure Bicep Parameter Files"
date: 2023-07-08
author: Matt Allford
draft: false
url: /2023/azure-bicep-parameter-files/
categories:
- '2023'
- July

tags:
- Azure
- Bicep
- IaC
- Infrastructure as Code
---

If you prefer to consume video content, similar content that is in this blog post is in the video below. If you prefer to read, please skip past the video and read on!

{{< youtube AMOj5-puoGI >}}

# Introduction

Until now, if you've been authoring Azure Bicep templates to deploy and manage your Azure resources using Infrastructure as Code, and you use parameter files to define values for Bicep parameters, you've needed to use JSON based parameter files.

As I'm sure you can appreciate, JSON isn't enjoyable for humans to read and write, so thankfully as of now you are able to author parameter files using the native Bicep language!

This comes with a number of benefits, including but not limited to:

- Real time intellisense and validation as you create your parameter files
- Ability to use functions and expressions in the Bicep parameter file
- Ability to read in environment variables dynamically as parameter values in the parameter file

And there are many more benefits to come in future as this gets further developed.

The GitHub repository being used for examples in this blog post, and in the video above, can be found [here](https://github.com/mattallford/bicep-parameter-file).

> **_NOTE:_** The repository includes a `.bicepparam` file already as an example. If you want to follow along, just delete the exiting bicepparam file from the repository after you clone it down.

# Requirements

You will need to be using version 0.18.4 or newer of Bicep to take advantage of the new functionality. Also, you'll want to make sure your Visual Studio Code extension is also running that same version, or newer.

# Manually Create Your First Bicep Parameter File

Bicep parameter files have an extension of `.bicepparam`, and at the time of writing, you need to associate your bicep parameter file with a specific bicep template. This allows for real-time validation against the parameters defined in the Bicep template. I can see examples in the future as to where this may be relaced, especially if concepts such as nesting paramter files or similar patterns get introduced.

I've cloned down the repository referenced below, and then in the root directory you can create a new `.bicepparam` file. I'll name mine `parameters.bicepparam`:

<image>

You'll notice the icon in Visual Studio Code is the Bicep icon, but instead of being blue like a bicep template, it is purple, and enclosed in square brackets. This is the icon for a Bicep parameter file.

You'll also notice that even though we've just created an empty file, there is a "problem" with the file that needs some attention. This is because at the time of writing, there's a requirement to specify which bicep file the parameter file is associated with. You do this with a `using` keyword, and providing the path to the bicep file:

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

<image>

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

Manually creating a `.bicepparam` file like we did above is all well and good, but more often than not it will be more efficient and useful to generate the parameter file automatically, from the bicep template in question. There are a few ways to do this, which we'll explore below.

> **_NOTE:_** It's up to you, but you may want to delete the existing `.bicepparam` file each time you follow along with the exercises!

## Generate With Visual Studio Code

If you right click on a Bicep file, in my example this is the `deploy-environment.bicep` file, there is an option to generate a parameters file:

<image>

This option has been here for a while and used to create a JSON based parameter file, but what's new is that when you click on this now, you'll get asked to select the output format - either json or bicepparam.

After selecting bicepparam, you'll be asked if you want all parameters to be added to the bicepparam file, or just required parameters (therefore not defining those that have default values in the Bicep template in question). 

<image>

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


```

### Visual Stuio Code
You can use Visual Studio Code to decompile the JSON file in to a bicep param file:

- Right click on the JSON file and select **Decompile into Bicepparams**
- Select parameter file (bug, do they mean select bicep template?).
- The .bicepparam file will now be generated in the directory you are working in
- If you don't select the Bicep template to associate the bicepparam file with, the `using` keyword in the .bicepparam file won't have any value and you'll need to populate this manually


### Bicep CLI
You can also use the Bicep CLI to decompile the JSON file in to a bicep param file. Provide the path of the JSON file, and then using the `--bicep-file` argument, provide the path to the bicep file to associate the bicepparam file with.

```
bicep decompile-params .\deploy-environment.parameters.json --bicep-file .\deploy-environment.bicep
```


# Using Functions and Expressions

Create new parameter in the Bicep file, storageAccountName

Create new param in parameter file:
param storageAccountName = 'st${uniqueString('myuniquestring)}'

param deployStorage = environmentCode == 'dev' ? false : true

# Deploying Bicep With Bicepparam Files

## Azure CLI

## Azure PowerShell

## Visual Studio Code

# Read Environment Variables in Bicepparam Files