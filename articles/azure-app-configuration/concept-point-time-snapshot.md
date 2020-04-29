---
title: 從時間點取出索引鍵/值組
titleSuffix: Azure App Configuration
description: 使用 Azure 應用程式組態中的時間點快照集，抓取舊的機碼值組
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523647"
---
# <a name="point-in-time-snapshot"></a>時間點快照集

Azure 應用程式組態會維護對索引鍵/值組所做之變更的記錄。 此記錄會提供索引鍵/值變更的時間軸。 您可以重建任何索引鍵/值的歷程記錄，並在前七天內的任何時間提供其過去的值。 使用這項功能，您可以「準時」回溯並取出舊的索引鍵/值。 例如，您可以復原最近部署之前使用的設定，以便將應用程式回復到先前的設定。

## <a name="key-value-retrieval"></a>擷取金鑰-值

您可以使用 Azure PowerShell 來取出過去的索引鍵值。  使用`az appconfig revision list`，並新增適當的參數來抓取必要的值。  藉由提供存放區名稱（`--name {app-config-store-name}`）或使用連接字串（`--connection-string {your-connection-string}`），指定 Azure 應用程式組態實例。 指定特定時間點（`--datetime`），並指定要傳回的最大專案數（`--top`），以限制輸出。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

取得索引鍵/值的所有已記錄變更。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

取得金鑰`environment`和標籤`test`和`prod`的所有已記錄變更。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

取得階層式金鑰空間`environment:prod`中所有記錄的變更。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

在特定時間點取出金鑰`color`的所有已記錄變更。

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

將最後10個記錄的變更取出至您的索引鍵/值，並`key`只`label`傳回、 `last-modified`和時間戳記的值。

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立 ASP.NET Core Web 應用程式](./quickstart-aspnet-core-app.md)  
