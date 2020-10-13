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
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822783"
---
您可以存取從容器產生的主控台記錄。

請先執行下列命令來開啟容器記錄：

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

以適合您 Web 應用程式的名稱取代 `<app-name>` 和 `<resource-group-name>`。

開啟容器記錄後，請執行下列命令來查看記錄資料流：

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

如果您沒有立即看到主控台記錄，請在 30 秒後再查看。

若要隨時停止記錄資料流，請輸入 **Ctrl**+**C**。

您也可以在瀏覽器中的 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 檢查記錄檔。
