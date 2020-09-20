---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905862"
---
若要存取 App Service 中應用程式程式碼內部產生的主控台記錄，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令，以開啟診斷記錄功能：

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

`--level` 的可能值為：`Error`、`Warning`、`Info` 和 `Verbose`。 後續的每個層級都包含上一個層級。 例如：`Error` 只包含錯誤訊息，而 `Verbose` 包含所有訊息。

開啟診斷記錄後，請執行下列命令來查看記錄資料流：

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

如果您沒有立即看到主控台記錄，請在 30 秒後再查看。

> [!NOTE]
> 您也可以在瀏覽器中的 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 檢查記錄檔。

若要隨時停止記錄資料流，請輸入 `Ctrl`+`C`。
