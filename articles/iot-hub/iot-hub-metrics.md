---
title: 使用計量監視 Azure IoT 中樞 | Microsoft Docs
description: 如何使用 Azure IoT 中樞度量來評估和監視 IoT 中樞的整體健全狀況。
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 49ecf753b1dd547eab251222a6c2709d96e9c89b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146530"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>了解 IoT 中樞計量

IoT 中樞計量會提供您 Azure 訂用帳戶中 Azure IoT 資源狀態的相關資訊。 IoT 中樞度量可協助您評估 IoT 中樞服務及其連接之裝置的整體健全狀況。 這些使用者對應的統計資料可協助您瞭解 IoT 中樞的情況，並協助對問題執行根本原因分析，而不需要聯絡 Azure 支援。

預設會啟用計量。 您可以從 Azure 入口網站啟用 IoT 中樞計量。

> [!NOTE]
> 您可以使用 IoT 中樞計量來查看與 IoT 中樞連線 [IoT 隨插即用](../iot-pnp/overview-iot-plug-and-play.md) 裝置的相關資訊。

## <a name="how-to-view-iot-hub-metrics"></a>如何檢視 IoT 中樞計量

1. 建立 IoT 中樞。 您可以在[將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-dotnet.md)指南中找到有關如何建立 IoT 中樞的指示。

2. 開啟 IoT 中樞的刀鋒視窗。 按一下此處的 [計量]****。
   
    ![顯示 [IoT 中樞資源] 頁面中 [度量] 選項之位置的螢幕擷取畫面](./media/iot-hub-metrics/enable-metrics-1.png)

3. 您可以在 [計量] 刀鋒視窗中檢視 IoT 中樞的計量，並建立計量的自訂檢視。 
   
    ![顯示 IoT 中樞 [度量] 頁面的螢幕擷取畫面](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. 您可以選擇將計量資料傳送到事件中樞端點或 Azure 儲存體帳戶，方法是按一下 [ **診斷設定**]，然後 **新增診斷設定**。

   ![顯示 [診斷設定] 按鈕位置的螢幕擷取畫面](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT 中樞度量及其使用方式

IoT 中樞提供數個度量，以讓您概略了解中樞的健全狀況和所連線裝置的總數。 您可以結合多個度量的資訊，以便更清楚地了解 IoT 中樞的狀態。 下表描述每個 IoT 中樞所追蹤的度量，以及每個度量與 IoT 中樞整體狀態的關聯。

|計量|計量顯示名稱|Unit|彙總類型|描述|維度|
|---|---|---|---|---|---|
|RoutingDeliveries | 路由傳送嘗試 (預覽)  | 計數 | 總計 |這是路由傳遞度量。 使用維度來識別特定端點或特定路由來源的傳遞狀態。| Id<br>Result,<br>RoutingSource,<br>EndpointType<br>FailureReasonCategory,<br>EndpointName<br>*[**這裡**](#dimensions)的維度有更多詳細資料*。 |
|RoutingDeliveryLatency|  (預覽版的路由延遲)  | 毫秒 | Average |這是路由傳遞延遲度量。 使用維度來識別特定端點或特定路由來源的延遲。| Id<br>RoutingSource,<br>EndpointType<br>EndpointName<br>*[**這裡**](#dimensions)的維度有更多詳細資料*。|
|RoutingDataSizeInBytesDelivered| 路由傳送資料大小（以位元組為單位） (預覽) | 位元組 | 總計 |IoT 中樞路由至自訂端點和內建端點的總位元組數。 使用維度來識別路由至特定端點或特定路由來源的資料大小。| Id<br>RoutingSource,<br>EndpointType<br>EndpointName<br>*[**這裡**](#dimensions)的維度有更多詳細資料*。|
|d2c。<br>allProtocol|遙測訊息傳送嘗試|Count|總計|要嘗試傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|無|
|d2c。<br>成功|已傳送的遙測訊息|Count|總計|成功傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|無|
|c2d 輸出。<br>完成。成功|已完成的 C2D 訊息傳遞|Count|總計|裝置已成功完成的雲端到裝置訊息傳遞數目|無|
|c2d 輸出。<br>放棄。成功|已放棄的 C2D 訊息|Count|總計|裝置放棄的雲端到裝置訊息數目|無|
|c2d 輸出。<br>拒絕。成功|已拒絕的 C2D 訊息|Count|總計|裝置拒絕的雲端到裝置訊息數目|無|
|C2DMessagesExpired|已過期的 C2D 訊息 (預覽)|Count|總計|已過期的雲端到裝置訊息數目|無|
|devices.totalDevices|裝置總計 (已淘汰)|Count|總計|向 IoT 中樞註冊的裝置數目|無|
|connectedDevices。<br>allProtocol|連接的裝置 (已淘汰) |Count|總計|連接至 IoT 中樞的裝置數目|無|
|d2c。<br>成功|路由：已傳遞的遙測訊息|Count|總計|使用 IoT 中樞路由成功地將訊息傳遞到所有端點的次數。 如果將訊息路由至多個端點，這個值會為每一次成功傳遞加 1。 如果將訊息多次傳遞到同一個端點，這個值會為每一次成功傳遞加 1。|無|
|d2c。<br>下降|路由：已捨棄的遙測訊息 |Count|總計|IoT 中樞路由因為端點無效而捨棄訊息的次數。 這個值不會計算傳遞到後援路由的訊息，因為捨棄的訊息不會傳遞到那裡。|無|
|d2c。<br>遺棄|路由：已遺棄的遙測訊息 |Count|總計|當停用恢復路由時，由於 IoT 中樞路由不符合任何路由查詢，所以會將訊息孤立的次數。|無|
|d2c。<br>無效|路由：不相容的遙測訊息|Count|總計|IoT 中樞路由因為與端點不相容而無法傳遞訊息的次數。 當 Iot 中樞嘗試將訊息傳遞至端點，且因非暫時性錯誤而失敗時，訊息與端點不相容。 不正確訊息不會重試。 這個值不包含重試次數。|無|
|d2c。<br>fallback|路由：傳遞至後援的訊息|Count|總計|IoT 中樞路由傳遞訊息至與後援路由相關聯之端點的次數。|無|
|d2c。輸出。<br>eventHubs|路由：傳遞至事件中樞的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至類型事件中樞自訂端點的次數。 這不包括訊息路由至內建端點 (事件) 。|無|
|d2c。延遲。<br>eventHubs|路由：事件中樞的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到事件中樞類型的自訂端點之間的平均延遲 (毫秒) 。 這不包括訊息路由至內建端點 (事件) 。|無|
|d2c。輸出。<br>serviceBusQueues|路由：傳遞至服務匯流排佇列的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排佇列端點的次數。|無|
|d2c。延遲。<br>serviceBusQueues|路由：服務匯流排佇列的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到服務匯流排佇列端點之間的平均延遲 (毫秒) 。|無|
|d2c。輸出。<br>serviceBusTopics|路由：傳遞至服務匯流排主題的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排主題端點的次數。|無|
|d2c。延遲。<br>serviceBusTopics|路由：服務匯流排主題的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到服務匯流排主題端點之間的平均延遲 (毫秒) 。|無|
|d2c。輸出。<br>內建。事件|路由：傳遞至訊息/事件的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至內建端點的次數， (訊息/事件) 和退回路由。|無|
|d2c。延遲。<br>內建。事件|路由：訊息/事件的訊息延遲|毫秒|Average|在訊息輸入與 IoT 中樞之間的平均延遲 (毫秒) ，以及輸入至內建端點 (訊息/事件) 和回溯路由的訊息。|無|
|d2c。輸出。<br>儲存|路由：傳遞至儲存體的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至儲存體端點的次數。|無|
|d2c。延遲。<br>儲存|路由：儲存體的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到儲存體端點之間的平均延遲 (毫秒) 。|無|
|d2c。輸出。<br>儲存體。位元組|路由：傳遞至儲存體的資料|位元組|總計|IoT 中樞路由傳遞至儲存體端點的資料量 (位元組)。|無|
|d2c。輸出。<br>儲存體 blob|路由：傳遞至儲存體的 BLOB|Count|總計|IoT 中樞路由將 BLOB 傳遞至儲存體端點的次數。|無|
|EventGridDeliveries|事件方格傳遞 (預覽)|Count|總計|發佈至事件方格的 IoT 中樞事件數目。 您可使用 [結果] 維度來取得成功和失敗要求的數目。 [事件種類] 維度會顯示事件 (的類型 https://aka.ms/ioteventgrid) 。|Id<br/>Result,<br/>EventType|
|EventGridLatency|事件方格延遲 (預覽)|毫秒|Average|當事件發佈到事件方格時，從 Iot 中樞事件產生到的平均延遲 (毫秒) 。 此數位是所有事件種類之間的平均值。 您可以使用 [事件種類] 維度來查看特定事件種類的延遲。|Id<br/>EventType|
|d2c.twin.read.success|裝置的成功對應項讀取|Count|總計|裝置起始的所有成功對應項讀取的計數。|無|
|d2c.twin.read.failure|裝置的失敗對應項讀取|Count|總計|裝置起始的所有失敗對應項讀取的計數。|無|
|d2c.twin.read.size|裝置的對應項讀取回應大小|位元組|Average|裝置起始的所有成功對應項讀取的數目。|無|
|d2c.twin.update.success|裝置的成功對應項更新|Count|總計|裝置起始的所有成功對應項更新的計數。|無|
|d2c.twin.update.failure|裝置的失敗對應項更新|Count|總計|裝置起始的所有失敗對應項更新的計數。|無|
|d2c.twin.update.size|裝置的對應項更新大小|位元組|Average|裝置起始的所有成功對應項更新的總大小。|無|
|c2d.methods.success|成功直接方法叫用|Count|總計|所有成功直接方法呼叫的計數。|無|
|c2d.methods.failure|失敗直接方法叫用|Count|總計|所有失敗直接方法呼叫的計數。|無|
|c2d.methods.requestSize|直接方法叫用的要求大小|位元組|Average|所有成功直接方法要求的計數。|無|
|c2d.methods.responseSize|直接方法叫用的回應大小|位元組|Average|所有成功直接方法回應的計數。|無|
|c2d.twin.read.success|後端的成功對應項讀取|Count|總計|後端起始的所有成功對應項讀取的計數。|無|
|c2d.twin.read.failure|後端的失敗對應項讀取|Count|總計|後端起始的所有失敗對應項讀取的計數。|無|
|c2d.twin.read.size|後端的對應項讀取回應大小|位元組|Average|後端起始的所有成功對應項讀取的計數。|無|
|c2d.twin.update.success|後端的成功對應項更新|Count|總計|後端起始的所有成功對應項更新的計數。|無|
|c2d.twin.update.failure|後端的失敗對應項更新|Count|總計|後端起始的所有失敗對應項更新的計數。|無|
|c2d.twin.update.size|後端的對應項更新大小|位元組|Average|所有成功後端起始對應項更新的總大小。|無|
|twinQueries.success|成功對應項查詢|Count|總計|所有成功對應項查詢的計數。|無|
|twinQueries.failure|失敗對應項查詢|Count|總計|所有失敗對應項查詢的計數。|無|
|twinQueries.resultSize|對應項查詢結果大小|位元組|Average|所有成功對應項查詢的結果大小總計。|無|
|createTwinUpdateJob。<br>成功|成功建立的對應項更新作業|Count|總計|所有成功建立的對應項更新作業計數。|無|
|createTwinUpdateJob。<br>失敗|建立失敗的對應項更新作業|Count|總計|所有建立失敗的對應項更新作業計數。|無|
|createDirectMethodJob。<br>成功|成功建立的方法叫用作業|Count|總計|所有成功建立的直接方法叫用作業計數。|無|
|createDirectMethodJob。<br>失敗|建立失敗的方法叫用作業|Count|總計|所有建立失敗的直接方法叫用作業計數。|無|
|jobs.listJobs.success|成功呼叫列出作業|Count|總計|所有成功呼叫列出作業的計數。|無|
|jobs.listJobs.failure|呼叫列出作業失敗|Count|總計|所有呼叫列出作業失敗的計數。|無|
|jobs.cancelJob.success|成功取消作業|Count|總計|所有成功呼叫取消作業的計數。|無|
|jobs.cancelJob.failure|取消作業失敗|Count|總計|所有呼叫取消作業失敗的計數。|無|
|jobs.queryJobs.success|成功作業查詢|Count|總計|所有成功呼叫查詢作業的計數。|無|
|jobs.queryJobs.failure|失敗作業查詢|Count|總計|所有呼叫查詢作業失敗的計數。|無|
|jobs.completed|已完成的工作|Count|總計|所有已完成的作業計數。|無|
|jobs.failed|失敗作業|Count|總計|所有失敗作業計數。|無|
|d2c。<br>sendThrottle|節流錯誤數目|Count|總計|因裝置輸送量節流而導致的節流錯誤數目|無|
|dailyMessageQuotaUsed|已使用的訊息總數|Count|Average|今日已使用的總訊息數。 這是一個累計值，會在每天的 00:00 UTC 重設為零。|無|
|deviceDataUsage|裝置資料使用量總計|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|無|
|deviceDataUsageV2|裝置資料使用量總計 (預覽)|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|無|
|totalDeviceCount|裝置總計 (預覽)|Count|Average|向 IoT 中樞註冊的裝置數目|無|
|connectedDeviceCount|連接的裝置 (預覽)|Count|Average|連接至 IoT 中樞的裝置數目|無|
|組態|設定計量|Count|總計|在一組目標裝置上，針對裝置設定和 IoT Edge 部署執行的總 CRUD 作業數。 這也包括因這些設定而修改裝置對應項或模組對應項的作業數目。|無|

### <a name="dimensions"></a>維度
維度有助於找出更多關於計量的詳細資料。 某些路由計量會提供每個端點的資訊。 下表列出這些維度的可能值。

|尺寸|值|
|---|---|
|ResourceID|您的 IoT 中樞資源|
|結果|成功<br>失敗|
|RoutingSource|裝置訊息<br>對應項變更事件<br>裝置生命週期事件|
|EndpointType|eventHubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>內置<br>blobStorage|
|FailureReasonCategory|無效<br>下降<br>遺棄<br>null|
|EndpointName|端點名稱|

## <a name="next-steps"></a>後續步驟

現在您已了解 IoT 中樞度量的概觀，請循著下列連結來深入了解如何管理「Azure IoT 中樞」：

* [設定診斷記錄](iot-hub-monitor-resource-health.md)

* [瞭解如何針對訊息路由進行疑難排解](troubleshoot-message-routing.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南](iot-hub-devguide.md)

* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/quickstart-linux.md)