---
title: Azure 佇列儲存體參考
description: 尋找 Azure 佇列儲存體 API 參考、讀我檔案和用戶端程式庫套件。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/10/2020
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: ripohane
ms.openlocfilehash: 5544a6985ca357688b2585fa29c6dc63546c3e8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90014951"
---
# <a name="azure-queue-storage-reference"></a>Azure 佇列儲存體參考

尋找 Azure 佇列儲存體的 API 參考、程式庫套件、讀我檔案，以及開始使用的文章。

## <a name="net-client-libraries"></a>.NET 用戶端程式庫

下表列出 Azure 佇列儲存體 .NET Api 的參考與範例檔。

|  版本  | 參考文件 | Package | 快速入門 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [Azure。佇列命名空間](/dotnet/api/azure.storage.queues) | [套件 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/) | [快速入門：適用於 .NET 的 Azure 佇列儲存體用戶端程式庫 v12](/azure/storage/queues/storage-quickstart-queues-dotnet) |
| 11. x | [Node.js 命名空間](/dotnet/api/microsoft.azure.storage.queue) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/) | [快速入門：使用適用於 .NET 的 Azure 儲存體 SDK v11 來管理佇列](/azure/storage/queues/storage-quickstart-queues-dotnet-legacy) |

### <a name="storage-management"></a>儲存體管理

下表列出 Azure 儲存體管理 .NET Api 的參考檔。

|  版本  | 參考文件 | Package |
| :-------: | ----------------------- | ------- |
| 16. x | [Microsoft.Azure.Management.Storage](/dotnet/api/microsoft.azure.management.storage) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) |

### <a name="data-movement"></a>資料移動

下表列出 Azure 儲存體資料移動 .NET Api 的參考檔。

|  版本  | 參考文件 | Package |
| :-------: | ----------------------- | ------- |
| 1.x | [資料移動](/dotnet/api/microsoft.azure.storage.datamovement) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/) |

## <a name="java-client-libraries"></a>JAVA 用戶端程式庫

下表列出 Azure 佇列儲存體 JAVA Api 的參考與範例檔。

|  版本  | 參考文件 | Package | 快速入門 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [適用于 JAVA 的 Azure 儲存體佇列用戶端程式庫](/java/api/overview/azure/storage-queue-readme) | [套件 (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) \(英文\) | [快速入門：適用於 Java 的 Azure 佇列儲存體用戶端程式庫 v12](/azure/storage/queues/storage-quickstart-queues-java) |
| 8.x | [.com. azure。](/java/api/com.microsoft.azure.storage.queue) | [套件 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) \(英文\) | [快速入門：使用 JAVA v8 SDK 來管理 blob](/azure/storage/blobs/storage-quickstart-blobs-java-legacy) |

### <a name="storage-management"></a>儲存體管理

下表列出 Azure 儲存體管理 JAVA Api 的參考檔。

|  版本  | 參考文件 | Package |
| :-------: | ----------------------- | ------- |
| 0.9. x | [.com. azure. 管理儲存體](/java/api/overview/azure/storage/management) | [套件 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-svc-mgmt-storage) \(英文\) |

## <a name="python-client-libraries"></a>Python 用戶端程式庫

下表列出 Azure 佇列儲存體 Python Api 的參考與範例檔。

|  版本  | 參考文件 | Package | 快速入門 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [Azure 儲存體適用于 Python 的用戶端程式庫 v12](/azure/developer/python/sdk/storage/overview) | [封裝 (PyPI) ](https://pypi.org/project/azure-storage-queue/) | [快速入門：適用於 Python 的 Azure 佇列儲存體用戶端程式庫 v12](/azure/storage/queues/storage-quickstart-queues-python) |
| 2.x | [適用于 Python 的 Azure 儲存體用戶端程式庫 v2](/azure/developer/python/sdk/storage/overview?view=storage-py-v2&preserve-view=true) | [封裝 (PyPI) ](https://pypi.org/project/azure-storage-queue/2.1.0/) | [如何從 Python 使用 Azure 佇列儲存體 v2.1](/azure/storage/queues/storage-python-how-to-use-queue-storage) |

## <a name="javascript-client-libraries"></a>JavaScript 用戶端程式庫

下表列出 Azure 佇列儲存體 JavaScript Api 的參考與範例檔。

|  版本  | 參考文件 | Package | 快速入門 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [適用于 JavaScript 的 Azure 儲存體佇列用戶端程式庫](/javascript/api/overview/azure/storage-queue-readme) | [套件 (npm)](https://www.npmjs.com/package/@azure/storage-queue) | [快速入門：適用於 JavaScript 的 Azure 佇列儲存體用戶端程式庫 v12](/azure/storage/queues/storage-quickstart-queues-nodejs) |
| 10.x | [@azure/storage-queue](/javascript/api/@azure/storage-queue/?view=azure-node-legacy&preserve-view=true) | [套件 (npm)](https://www.npmjs.com/package/@azure/storage-queue/v/10.3.0) | [使用 Azure 佇列服務來建立和刪除 Node.js的佇列 ](/azure/storage/queues/storage-nodejs-how-to-use-queues) |

## <a name="rest-apis"></a>REST API

下表列出 Azure 佇列儲存體 REST Api 的參考與範例檔。

| 參考文件 | 概觀 |
| ----------------------- | -------- |
| [佇列服務 REST API](/rest/api/storageservices/queue-service-rest-api) | [佇列服務概念](/rest/api/storageservices/queue-service-concepts) |

### <a name="other-rest-reference"></a>其他 REST 參考

- [Azure 儲存體匯入-匯出 REST API ](/rest/api/storageimportexport/) 可協助您管理匯入/匯出作業，以將資料傳入或傳出 Blob 儲存體。

## <a name="other-languages-and-platforms"></a>其他語言和平臺

下列清單包含其他程式設計語言和平臺程式庫的連結。

- [C++](https://azure.github.io/azure-storage-cpp)
- [Ruby](https://azure.github.io/azure-storage-ruby)
- [PHP](https://azure.github.io/azure-storage-php/)
- [iOS](https://azure.github.io/azure-storage-ios/)
- [Android](https://azure.github.io/azure-storage-android)

## <a name="powershell"></a>PowerShell

下表包含參考內容最新版本的連結。

| 版本 | 平台 |
| ------- | -------- |
|  4.x  | [PowerShell](/powershell/module/az.storage/?view=azps-4.6.1&preserve-view=true) |
|  3.x  | [PowerShell](/powershell/module/az.storage/?view=azps-3.8.0&preserve-view=true) |
|  2.x  | [PowerShell](/powershell/module/az.storage/?view=azps-2.8.0&preserve-view=true) |

## <a name="azure-cli"></a>Azure CLI

- [Azure CLI](/cli/azure/storage)
