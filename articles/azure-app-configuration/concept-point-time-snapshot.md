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
ms.openlocfilehash: b706b5d5ec68daa10fd6eac237b7b7416764167b
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830099"
---
# <a name="point-in-time-snapshot"></a>時間點快照集

Azure 應用程式組態會維護對索引鍵/值所做之變更的記錄。 此記錄會提供索引鍵/值變更的時間軸。 您可以重建任何索引鍵/值的歷程記錄，並在金鑰歷程記錄期間的任何時間提供其過去的值 (7 天的免費層存放區，或在標準層存放區的30天內) 。 使用這項功能，您可以「準時」回溯並取出舊的索引鍵/值。 例如，您可以復原最近部署之前使用的設定，以便將應用程式回復到先前的設定。

## <a name="key-value-retrieval"></a>擷取金鑰-值

您可以使用 Azure 入口網站或 CLI 來取出過去的索引鍵/值。 在 Azure CLI 中，使用 `az appconfig revision list` ，新增適當的參數來抓取必要的值。  指定 Azure 應用程式組態實例，方法是提供 () 的存放區名稱， `--name <app-config-store-name>` 或使用 () 的連接字串 `--connection-string <your-connection-string>` 。 藉由指定特定時間點 (`--datetime`) ，並藉由指定要傳回 () 的最大專案數，來限制輸出 `--top` 。

如果您未在本機安裝 Azure CLI，您可以選擇使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

取得索引鍵/值的所有已記錄變更。

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name>.
```

取得金鑰 `environment` 和標籤和的所有已記錄 `test` 變更 `prod` 。

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

取得階層式金鑰空間中所有記錄的變更 `environment:prod` 。

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

在特定時間點取出金鑰的所有已記錄變更 `color` 。

```azurecli-interactive
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

將最後10個記錄的變更取出至您的索引鍵/值，並只傳回 `key` 、 `label` 和 `last_modified` 時間戳記的值。

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立 ASP.NET Core Web 應用程式](./quickstart-aspnet-core-app.md)  
