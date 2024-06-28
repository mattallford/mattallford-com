---
title: "Azure Bicep Linting and Validation"
date: 2024-06-28
author: Matt Allford
draft: false
url: /2024/azure-bicep-linting-and-validation/
categories:
- '2024'
- June

tags:
- Azure
- Bicep
- IaC
- Infrastructure as Code
---

# Azure bicep: Zero to Hero Course

As a shameless plug, if you like the content you see here on Azure Bicep and are looking to further expand your skills, I've created an **Azure Bicep: Zero to Hero** course, which can be found [here](https://learn.mattallford.com/p/azure-bicep-from-zero-to-hero).

# Linting and Preflight Validation in Azure Bicep: Ensuring High-Quality Templates

As you start building out your Azure Bicep templates, it’s crucial to ensure that your code adheres to best practices and patterns. Two key techniques for achieving this are linting and preflight validation. These practices help you catch potential issues early in the development process, saving you time and preventing deployment failures.

## Linting: Catching Issues Early

Linting is a common practice in software development, acting as a static code analyzer that reviews your code to identify potential errors, stylistic issues, and areas for improvement. For Bicep templates, linting helps maintain high-quality code by flagging problems before they escalate.

### Why Lint Your Bicep Code?

Linting your Bicep code ensures:

- **Error Detection:** Identifies syntax errors and other issues that could cause deployment failures.
- **Code Consistency:** Enforces coding standards and best practices.
- **Improved Readability:** Helps maintain a clean and readable codebase, making it easier for you and your team to collaborate.

### Hands-On: Linting a Bicep File

Let’s walk through a practical example of linting a Bicep file. Here's a simple Bicep template to deploy a virtual network. If you'd like to follow along, go ahead and save this as vnet.bicep.

```bicep
param vNetName string
param location string = resourceGroup().location

resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetName
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
  }
}
```

We’ll use the `az bicep lint` command to check our template for issues.

```bash
az bicep lint --file ./vnet.bicep
```

Initially, if there are no errors, you won’t see any output from the lint. But if we introduce an issue in the Bicep file that the linter is configured to report on, and then run the lint command again, it will flag the issue allowing us to correct it.

Let's add a simple syntax error to a Bicep file. For instance, defining a variable, but then not using it, triggering the linter to flag the issue of "unused variables":

```bicep
param vNetName string
param location string = resourceGroup().location

var addressPrefix = '10.0.0.0/16'

resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetName
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/16'
      ]
    }
  }
}
```

As soon as you make this change in Visual Studio Code, even before saving the file, you'll notice a few things:

1. The name of the file in the explorer view in VC Code, and at the tab at the top, both change to yellow
2. The variable named addressPrefix becomes underlined in yellow, indicating a warning
3. The issue is highlighted in the problems tab in the output and terminal panel

[![](/post/images/azure-bicep-linting-and-validation-01.png)](/post/images/azure-bicep-linting-and-validation-01.png)


Now save the Bicep template and run the lint at the command line again:

```bash
az bicep lint --file ./vnet.bicep
```

This time you'll receive some output, identifying the issue at a warning level, with a link to the Microsoft Learn site to help further understand the issue being flagged by the linter.


`Warning no-unused-vars: Variable "addressPrefix" is declared but never used. [https://aka.ms/bicep/linter/no-unused-vars]`



**Tip:** You can use a configuration file named bicepconfig.json to control the behavior of the Bicep linter, both globally, and on a per-rule basis.


## Preflight Validation: Ensuring Deployment Success

Preflight validation is another powerful tool that helps ensure your Bicep templates will deploy successfully. This process involves asking Azure Resource Manager to review your template and flag any potential issues before actual deployment.

### Why Perform Preflight Validation?
Preflight validation helps you:

- **Identify Deployment Issues:** Detects configuration problems that might prevent successful deployment.
- **Validate Resource Configuration:** Ensures that all resource definitions and configurations are correct.
- **Save Time and Resources:** Avoids failed deployments and the need for subsequent fixes.

### Hands-On: Preflight Validation of a Bicep File

To perform preflight validation, you’ll need to log into Azure and use the validation command.

1. Log into Azure:

```
az login
```

Don't forget to use `az show` to confirm you are looking at the correct subscription, if you have access to multiple subscriptions.

2. Run the Validation Command (this assumes you already have a resource group available to deploy the virtual network to):
```
az deployment group validate --resource-group rg-vnet-demo --template-file ./vnet.bicep --parameters vNetName=vnet-001

```

You'll notice a few things:

1. Performing a validate also performs a lint, so you will have received a warning about the unused variable in the output
2. The validation succeeded
3. The resource wasn't actually deployed


#### Introducing an Error Not Caught by the Linter

Now, let's add an error that won't be caught by the linter but _would_ be caught by preflight validation. An easy example here is to change the VNET address prefix to something invalid, like I have in the exmaple below where I've set it to `/160` instead of `/16`.

Note that I've also removed the empty variable for now as well.

```bicep
param vNetName string
param location string = resourceGroup().location

resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetName
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        '10.0.0.0/160'
      ]
    }
  }
}
```

Let's perform a lint on the file manually at the command line:

```bash
az bicep lint --file ./vnet.bicep
```

The linter comes back with no errors or warnings at all, looking great! Let's try a preflight validation again:

```
az deployment group validate --resource-group rg-vnet-demo --template-file ./vnet.bicep --parameters vNetName=vnet-001

```

Whoa! This time we get an `InvalidTemplateDeployment` response. Sepcifically:

`InvalidAddressPrefixFormat ... vnet-001 is not formatted correctly. It should follow CIDR notation, for example 10.0.0.0/24.`

The linter didn't flag this as an error because it's not a syntax issue. However, when we perform preflight validation, Azure Resource Manager will check the validity of the properties and configuration, and flag it as an error. Now we can quickly fix the error before trying a real deployment.

# Conclusion

By incorporating linting and preflight validation into your Azure Bicep development workflow, you can significantly improve the quality and reliability of your templates. These practices help catch issues early, enforce best practices, and ensure successful deployments, ultimately saving you time and resources.