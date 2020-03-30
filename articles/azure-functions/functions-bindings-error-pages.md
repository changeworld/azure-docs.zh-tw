---
title: Azure 函數錯誤處理指南
description: 瞭解如何使用指向特定綁定錯誤的連結處理 Azure 函數中的錯誤。
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586184"
---
# <a name="azure-functions-error-handling"></a>Azure 函式錯誤處理

處理 Azure 函數中的錯誤對於避免資料丟失、錯過事件以及監視應用程式的運行狀況非常重要。

本文介紹了錯誤處理的一般策略以及特定于綁定的錯誤的連結。

## <a name="handling-errors"></a>處理錯誤

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>繫結錯誤碼

與 Azure 服務集成時，錯誤可能來自基礎服務的 API。 以下文章的 **"例外"和"返回代碼**"部分提供了與綁定特定錯誤有關的資訊：

+ [Azure 宇宙資料庫](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob 儲存體](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [事件中樞](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [物聯網中心](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [通知中樞](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [佇列存儲](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [服務匯流排](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [表存儲](functions-bindings-storage-table.md#exceptions-and-return-codes)
