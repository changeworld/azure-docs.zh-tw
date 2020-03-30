---
title: 從時間點檢索鍵值對
titleSuffix: Azure App Configuration
description: 使用 Azure 應用配置中的時點快照檢索舊鍵值對
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523647"
---
# <a name="point-in-time-snapshot"></a>時間點快照集

Azure 應用配置維護對鍵值對所做的更改的記錄。 此記錄提供鍵值更改的時間表。 您可以重建任何鍵值的歷史記錄，並在過去七天內隨時提供其過去值。 使用此功能，您可以向後"時間旅行"並檢索舊鍵值。 例如，您可以恢復最近部署之前使用的配置設置，以便將應用程式回滾到以前的配置。

## <a name="key-value-retrieval"></a>擷取金鑰-值

可以使用 Azure PowerShell 檢索過去的鍵值。  使用`az appconfig revision list`添加適當的參數來檢索所需的值。  通過提供存儲名稱 （`--name {app-config-store-name}`） 或使用連接字串 （）`--connection-string {your-connection-string}`指定 Azure 應用配置實例。 通過指定特定的時間點 （）`--datetime`和指定要返回的最大項數 （）`--top`來限制輸出。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

檢索對鍵值的所有記錄更改。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

檢索鍵`environment`和標籤`test`的所有記錄更改以及`prod`。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

檢索分層鍵空間`environment:prod`中記錄的所有更改。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

在特定時間點檢索金鑰`color`記錄的所有更改。

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

檢索對鍵值的最後 10 個記錄的更改，並僅返回`key`的值`label`，`last-modified`和時間戳記。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立 ASP.NET Core Web 應用程式](./quickstart-aspnet-core-app.md)  
