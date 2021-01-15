---
title: 串流內嵌事件來源-Azure 時間序列深入解析 Gen2 |Microsoft Docs
description: 瞭解如何將資料串流至 Azure 時間序列深入解析 Gen2。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: ee13b2fbe4abbaf9bddf4975f8e25d746dc78f5e
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232177"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure 時間序列深入解析 Gen2 事件來源

 您的 Azure 時間序列深入解析 Gen2 環境最多可有兩個串流事件來源。 支援兩種類型的 Azure 資源作為輸入：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中樞](../event-hubs/event-hubs-about.md)

事件必須以 UTF-8 編碼的 JSON 形式傳送。

## <a name="create-or-edit-event-sources"></a>建立或編輯事件來源

您的事件來源資源 (s) 可以與 Azure 時間序列深入解析 Gen2 環境或不同的訂用帳戶存留在相同的 Azure 訂用帳戶中。您可以使用 [Azure 入口網站](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment)、 [Azure CLI](https://github.com/Azure/azure-cli-extensions/tree/master/src/timeseriesinsights)、 [ARM 範本](time-series-insights-manage-resources-using-azure-resource-manager-template.md)和 [REST API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) 來建立、編輯或移除您環境的事件來源。

當您連線到事件來源時，您的 Azure 時間序列深入解析 Gen2 環境會從最舊的事件開始，讀取目前儲存在 Iot 或事件中樞中的所有事件。

> [!IMPORTANT]
>
> - 將事件來源附加至 Azure 時間序列深入解析 Gen2 環境時，您可能會遇到高初始延遲的情況。
> - 事件來源延遲取決於目前在 IoT 中樞或事件中樞內的事件數目。
> - 在第一次擷取事件來源資料之後，將會減少高延遲。 如果您遭遇持續高延遲的情況，請透過 Azure 入口網站提交支援票證。

## <a name="streaming-ingestion-best-practices"></a>串流內嵌最佳作法

- 一律為您的 Azure 時間序列深入解析 Gen2 環境建立唯一的取用者群組，以取用來自事件來源的資料。 重複使用取用者群組可能會造成隨機中斷連線，而且可能會導致資料遺失。

- 在相同的 Azure 區域中，設定您的 Azure 時間序列深入解析 Gen2 環境和 IoT 中樞和/或事件中樞。 雖然您可以在不同的區域中設定事件來源，但不支援此案例，我們無法保證高可用性。

- 請勿超過您環境的 [輸送量速率限制](./concepts-streaming-ingress-throughput-limits.md) 或每個分割區的限制。

- 如果您的環境在處理資料時遇到問題，請設定延遲 [警示](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) 。 請參閱下方的 [生產工作負載](./concepts-streaming-ingestion-event-sources.md#production-workloads) ，以取得建議的警示狀況。 

- 僅針對近乎即時和最近的資料使用串流擷取，不支援串流歷程記錄資料。

- 瞭解如何將屬性（property），以及如何將 JSON [資料壓平合併和儲存。](./concepts-json-flattening-escaping-rules.md)

- 提供事件來源連接字串時，請遵循最低許可權的原則。 針對事件中樞，請只使用 *傳送* 宣告來設定共用存取原則，而針對 IoT 中樞，請只使用 *服務連接* 許可權。

## <a name="production-workloads"></a>生產工作負載

除了上述的最佳作法之外，建議您針對商務關鍵工作負載執行下列各項。 

- 將您的 IoT 中樞或事件中樞資料保留時間增加到最多7天。

- 在 Azure 入口網站中建立環境警示。 以平臺 [計量](https://docs.microsoft.com/azure/time-series-insights/how-to-monitor-tsi-reference#metrics) 為基礎的警示可讓您驗證端對端管線行為。 [此處](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency#monitor-latency-and-throttling-with-alerts)提供建立和管理警示的指示。 建議的警示條件：

     - IngressReceivedMessagesTimeLag 超過5分鐘
     - IngressReceivedBytes 為0
- 在 IoT 中樞或事件中樞磁碟分割之間保持內嵌負載平衡。

### <a name="historical-data-ingestion"></a>歷程記錄資料擷取

Azure 時間序列深入解析的 Gen2 目前不支援使用串流管線來匯入歷程記錄資料。 如果您需要將過去的資料匯入至您的環境，請遵循下列指導方針：

- 請不要以平行方式串流即時和歷程記錄資料。 擷取不按順序的資料會導致查詢效能降低。
- 依時間排序的方式擷取歷程記錄資料，以獲得最佳效能。
- 保持在下列的擷取輸送量速率限制內。
- 如果資料比您的溫存放區保留期間還舊，請停用溫存放區。

## <a name="event-source-timestamp"></a>事件來源時間戳記

設定事件來源時，系統會要求您提供時間戳記識別碼屬性。 時間戳記屬性是用來追蹤一段時間的事件，這是用來做為 $event 的時間。 $ts 在 [查詢 api](/rest/api/time-series-insights/dataaccessgen2/query/execute) 中，以及用於 Azure 時間序列深入解析 Explorer 中的繪圖數列。 如果在建立時未提供任何屬性，或事件中缺少時間戳記屬性，則會使用事件的 IoT 中樞或事件中樞排入佇列的時間作為預設值。 時間戳記屬性值會以 UTC 格式儲存。

一般情況下，使用者會選擇自訂時間戳記屬性，並使用感應器或標記產生讀取的時間，而不是使用預設的中樞排入佇列時間。 當裝置有間歇性的連線遺失，且延遲的訊息批次轉送到 Azure 時間序列深入解析 Gen2 時，這是特別必要的。

如果您的自訂時間戳記是在嵌套的 JSON 物件或陣列中，則您必須在簡維 [和將命名慣例](concepts-json-flattening-escaping-rules.md)之後提供正確的屬性名稱。 例如， [此處](concepts-json-flattening-escaping-rules.md#example-a) 所示之 JSON 承載的事件來源時間戳記應輸入為 `"values.time"` 。

### <a name="time-zone-offsets"></a>時區位移

時間戳記必須以 ISO 8601 格式傳送，而且會以 UTC 格式儲存。 如果提供了時區位移，則會套用位移，然後以 UTC 格式儲存和傳回時間。 如果位移的格式不正確，則會被忽略。 在您的方案可能沒有原始位移內容的情況下，您可以在其他個別的事件屬性中傳送位移資料，以確保它會保留下來，且您的應用程式可以在查詢回應中參考。

時區位移應設定為下列其中一項：

± HHMMZ</br>
± HH： MM</br>
± HH： MMZ</br>

## <a name="next-steps"></a>後續步驟

- 閱讀 JSON 簡維 [和轉義規則](./concepts-json-flattening-escaping-rules.md) ，以瞭解事件的儲存方式。

- 瞭解您環境的 [輸送量限制](./concepts-streaming-ingress-throughput-limits.md)