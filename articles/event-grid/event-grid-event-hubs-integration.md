---
title: 教學課程：將事件中樞資料傳送至資料倉儲 - 事件方格
description: 說明如何透過 Azure Functions 和事件方格觸發程序，將事件中樞擷取的資料儲存到 Azure Synapse Analytics 中。
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7b0e471e32650490e1896bb6ea171c8223b21378
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854711"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>教學課程：將巨量資料串流處理至資料倉儲
Azure [事件方格](overview.md)是一項智慧型事件路由服務，可讓您對應用程式和服務發出的通知或事件做出回應。 例如，其可以觸發 Azure 函式來處理已擷取至 Blob 儲存體或 Data Lake Storage 的事件中樞資料。 此[範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)會示範如何使用事件方格和 Azure Functions，將已擷取的事件中樞資料從 Blob 儲存體遷移至 Azure Synapse Analytics，特別是專用的 SQL 集區。

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>後續步驟

* 若要了解 Azure 傳訊服務的差異，請參閱[在傳遞訊息的 Azure 服務之間做選擇](compare-messaging-services.md)。
* 如需 Event Grid 的簡介，請參閱[關於 Event Grid](overview.md)。
* 如需事件中樞擷取的簡介，請參閱[使用 Azure 入口網站啟用事件中樞擷取](../event-hubs/event-hubs-capture-enable-through-portal.md)。
* 如需設定及執行範例的詳細資訊，請參閱[事件中樞擷取和 Event Grid 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)。
