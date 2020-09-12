---
title: 回應 Azure 媒體服務事件 | Microsoft Docs
description: 本文說明如何使用 Azure 事件方格來訂閱媒體服務事件。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 4ef6a920e9334c6e98b18d1db1abf39136c6f4e2
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289147"
---
# <a name="handling-event-grid-events"></a>處理事件方格事件

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

媒體服務事件可讓應用程式利用現代無伺服器架構，針對不同事件做出反應，例如，工作狀態變更事件。 它不需要複雜的程式碼或昂貴且無效率的輪詢服務來執行此動作。 而是改為透過 [Azure 事件格線](https://azure.microsoft.com/services/event-grid/)，將事件推送給事件處理常式，例如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)，甚至推送到您自己的Webhook，而且只有使用時，才須支付相關費用。 如需價格的資訊，請參閱[事件格線價格](https://azure.microsoft.com/pricing/details/event-grid/)。

媒體服務事件的可用性會繫結至事件格線[可用性](../../event-grid/overview.md)，並且將在其他區域中變成可用狀態，就像事件格線所做的一樣。  

## <a name="media-services-events-and-schemas"></a>媒體服務事件和架構

Event Grid 使用[事件訂閱](../../event-grid/concepts.md#event-subscriptions)將事件訊息路由至訂閱者。 媒體事件包含了回應資料變更時所需的一切資訊。 因為 eventType 屬性開頭為 “Microsoft.Media”，所以您可以藉此識別出媒體服務事件。

如需詳細資訊，請參閱[媒體服務事件結構描述](media-services-event-schemas.md)。

## <a name="practices-for-consuming-events"></a>消費事件做法

處理媒體服務事件的應用程式，應該遵循幾個建議做法：

* 由於可設定多個訂用帳戶以將事件路由至相同的事件處理常式，因此重要的是，不要假設事件來自於特定來源，而要檢查訊息主題以確定其來自預期的儲存體帳戶。
* 同樣地，檢查 eventType 也是必須進行的步驟之一，而且不要假設您收到的所有事件都是您預期的類型。
* 請忽略您不了解的欄位。  此做法將有助於保持未來可能新增功能的彈性。
* 使用 "subject" 前置詞和後置詞相符，將事件限制為特定的事件。

> [!NOTE]
> 事件會受限於事件方格 [服務等級協定 (SLA) ](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/)。 如果您想要使用 Api 取得事件通知，請參閱如何使用 [.NET sdk](https://github.com/Azure-Samples/media-services-v3-dotnet) 或 [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java)來取用事件的範例。

## <a name="next-steps"></a>接下來的步驟

* [監視事件-入口網站](monitor-events-portal-how-to.md)
* [監視事件 - CLI](job-state-events-cli-how-to.md)
