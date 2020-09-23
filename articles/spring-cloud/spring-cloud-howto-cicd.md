---
title: Azure 春季雲端的 CI/CD
description: Azure 春季雲端的 CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 40e674594c80a076fc9775fd4315aee938a43593
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888686"
---
# <a name="cicd-for-azure-spring-cloud"></a>Azure 春季雲端的 CI/CD

持續整合與持續傳遞工具可讓您快速地將更新部署至現有的應用程式，並使其具有極大的影響。 Azure DevOps 可協助您組織及控制這些重要工作。 目前，Azure 春季雲端未提供特定的 Azure DevOps 外掛程式。  不過，您可以使用 [Azure CLI](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops&preserve-view=true)工作，將您的春季雲端應用程式與 DevOps 整合。

本文說明如何搭配使用 Azure CLI 工作與 Azure 春季雲端來與 Azure DevOps 整合。

## <a name="create-an-azure-resource-manager-service-connection"></a>建立 Azure Resource Manager 服務連接

請閱讀 [這篇文章](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops&preserve-view=true) ，以瞭解如何建立 Azure DevOps 專案的 Azure Resource Manager 服務連接。 請務必選取您要用於 Azure 春季雲端服務實例的相同訂用帳戶。

## <a name="azure-cli-task-templates"></a>Azure CLI 工作範本
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>部署成品

您可以使用一系列的來建立和部署專案 `tasks` 。 此程式碼片段會定義變數、用來建立應用程式的 .NET Core 工作，以及用來部署 *.zip* 檔案的 Azure CLI 工作。

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --is-public true
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>部署成品

您可以使用一系列的來建立和部署專案 `tasks` 。 此程式碼片段會先定義用來建立應用程式的 Maven 工作，然後再以第二個工作（使用 Azure 春天雲端 Azure CLI 擴充功能）部署 JAR 檔案。

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>從來源部署

您可以直接部署至 Azure，而不需要個別的組建步驟。

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>下一步

* [快速入門：部署您的第一個 Azure Spring Cloud 應用程式](spring-cloud-quickstart.md)
