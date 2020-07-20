---
title: 與其他服務整合
titleSuffix: Azure Digital Twins
description: 瞭解部署 Azure 數位 Twins 時的輸入和輸出需求。
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ba4188445f9bdf791ef760936bd27ac0f507fb4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737167"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>將 Azure 數位 Twins 與其他服務整合

Azure 數位 Twins 通常會與其他服務搭配使用。 使用[**事件路由**](concepts-route-events.md)，Azure 數位 Twins 會接收來自上游服務（例如[IoT 中樞](../iot-hub/about-iot-hub.md)）的資料，用來傳遞遙測和通知。 Azure 數位 Twins 也可以將資料路由至下游服務，以進行儲存體、工作流程整合、分析和其他用途。 

## <a name="data-ingress"></a>資料輸入

Azure 數位 Twins 可透過 IoT 中樞的資料來驅動。 這可讓您從環境中的實體裝置收集遙測，並使用雲端中的 Azure 數位 Twins 圖形來處理此資料。

Azure 數位 Twins 沒有內建的 IoT 中樞。 您可以使用目前在生產環境中現有的 IoT 中樞，或部署新的。 這可讓您完整存取 IoT 中樞的所有裝置管理功能。

若要將 IoT 中樞中的資料內嵌至 Azure 數位 Twins，請使用[azure](../azure-functions/functions-overview.md)函式。 若要深入瞭解此模式，請前往[如何：從 IoT 中樞內嵌遙測](how-to-ingest-iot-hub-data.md)，或在[Azure 數位 Twins 教學課程中自行嘗試：連接端對端解決方案](tutorial-end-to-end.md)。

## <a name="data-egress-services"></a>資料輸出服務

Azure 數位 Twins 可將資料傳送至已連線的**端點**。 支援的端點可以是：
* [事件中樞](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)

端點會使用管理 Api 或 Azure 入口網站附加至 Azure 數位 Twins。 若要深入瞭解如何將端點連結至 Azure 數位 Twins，請前往[如何：管理端點和路由](how-to-manage-routes.md)。

還有許多其他的服務，您可能會想要將資料導向其中，例如[Azure 儲存體](../storage/common/storage-introduction.md)或[時間序列深入解析](../time-series-insights/time-series-insights-update-overview.md)。 若要將您的資料傳送到這類服務，請將目的地服務附加至端點。

例如，如果您也使用[Azure 地圖服務](../azure-maps/about-azure-maps.md)，而且想要讓位置與您的 Azure 數位 Twins 對應項[圖形](concepts-twins-graph.md)相互關聯，您可以使用 Azure Functions 搭配事件方格，在部署中的所有服務之間建立通訊。

## <a name="next-steps"></a>後續步驟

深入瞭解端點和將事件路由至外部服務：
* [概念：路由 Azure 數位 Twins 事件](concepts-route-events.md)

請參閱如何設定 Azure 數位 Twins，以內嵌來自 IoT 中樞的資料：
* [如何：從 IoT 中樞內嵌遙測](how-to-ingest-iot-hub-data.md)
