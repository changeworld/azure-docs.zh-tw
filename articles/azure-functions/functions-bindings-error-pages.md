---
title: Azure Functions 錯誤處理指引
description: 瞭解如何在 Azure Functions 中處理錯誤，並連結至特定的系結錯誤。
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77586184"
---
# <a name="azure-functions-error-handling"></a>Azure 函式錯誤處理

在 Azure Functions 中處理錯誤，是為了避免資料遺失、遺漏事件，以及監視應用程式的健全狀況。

本文說明錯誤處理的一般策略，以及系結特定錯誤的連結。

## <a name="handling-errors"></a>處理錯誤

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>繫結錯誤碼

與 Azure 服務整合時，錯誤可能來自基礎服務的 Api。 有關系結特定錯誤的資訊可在下列文章的**例外狀況和傳回碼**一節中取得：

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob 儲存體](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [事件中樞](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT 中樞](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [通知中樞](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [佇列儲存體](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [服務匯流排](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [資料表儲存體](functions-bindings-storage-table.md#exceptions-and-return-codes)
