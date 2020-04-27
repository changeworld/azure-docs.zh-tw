---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/22/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ca5ef9cebfddb002cd62a9fb0fbbfdf444e0e754
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085890"
---
在 `myAppServicePlan` App Service 方案中建立 [Web 應用程式](../articles/app-service/containers/app-service-linux-intro.md)。 

在 Cloud Shell 中，您可以使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest) 命令。 在下列範,了中，使用全域唯一的應用程式名稱 (有效的字元為 `a-z`、`0-9` 和 `-`) 取代 `<app-name>`。 執行階段會設定為 `DOTNETCORE|LTS`，也就是 .NET Core 3.1。 若要查看所有支援的執行階段，請執行 [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest)。 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|LTS" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|LTS" --deployment-local-git
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的輸出：

<pre>
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
</pre>

您已在 Linux 容器中建立空的 Web 應用程式，並已啟用 Git 部署。

> [!NOTE]
> Git 遠端的 URL 會顯示在 `deploymentLocalGitUrl` 屬性中，其格式為 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`。 儲存此 URL，稍後您會用到此資訊。
>

目前，您必須執行下列額外的命令，才能正確地設定 .NET Core 版本 (將 `<app-name>` 取代為上一個步驟中的內容)：

```azurecli-interactive
# Bash
az webapp config set --resource-group myResourceGroup --name <app-name> --linux-fx-version "DOTNETCORE|3.1.0"
# PowerShell
az --% webapp config set --resource-group myResourceGroup --name <app-name> --linux-fx-version "DOTNETCORE|3.1.0"
```
