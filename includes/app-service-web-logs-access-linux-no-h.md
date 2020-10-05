---
title: Include 檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "84905611"
---
您可以存取從容器產生的主控台記錄。 請先在 Cloud Shell 中執行下列命令來開啟容器記錄：

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

開啟容器記錄後，請執行下列命令來查看記錄資料流：

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

如果您沒有立即看到主控台記錄，請在 30 秒後再查看。

> [!NOTE]
> 您也可以在瀏覽器中的 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 檢查記錄檔。

若要隨時停止記錄資料流，請輸入 `Ctrl`+`C`。
