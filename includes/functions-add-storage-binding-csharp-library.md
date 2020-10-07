---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "78190905"
---
在 C# 類別庫專案中，繫結會被定義為函式方法上的繫結屬性。 接著，系統會根據這些屬性自動產生函式所需的 function.json  檔案。

開啟 *HttpExample.cs* 專案檔，然後將下列參數新增至 `Run` 方法定義：

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg` 參數是 `ICollector<T>` 類型，其代表會在函式完成時寫入輸出繫結的訊息集合。 在此情況下，輸出是名為 `outqueue` 的儲存體佇列。 儲存體帳戶的連接字串是由 `StorageAccountAttribute` 設定。 此屬性表示包含儲存體帳戶連接字串的設定，並可以被套用至類別、方法或參數層級。 在此情況下，您可以省略 `StorageAccountAttribute`，因為您已經正在使用預設的儲存體帳戶。

Run 方法定義現在應該如下所示︰  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
