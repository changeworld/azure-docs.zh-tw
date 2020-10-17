---
title: 針對 Azure IoT 訊息路由進行疑難排解
description: 如何針對 Azure IoT 訊息路由進行疑難排解
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 84be56ae372f8a902b12c06f9ce93c1f7210dc5b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150590"
---
# <a name="troubleshooting-message-routing"></a>疑難排解訊息路由

本文提供 IoT 中樞 [訊息路由](iot-hub-devguide-messages-d2c.md)常見問題和解決方案的監視和疑難排解指引。 

## <a name="monitoring-message-routing"></a>監視訊息路由

[IoT 中樞計量](iot-hub-metrics.md)列出對於 IoT 中樞預設啟用的所有計量。 建議您監視與訊息路由和端點相關的計量，讓您瞭解所傳送的訊息。 此外，請在 Azure 監視器診斷設定中開啟 [診斷記錄](iot-hub-monitor-resource-health.md) ，以追蹤 **路由**的作業。 這些診斷記錄可以傳送給 Azure 監視器記錄檔、事件中樞或 Azure 儲存體以進行自訂處理。 瞭解如何 [設定和使用 IoT 中樞的計量和診斷記錄](tutorial-use-metrics-and-diags.md)。

如果您想要維護不符合任何路由上查詢的訊息，我們也建議您啟用 [fallback 路由](iot-hub-devguide-messages-d2c.md#fallback-route) 。 這些可以保留在所設定之保留天數的內 [建端點](iot-hub-devguide-messages-read-builtin.md) 中。 

## <a name="top-issues"></a>常見問題

以下是訊息路由所觀察到的最常見問題。 若要開始進行疑難排解，請按一下問題以取得詳細步驟。

* [來自我裝置的訊息未如預期般路由傳送](#messages-from-my-devices-are-not-being-routed-as-expected)
* [我突然停止取得內建事件中樞端點的訊息](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>來自我裝置的訊息未如預期般路由傳送

若要針對此問題進行疑難排解，請分析下列各項。

#### <a name="the-routing-metrics-for-this-endpoint"></a>此端點的路由度量
所有與路由相關的 [IoT 中樞計量](iot-hub-devguide-endpoints.md) 都會加上 *路由*。 您可以結合多個計量的資訊，以識別問題的根本原因。 例如，使用計量 **路由傳遞嘗試** 來識別已傳遞至端點的訊息數目，或當它們不符合任何路由上的查詢，且已停用回復路由時，就會捨棄訊息。 檢查 **路由延遲** 度量，以觀察訊息傳遞的延遲是否穩定或增加。 成長的延遲可能表示特定端點有問題，建議您檢查 [端點的健康情況](#the-health-of-the-endpoint)。 這些路由計量也有可提供計量詳細資料的 [維度](iot-hub-metrics.md#dimensions) ，例如端點類型、特定端點名稱，以及未傳遞訊息的原因。

#### <a name="the-diagnostic-logs-for-any-operational-issues"></a>任何操作問題的診斷記錄 
請觀察**路由**[診斷記錄](iot-hub-monitor-resource-health.md#routes)，以取得路由和端點[作業](#operation-names)的詳細資訊，或找出錯誤和相關[錯誤碼](#common-error-codes)以進一步瞭解問題。 例如，在記錄檔中 **RouteEvaluationError** 的作業名稱表示無法評估路由，因為訊息格式有問題。 使用針對特定作業 [名稱](#operation-names) 提供的秘訣來緩和問題。 當事件記錄為錯誤時，記錄檔也會提供評估失敗原因的詳細資訊。 例如，如果作業名稱是 **EndpointUnhealthy**， [錯誤碼](#common-error-codes) 403004 表示端點的空間不足。

#### <a name="the-health-of-the-endpoint"></a>端點的健康情況
使用 REST API [取得端點健全狀況](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 來取得端點的 [健康情況狀態](iot-hub-devguide-endpoints.md#custom-endpoints) 。 *取得端點健康*情況 API 也會提供最後一次成功將訊息傳送至端點、最後一個已知的[錯誤](#last-known-errors-for-iot-hub-routing-endpoints)、最後一個已知的錯誤時間，以及上次對此端點發出傳送時的相關資訊。 使用針對特定 [上一個已知錯誤](#last-known-errors-for-iot-hub-routing-endpoints)所提供的可能緩和措施。

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>我突然停止在內建端點取得訊息

若要針對此問題進行疑難排解，請分析下列各項。

#### <a name="was-a-new-route-created"></a>是否已建立新的路由？
建立路由之後，除非將路由建立至該端點，否則資料會停止流向內建端點。 為確保在新增路由時，訊息會持續流向內建端點，請設定 *事件* 端點的路由。 

#### <a name="was-the-fallback-route-disabled"></a>是否已停用 Fallback 路由？
如果有與[事件中樞](../event-hubs/index.yml)相容的內建事件中樞 (訊息/事件) ，則回溯路由會將所有不符合查詢準則的訊息傳送至[內建事件中樞](iot-hub-devguide-messages-read-builtin.md)。 如果訊息路由已開啟，您即可啟用後援路由功能。 如果內建端點沒有路由，而且後援路由已啟用，則與路由的任何查詢條件不符的訊息會傳送至內建端點。 此外，如果刪除所有的現有路由，必須啟用後援路由接收內建端點的所有資料。

您可以啟用/停用 Azure 入口網站 >訊息路由分頁中的回溯路由。 您也可以對於 [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) 使用 Azure 資源管理員來使用後援路由的自訂端點。

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>IoT 中樞路由端點的最後已知錯誤

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-diagnostic-logs"></a>路由診斷記錄

以下是記錄在 [診斷記錄](iot-hub-monitor-resource-health.md#routes)中的作業名稱和錯誤碼。

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>作業名稱

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>常見的錯誤碼

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>後續步驟

如果您需要更多協助，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。