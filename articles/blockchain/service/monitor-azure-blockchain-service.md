---
title: 監視 Azure 區塊鏈 Service （ABS）
description: 透過 Azure 監視器監視 Azure 區塊鏈服務
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76293244"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>透過 Azure 監視器監視 Azure 區塊鏈服務  

當客戶在 Azure 區塊鏈 Service （ABS）上執行生產等級區塊鏈案例時，監視資源的可用性、效能和作業就變得很重要。 本文說明 Azure 區塊鏈 Service 所產生的監視資料，以及如何使用 Azure 監視器的各種功能和整合來分析和警示，以管理生產等級環境。  

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？

Azure 區塊鏈 Service 會使用 Azure 監視器建立監視資料，這是 Azure 中的完整堆疊監視服務，可提供一組完整的功能來監視您的 Azure 資源。 如需 Azure 監視器的詳細資訊，請參閱[使用 Azure 監視器監視 Azure 資源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)。
 

下列各節將描述從 Azure 區塊鏈 Service 收集的特定資料，並提供使用 Azure tools 設定資料收集和分析此資料的範例，以本文為您所建立。

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>監視從 Azure 區塊鏈 Service 收集的資料  

Azure 區塊鏈服務會收集與其他 Azure 資源相同類型的監視資料，如從 Azure 資源[監視資料](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)中所述。 如需 Azure 區塊鏈 Service 所建立之記錄和計量的詳細參考，請參閱[監視 Azure 區塊鏈 Service 資料參考](#monitor-azure-blockchain-service-data-reference)。

每個 Azure 區塊鏈服務成員資源的 Azure 入口網站中的 [總覽] 頁面包含交易的簡要觀點，包括要求已處理和已處理的區塊。 當您建立 Azure 區塊鏈服務成員資源時，會自動收集這項資料並可供分析，而您可以使用其他設定來啟用其他資料收集。

## <a name="diagnostic-settings"></a>診斷設定  

系統會自動收集平臺計量和活動記錄檔，但您必須建立診斷設定來收集資源記錄，或將它們轉送到 Azure 監視器外部。 如需使用 Azure 入口網站、CLI 或 PowerShell 建立診斷設定的詳細程式，請參閱[建立診斷設定以收集 Azure 中的平臺記錄和計量](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)。

當您建立診斷設定時，可以指定要收集的記錄類別。 Azure 區塊鏈服務的類別如下所示。

**區塊鏈 proxy 記錄**-如果您想要監視 NGNIX proxy 記錄，請選取 [類別]。 所有客戶交易詳細資料都適用于 audit 和 debug 用途。  

**區塊鏈應用程式記錄**–選取類別以取得受控服務所裝載之區塊鏈應用程式的記錄。 例如，針對 ABS 仲裁成員，這些記錄會是來自仲裁本身的記錄。  

計量**要求**：選取選項，將計量資料從 Azure Cosmos DB 收集到診斷設定中的目的地，這會在 Azure 計量中自動收集。 使用資源記錄收集計量資料，以同時分析兩種資料，並在 Azure 監視器外部傳送計量資料。

## <a name="analyze-metric-data"></a>分析度量資料  

您可以使用計量瀏覽器來分析 Azure 區塊鏈 Service 的計量，流覽至 ABS 資源分頁中 [監視] 區段底下的 [計量] 索引標籤。 如需使用此工具的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)。 Azure 區塊鏈 Service 的完整計量位於命名空間 Azure 區塊鏈 Service 標準計量中。

新增篩選或分割計量時，您可以使用**節點**維度，這基本上會提供每個交易節點的度量值，以及 ABS 成員的驗證器節點。

## <a name="analyze-log-data"></a>分析記錄資料

以下是一些您可以在記錄搜尋列中輸入的查詢，以協助您監視您的 Azure 區塊鏈服務成員。 這些查詢使用[新語言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

若要查詢區塊鏈應用程式記錄檔中的錯誤狀況，請使用下列查詢：

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

若要查詢區塊鏈 proxy 記錄中的錯誤狀況，請使用下列查詢  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
您可以使用 Azure 記錄中提供的時間篩選準則來篩選特定時間範圍內的查詢。

## <a name="monitor-azure-blockchain-service-data-reference"></a>監視 Azure 區塊鏈 Service 資料參考  

本文提供收集的記錄和計量資料的參考，以分析 Azure 區塊鏈 Service 的效能和可用性。  

### <a name="resource-logs"></a>資源記錄

所有資源記錄都會共用最上層的通用架構，其中包含區塊鏈服務特有的幾個唯一屬性。 您可以參閱[最上層資源記錄架構一](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)文，以下涵蓋 Azure 區塊鏈服務特定屬性的詳細資料  

下表列出 Azure 區塊鏈 proxy 記錄檔在 Azure 監視器記錄或 Azure 儲存體中收集時的屬性。  


| 屬性名稱  | 描述 |
|:---|:---|
| time | 作業發生的日期和時間 (UTC)。 |
| resourceID  | 啟用記錄的 Azure 區塊鏈服務資源。  |
| category  |針對 Azure 區塊鏈 Service，可能的值為**Proxylogs**和**Applicationlogs**。 |
| operationName  | 此事件所代表的作業名稱。   |
| 記錄層級  | 根據預設，Azure 區塊鏈 Service 會啟用**資訊**記錄層級。   |
| NodeLocation  | 部署區塊鏈成員的 Azure 區域。  |
| BlockchainNodeName  | 執行作業所在之 Azure 區塊鏈服務成員的節點名稱。   |
| EthMethod  | 基礎區塊鏈通訊協定在仲裁中呼叫的方法，可能會 eth_sendTransactions、eth_getBlockByNumber 等。  |
| 代理程式  | 代表使用者採取行動的使用者代理程式，例如網頁瀏覽器 Mozilla、Edge 等等。值的範例包括： "Mozilla/5.0 （Linux x64） node.js/8.16.0 v8/6.2.414.77"  |
| 程式碼   | HTTP 錯誤碼。 4XX 和5XX 通常是錯誤狀況。  |
| NodeHost  | 節點的 DNS 名稱。   |
| RequestMethodName | 呼叫 HTTP 方法，這裡的可能值為 create member、取得現有成員的詳細資料、刪除成員的刪除，以及更新成員的修補。   |
| BlockchainMemberName  | 使用者所提供的 Azure 區塊鏈服務成員名稱。  |
| 聯盟 | 使用者所提供的聯盟名稱。   |
| 遠端  | 要求的來源用戶端 IP。  |
| RequestSize  | 以位元組為單位提出的要求大小。  |
| RequestTime  | 要求的持續時間（以毫秒為單位）。|




下表列出 Azure 區塊鏈應用程式記錄的屬性。


| 屬性名稱  | 描述 |
|:---|:---|
| time | 作業發生的日期和時間 (UTC)。 |
| resourceID  | 啟用記錄的 Azure 區塊鏈服務資源。|
| category  |針對 Azure 區塊鏈 Service，可能的值為**Proxylogs**和**Applicationlogs**。  |
| operationName  | 此事件所代表的作業名稱。   |
| 記錄層級  | 根據預設，Azure 區塊鏈 Service 會啟用**資訊**記錄層級。   |
| NodeLocation  | 部署區塊鏈成員的 Azure 區域。  |
| BlockchainNodeName  | 執行作業所在之 Azure 區塊鏈服務成員的節點名稱。   |
| BlockchainMessage    | 此欄位將包含區塊鏈應用程式記錄檔，也就是資料純記錄。 針對 ABS-仲裁，這會有仲裁記錄。 其中包含有關記錄專案類型的資訊、錯誤、警告，以及提供所執行動作之詳細資訊的字串。   |
| TenantID    | Azure 區塊鏈服務的區域特定租使用者。 此欄位的格式為https://westlake-rp-prod。<region>. cloudapp.azure.com，其中 region 指定部署之成員的 azure 區域。       |
| SourceSystem   | 系統會填入記錄檔，在此案例中為**Azure**。    |



### <a name="metrics"></a>計量

下表列出為 Azure 區塊鏈 Service 收集的平臺計量。 所有計量都會儲存在命名空間**Azure 區塊鏈 Service**標準計量中。

如需所有 Azure 監視器支援的計量（包括 Azure 區塊鏈 Service）的清單，請參閱[Azure 監視器支援的計量](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)。

### <a name="blockchain-metrics"></a>區塊鏈計量

下表指定針對 Azure 區塊鏈服務成員資源所收集的區塊鏈計量清單。


| 度量名稱 | 單位  |  彙總類型| 描述   |
|---|---|---|---|
| 暫止交易   | 計數  |  Average | 正在等候挖掘的交易數目。   |
| 已處理的區塊   | 計數  | Sum  |  每個時間間隔內處理的區塊數目。 區塊大小目前為5秒，因此在一分鐘內，每個節點會在5分鐘內處理12個區塊和60個區塊。   |
|已處理的交易    | 計數  | Sum  | 在區塊中處理的交易數目。    |
|佇列交易    |  計數 | Average  | 無法立即挖掘的交易數目。 這可能是因為它們未按順序抵達，而未來的交易會等待前一筆交易抵達。 或者，可以是兩筆交易，只使用一次（nonce）和相同的天然氣值，因此無法挖掘第二個。   |

### <a name="connection-metrics"></a>連接計量  

下表列出針對 Azure 區塊鏈服務成員資源所收集的不同連接計量。 這些是 NGINX proxy 計量。


| 度量名稱 | 單位  |  彙總類型| 描述 |
|---|---|---|---|
| 接受的連接   | 計數  |  Sum | 已接受的用戶端連接總數。   |
| 作用中的連線  | 計數  | Average  |  目前的作用中用戶端連線數目，包括等候連接。    |
|已處理的連接    | 計數  | Sum  | 已處理的連接總數。 一般而言，除非已達到一些資源限制，否則參數值會與已接受的連接相同。     |
|已處理的要求     |  計數 | Sum  | 用戶端要求總數。  |


### <a name="performance-metrics"></a>效能度量

下表列出針對 Azure 區塊鏈成員資源的每個節點收集的效能計量。  


| 度量名稱 | 單位  |  彙總類型| 描述   |
|---|---|---|---|
| CPU 使用量百分比   | 百分比  |  最大值 | CPU 使用量的百分比。     |
| IO 讀取位元組   | KB   | Sum  |  區塊鏈成員資源的所有節點之間的 IO 讀取位元組總和。      |
|IO 寫入位元組     | KB   | Sum  | IO 在區塊鏈成員資源的所有節點之間寫入位元組的總和。     |
|記憶體限制       |  千兆   | Average    | 每個節點的區塊鏈進程可用的記憶體上限。 |
|記憶體使用量     | 千兆  |  Average | 在所有節點上平均使用的記憶體數量。  |
| 記憶體使用量百分比     | 百分比   | Average  |  在所有節點上平均使用的記憶體百分比。       |
|存放裝置使用量      | 千兆   | Average  | 在所有節點上平均使用的儲存空間（GB）。       |


## <a name="next-steps"></a>後續步驟

深入瞭解[區塊鏈資料管理員](https://docs.microsoft.com/azure/blockchain/service/data-manager)，以取得區塊鏈資料並將其轉換為 Azure 事件方格。
