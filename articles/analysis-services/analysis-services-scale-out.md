---
title: Azure Analysis Services 擴充 | Microsoft Docs
description: 使用相應放大來複寫 Azure Analysis Services 伺服器。然後可以在向外延展查詢集區中的多個查詢複本之間散發用戶端查詢。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3ea304d038618fc428f20e7ad72b398f593d09a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "78247998"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 擴充

使用向外延展時，用戶端查詢可以分散于*查詢集*區中的多個*查詢複本*，以減少高查詢工作負載期間的回應時間。 您也可以將處理作業從查詢集區中區隔出來，確保用戶端查詢不會受到處理作業的不良影響。 可以利用 Azure 入口網站或 Analysis Services REST API 設定擴充。

標準定價層中的伺服器可以使用擴充。 每個查詢複本會以您伺服器的相同費率計費。 所有查詢複本都會建立在與伺服器相同的區域內。 您可以設定的查詢複本數目受限於您伺服器所在的區域。 若要深入了解，請參閱[依區域的可用性](analysis-services-overview.md#availability-by-region)。 擴充不會增加您伺服器的可用記憶體量。 若要增加記憶體則需升級方案。 

## <a name="why-scale-out"></a>為何要相應放大？

在一般伺服器部署中，一部伺服器會作為處理伺服器和查詢伺服器使用。 如果伺服器上對應模型的用戶端查詢數量超過伺服器方案的查詢處理器 (QPU)，或是模型的處理作業與高度的查詢工作負載同時發生，則效能可能會降低。 

使用向外延展時，您可以建立包含多達7個額外查詢複本資源的查詢集區（總共八個，包括您的*主*伺服器）。 您可以調整查詢集區中的複本數目，以符合關鍵時間的 QPU 需求，而且您可以隨時將處理伺服器與查詢集區分開。 

無論您在查詢集區中擁有多少個查詢複本，處理工作負載都不會分散在各查詢複本之間。 主伺服器可作為處理伺服器。 查詢複本只會針對在主伺服器與查詢集區中的每個複本之間同步處理的模型資料庫提供查詢。 

相應放大時，最多可能需要五分鐘的時間，新的查詢複本才會累加加入至查詢集區。 當所有新的查詢複本都已啟動並執行時，新的用戶端連接會在查詢集區中的資源間進行負載平衡。 現有的用戶端連線仍會連接到目前的資源，而不會變更。 在相應縮小時，若有用戶端連線至要從查詢集區中移除的查詢集區資源，其現有的連線一律會終止。 用戶端可以重新連接到其餘的查詢集區資源。

## <a name="how-it-works"></a>運作方式

第一次設定相應放大時，主伺服器上的模型資料庫會*自動*與新的查詢集區中的新複本同步處理。 自動同步處理只會執行一次。 在自動同步處理期間，主伺服器的資料檔案（blob 儲存體中的待用加密）會複製到第二個位置，也會在 blob 儲存體中待用加密。 然後會使用第二組檔案中的資料來*序列化*查詢集區中的複本。 

只有當您第一次向外延展伺服器時，才會執行自動同步處理，您也可以執行手動同步處理。 同步處理可確保查詢集區中複本上的資料與主伺服器相符。 在主伺服器上處理（重新整理）模型時，必須在處理作業完成*後*執行同步處理。 這項同步處理會將更新的資料從 blob 儲存體中的主伺服器檔案複製到第二組檔案。 接著，會使用 blob 儲存體中第二組檔案的更新資料來序列化查詢集區中的複本。 

例如，在執行後續的向外延展作業時，將查詢集區中的複本數目從二增加到五個，新的複本會以 blob 儲存體中第二組檔案的資料進行序列化。 沒有同步處理。 如果您接著在相應放大後執行同步處理，則查詢集區中的新複本會序列化兩次，也就是多餘的序列化。 執行後續的向外延展作業時，請務必記住：

* *在相應放大作業之前*執行同步處理，以避免新增複本的重複序列化。 不允許同時執行的並行同步處理和相應放大作業。

* 將處理*和*相應放大作業自動化時，請務必先在主伺服器上處理資料，然後執行同步處理，然後執行相應放大操作。 此順序會確保對 QPU 和記憶體資源的影響最小。

* 即使查詢集區中沒有任何複本，也允許同步處理。 如果您要從零向外延展到一個或多個複本，並在主伺服器上處理作業的新資料，請先執行同步處理，而不要在查詢集區中使用任何複本，然後再向外延展。在相應放大之前進行同步處理，可避免新加入複本的重複序列化。

* 從主伺服器刪除模型資料庫時，不會自動從查詢集區中的複本刪除它。 您必須執行同步處理作業，方法是使用[AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell 命令，從複本的共用 blob 儲存位置移除該資料庫的檔案，然後刪除查詢集區中複本上的 model 資料庫。 若要判斷模型資料庫是否存在於查詢集區中的複本上，但不在主伺服器上，請確定 [**從查詢集區中個別的處理伺服器**] 設定為 **[是]**。 然後使用此`:rw`辨識符號，透過 SSMS 連接到主伺服器，以查看資料庫是否存在。 然後連接到查詢集區中的複本，方法是`:rw`不使用限定詞連接，以查看相同的資料庫是否也存在。 如果資料庫存在於查詢集區中的複本上，而不是在主伺服器上，請執行同步作業。   

* 重新命名主伺服器上的資料庫時，需要額外的步驟，以確保資料庫已正確地同步處理至任何複本。 重新命名之後，請使用[AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)命令來指定具有舊資料庫名稱的`-Database`參數，以執行同步處理。 這個同步處理會從任何複本移除具有舊名稱的資料庫和檔案。 然後執行另一個同步處理`-Database` ，並指定具有新資料庫名稱的參數。 第二次同步處理會將新命名的資料庫複製到第二組檔案，並會產生任何複本。 無法使用入口網站中的 [同步處理模型] 命令來執行這些同步作業。

### <a name="synchronization-mode"></a>同步處理模式

根據預設，查詢複本會以完整的方式解除凍結，而不是累加。 解除凍結會分階段發生。 它們會一次卸離並連結兩個（假設至少有三個複本），以確保在任何指定的時間，至少有一個複本保持在線上，供查詢使用。 在某些情況下，當此程式進行時，用戶端可能需要重新連線到其中一個線上複本。 藉由使用 [（預覽）] **ReplicaSyncMode**設定，您現在可以指定以平行方式進行查詢複本同步處理。 平行同步處理提供下列優點： 

- 大幅減少同步處理時間。 
- 在同步處理過程中，跨複本的資料比較可能一致。 
- 由於資料庫會在整個同步處理常式的所有複本上保持線上狀態，因此用戶端不需要重新連線。 
- 記憶體內部快取只會以已變更的資料累加更新，這可能比完全解除凍結模型更快。 

#### <a name="setting-replicasyncmode"></a>設定 ReplicaSyncMode

使用 SSMS 來設定 Advanced 屬性中的 ReplicaSyncMode。 可能的值包括： 

- `1`（預設值）：階段中的完整複本資料庫解除凍結（增量）。 
- `2`：以平行方式優化同步處理。 

![RelicaSyncMode 設定](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

當設定**ReplicaSyncMode = 2**時，視需要更新的快取數量而定，查詢複本可能會耗用額外的記憶體。 若要讓資料庫保持連線並可供查詢使用，視資料的多少變更而定，此作業最多可能需要*兩倍*的複本，因為舊的和新的區段會同時保存在記憶體中。 複本節點具有與主要節點相同的記憶體配置，而且主要節點上通常會有額外的記憶體來進行重新整理作業，因此複本可能不太可能會用盡記憶體。 此外，常見的案例是在主要節點上以累加方式更新資料庫，因此，記憶體的雙精確度需求應該很罕見。 如果同步處理作業發生記憶體不足的錯誤，則會使用預設的技術（一次附加/卸離兩個）重試。 

### <a name="separate-processing-from-query-pool"></a>與查詢集區分開處理

若要讓處理和查詢作業達到最佳效能，您可以選擇將處理伺服器和查詢集區區隔開來。 分隔時，新的用戶端連接只會指派給查詢集區中的查詢複本。 如果處理作業短時間即可完成，您可以選擇只在執行處理和同步處理作業的期間內區隔處理伺服器與查詢集區，且隨後就將它重新納入查詢集區中。 將處理伺服器與查詢集區分開，或將其新增回查詢集區時，最多可能需要五分鐘的時間才能完成作業。

## <a name="monitor-qpu-usage"></a>監視 QPU 使用量

若要判斷是否需要伺服器的相應放大，請使用計量在 Azure 入口網站中[監視您的伺服器](analysis-services-monitor.md)。 如果您的 QPU 經常超出最大值，表示對應模型的查詢數目超出方案的 QPU 限制。 查詢執行緒集區佇列中的查詢數目超過可用 QPU 時，查詢集區作業佇列長度計量也會增加。 

另一個要監看的良好度量是 ServerResourceType 的平均 QPU。 此度量會比較主伺服器與查詢集區的平均 QPU。 

![查詢相應放大計量](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**若要設定 QPU by ServerResourceType**

1. 在 [計量] 折線圖中，按一下 [**新增度量**]。 
2. 在 **[資源**] 中選取您的伺服器，然後在 [計量**命名空間**] 中選取 [ **Analysis Services 標準計量**]，然後在 [**度量**] 中選取 [ **QPU**]，然後在 [**匯總**] 中選取 [ **Avg** 
3. 按一下 [套用**分割**]。 
4. 在 [**值**] 中，選取 [ **ServerResourceType**]。  

### <a name="detailed-diagnostic-logging"></a>詳細的診斷記錄

使用 Azure 監視器記錄，以取得相應放大伺服器資源的詳細診斷。 使用記錄，您可以使用 Log Analytics 查詢，依伺服器和複本細分 QPU 和記憶體。 若要深入瞭解，請參閱[Analysis Services 診斷記錄](analysis-services-logging.md#example-queries)中的範例查詢。


## <a name="configure-scale-out"></a>設定擴充

### <a name="in-azure-portal"></a>Azure 入口網站

1. 在入口網站中，按一下 [**相應**放大]。使用滑杆來選取查詢複本伺服器的數目。 所選擇的複本數目不包括現有的伺服器。  

2. 在 [Separate the processing server from the querying pool]\(區隔處理伺服器與查詢集區\)**** 中，選取 [是] 以將處理伺服器從查詢伺服器排除。 使用[connections](#connections)預設連接字串（不含`:rw`）的用戶端連接會重新導向至查詢集區中的複本。 

   ![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 按一下 [儲存]**** 以佈建您的新查詢複本伺服器。 

第一次為伺服器設定相應放大時，主伺服器上的模型會自動與查詢集區中的複本同步處理。 只有當您第一次設定一個或多個複本的相應放大時，自動同步處理才會發生一次。 對相同伺服器上的複本數目所做的後續變更，*將不會觸發另一個自動同步*處理。 即使您將伺服器設定為零個複本，然後再次向外延展到任何數目的複本，自動同步處理也不會再次發生。 

## <a name="synchronize"></a>同步處理 

同步處理作業必須以手動方式或使用 REST API 來執行。

### <a name="in-azure-portal"></a>Azure 入口網站

在 [概觀]**** > [模型] > [同步處理模型]**** 中。

![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

使用**同步**作業。

#### <a name="synchronize-a-model"></a>同步處理模型   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>取得同步處理狀態  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

傳回狀態碼：


|程式碼  |說明  |
|---------|---------|
|-1     |  無效       |
|0     | Replicating        |
|1     |  解除凍結       |
|2     |   Completed       |
|3     |   Failed      |
|4     |    正在完成     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用 PowerShell 之前，請[安裝或更新最新的 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 

若要執行同步處理，請使用[同步處理-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)。

若要設定查詢複本的數目，請使用[AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定選擇性的 `-ReadonlyReplicaCount` 參數。

若要將處理伺服器與查詢集區分開，請使用[AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定要使用`-DefaultConnectionMode` `Readonly`的選擇性參數。

若要深入瞭解，請參閱[使用服務主體搭配 Az microsoft.analysisservices 模組](analysis-services-service-principal.md#azmodule)。

## <a name="connections"></a>連接

在伺服器的 [概觀] 頁面中，有兩個伺服器名稱。 如果您還沒有為伺服器設定擴充，則這兩個伺服器名稱會以相同方式運作。 一旦設定了伺服器擴充，即必須根據連線類型指定適當的伺服器名稱。 

若為使用者用戶端連線（例如 Power BI Desktop、Excel 和自訂應用程式），請使用**伺服器名稱**。 

針對 SSMS、Visual Studio 和 PowerShell、Azure 函式應用程式和 AMO 中的連接字串，請使用**管理伺服器名稱**。 管理伺服器名稱包含特殊 `:rw` (讀寫) 限定詞。 所有處理作業都是在（主要）管理伺服器上進行。

![伺服器名稱](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>向上擴充、相應減少和向外延展

您可以在具有多個複本的伺服器上變更定價層。 相同的定價層會套用至所有複本。 調整規模作業會先將所有複本一次關閉，然後在新的定價層上顯示所有複本。

## <a name="troubleshoot"></a>疑難排解

**問題︰** 使用者收到錯誤「找不到處於連線模式 'ReadOnly' 的伺服器 '\<伺服器名稱>' 執行個體。****

**解決方案：****從 [查詢集區] 選項選取不同的處理伺服器**時，使用預設連接字串（不含`:rw`）的用戶端連接會重新導向至查詢集區複本。 如果查詢集區中的複本因為尚未完成同步處理而未上線，則重新導向的用戶端連線可能會失敗。 若要避免連線失敗，則在執行同步處理時，查詢集區中必須有至少兩部伺服器。 每部伺服器會個別同步，而其他伺服器則維持線上狀態。 如果您選擇在處理期間查詢集區中不要有處理中的伺服器，可以選擇從集區中移除該伺服器以供處理，然後在處理完成之後，但在同步處理之前，將它加回集區。 使用記憶體和 QPU 計量來監視同步處理狀態。



## <a name="related-information"></a>相關資訊

[監視伺服器計量](analysis-services-monitor.md)   
[管理 Azure Analysis Services](analysis-services-manage.md) 
