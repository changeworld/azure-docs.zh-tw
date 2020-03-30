---
title: Azure Analysis Services 擴充 | Microsoft Docs
description: 使用橫向擴展複製 Azure 分析服務伺服器。然後，可以將用戶端查詢分佈在橫向擴展查詢池中的多個查詢副本之間。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3ea304d038618fc428f20e7ad72b398f593d09a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247998"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 擴充

通過橫向擴展，用戶端查詢可以分佈在*查詢池*中的多個*查詢副本*中，從而縮短高查詢工作負載期間的回應時間。 您也可以將處理作業從查詢集區中區隔出來，確保用戶端查詢不會受到處理作業的不良影響。 可以利用 Azure 入口網站或 Analysis Services REST API 設定擴充。

標準定價層中的伺服器可以使用擴充。 每個查詢複本會以您伺服器的相同費率計費。 所有查詢複本都會建立在與伺服器相同的區域內。 您可以設定的查詢複本數目受限於您伺服器所在的區域。 若要深入了解，請參閱[依區域的可用性](analysis-services-overview.md#availability-by-region)。 擴充不會增加您伺服器的可用記憶體量。 若要增加記憶體則需升級方案。 

## <a name="why-scale-out"></a>為什麼要橫向擴展？

在一般伺服器部署中，一部伺服器會作為處理伺服器和查詢伺服器使用。 如果伺服器上對應模型的用戶端查詢數量超過伺服器方案的查詢處理器 (QPU)，或是模型的處理作業與高度的查詢工作負載同時發生，則效能可能會降低。 

通過橫向擴展，您可以創建一個查詢池，其中最多包含七個附加的查詢副本資源（總共八個，*包括主伺服器*）。 您可以縮放查詢池中的副本數以滿足關鍵時間 QPU 需求，並且可以隨時將處理伺服器與查詢池分開。 

無論您在查詢集區中擁有多少個查詢複本，處理工作負載都不會分散在各查詢複本之間。 主伺服器用作處理伺服器。 查詢副本僅針對在主伺服器和查詢池中的每個副本之間同步的模型資料庫提供查詢。 

橫向擴展時，新查詢副本最多可能需要五分鐘才能增量添加到查詢池。 當所有新的查詢副本都啟動並運行時，新的用戶端連接在查詢池中的資源之間負載平衡。 現有的用戶端連線仍會連接到目前的資源，而不會變更。 在相應縮小時，若有用戶端連線至要從查詢集區中移除的查詢集區資源，其現有的連線一律會終止。 用戶端可以重新連接到剩餘的查詢池資源。

## <a name="how-it-works"></a>運作方式

首次配置橫向擴展時，主伺服器上的模型資料庫*將自動*與新查詢池中的新副本同步。 自動同步只發生一次。 在自動同步期間，主伺服器的資料檔案（在 blob 存儲中靜態加密）將複製到第二個位置，在 blob 存儲中也加密靜態。 然後，查詢池中的副本將與其他檔組的資料*一起水化*。 

雖然自動同步僅在首次橫向擴展伺服器時執行，但您也可以執行手動同步。 同步可確保查詢池中副本上的資料與主伺服器的資料匹配。 在主伺服器上處理（刷新）模型時，必須在完成處理操作*後*執行同步。 此同步將更新的資料從 blob 存儲中的主伺服器檔案複製到第二組檔。 然後，查詢池中的副本將用 blob 存儲中第二組檔中的更新資料進行水化。 

例如，執行後續橫向擴展操作時，將查詢池中的副本數從 2 增加到 5 個，新副本將與 blob 存儲中第二組檔的資料進行水合。 沒有同步。 如果在橫向擴展後執行同步，則查詢池中的新副本將水合兩次 - 冗余水化。 執行後續橫向擴展操作時，請務必記住：

* 在*橫向擴展操作之前*執行同步，以避免添加的副本的冗余水化。 不允許同時運行併發同步和橫向擴展操作。

* 在自動化處理*和*橫向擴展操作時，首先處理主伺服器上的資料，然後執行同步，然後執行橫向擴展操作非常重要。 此序列可確保對 QPU 和記憶體資源的影響最小。

* 即使查詢池中沒有副本，也允許同步。 如果要使用主伺服器上的處理操作中的新資料從零擴展到一個或多個副本，請首先在查詢池中沒有副本時執行同步，然後橫向擴展。在橫向擴展之前進行同步可避免新添加的副本的冗余水化。

* 從主伺服器中刪除模型資料庫時，它不會自動從查詢池中的副本中刪除。 您必須使用[Sync-AzAnalysisServicesServicesAOnSPowerShell](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)命令執行同步操作，該命令從副本的共用 Blob 存儲位置中刪除該資料庫的檔，然後刪除查詢池中副本上的模型資料庫。 要確定模型資料庫是否存在在查詢池中的副本上，而不是主伺服器上，請確保**將處理伺服器從查詢池設置中分離出來**為 **"是**"。 然後使用 SSMS 使用`:rw`限定詞連接到主伺服器以查看資料庫是否存在。 然後，通過不帶限定詞連接連接到查詢池中的副本`:rw`，以查看是否存在同一資料庫。 如果資料庫存在於查詢池中的副本上，但不在主伺服器上，請運行同步操作。   

* 重命名主伺服器上的資料庫時，還需要執行其他步驟，以確保資料庫正確同步到任何副本。 重命名後，請使用使用舊資料庫名稱指定`-Database`參數的[Sync-AzAnalysisServicesServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)命令執行同步。 此同步從任何副本中刪除具有舊名稱的資料庫和檔。 然後，使用新的資料庫名稱`-Database`執行另一個同步，指定參數。 第二個同步將新命名的資料庫複製到第二組檔，並凍結任何副本。 無法使用門戶中的"同步模型"命令執行這些同步。

### <a name="synchronization-mode"></a>同步模式

預設情況下，查詢副本將完全（而不是增量）重新水化。 補液分期分期進行。 它們一次分離並附加兩個副本（假設至少有三個副本），以確保在任何給定時間至少有一個副本保持連線以查詢。 在某些情況下，用戶端可能需要在此過程進行時重新連接到其中一個連線副本。 通過使用（預覽版）**副本同步模式**設置，現在可以指定並行發生的查詢副本同步。 並行同步提供以下好處： 

- 顯著縮短同步時間。 
- 在同步過程中，跨副本的資料更有可能是一致的。 
- 由於在整個同步過程中，所有副本上的資料庫都保持線上狀態，因此用戶端不需要重新連接。 
- 記憶體中緩存僅使用更改的資料進行增量更新，這些資料可能比完全重新填充模型更快。 

#### <a name="setting-replicasyncmode"></a>設置副本同步模式

使用 SSMS 在高級屬性中設置副本同步模式。 可能的值包括： 

- `1`（預設）：分階段（增量）完全副本資料庫補液。 
- `2`：並行優化同步。 

![RelicaSyncMode 設置](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

設置**ReplicaSyncMode_2**時，根據需要更新的緩存量，查詢副本可能會佔用額外的記憶體。 為了保持資料庫連線且可用於查詢，根據更改的資料量，該操作可能需要副本上的*記憶體增加一倍*，因為新舊段同時保存在記憶體中。 副本節點的記憶體分配與主節點相同，並且主節點上通常有額外的記憶體用於刷新操作，因此副本的記憶體可能不足。 此外，常見的方案是資料庫在主節點上以增量方式更新，因此對記憶體加倍的要求應該並不常見。 如果 Sync 操作確實遇到記憶體不足錯誤，它將使用預設技術重試（一次附加/分離兩個）。 

### <a name="separate-processing-from-query-pool"></a>與查詢集區分開處理

若要讓處理和查詢作業達到最佳效能，您可以選擇將處理伺服器和查詢集區區隔開來。 分離後，新用戶端連接將分配給僅在查詢池中的查詢副本。 如果處理作業短時間即可完成，您可以選擇只在執行處理和同步處理作業的期間內區隔處理伺服器與查詢集區，且隨後就將它重新納入查詢集區中。 將處理伺服器與查詢池分離或將其重新添加到查詢池最多可能需要五分鐘才能完成操作。

## <a name="monitor-qpu-usage"></a>監視 QPU 使用量

要確定是否需要為伺服器橫向擴展，請使用指標監視 Azure 門戶中的[伺服器](analysis-services-monitor.md)。 如果您的 QPU 經常超出最大值，表示對應模型的查詢數目超出方案的 QPU 限制。 查詢執行緒集區佇列中的查詢數目超過可用 QPU 時，查詢集區作業佇列長度計量也會增加。 

另一個需要注意的指標是按伺服器資源類型進行的平均 QPU。 此指標將主伺服器的平均 QPU 與查詢池進行比較。 

![查詢橫向擴展指標](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**按伺服器資源類型配置 QPU**

1. 在指標折線圖中，按一下"**添加指標**"。 
2. 在 **"資源"中**，選擇您的伺服器，然後在 **"指標"中**選擇 **"分析服務標準指標**"，然後在 **"指標"** 中選擇 **"QPU"，** 然後在 **"聚合"** 中選擇 **"平均**"。 
3. 按一下 **"應用拆分**"。 
4. 在**VALUE 中**，選擇**伺服器資源類型**。  

### <a name="detailed-diagnostic-logging"></a>詳細的診斷日誌記錄

使用 Azure 監視器日誌對已縮小的伺服器資源進行更詳細的診斷。 使用日誌，可以使用日誌分析查詢按伺服器和副本分解 QPU 和記憶體。 要瞭解更多資訊，請參閱[分析服務診斷日誌記錄](analysis-services-logging.md#example-queries)中的依例查詢。


## <a name="configure-scale-out"></a>設定擴充

### <a name="in-azure-portal"></a>Azure 入口網站

1. 在門戶中，按一下 **"橫向擴展**"。使用滑塊選取查詢副本伺服器的數量。 所選擇的複本數目不包括現有的伺服器。  

2. 在 [Separate the processing server from the querying pool]\(區隔處理伺服器與查詢集區\)**** 中，選取 [是] 以將處理伺服器從查詢伺服器排除。 使用[connections](#connections)預設連接字串（不 ）`:rw`的用戶端連接將重定向到查詢池中的副本。 

   ![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 按一下 [儲存]**** 以佈建您的新查詢複本伺服器。 

首次佈建服務器橫向擴展時，主伺服器上的模型將自動與查詢池中的副本同步。 首次將橫向擴展配置為一個或多個副本時，自動同步僅發生一次。 對同一伺服器上的副本數的後續更改*不會觸發另一個自動同步*。 即使將伺服器設置為零副本，然後再次橫向擴展為任意數量的副本，也不會再次發生自動同步。 

## <a name="synchronize"></a>同步處理 

同步操作必須手動執行或使用 REST API。

### <a name="in-azure-portal"></a>Azure 入口網站

在 [概觀]**** > [模型] > [同步處理模型]**** 中。

![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

使用**同步**作業。

#### <a name="synchronize-a-model"></a>同步處理模型   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>取得同步處理狀態  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

返回狀態碼：


|程式碼  |描述  |
|---------|---------|
|-1     |  無效       |
|0     | Replicating        |
|1     |  補水       |
|2     |   Completed       |
|3     |   失敗      |
|4     |    敲定     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在使用 PowerShell 之前，[請安裝或更新最新的 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 

要運行同步，請使用[同步分析服務實例](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)。

要設置查詢副本的數量，請使用[Set-AzAnalysis 服務伺服器](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定選擇性的 `-ReadonlyReplicaCount` 參數。

要將處理伺服器與查詢池分開，請使用[Set-AzAnalysis服務伺服器](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定要使用的可選`-DefaultConnectionMode`參數`Readonly`。

要瞭解更多資訊，請參閱[使用 Az.Analysis 服務模組的服務主體](analysis-services-service-principal.md#azmodule)。

## <a name="connections"></a>連接

在伺服器的 [概觀] 頁面中，有兩個伺服器名稱。 如果您還沒有為伺服器設定擴充，則這兩個伺服器名稱會以相同方式運作。 一旦設定了伺服器擴充，即必須根據連線類型指定適當的伺服器名稱。 

對於最終使用者用戶端連接（如 Power BI 桌面、Excel 和自訂應用），請使用**伺服器名稱**。 

對於 PowerShell、Azure 函數應用和 AMO 中的 SSMS、視覺化工作室和連接字串，請使用**管理伺服器名稱**。 管理伺服器名稱包含特殊 `:rw` (讀寫) 限定詞。 所有處理操作都發生在（主）管理伺服器上。

![伺服器名稱](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>放大縮小字體功能 放大縮小字體功能

您可以在具有多個副本的伺服器上更改定價層。 相同的定價層適用于所有副本。 規模操作將首先將所有副本全部降下來，然後在新定價層上啟動所有副本。

## <a name="troubleshoot"></a>疑難排解

**問題︰** 使用者收到錯誤「找不到處於連線模式 'ReadOnly' 的伺服器 '\<伺服器名稱>' 執行個體。****

**解決方案：** 選擇"**將處理伺服器從查詢池**"選項中分離出來時，使用預設連接字串（不 ） 的`:rw`用戶端連接將重定向到查詢池副本。 如果查詢集區中的複本因為尚未完成同步處理而未上線，則重新導向的用戶端連線可能會失敗。 若要避免連線失敗，則在執行同步處理時，查詢集區中必須有至少兩部伺服器。 每部伺服器會個別同步，而其他伺服器則維持線上狀態。 如果您選擇在處理期間查詢集區中不要有處理中的伺服器，可以選擇從集區中移除該伺服器以供處理，然後在處理完成之後，但在同步處理之前，將它加回集區。 使用記憶體和 QPU 計量來監視同步處理狀態。



## <a name="related-information"></a>相關資訊

[監視伺服器指標](analysis-services-monitor.md)   
[管理 Azure Analysis Services](analysis-services-manage.md) 
