---
title: 從時間點取得索引鍵/值組
titleSuffix: Azure App Configuration
description: 使用 Azure 應用程式組態中的時間點快照集取出舊的索引鍵/值組，其會維護索引鍵/值變更的記錄。
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 23f613584638026a2b371849c7d1014b3bb1f136
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073065"
---
# <a name="point-in-time-snapshot"></a>時間點快照集

Azure 應用程式組態會維護對索引鍵/值所做之變更的記錄。 此記錄提供索引鍵/值變更的時間軸。 您可以重建任何索引鍵/值的歷程記錄，並在金鑰歷程記錄期間內的任何時間都提供其過去的值 (適用于免費層商店的7天，或適用于標準層商店的30天) 。 使用這項功能時，您可以「時間移動」並取出舊的索引鍵/值。 例如，您可以復原最近部署之前所使用的設定，以便將應用程式回復為先前的設定。

## <a name="key-value-retrieval"></a>擷取金鑰-值

您可以使用 Azure 入口網站或 CLI 來取出過去的索引鍵/值。 在 Azure CLI 中， `az appconfig revision list` 請使用，並新增適當的參數來取得必要的值。  藉由提供存放區名稱 (`--name <app-config-store-name>`) 或使用 () 的連接字串，來指定 Azure 應用程式組態實例 `--connection-string <your-connection-string>` 。 藉由指定特定的時間點 (`--datetime`) ，以及指定要傳回 () 的最大專案數，來限制輸出 `--top` 。

如果您沒有 Azure CLI 安裝在本機，您可以選擇性地使用 [Azure Cloud Shell](../cloud-shell/overview.md)。

取得索引鍵/值的所有已記錄變更。

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

取得索引鍵 `environment` 和標籤和標籤的所有已記錄變更 `test` `prod` 。

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

取出階層式索引鍵空間中所有記錄的變更 `environment:prod` 。

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

在特定時間點捕獲金鑰的所有已記錄變更 `color` 。

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

將最後10筆記錄的變更取出至您的索引鍵/值，並僅傳回 `key` 、 `label` 和 `last_modified` 時間戳記的值。

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立 ASP.NET Core Web 應用程式](./quickstart-aspnet-core-app.md)