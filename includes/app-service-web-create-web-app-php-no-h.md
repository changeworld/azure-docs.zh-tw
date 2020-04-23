---
title: 包含檔案
description: 包含檔案
services: app-service
author: msangapu-msft
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 14b84c27140c7aebf83684d7c80dfbae5713850b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085184"
---
在 [ App Service 方案中建立 ](../articles/app-service/containers/app-service-linux-intro.md)Web 應用程式`myAppServicePlan`。 

在 Cloud Shell 中，您可以使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 命令。 在下列範,了中，使用全域唯一的應用程式名稱 (有效的字元為 `<app-name>`、`a-z` 和 `0-9`) 取代 `-`。 執行階段設定為 `PHP|7.3`。 若要查看所有支援的執行階段，請執行 [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)。 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的輸出：

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

您已建立空的新 Web 應用程式，其中已啟用 Git 部署。

> [!NOTE]
> Git 遠端的 URL 會顯示在 `deploymentLocalGitUrl` 屬性中，其格式為 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`。 儲存此 URL，稍後您會用到此資訊。
>
