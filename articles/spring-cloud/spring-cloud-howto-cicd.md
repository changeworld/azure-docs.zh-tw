---
title: 適用于 Azure 春季雲端的 CI/CD
description: 適用于 Azure 春季雲端的 CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d31be45f5cf608b7dfd727741de68544574d7f8a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496452"
---
# <a name="cicd-for-azure-spring-cloud"></a>適用于 Azure 春季雲端的 CI/CD

持續整合與持續傳遞工具可讓開發人員以最少的方式和風險，快速地將更新部署至現有的應用程式。 Azure DevOps 可協助您組織及控制這些金鑰作業。 Azure 春季雲端目前未提供特定的 Azure DevOps 外掛程式。  不過，您可以使用[Azure CLI](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)工作，將您的春天雲端應用程式與 DevOps 整合。 本文將說明如何使用 Azure 春季雲端的 Azure CLI 工作，與 Azure DevOps 整合。

## <a name="create-an-azure-resource-manager-service-connection"></a>建立 Azure Resource Manager 服務連線

閱讀[這篇文章](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)，瞭解如何建立 Azure DevOps 專案的 Azure Resource Manager 服務連接。 請務必選取您用於 Azure 春季雲端服務實例的相同訂用帳戶。

## <a name="azure-cli-task-templates"></a>Azure CLI 工作範本

### <a name="deploy-artifacts"></a>部署成品

您可以使用一系列的來建立及部署專案 `tasks` 。 此程式碼片段會先定義用來建立應用程式的 Maven 工作，接著會使用 Azure 春季 Cloud Azure CLI 擴充功能來部署 JAR 檔案的第二個工作。

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

您可以直接部署到 Azure，而不需要個別的組建步驟。

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
