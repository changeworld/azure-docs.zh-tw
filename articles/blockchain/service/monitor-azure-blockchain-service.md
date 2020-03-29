---
title: 監控 Azure 區塊鏈服務 （ABS）
description: 通過 Azure 監視器監視 Azure 區塊鏈服務
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293244"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>通過 Azure 監視器監視 Azure 區塊鏈服務  

當客戶在 Azure 區塊鏈服務 （ABS） 上運行生產級區塊鏈方案時，監視資源的可用性、性能和操作就變得至關重要。 本文介紹了 Azure 區塊鏈服務生成的監視資料，以及如何使用 Azure 監視器的各種功能和集成來分析和警報，以管理生產級環境。  

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？

Azure 區塊鏈服務使用 Azure 監視器創建監視資料，Azure 監視器是 Azure 中的完整堆疊監視服務，提供一整套功能來監視 Azure 資源。 有關 Azure 監視器的詳細資訊，請參閱[使用 Azure 監視器監視 Azure 資源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)。
 

以下各節通過描述從 Azure 區塊鏈服務收集的特定資料，並提供用於使用 Azure 工具配置資料收集和分析此資料的示例，從而構建本文。

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>監控從 Azure 區塊鏈服務收集的資料  

Azure 區塊鏈服務收集與其他 Azure 資源相同的監視資料，這在監視 Azure 資源[中](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)描述。 有關 Azure 區塊鏈服務創建的日誌和指標的詳細資訊，請參閱[監視 Azure 區塊鏈服務資料參考](#monitor-azure-blockchain-service-data-reference)。

每個 Azure 區塊鏈服務成員資源的 Azure 門戶中的概述頁包括事務的簡短視圖，包括處理和處理的請求塊。 其中一些資料會自動收集，並在創建 Azure 區塊鏈服務成員資源後可供分析，同時可以通過其他配置啟用其他資料收集。

## <a name="diagnostic-settings"></a>診斷設定  

平臺指標和活動日誌會自動收集，但必須創建診斷設置以收集資源日誌或在 Azure 監視器之外轉發它們。 有關使用 Azure 門戶、CLI 或 PowerShell 創建診斷設置的詳細過程，請參閱[創建診斷設置以在 Azure 中收集平臺日誌和指標](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)。

創建診斷設置時，可以指定要收集的日誌類別。 Azure 區塊鏈服務的類別如下。

**區塊鏈代理日誌**– 如果要監視 NGNIX 代理日誌，請選擇類別。 所有客戶交易記錄詳細資訊都可用於審核和調試目的。  

**區塊鏈應用程式日誌**– 選擇類別來獲取託管服務託管的區塊鏈應用程式的日誌。 例如，對於 ABS-Quorum 成員，這些日誌將是來自 Quorum 本身的日誌。  

**指標請求**：選擇從 Azure Cosmos DB 到診斷設置中的目標的指標資料的選項，該選項在 Azure 指標中自動收集。 使用資源日誌收集指標資料，以同時分析這兩種資料，並將指標資料發送到 Azure 監視器之外。

## <a name="analyze-metric-data"></a>分析指標資料  

您可以使用指標資源管理器分析 Azure 區塊鏈服務的指標，在 ABS 資源邊欄選項卡中的"監視"部分下導航到指標選項卡。 有關使用該工具的詳細資訊[，請參閱使用 Azure 指標資源管理器入門](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)。 Azure 區塊鏈服務的完整指標位於命名空間 Azure 區塊鏈服務標準指標中。

在添加篩選器或拆分指標時，可以使用**節點**維度，指標基本上提供每個事務節點和 ABS 成員的驗證器節點的指標值。

## <a name="analyze-log-data"></a>分析記錄資料

以下是一些查詢，您可以在日誌搜索欄中輸入這些查詢，以説明您監視 Azure 區塊鏈服務成員。 這些查詢使用[新語言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

要查詢區塊鏈應用程式日誌中的錯誤條件，請使用以下查詢：

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

要查詢區塊鏈代理日誌中的錯誤條件，請使用以下查詢  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
可以使用 Azure 日誌中可用的時間篩選器來篩選特定時間範圍的查詢。

## <a name="monitor-azure-blockchain-service-data-reference"></a>監控 Azure 區塊鏈服務資料引用  

本文提供了為分析 Azure 區塊鏈服務的性能和可用性而收集的日誌和指標資料的參考。  

### <a name="resource-logs"></a>資源記錄

所有資源日誌共用一個頂級通用架構，其中很少有特定于區塊鏈服務的唯一屬性。 您可以參考文章[頂級資源日誌架構](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)，下面介紹 Azure 區塊鏈服務特定屬性的詳細資訊  

下表列出了 Azure 區塊鏈代理日誌在 Azure 監視器日誌或 Azure 存儲中收集時的屬性。  


| 屬性名稱  | 描述 |
|:---|:---|
| time | 作業發生的日期和時間 (UTC)。 |
| 資源識別碼  | 為其啟用日誌的 Azure 區塊鏈服務資源。  |
| category  |對於 Azure 區塊鏈服務，可能的值是**代理日誌**和**應用程式日誌**。 |
| operationName  | 此事件所代表的作業名稱。   |
| 記錄層級  | 預設情況下，Azure 區塊鏈服務啟用**資訊日誌**級別。   |
| 節點位置  | 部署區塊鏈成員的 Azure 區域。  |
| 區塊鏈節點名稱  | 執行操作的 Azure 區塊鏈服務成員節點的名稱。   |
| EthMethod  | 該方法，由底層區塊鏈協定調用，在Quorum中，它可以eth_sendTransactions，eth_getBlockByNumber等。  |
| 代理程式  | 代表使用者代理的使用者代理，例如 Web 瀏覽器 Mozilla、Edge 等。值的示例包括："Mozilla/5.0 （Linux x64） 節點.js/8.16.0 v8/6.2.414.77"  |
| 程式碼   | HTTP 錯誤代碼。 通常 4XX 和 5XX 是錯誤條件。  |
| 節點主機  | 節點的 DNS 名稱。   |
| 請求方法名稱 | 稱為 HTTP 方法，此處的可能值為"為創建成員的 PUT"，用於獲取現有成員詳細資訊的 GET，刪除用於刪除成員的刪除，用於更新成員的 PATCH。   |
| 區塊鏈會員名稱  | 使用者提供的 Azure 區塊鏈服務成員名稱。  |
| 聯盟 | 使用者提供的聯合體的名稱。   |
| 遠端  | 請求即將到的用戶端的 IP。  |
| RequestSize  | 以位元組為單位發出的請求的大小。  |
| 請求時間  | 請求的持續時間（以毫秒為單位）。|




下表列出了 Azure 區塊鏈應用程式日誌的屬性。


| 屬性名稱  | 描述 |
|:---|:---|
| time | 作業發生的日期和時間 (UTC)。 |
| 資源識別碼  | 為其啟用日誌的 Azure 區塊鏈服務資源。|
| category  |對於 Azure 區塊鏈服務，可能的值是**代理日誌**和**應用程式日誌**。  |
| operationName  | 此事件所代表的作業名稱。   |
| 記錄層級  | 預設情況下，Azure 區塊鏈服務啟用**資訊日誌**級別。   |
| 節點位置  | 部署區塊鏈成員的 Azure 區域。  |
| 區塊鏈節點名稱  | 執行操作的 Azure 區塊鏈服務成員節點的名稱。   |
| 區塊鏈消息    | 此欄位將包含資料普通日誌的區塊鏈應用程式日誌。 對於 ABS-Quorum，這將具有仲裁記錄檔。 它包含有關日誌項目類型的資訊，它是資訊性、錯誤、警告和提供有關所執行操作的詳細資訊的字串。   |
| TenantID    | Azure 區塊鏈服務的特定于區域的租戶。 此欄位的格式為https://westlake-rp-prod。<region>.cloudapp.azure.com區域指定已部署的成員的 Azure 區域。       |
| SourceSystem   | 系統填充日誌，在這種情況下，它是**Azure**。    |



### <a name="metrics"></a>計量

下表列出了為 Azure 區塊鏈服務收集的平臺指標。 所有指標都存儲在命名空間 Azure**區塊鏈服務**標準指標中。

有關所有 Azure 監視器支援指標的清單（包括 Azure 區塊鏈服務），請參閱[Azure 監視器支援的指標](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)。

### <a name="blockchain-metrics"></a>區塊鏈指標

下表指定為 Azure 區塊鏈服務成員資源收集的區塊鏈指標清單。


| 度量名稱 | 單位  |  彙總類型| 描述   |
|---|---|---|---|
| 待處理事務   | Count  |  Average | 等待開採的事務數。   |
| 已處理的塊   | Count  | Sum  |  在每個時間間隔內處理的塊數。 目前塊大小為 5 秒，因此在一分鐘內每個節點將在 5 分鐘內處理 12 個塊和 60 個塊。   |
|已處理的交易    | Count  | Sum  | 塊中處理的事務數。    |
|排隊的事務    |  Count | Average  | 無法立即挖掘的交易記錄數。 這可能是因為他們到達順序，未來一個正在等待以前的事務到達。 或者，它可以是兩個事務具有相同的數位，只使用一次 （nonce） 和相同的氣體值，因此第二個事務不能開採。   |

### <a name="connection-metrics"></a>連接計量  

下表列出了為 Azure 區塊鏈服務成員資源收集的不同連接指標。 這些是 NGINX 代理指標。


| 度量名稱 | 單位  |  彙總類型| 描述 |
|---|---|---|---|
| 已接受連接   | Count  |  Sum | 接受的用戶端連接的總數。   |
| 作用中的連線  | Count  | Average  |  當前活動用戶端連接數，包括等待連接。    |
|已處理的連接    | Count  | Sum  | 已處理的連接總數。 通常，除非達到某些資源限制，否則參數值與接受的連接相同。     |
|已處理的請求     |  Count | Sum  | 用戶端請求的總數。  |


### <a name="performance-metrics"></a>效能度量

下表列出了為 Azure 區塊鏈成員資源的每個節點收集的性能指標。  


| 度量名稱 | 單位  |  彙總類型| 描述   |
|---|---|---|---|
| CPU 使用率百分比   | 百分比  |  最大值 | CPU 使用率的百分比。     |
| IO 讀取位元組   | KB   | Sum  |  跨區塊鏈成員資源所有節點的 IO 讀取位元組的總和。      |
|IO 寫入位元組     | KB   | Sum  | 跨區塊鏈成員資源的所有節點的 IO 寫入位元組的總和。     |
|記憶體限制       |  G   | Average    | 每個節點的區塊鏈進程的最大記憶體。 |
|記憶體使用量     | G  |  Average | 所有節點的平均使用的記憶體量。  |
| 記憶體使用百分比     | 百分比   | Average  |  在所有節點上平均使用的記憶體百分比。       |
|存儲使用方式      | G   | Average  | 在所有節點上平均使用的存儲 GB。       |


## <a name="next-steps"></a>後續步驟

詳細瞭解[區塊鏈資料管理器](https://docs.microsoft.com/azure/blockchain/service/data-manager)，以捕獲區塊鏈資料並將其轉換為 Azure 事件網格。
