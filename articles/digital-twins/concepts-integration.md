---
title: 與其他服務整合
titleSuffix: Azure Digital Twins
description: 瞭解部署 Azure 數位 Twins 時的輸入和輸出需求。
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 7e360c158c7887109684d13f774cbbda1813373e
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91729129"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>整合 Azure 數位 Twins 與其他服務

Azure 數位 Twins 通常會與其他服務搭配使用。 使用 [**事件路由**](concepts-route-events.md)，Azure 數位 Twins 會接收來自上游服務（例如 [IoT 中樞](../iot-hub/about-iot-hub.md) 或 [Logic Apps](../logic-apps/logic-apps-overview.md)）的資料，這些服務是用來傳遞遙測和通知。 

Azure 數位 Twins 也可以將資料路由傳送至下游服務，例如 [Azure 地圖服務](../azure-maps/about-azure-maps.md) 和 [時間序列深入](../time-series-insights/time-series-insights-update-overview.md)解析，以便進行儲存體、工作流程整合、分析等等。 

## <a name="data-ingress"></a>資料輸入

Azure 數位 Twins 可與任何服務中的資料和事件（[IoT 中樞](../iot-hub/about-iot-hub.md)、 [Logic Apps](../logic-apps/logic-apps-overview.md)、您自己的自訂服務等等）進行驅動。 這可讓您從環境中的實體裝置收集遙測資料，並使用雲端中的 Azure 數位 Twins 圖形來處理此資料。

Azure 數位 Twins 沒有內建的 IoT 中樞。 您可以使用目前在生產環境中擁有的現有 IoT 中樞，或部署新的 IoT 中樞。 這可讓您完整存取 IoT 中樞的所有裝置管理功能。

若要將任何來源的資料內嵌至 Azure 數位 Twins，請使用 [**azure 函數**](../azure-functions/functions-overview.md)。 在 how [*to：從 IoT 中樞內嵌遙測*](how-to-ingest-iot-hub-data.md)，或在 Azure 數位 Twins [*教學課程：連接端對端解決方案*](tutorial-end-to-end.md)中自行試用，以深入瞭解這種模式。 

您也可以瞭解如何將 Azure 數位 Twins 連接到 [*如何：與 Logic Apps 整合*](how-to-integrate-logic-apps.md)的 Logic Apps 觸發程式。

## <a name="data-egress-services"></a>資料輸出服務

Azure 數位 Twins 可將資料傳送至連線的 **端點**。 支援的端點可以是：
* [事件中樞](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)

端點會使用管理 Api 或 Azure 入口網站連接至 Azure 數位 Twins。 深入瞭解如何在 how [*to：管理端點和路由*](how-to-manage-routes-apis-cli.md)中，將端點附加至 Azure 數位 Twins。

還有許多其他服務，您可能會想要最終將資料導向，例如 [Azure 儲存體](../storage/common/storage-introduction.md)、 [Azure 地圖服務](../azure-maps/about-azure-maps.md)或 [時間序列深入](../time-series-insights/time-series-insights-update-overview.md)解析。 若要將您的資料傳送給這類服務，請將目的地服務附加至端點。

例如，如果您也使用 Azure 地圖服務，並且想要讓位置與您的 Azure 數位 Twins 對應項 [圖形](concepts-twins-graph.md)相互關聯，您可以使用 Azure Functions 搭配事件方格，在部署中的所有服務之間建立通訊。 深入瞭解 how [ *To：使用 Azure 數位 Twins 來更新 Azure 地圖服務室內地圖*](how-to-integrate-maps.md)

您也可以在 how [*to：與時間序列深入解析整合*](how-to-integrate-time-series-insights.md)，瞭解如何以類似的方式將資料路由傳送到時間序列深入解析。

## <a name="next-steps"></a>後續步驟

深入瞭解端點和將事件路由至外部服務：
* [*概念：路由傳送 Azure 數位 Twins 活動*](concepts-route-events.md)

瞭解如何設定 Azure 數位 Twins 以從 IoT 中樞內嵌資料：
* [*作法：從 IoT 中樞內嵌遙測*](how-to-ingest-iot-hub-data.md)
