---
title: 監視時間序列深入解析 |Microsoft Docs
description: 監視適用于可用性、效能和操作的時間序列深入解析。
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: cff0c54cf5aa8854273be9502f5cf6df4e0a055b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632628"
---
# <a name="monitoring-time-series-insights"></a>監視時間序列深入解析

當您有依賴 Azure 資源的重要應用程式和商務程序時，您會想要監視這些資源的可用性、效能和操作。 本文說明時間序列深入解析所產生的監視資料，以及您可以如何使用 Azure 監視器的功能來分析此資料併發出警示。

## <a name="monitor-overview"></a>監視概觀

每個時間序列深入解析環境的 Azure 入口網站中的 [ **總覽** ] 頁面包含資源使用量的簡短觀點，例如接收的訊息數目和儲存的位元組數目。 這項資訊很有説明，不過這個窗格中只會提供少量的監視資料。 這部分資料會自動收集，並在您建立資源時立即可供分析。 您可使用一些設定來啟用其他類型的資料收集。

## <a name="what-is-azure-monitor"></a>什麼是 Azure 監視器

時間序列深入解析會使用 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)建立監視資料，這是 Azure 中的完整堆疊監視服務，除了其他雲端和內部部署中的資源之外，還提供一組完整的功能來監視您的 azure 資源。

請從 [使用 Azure 監視器來監視 Azure 資源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)的文章開始，其中說明下列概念：

- Azure 監視器是什麼？
- 與監視相關聯的成本
- 在 Azure 中收集的監視資料
- 設定資料收集
- Azure 中用來分析和警示監視資料的標準工具

下列各節將說明針對 Azure 時間序列深入解析所收集的特定資料，在本文中建立。 這些章節也會提供範例，說明如何使用 Azure 工具來設定資料收集和分析此資料。

> [!TIP]
> 若要瞭解與 Azure 監視器相關聯的成本，請參閱 [使用量和估計成本](../azure-monitor/platform/usage-estimated-costs.md)。 若要瞭解您的資料在 Azure 監視器中顯示所花費的時間，請參閱 [記錄資料內嵌時間](../azure-monitor/platform/data-ingestion-time.md)。

## <a name="monitoring-data-from-azure-time-series-insights"></a>監視 Azure 時間序列深入解析的資料

Azure 時間序列深入解析會收集與 [azure 資源監視資料](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 資源相同的監視資料類型。 

如需您可以收集之記錄和計量的詳細參考，請參閱 [Azure 時間序列深入解析監視資料參考](how-to-monitor-tsi-reference.md) 。

## <a name="collection-and-routing"></a>收集和路由

系統會自動收集和儲存平臺計量，但可使用診斷設定將其路由至其他位置。

在您建立診斷設定並將其路由傳送至一或多個位置之前，不會收集並儲存資源記錄。
如需使用 Azure 入口網站、CLI 或 PowerShell 建立診斷設定的詳細程式，請參閱 [建立診斷設定以收集 Azure 中的平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 。 當您建立診斷設定時，可以指定要收集的記錄類別。

您可以從下列類別收集 Azure 時間序列深入解析的記錄：

   | 類別 | 描述 |
   |---|---|
   | 輸入  | 輸入類別會追蹤輸入管線中發生的錯誤。 這個類別包含接收事件時所發生的錯誤 (例如，連接到事件來源) 和處理事件時所發生的錯誤 (例如剖析事件承載) 時發生錯誤。 |

## <a name="analyzing-metrics"></a>分析計量

您可以從 [Azure 監視器] 功能表開啟計量，以分析 Azure 時間序列深入解析的計量，以及來自其他 Azure 服務的度量。 如需使用此工具的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)。

如需所收集的平臺度量清單，請參閱 [監視 Azure 時間序列深入解析資料參考](how-to-monitor-tsi-reference.md#metrics)

此範例顯示從所有事件來源接收到 Azure 時間序列深入解析環境的位元組計數。

輸入 **接收位元組數** [ ![ Azure 時間序列輸入接收位元組](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

此範例會顯示已成功處理且可在 Azure 時間序列深入解析環境中進行查詢的位元組計數。

輸入 **預存位元組** [ ![ Azure 時間序列輸入儲存的位元組](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>分析記錄
您可以儲存體帳戶中的 Blob 形式、以事件資料形式，或透過 Log Analytics 查詢來存取資源記錄。

Azure 監視器記錄中的資料會儲存在資料表中，其中每個資料表都有一組專屬的唯一屬性。

Azure 監視器中的所有資源記錄都有相同的欄位，後面接著服務特定的欄位。 一般架構 [Azure 監視器資源記錄架構](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema)中所述。 如需針對 Azure 時間序列深入解析收集的資源記錄類型清單，請參閱 [Azure 時間序列深入解析監視資料參考](how-to-monitor-tsi-reference.md#resource-logs)。

Azure 時間序列深入解析會將資料儲存在下列資料表中。

| Table | 描述 |
|:---|:---|
| TSIIngress | 從輸入分類儲存資料的資料表。 輸入類別會追蹤輸入管線中發生的錯誤。 這個類別包含接收事件時所發生的錯誤 (例如，連接到事件來源) 和處理事件時所發生的錯誤 (例如剖析事件承載) 時發生錯誤。

若要將資料路由傳送至 Azure 監視器記錄，您必須建立診斷設定，以將資源記錄或平臺計量傳送至 Log Analytics 工作區。 若要深入瞭解，請參閱 [收集和路由](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing)。

## <a name="sample-queries"></a>範例查詢

以下是您可以用來協助監視 Azure 時間序列深入解析環境的查詢：

+ 取得過去五天內事件來源連接失敗的詳細資料：

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ 取得過去五天內收到的無效訊息詳細資料：

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>警示

當您在監視資料中找到重要的條件時，Azure 監視器警示會主動通知您。 它們可讓您識別並解決您的系統中的問題，然後客戶才會注意到這些問題。 您可以設定 [計量](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview)、 [記錄](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)和 [活動記錄](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts)的警示。 不同類型的警示有其優點和缺點。

根據平臺計量建立警示規則時，請注意，對於以計數單位收集的時間序列深入解析平臺計量，某些匯總可能無法使用或無法使用。

## <a name="next-steps"></a>後續步驟

* 如需 Azure 時間序列深入解析所建立之記錄和計量的參考，請參閱 [Azure 時間序列深入解析監視資料參考](how-to-monitor-tsi-reference.md) 。
* 如需監視 Azure 資源的詳細資訊，請參閱[使用 Azure 監視器來監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)。
