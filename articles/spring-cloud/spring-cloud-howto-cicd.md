---
title: Azure 春雲的 CI/CD
description: Azure 春雲的 CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278520"
---
# <a name="cicd-for-azure-spring-cloud"></a>Azure 春雲的 CI/CD

持續集成和持續交付工具使開發人員能夠以最小的工作量和風險快速將更新部署到現有應用程式。 Azure DevOps 可説明您組織和控制這些關鍵作業。 目前，Azure 春雲不提供特定的 Azure DevOps 外掛程式。  但是，您可以使用[Azure CLI 任務](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)將春雲應用程式與 DevOps 集成。 本文將介紹如何使用 Azure 春雲的 Azure CLI 任務與 Azure DevOps 集成。

## <a name="create-an-azure-resource-manager-service-connection"></a>創建 Azure 資源管理器服務連接

閱讀[本文](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)，瞭解如何創建 Azure 資源管理器服務連接到 Azure DevOps 專案。 請務必選擇與 Azure Spring 雲服務實例相同的訂閱。

## <a name="azure-cli-task-templates"></a>Azure CLI 任務範本

### <a name="deploy-artifacts"></a>部署成品

您可以使用一系列`tasks`生成和部署專案。 此程式碼片段首先定義 Maven 任務以生成應用程式，然後是使用 Azure SpringCloud Azure CLI 副檔名部署 JAR 檔的第二個任務。

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

### <a name="deploy-from-source"></a>從源部署

無需單獨的生成步驟即可直接部署到 Azure。

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
