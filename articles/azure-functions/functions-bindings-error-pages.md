---
title: Azure Functions 錯誤處理和重試指引
description: 瞭解如何使用特定系結錯誤的連結，處理 Azure Functions 中的錯誤和重試事件。
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: e52babd5b0612b4590f1422a941b25ba30c3f169
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284449"
---
# <a name="azure-functions-error-handling-and-retries"></a>Azure Functions 錯誤處理和重試次數

處理 Azure Functions 中的錯誤對於避免遺失資料、遺失事件，以及監視應用程式的健全狀況而言很重要。

本文描述錯誤處理的一般策略以及系結特定錯誤的連結。

## <a name="handling-errors"></a>處理錯誤

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## <a name="binding-error-codes"></a>繫結錯誤碼

與 Azure 服務整合時，錯誤可能源自于基礎服務的 Api。 與系結特定錯誤相關的資訊可在下列文章的 **例外狀況和傳回碼** 一節中取得：

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob 儲存體](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [事件中樞](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT 中樞](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [通知中樞](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [佇列儲存體](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [服務匯流排](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [表格儲存體](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
