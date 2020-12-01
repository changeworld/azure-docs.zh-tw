---
title: 診斷記錄
titleSuffix: Azure Content Delivery Network
description: 瞭解如何使用 Azure 診斷記錄來儲存核心分析，讓您能夠從 Azure 內容傳遞網路端點匯出使用計量。
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: fdb609a243656e2c75159cd2d4e70e2f965ae896
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352111"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>診斷記錄-Azure 內容傳遞網路

透過 Azure 診斷記錄，您可以檢視核心分析，並將它們儲存到一或多個目的地，包括：

* Azure 儲存體帳戶
* Log Analytics 工作區
* Azure 事件中心

這項功能適用於所有定價層的 CDN 端點。 

診斷記錄可讓您將 CDN 端點的基本使用計量匯出至不同種類的來源，讓您可以使用自訂的方式來取用它們。 您可以執行下列類型的資料匯出：

* 匯出資料至 Blob 儲存體、匯出至 CSV，以及在 Excel 中產生圖表。
* 匯出資料至事件中樞，並將資料與其他 Azure 服務相互關聯。
* 將資料匯出至 Azure 監視器記錄，並在您自己的 Log Analytics 工作區中查看資料

需要有 Azure CDN 設定檔，才能執行下列步驟。 請參閱 [建立 AZURE CDN 設定檔和端點](cdn-create-new-endpoint.md) ，再繼續進行操作。

## <a name="enable-logging-with-the-azure-portal"></a>使用 Azure 入口網站啟用記錄

遵循下列步驟來為您的 Azure CDN 端點啟用記錄：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

2. 在 Azure 入口網站中，流覽至 **All resources**  ->  **您的 cdn 設定檔** 的所有資源

2. 選取您要啟用診斷記錄的 CDN 端點：

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="選取 CDN 端點。" border="true":::

3. 在 [**監視**] 區段中選取 **診斷記錄**：

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="選取 [診斷記錄]。" border="true":::

### <a name="enable-logging-with-azure-storage"></a>使用 Azure 儲存體來啟用記錄功能

若要使用儲存體帳戶來儲存記錄，請遵循下列步驟：

 >[!NOTE] 
 >需要儲存體帳戶才能完成這些步驟。 如需詳細資訊，請參閱： **[建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)** 。
    
1. 針對 [ **診斷設定名稱**]，輸入診斷記錄設定的名稱。
 
2. 選取 [封存至儲存體帳戶]，然後選取 [CoreAnalytics]。 

3. 針對 [保留期 (天數)]，選擇保留天數。 保留天數為 0 會無限期地儲存記錄。 

4. 選取記錄的訂用帳戶和儲存體帳戶。

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="診斷記錄-儲存體。" border="true":::

3. 選取 [儲存]。

### <a name="send-to-log-analytics"></a>傳送至 Log Analytics

若要針對記錄使用 Log Analytics，請遵循下列步驟：

>[!NOTE] 
>需要 log analytics 工作區才能完成這些步驟。 如需詳細資訊，請參閱： **[在 Azure 入口網站中建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)** 。
    
1. 針對 [ **診斷設定名稱**]，輸入診斷記錄設定的名稱。

2. 選取 [ **傳送至 Log Analytics**]，然後選取 [ **[coreanalytics**]。 

3. 選取記錄的訂用帳戶和 Log Analytics 工作區。

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="診斷記錄-Log Analytics。" border="true":::

4. 選取 [儲存]。

### <a name="stream-to-an-event-hub"></a>串流至事件中樞

若要針對記錄使用事件中樞，請遵循下列步驟：

>[!NOTE] 
>需要事件中樞才能完成這些步驟。 如需詳細資訊，請參閱： **[快速入門：使用 Azure 入口網站建立事件中樞](../event-hubs/event-hubs-create.md)** 。
    
1. 針對 [ **診斷設定名稱**]，輸入診斷記錄設定的名稱。

2. 選取 [ **串流至事件中樞**]，然後選取 [ **[coreanalytics**]。 

3. 選取記錄的訂用帳戶和事件中樞命名空間。

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="診斷記錄-事件中樞。" border="true":::

4. 選取 [儲存]。


## <a name="enable-logging-with-powershell"></a>使用 PowerShell 啟用記錄

下列範例說明如何透過 Azure PowerShell Cmdlet 啟用診斷記錄。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>在儲存體帳戶中啟用診斷記錄

1. 登入 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. 若要在儲存體帳戶中啟用診斷記錄，請輸入下列命令。 將變數取代為您的值：

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>啟用 Log Analytics 工作區的診斷記錄

1. 登入 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 若要啟用 Log Analytics 工作區的診斷記錄，請輸入下列命令。 將變數取代為您的值：

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>啟用事件中樞命名空間的診斷記錄

1. 登入 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 若要啟用 Log Analytics 工作區的診斷記錄，請輸入下列命令。 將變數取代為您的值：

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>從 Azure 儲存體取用診斷記錄
本節說明 CDN 核心分析的架構、Azure 儲存體帳戶中的組織，並提供可在 CSV 檔案中下載記錄的範例程式碼。

### <a name="using-microsoft-azure-storage-explorer"></a>使用 Microsoft Azure 儲存體總管
若要下載此工具，請參閱 [Azure 儲存體總管](https://storageexplorer.com/)。 下載並安裝軟體後，請將其設定為使用同一個已設定為 CDN 診斷記錄目的地的 Azure 儲存體帳戶。

1.  開啟 **Microsoft Azure 儲存體總管**
2.  找到儲存體帳戶
3.  展開此儲存體帳戶下方的 [Blob 容器] 節點。
4.  選取名為 *insights-logs-coreanalytics* 的容器。
5.  結果會顯示在右側窗格，開頭的第一層會顯示 *resourceId=*。 繼續選取每個層級，直到您找到 *PT1H.json* 檔案為止。 如需路徑的說明，請參閱 [Blob 路徑格式](cdn-azure-diagnostic-logs.md#blob-path-format)。
6.  每個 Blob *PT1H.json* 檔案均代表特定 CDN 端點或其自訂網域一小時的分析記錄。
7.  此 JSON 檔案內容的結構描述如＜核心分析記錄結構描述＞一節所述。


#### <a name="blob-path-format"></a>Blob 路徑格式

每個小時會產生 Core Analytics 記錄，且會收集資料並加以儲存在單一 Azure blob 作為 JSON 承載。 Storage explorer 工具會將 '/' 解讀為目錄分隔符號，並顯示階層。 Azure blob 的路徑看起來會像是階層式結構，並代表 blob 名稱。 此 blob 名稱會遵循下列命名慣例：    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**欄位說明：**

|值|說明|
|-------|---------|
|訂用帳戶識別碼    |使用 GUID 格式的 Azure 訂用帳戶識別碼。|
|資源群組名稱 |CDN 資源所屬資源群組的名稱。|
|設定檔名稱 |CDN 設定檔名稱|
|端點名稱 |CDN 端點名稱|
|Year|  四位數的年份表示法，例如 2017|
|Month| 兩位數的月份表示法。 01 = 一月 .。。12 = 十二月|
|天|   兩位數的當月日期表示法|
|PT1H.json| 儲存分析資料的實際 JSON 檔案|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>將 Core Analytics 資料匯出至 CSV 檔案

若要存取核心分析，可提供工具的範例程式碼。 此工具可讓您將 JSON 檔案下載至以逗號分隔的一般檔案格式，用來建立圖表或其他彙總。

以下為使用此工具的方式：

1.  造訪 GitHub 連結： [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  下載程式碼。
3.  依照指示編譯與設定。
4.  執行工具。
5.  產生的 CSV 檔案會以簡單的平面階層顯示分析資料。

## <a name="log-data-delays"></a>記錄資料延遲

下表顯示 **來自 Microsoft 的標準 Azure CDN**、**來自 Akamai 的標準 Azure CDN** 和 **來自 Verizon 的標準 Azure CDN** 的記錄資料延遲。

Microsoft 記錄資料延遲 | Verizon 記錄資料延遲 | Akamai 記錄資料延遲
--- | --- | ---
延遲 1 小時。 | 延遲 1 小時，而且在端點傳播完成後需花費最多 2 小時才會開始出現。 | 延遲 24 小時，如果資料是在 24 小時前建立的，則需花費最多 2 小時才會開始出現。 如果記錄是剛建立的，則最多需要 25 小時才會開始出現。

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN Core Analytics 的診斷記錄類型

Microsoft 目前僅提供核心分析記錄，其中包含的計量會顯示 HTTP 回應統計資料和輸出統計資料，如從 CDN POP/邊緣所見。

### <a name="core-analytics-metrics-details"></a>Core Analytics 計量詳細資料
下表顯示核心分析記錄中可用的計量清單：

* **來自 Microsoft 的 Azure CDN 標準**
* **來自 Akamai 的 Azure CDN 標準**
* **來自 Verizon 的 Azure CDN Standard/Premium**

並非所有提供者的所有計量皆可用，雖然這樣的差異極少。 下表也顯示提供者是否提供指定的計量。 計量僅適用於有流量的 CDN 端點。


|Metric                     | 描述 | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | 這段期間要求命中總數。 | 是 | 是 |是 |
| RequestCountHttpStatus2xx | 產生 2xx HTTP 代碼 (例如 200、202) 的所有要求計數。 | 是 | 是 |是 |
| RequestCountHttpStatus3xx | 產生 3xx HTTP 代碼 (例如 300、302) 的所有要求計數。 | 是 | 是 |是 |
| RequestCountHttpStatus4xx | 產生 4xx HTTP 代碼 (例如 400、404) 的所有要求計數。 | 是 | 是 |是 |
| RequestCountHttpStatus5xx | 產生 5xx HTTP 代碼 (例如 500、504) 的所有要求計數。 | 是 | 是 |是 |
| RequestCountHttpStatusOthers | 所有其他 HTTP 代碼 (2xx-5xx 以外) 的計數。 | 是 | 是 |是 |
| RequestCountHttpStatus200 | 產生 200 HTTP 代碼回應的所有要求計數。 | 是 | 否  |是 |
| RequestCountHttpStatus206 | 產生 206 HTTP 代碼回應的所有要求計數。 | 是 | 否  |是 |
| RequestCountHttpStatus302 | 產生 302 HTTP 代碼回應的所有要求計數。 | 是 | 否  |是 |
| RequestCountHttpStatus304 | 產生 304 HTTP 代碼回應的所有要求計數。 | 是 | 否  |是 |
| RequestCountHttpStatus404 | 產生 404 HTTP 代碼回應的所有要求計數。 | 是 | 否  |是 |
| RequestCountCacheHit | 產生快取命中之所有要求的計數。 資產是從 POP 直接提供給用戶端。 | 是 | 是 | 否  |
| RequestCountCacheMiss | 產生快取遺漏之所有要求的計數。 快取遺漏表示在最接近用戶端的 POP 上找不到資產，且已從來源抓取。 | 是 | 是 | 否 |
| RequestCountCacheNoCache | 因為邊緣上的使用者設定，而無法快取的資產所有要求計數。 | 是 | 是 | 否 |
| RequestCountCacheUncacheable | 資產的 Cache-Control 和 Expires 標頭無法快取的所有資產要求計數。 此計數表示不應在 POP 上或由 HTTP 用戶端快取它。 | 是 | 是 | 否 |
| RequestCountCacheOthers | 非上述快取狀態的所有要求計數。 | 否 | 是 | 否  |
| EgressTotal | 輸出資料傳輸 (單位 GB) | 是 |是 |是 |
| EgressHttpStatus2xx | 狀態代碼為 2xx HTTP 之回應的輸出資料傳輸* (單位為 GB)。 | 是 | 是 | 否  |
| EgressHttpStatus3xx | 狀態代碼為 3xx HTTP 之回應的輸出資料傳輸 (單位為 GB)。 | 是 | 是 | 否  |
| EgressHttpStatus4xx | 狀態代碼為 4xx HTTP 之回應的輸出資料傳輸 (單位為 GB)。 | 是 | 是 | 否  |
| EgressHttpStatus5xx | 狀態代碼為 5xx HTTP 之回應的輸出資料傳輸 (單位為 GB)。 | 是 | 是 | 否 |
| EgressHttpStatusOthers | 狀態代碼為其他 HTTP 之回應的輸出資料傳輸 (單位為 GB)。 | 是 | 是 | 否  |
| EgressCacheHit | 直接從 CDN POP/邊緣上 CDN 快取所傳遞回應的輸出資料傳輸。 | 是 | 是 | 否 |
| EgressCacheMiss。 | 在最接近的 POP 伺服器上找不到回應的輸出資料傳輸，並從源伺服器抓取。 | 是 | 是 | 否 |
| EgressCacheNoCache | 因為邊緣上的使用者設定，而無法快取的資產輸出資料傳輸。 | 是 | 是 | 否 |
| EgressCacheUncacheable | 無法由資產的 Cache-Control 和或 Expires 標頭快取的資產輸出資料傳輸。 指出不應該在 POP 上或由 HTTP 用戶端快取它。 | 是 | 是 | 否 |
| EgressCacheOthers | 其他快取案例的輸出資料傳輸。 | 否 | 是 | 否 |

*輸出資料傳輸是指從 CDN POP 伺服器傳遞到用戶端的流量。


### <a name="schema-of-the-core-analytics-logs"></a>Core Analytics 記錄結構描述 

所有記錄都以 JSON 格式儲存，且每個項目都有根據下列結構描述的字串欄位：

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

其中 time 代表報告某段時間統計資料時該時間範圍的開始時間。 CDN 提供者不支援的計量，而不是雙精度浮點數或整數值，因此會產生 null 值。 此 null 值表示沒有計量，且與 0 值不同。 您可以在端點上設定每個網域的其中一組計量。

範例屬性︰

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>其他資源

* [Azure 診斷記錄](../azure-monitor/platform/platform-logs-overview.md)
* [分析 Azure CDN 使用模式](./cdn-analyze-usage-patterns.md)
* [Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md)
* [Azure Log Analytics REST API](/rest/api/loganalytics)