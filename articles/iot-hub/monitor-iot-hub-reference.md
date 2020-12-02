---
title: 監視 Azure IoT 中樞資料參考
description: 當您監視時所需的重要參考資料 Azure IoT 中樞
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.date: 10/22/2020
ms.openlocfilehash: 609c44991e7deca183321758fabdef80b71210c5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460866"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>監視 Azure IoT 中樞資料參考

如需收集和分析監視資料的詳細資訊，請參閱 [監視 Azure IoT 中樞](monitor-iot-hub.md) Azure IoT 中樞。

## <a name="metrics"></a>計量

此區段會列出 Azure IoT 中樞的所有自動收集平臺計量。 IoT 中樞度量的資源提供者命名空間為 **Microsoft。裝置** 和類型命名空間為 **IoTHubs**。

下列子區段會依 [一般] 分類來細分 IoT 中樞平臺計量，並依其出現在 Azure 入口網站中的顯示名稱列出。 此外，也會提供有關每個子區段中所顯示之計量的相關資訊。

您也可以在 Azure 監視器檔中，找到依計量名稱列出所有 IoT 中樞平臺計量的單一[資料表。](../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs) 請注意，此資料表並不提供部分資訊，例如本文所提供的一些計量 [支援](#supported-aggregations) 的匯總。

若要深入瞭解其他 Azure 服務所支援的計量，請參閱 [支援的 Azure 監視器計量](../azure-monitor/platform/metrics-supported.md)。

**本節中的主題**

- [支援的匯總](#supported-aggregations)
- [雲端到裝置的命令計量](#cloud-to-device-command-metrics)
- [雲端到裝置直接方法計量](#cloud-to-device-direct-methods-metrics)
- [雲端到裝置對應項作業計量](#cloud-to-device-twin-operations-metrics)
- [設定計量](#configurations-metrics)
- [每日配額計量](#daily-quota-metrics)
- [裝置計量](#device-metrics)
- [裝置遙測計量](#device-telemetry-metrics)
- [裝置到雲端對應項作業計量](#device-to-cloud-twin-operations-metrics)
- [事件方格計量](#event-grid-metrics)
- [作業計量](#jobs-metrics)
- [路由計量](#routing-metrics)
- [對應項查詢計量](#twin-query-metrics)

### <a name="supported-aggregations"></a>支援的匯總

每個資料表中的 [ **匯總類型** ] 資料行，都會對應到選取圖表或警示的度量時所使用的預設匯總。

   ![顯示計量匯總的螢幕擷取畫面](./media/monitor-iot-hub-reference/aggregation-type.png)

對於大部分的計量而言，所有匯總類型都是有效的;不過，對於計數計量而言，其 **單位** 資料行值為 **count**，則只有部分匯總有效。 計數計量可以是下列兩種類型之一：

* 針對 **單一點** 計數計量，IoT 中樞會在每次進行測量的作業時，註冊單一資料點，基本上是 1--。 Azure 監視器然後將這些資料點加總在指定的資料細微性上。 **單一點** 計量的範例包括已 *傳送的遙測訊息*，以及 *已完成的 C2D 訊息傳遞*。 針對這些計量，唯一相關的匯總類型 (Sum) 總計。 入口網站可讓您選擇最小值、最大值和平均值;不過，這些值一律會是1。

* 針對 **快照** 集計數計量，IoT 中樞會在測量的作業發生時，註冊總計計數。 目前，IoT 中樞會發出三個 **快照** 計量：已 *使用的訊息總數*、 *(預覽) 的裝置總數*，以及 *連線的裝置 (preview)*。 因為這些計量會在每次發出時呈現「總計」數量，所以在指定的資料細微性上加總將沒有任何意義。 Azure 監視器會限制您針對這些計量選取匯總類型的 [平均]、[最小值] 和 [最大值]。

### <a name="cloud-to-device-command-metrics"></a>雲端到裝置的命令計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|已過期的 C2D 訊息 (預覽)|C2DMessagesExpired|計數|總計|已過期的雲端到裝置訊息數目|無|
|已完成的 C2D 訊息傳遞|c2d.commands.egress.complete.success|計數|總計|裝置已成功完成的雲端到裝置訊息傳遞數目|無|
|已放棄的 C2D 訊息|c2d.commands.egress.abandon.success|計數|總計|裝置放棄的雲端到裝置訊息數目|無|
|已拒絕的 C2D 訊息|c2d.commands.egress.reject.success|計數|總計|裝置拒絕的雲端到裝置訊息數目|無|

針對 **單位** 值為 **Count** 的計量，只有總計) 匯總 (總計有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

### <a name="cloud-to-device-direct-methods-metrics"></a>雲端到裝置直接方法計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|失敗直接方法叫用|c2d.methods.failure|計數|總計|所有失敗直接方法呼叫的計數。|無|
|直接方法叫用的要求大小|c2d.methods.requestSize|位元組|Average|所有成功直接方法要求的計數。|無|
|直接方法叫用的回應大小|c2d.methods.responseSize|位元組|Average|所有成功直接方法回應的計數。|無|
|成功直接方法叫用|c2d.methods.success|計數|總計|所有成功直接方法呼叫的計數。|無|

針對 **單位** 值為 **Count** 的計量， (sum) 匯總有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

### <a name="cloud-to-device-twin-operations-metrics"></a>雲端到裝置對應項作業計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|後端的失敗對應項讀取|c2d.twin.read.failure|計數|總計|後端起始的所有失敗對應項讀取的計數。|無|
|後端的失敗對應項更新|c2d.twin.update.failure|計數|總計|後端起始的所有失敗對應項更新的計數。|無|
|後端的對應項讀取回應大小|c2d.twin.read.size|位元組|Average|後端起始的所有成功對應項讀取的計數。|無|
|後端的對應項更新大小|c2d.twin.update.size|位元組|Average|所有成功後端起始對應項更新的總大小。|無|
|後端的成功對應項讀取|c2d.twin.read.success|計數|總計|後端起始的所有成功對應項讀取的計數。|無|
|後端的成功對應項更新|c2d.twin.update.success|計數|總計|後端起始的所有成功對應項更新的計數。|無|

針對 **單位** 值為 **Count** 的計量，只有總計) 匯總 (總計有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

### <a name="configurations-metrics"></a>設定計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|設定計量|組態|計數|總計|在一組目標裝置上，針對裝置設定和 IoT Edge 部署執行的總 CRUD 作業數。 這也包括因這些設定而修改裝置對應項或模組對應項的作業數目。|無|

針對 **單位** 值為 **Count** 的計量，只有總計) 匯總 (總計有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

### <a name="daily-quota-metrics"></a>每日配額計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|裝置資料使用量總計|deviceDataUsage|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|無|
|裝置資料使用量總計 (預覽)|deviceDataUsageV2|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|無|
|已使用的訊息總數|dailyMessageQuotaUsed|Count|Average|今日已使用的總訊息數。 這是一個累計值，會在每天的 00:00 UTC 重設為零。|無|

針對 *使用的總訊息數*，只支援最小值、最大值和平均匯總。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

### <a name="device-metrics"></a>裝置計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|裝置總計 (已淘汰)|devices.totalDevices|計數|總計|向 IoT 中樞註冊的裝置數目|無|
|連接的裝置 (已淘汰) |devices.connectedDevices.allProtocol|計數|總計|連接至 IoT 中樞的裝置數目|無|
|裝置總計 (預覽)|totalDeviceCount|Count|Average|向 IoT 中樞註冊的裝置數目|無|
|連接的裝置 (預覽)|connectedDeviceCount|Count|Average|連接至 IoT 中樞的裝置數目|無|

針對已淘汰的 *裝置總數 (已淘汰的)* 和 *連線的裝置 (已淘汰的)*，只有總 (總和) 匯總有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

針對 *裝置總數 (預覽)* 和 *連線裝置 (預覽)*，只有最小值、最大值和平均匯總有效。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

*連線的裝置 (預覽)* 和 *裝置總數 (預覽)* 無法透過診斷設定匯出。

### <a name="device-telemetry-metrics"></a>裝置遙測計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|節流錯誤數目|d2c.telemetry.ingress.sendThrottle|計數|總計|因裝置輸送量節流而導致的節流錯誤數目|無|
|遙測訊息傳送嘗試|d2c.telemetry.ingress.allProtocol|計數|總計|要嘗試傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|無|
|已傳送的遙測訊息|d2c.telemetry.ingress.success|計數|總計|成功傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|無|

針對 **單位** 值為 **Count** 的計量，只有總計) 匯總 (總計有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

### <a name="device-to-cloud-twin-operations-metrics"></a>裝置到雲端對應項作業計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|裝置的失敗對應項讀取|d2c.twin.read.failure|計數|總計|裝置起始的所有失敗對應項讀取的計數。|無|
|裝置的失敗對應項更新|d2c.twin.update.failure|計數|總計|裝置起始的所有失敗對應項更新的計數。|無|
|裝置的對應項讀取回應大小|d2c.twin.read.size|位元組|Average|裝置起始的所有成功對應項讀取的數目。|無|
|裝置的對應項更新大小|d2c.twin.update.size|位元組|Average|裝置起始的所有成功對應項更新的總大小。|無|
|裝置的成功對應項讀取|d2c.twin.read.success|計數|總計|裝置起始的所有成功對應項讀取的計數。|無|
|裝置的成功對應項更新|d2c.twin.update.success|計數|總計|裝置起始的所有成功對應項更新的計數。|無|

針對 **單位** 值為 **Count** 的計量，只有總計) 匯總 (總計有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

### <a name="event-grid-metrics"></a>事件方格計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|事件方格傳遞 (預覽) |EventGridDeliveries|計數|總計|發佈至事件方格的 IoT 中樞事件數目。 您可使用 [結果] 維度來取得成功和失敗要求的數目。 [事件種類] 維度會顯示事件 (的類型 https://aka.ms/ioteventgrid) 。|Result,<br/>EventType<br>*如需詳細資訊，請參閱計量 [維度](#metric-dimensions)*。|
|事件方格延遲 (預覽)|EventGridLatency|毫秒|Average|當事件發佈到事件方格時，從 Iot 中樞事件產生到的平均延遲 (毫秒) 。 此數位是所有事件種類之間的平均值。 您可以使用 [事件種類] 維度來查看特定事件種類的延遲。|EventType<br>*如需詳細資訊，請參閱計量 [維度](#metric-dimensions)*。|

針對 **單位** 值為 **Count** 的計量，只有總計) 匯總 (總計有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

### <a name="jobs-metrics"></a>作業計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|已完成的工作|jobs.completed|計數|總計|所有已完成的作業計數。|無|
|呼叫列出作業失敗|jobs.listJobs.failure|計數|總計|所有呼叫列出作業失敗的計數。|無|
|建立失敗的方法叫用作業|jobs.createDirectMethodJob.failure|計數|總計|所有建立失敗的直接方法叫用作業計數。|無|
|建立失敗的對應項更新作業|jobs.createTwinUpdateJob.failure|計數|總計|所有建立失敗的對應項更新作業計數。|無|
|取消作業失敗|jobs.cancelJob.failure|計數|總計|所有呼叫取消作業失敗的計數。|無|
|失敗作業查詢|jobs.queryJobs.failure|計數|總計|所有呼叫查詢作業失敗的計數。|無|
|失敗作業|jobs.failed|計數|總計|所有失敗作業計數。|無|
|成功呼叫列出作業|jobs.listJobs.success|計數|總計|所有成功呼叫列出作業的計數。|無|
|成功建立的方法叫用作業|jobs.createDirectMethodJob.success|計數|總計|所有成功建立的直接方法叫用作業計數。|無|
|成功建立的對應項更新作業|createTwinUpdateJob。<br>成功|計數|總計|所有成功建立的對應項更新作業計數。|無|
|成功取消作業|jobs.cancelJob.success|計數|總計|所有成功呼叫取消作業的計數。|無|
|成功作業查詢|jobs.queryJobs.success|計數|總計|所有成功呼叫查詢作業的計數。|無|

針對 **單位** 值為 **Count** 的計量，只有總計) 匯總 (總計有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

### <a name="routing-metrics"></a>路由計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
| 路由傳送嘗試 (預覽)  |RoutingDeliveries | 計數 | 總計 |這是路由傳遞度量。 使用維度來識別特定端點或特定路由來源的傳遞狀態。| Result,<br>RoutingSource,<br>EndpointType<br>FailureReasonCategory,<br>EndpointName<br>*如需詳細資訊，請參閱計量 [維度](#metric-dimensions)*。 |
| 路由傳送資料大小（以位元組為單位） (預覽) |RoutingDataSizeInBytesDelivered| 位元組 | 總計 |IoT 中樞路由至自訂端點和內建端點的總位元組數。 使用維度來識別路由至特定端點或特定路由來源的資料大小。| RoutingSource,<br>EndpointType<br>EndpointName<br>*如需詳細資訊，請參閱計量 [維度](#metric-dimensions)*。|
|  (預覽版的路由延遲)  |RoutingDeliveryLatency| 毫秒 | Average |這是路由傳遞延遲度量。 使用維度來識別特定端點或特定路由來源的延遲。| RoutingSource,<br>EndpointType<br>EndpointName<br>*如需詳細資訊，請參閱計量 [維度](#metric-dimensions)*。|
|路由：傳遞至儲存體的 BLOB|d2c.endpoints.egress.storage.blobs|計數|總計|IoT 中樞路由將 BLOB 傳遞至儲存體端點的次數。|無|
|路由：傳遞至儲存體的資料|d2c.endpoints.egress.storage.bytes|位元組|總計|IoT 中樞路由傳遞至儲存體端點的資料量 (位元組)。|無|
|路由：事件中樞的訊息延遲|d2c.endpoints.latency.eventHubs|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到事件中樞類型的自訂端點之間的平均延遲 (毫秒) 。 這不包括訊息路由至內建端點 (事件) 。|無|
|路由：服務匯流排佇列的訊息延遲|d2c.endpoints.latency.serviceBusQueues|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到服務匯流排佇列端點之間的平均延遲 (毫秒) 。|無|
|路由：服務匯流排主題的訊息延遲|d2c.endpoints.latency.serviceBusTopics|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到服務匯流排主題端點之間的平均延遲 (毫秒) 。|無|
|路由：訊息/事件的訊息延遲|d2c.endpoints.latency.builtIn.events|毫秒|Average|在訊息輸入與 IoT 中樞之間的平均延遲 (毫秒) ，以及輸入至內建端點 (訊息/事件) 和回溯路由的訊息。|無|
|路由：儲存體的訊息延遲|d2c.endpoints.latency.storage|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到儲存體端點之間的平均延遲 (毫秒) 。|無|
|路由：傳遞至事件中樞的訊息|d2c.endpoints.egress.eventHubs|計數|總計|IoT 中樞路由成功將訊息傳遞至類型事件中樞自訂端點的次數。 這不包括訊息路由至內建端點 (事件) 。|無|
|路由：傳遞至服務匯流排佇列的訊息|d2c.endpoints.egress.serviceBusQueues|計數|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排佇列端點的次數。|無|
|路由：傳遞至服務匯流排主題的訊息|d2c.endpoints.egress.serviceBusTopics|計數|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排主題端點的次數。|無|
|路由：傳遞至後援的訊息|d2c.telemetry.egress.fallback|計數|總計|IoT 中樞路由傳遞訊息至與後援路由相關聯之端點的次數。|無|
|路由：傳遞至訊息/事件的訊息|d2c.endpoints.egress.builtIn.events|計數|總計|IoT 中樞路由成功將訊息傳遞至內建端點的次數， (訊息/事件) 和退回路由。|無|
|路由：傳遞至儲存體的訊息|d2c.endpoints.egress.storage|計數|總計|IoT 中樞路由成功將訊息傳遞至儲存體端點的次數。|無|
|路由：已傳遞的遙測訊息|d2c.telemetry.egress.success|計數|總計|使用 IoT 中樞路由成功地將訊息傳遞到所有端點的次數。 如果將訊息路由至多個端點，這個值會為每一次成功傳遞加 1。 如果將訊息多次傳遞到同一個端點，這個值會為每一次成功傳遞加 1。|無|
|路由：已捨棄的遙測訊息 |d2c.telemetry.egress.dropped|計數|總計|IoT 中樞路由因為端點無效而捨棄訊息的次數。 這個值不會計算傳遞到後援路由的訊息，因為捨棄的訊息不會傳遞到那裡。|無|
|路由：不相容的遙測訊息|d2c.telemetry.egress.invalid|計數|總計|IoT 中樞路由因為與端點不相容而無法傳遞訊息的次數。 當 Iot 中樞嘗試將訊息傳遞至端點，且因非暫時性錯誤而失敗時，訊息與端點不相容。 不正確訊息不會重試。 這個值不包含重試次數。|無|
|路由：已遺棄的遙測訊息 |d2c.telemetry.egress.orphaned|計數|總計|當停用恢復路由時，由於 IoT 中樞路由不符合任何路由查詢，所以會將訊息孤立的次數。|無|

針對 **單位** 值為 **Count** 的計量，只有總計) 匯總 (總計有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

### <a name="twin-query-metrics"></a>對應項查詢計量

|計量顯示名稱|計量|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|失敗對應項查詢|twinQueries.failure|計數|總計|所有失敗對應項查詢的計數。|無|
|成功對應項查詢|twinQueries.success|計數|總計|所有成功對應項查詢的計數。|無|
|對應項查詢結果大小|twinQueries.resultSize|位元組|Average|所有成功對應項查詢的結果大小總計。|無|

針對 **單位** 值為 **Count** 的計量，只有總計) 匯總 (總計有效。 最小值、最大值和平均匯總一律會傳回1。 如需詳細資訊，請參閱 [支援](#supported-aggregations)的匯總。

## <a name="metric-dimensions"></a>度量維度

Azure IoT 中樞具有與部分路由和事件方格度量相關聯的下列維度。

|維度名稱 | 描述|
|---|---|
||
|**點**| 端點名稱。|
|**EndpointType**|下列其中一項： **eventHubs**、 **serviceBusQueues**、 **cosmosDB**、 **serviceBusTopics**。 內 **建** 或 **blobStorage**。|
|**EventType**| 下列其中一個事件方格事件種類： **DeviceCreated**。 **DeviceDeleted**、 **DeviceConnected**、DeviceDisconnected 或，或，或 **Microsoft.Devices.DeviceDisconnected** 或 **DeviceTelemetry**。 如需詳細資訊，請參閱 [事件種類](iot-hub-event-grid.md#event-types)。|
|**FailureReasonCategory**| 下列其中一項：**無效**、**已卸載、****孤立** 或 **null**。|
|**結果**| **成功** 或 **失敗**。|
|**RoutingSource**| 裝置訊息<br>對應項變更事件<br>裝置生命週期事件|

若要深入瞭解計量維度，請參閱 [多維度計量](../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics)。

## <a name="resource-logs"></a>資源記錄

此區段會列出針對 Azure IoT 中樞收集的所有資源記錄類別類型和架構。 所有 IoT 中樞記錄的資源提供者和類型都是 [Microsoft. Devices/IotHubs](../azure-monitor/platform/resource-logs-categories.md#microsoftdevicesiothubs)。

**本節中的主題**

- [連線](#connections)
- [裝置遙測](#device-telemetry)
- [雲端到裝置的命令](#cloud-to-device-commands)
- [裝置身分識別作業](#device-identity-operations)
- [檔案上傳作業](#file-upload-operations)
- [路由](#routes)
- [裝置到雲端對應項作業](#device-to-cloud-twin-operations)
- [雲端到裝置對應項作業](#cloud-to-device-twin-operations)
- [對應項查詢](#twin-queries)
- [作業的操作](#jobs-operations)
- [直接方法](#direct-methods)
- [分散式追蹤 (預覽)](#distributed-tracing-preview)
  - [IoT 中樞 D2C (裝置到雲端) 記錄](#iot-hub-d2c-device-to-cloud-logs)
  - [IoT 中樞輸入記錄](#iot-hub-ingress-logs)
  - [IoT 中樞輸出記錄](#iot-hub-egress-logs)
- [組態](#configurations)
- [裝置串流 (預覽) ](#device-streams-preview)

### <a name="connections"></a>連接

連線類別會追蹤來自 IoT 中樞的裝置連線和中斷連線事件以及錯誤。 此類別對於識別未經授權的連線嘗試，及/或在遺失裝置連線時發出警示非常有用。

> [!NOTE]
> 針對可靠的裝置連線狀態，勾選 [裝置活動訊號](iot-hub-devguide-identity-registry.md#device-heartbeat)。

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>裝置遙測

裝置遙測類別會追蹤在 IoT 中樞發生，且與遙測管線相關的錯誤。 此類別包括在傳送遙測事件 (例如節流) 和接收遙測事件 (例如未經授權的讀取器) 時所發生的錯誤。 這個類別無法捕捉裝置本身所執行之程式碼所造成的錯誤。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>雲端到裝置的命令

雲端到裝置的命令類別會追蹤在 IoT 中樞發生，且與雲端到裝置訊息管線相關的錯誤。 此類別包括發生自下列動作的錯誤：

* 傳送雲端到裝置訊息 (例如未經授權的寄件者錯誤)、
* 接收雲端到裝置訊息 (例如已超過傳遞計數錯誤)，以及
* 接收雲端到裝置訊息意見反應 (例如意見反應已過期的錯誤)。

當雲端到裝置訊息成功傳遞但裝置未正確處理時，此類別不會攔截錯誤。

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>裝置身分識別作業

裝置身分識別作業類別會追蹤您嘗試在其 IoT 中樞的身分識別登錄中建立、更新或刪除項目時所發生的錯誤。 佈建案例就很適合追蹤此類別。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>檔案上傳作業

檔案上傳類別會追蹤在 IoT 中樞發生且與檔案上傳功能相關的錯誤。 此類別包括︰

* SAS URI 所發生的錯誤，例如當它在裝置通知中樞已完成上傳之前就到期時。

* 裝置所報告的失敗上傳。

* IoT 中樞通知訊息建立期間在儲存體中找不到檔案時所發生的錯誤。

此類別無法捕捉直接發生在裝置將檔案上傳到儲存體時的錯誤。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>路由

[訊息路由](./iot-hub-devguide-messages-d2c.md)類別會追蹤訊息路由評估期間發生的錯誤，以及 IoT 中樞所察覺的端點健康情況。 此類別包括下列事件，例如：

* 規則評估為「未定義」、
* IoT 中樞將端點標示為無效，或
* 從端點收到的任何錯誤。

此類別不包含有關訊息本身的特定錯誤 (例如裝置節流錯誤)，這些是在「裝置遙測」類別下報告。

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

以下是路由資源記錄的更多詳細資料：

* [路由資源記錄檔錯誤碼的清單](troubleshoot-message-routing.md#diagnostics-error-codes)
* [路由資源記錄檔 operationNames 清單](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>裝置到雲端對應項作業

裝置到雲端對應項作業類別會追蹤裝置對應項上裝置起始的事件。 這些作業可能包括取得對應項、更新回報的屬性，以及訂閱所需的屬性。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>雲端到裝置對應項作業

雲端到裝置對應項作業類別會追蹤裝置對應項上服務起始的事件。 這些作業可能包括取得對應項、更新或取代標記，以及更新或取代所需的屬性。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>對應項查詢

對應項查詢類別會針對在雲端中起始之裝置對應項的查詢要求，進行回報。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>作業的操作

作業操作類別會針對用來更新裝置對應項或對多部裝置叫用直接方法的作業要求，進行回報。 這些要求是在雲端中起始的。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>直接方法

直接方法類別會追蹤傳送給個別裝置的「要求-回應」互動。 這些要求是在雲端中起始的。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>分散式追蹤 (預覽)

分散式追蹤類別會追蹤相互關聯識別碼，以尋找帶有追蹤內容標頭的訊息。 若要完整啟用這些記錄，您必須更新用戶端程式代碼，方法是遵循 [Iot 中樞分散式追蹤的端對端分析和診斷 iot 應用程式 (預覽) ](iot-hub-distributed-tracing.md)。

請注意， `correlationId` 符合 [W3C 追蹤內容](https://github.com/w3c/trace-context) 提案，其中包含和 `trace-id` `span-id` 。

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT 中樞 D2C (裝置到雲端) 記錄

當包含有效追蹤屬性的訊息抵達 IoT 中樞，IoT 中樞便會記下這個記錄。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

在這裡，`durationMs` 不會進行計算，因為 IoT 中樞的時鐘可能未與裝置的時鐘同步，因此計算持續時間可能會產生誤導。 建議您使用 `properties` 區段中的時間戳記來撰寫邏輯，以擷取暴增的裝置到雲端延遲。

| 屬性 | 類型 | 描述 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | 整數 | 裝置到雲端訊息的大小 (以位元組為單位) |
| **deviceId** | 由 ASCII 7 位元英數字元組成的字串 | 裝置的身分識別 |
| **callerLocalTimeUtc** | UTC 時間戳記 | 裝置本機時鐘所報告的訊息建立時間 |
| **calleeLocalTimeUtc** | UTC 時間戳記 | IoT 中樞服務端時鐘所報告、訊息抵達 IoT 中樞閘道的時間 |

#### <a name="iot-hub-ingress-logs"></a>IoT 中樞輸入記錄

當包含有效追蹤屬性的訊息寫入到內部或內建的事件中樞時，IoT 中樞便會記下這個記錄。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

在 `properties` 區段中，此記錄檔包含有關訊息輸入的其他資訊。

| 屬性 | 類型 | 描述 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | 可為 true 或 false，會指出 IoT 中樞是否已啟用訊息路由 |
| **parentSpanId** | String | 父代訊息的[範圍識別碼](https://w3c.github.io/trace-context/#parent-id)，在此案例中會是 D2C 訊息追蹤 |

#### <a name="iot-hub-egress-logs"></a>IoT 中樞輸出記錄

當[路由](iot-hub-devguide-messages-d2c.md)已啟用且有訊息寫入到[端點](iot-hub-devguide-endpoints.md)時，IoT 中樞便會記下這個記錄。 如果路由未啟用，IoT 中樞則不會記下這個記錄。

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

在 `properties` 區段中，此記錄檔包含有關訊息輸入的其他資訊。

| 屬性 | 類型 | 描述 |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **點** | String | 路由端點的名稱 |
| **endpointType** | String | 路由端點的類型 |
| **parentSpanId** | String | 父代訊息的[範圍識別碼](https://w3c.github.io/trace-context/#parent-id)，在此案例中會是 IoT 中樞輸入訊息追蹤 |

### <a name="configurations"></a>組態

IoT 中樞設定記錄會追蹤自動裝置管理功能集的事件和錯誤。

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>裝置串流 (預覽) 

裝置串流類別會追蹤傳送給個別裝置的要求-回應互動。

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Azure 監視器記錄資料表
<!-- REQUIRED. Please keep heading in this order -->

本節參考所有與 Azure IoT 中樞相關的 Azure 監視器記錄 Kusto 資料表，並且可供 Log Analytics 查詢。 如需這些資料表的清單以及 IoT 中樞資源類型詳細資訊的連結，請參閱 Azure 監視器 Logs 資料表參考中的 [Iot 中樞](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) 。

如需所有 Azure 監視器記錄檔/Log Analytics 資料表的參考，請參閱 [Azure 監視器記錄資料表參考](/azure/azure-monitor/reference/tables/tables-resourcetype)。

## <a name="see-also"></a>另請參閱

* 如需監視 Azure IoT 中樞的說明，請參閱 [監視 Azure IoT 中樞](monitor-iot-hub.md) 。
* 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器來監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)。