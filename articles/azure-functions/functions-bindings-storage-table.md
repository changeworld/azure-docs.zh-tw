---
title: Azure Functions 的 Azure 資料表儲存體繫結
description: 了解如何在 Azure Functions 中使用 Azure 資料表繫結。
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4f2b890dc60cd50b5fcaefabe8d418268b738c20
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096720"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions 的 Azure 資料表儲存體繫結

Azure Functions 透過[觸發程式和](./functions-triggers-bindings.md)系結與[Azure 儲存體](../storage/index.yml)整合。 與資料表儲存體整合可讓您建立可讀取和寫入資料表儲存體資料的函數。

| 動作 | 類型 |
|---------|---------|
| 讀取函數中的資料表儲存體資料 | [輸入系結](./functions-bindings-storage-table-input.md) |
| 允許函數撰寫資料表儲存體資料 |[輸出系結](./functions-bindings-storage-table-output.md) |

## <a name="packages---functions-2x-and-higher"></a>封裝-函數2.x 和更新版本

[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 套件 3.x 版中提供資料表儲存體繫結。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub 存放庫中。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 套件 2.x 版中提供資料表儲存體繫結。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub 存放庫中。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>後續步驟

- [在函數執行時讀取資料表儲存體資料](./functions-bindings-storage-table-input.md)
- [從函數撰寫資料表儲存體資料](./functions-bindings-storage-table-output.md)
