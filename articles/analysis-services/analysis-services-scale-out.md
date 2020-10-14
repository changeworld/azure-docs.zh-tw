---
title: Azure Analysis Services 擴充 | Microsoft Docs
description: 使用相應放大來複寫 Azure Analysis Services 伺服器。然後，用戶端查詢可以在相應放大查詢集區中的多個查詢複本之間散發。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 24ee31b941d836d296c30927cfb9636f3023fa89
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019423"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 擴充

使用向外延展時，用戶端查詢可以在*查詢集*區中的多個*查詢複本*之間散發，減少在高查詢工作負載期間的回應時間。 您也可以將處理作業從查詢集區中區隔出來，確保用戶端查詢不會受到處理作業的不良影響。 可以利用 Azure 入口網站或 Analysis Services REST API 設定擴充。

標準定價層中的伺服器可以使用擴充。 每個查詢複本會以您伺服器的相同費率計費。 所有查詢複本都會建立在與伺服器相同的區域內。 您可以設定的查詢複本數目受限於您伺服器所在的區域。 若要深入了解，請參閱[依區域的可用性](analysis-services-overview.md#availability-by-region)。 擴充不會增加您伺服器的可用記憶體量。 若要增加記憶體則需升級方案。 

## <a name="why-scale-out"></a>為何要相應放大？

在一般伺服器部署中，一部伺服器會作為處理伺服器和查詢伺服器使用。 如果伺服器上對應模型的用戶端查詢數量超過伺服器方案的查詢處理器 (QPU)，或是模型的處理作業與高度的查詢工作負載同時發生，則效能可能會降低。 

使用向外延展，您可以建立最多七個額外查詢複本資源的查詢集區 (總共8個，包括您的 *主* 伺服器) 。 您可以調整查詢集區中的複本數目，以符合重要時間的 QPU 需求，而且您可以隨時將處理伺服器與查詢集區分開。 

無論您在查詢集區中擁有多少個查詢複本，處理工作負載都不會分散在各查詢複本之間。 主伺服器可作為處理伺服器。 查詢複本僅針對在主伺服器與查詢集區中的每個複本之間同步處理的模型資料庫，提供查詢。 

相應放大時，最多可能需要五分鐘的時間，新的查詢複本才能以累加方式新增至查詢集區。 當所有新的查詢複本都已啟動並執行時，新的用戶端連線會在查詢集區中的資源之間進行負載平衡。 現有的用戶端連線仍會連接到目前的資源，而不會變更。 在相應縮小時，若有用戶端連線至要從查詢集區中移除的查詢集區資源，其現有的連線一律會終止。 用戶端可以重新連接至其餘的查詢集區資源。

## <a name="how-it-works"></a>運作方式

當您第一次設定相應放大時，主伺服器上的模型資料庫會 *自動* 與新的查詢集區中的新複本同步處理。 自動同步處理只會進行一次。 在自動同步處理期間，會將主伺服器的資料檔案 (在 blob 儲存體中待用加密) 複製到第二個位置，也會在 blob 儲存體中待用加密。 然後，系統會使用第二組檔案中的資料來 *水合* 查詢集區中的複本。 

只有當您第一次向外擴充伺服器時，才會執行自動同步處理，您也可以執行手動同步處理。 同步處理可確保查詢集區中複本的資料與主伺服器的複本相符。 在主伺服器上處理 (重新整理) 模型時，必須在處理作業完成 *後* 執行同步處理。 此同步處理會將更新的資料從 blob 儲存體中的主伺服器檔案複製到第二組檔案。 然後，系統會使用 blob 儲存體中第二組檔案的更新資料來水合查詢集區中的複本。 

例如，在執行後續的相應放大作業（例如，將查詢集區中的複本數目從二增加到五個）時，會使用 blob 儲存體中第二組檔案的資料來水合新的複本。 沒有同步處理。 如果您在相應放大後執行同步處理，則會將查詢集區中的新複本水合兩次，也就是重複的序列化。 執行後續的相應放大作業時，請務必牢記：

* 在向外延展作業 *之前* 執行同步處理，以避免新增複本的重複序列化。 不允許同時執行的並行同步處理和相應放大作業。

* 當您將處理 *和* 相應放大作業自動化時，請務必先處理主伺服器上的資料，然後執行同步處理，然後執行相應放大作業。 此序列能確保對 QPU 和記憶體資源的影響降至下限。

* 在相應放大作業期間，查詢集區中的所有伺服器（包括主伺服器）都會暫時離線。

* 即使查詢集區中沒有任何複本，也允許同步處理。 如果您要從零向外延展到一或多個複本，並在主伺服器上處理作業的新資料，請先執行與查詢集區中沒有複本的同步處理，然後再向外擴充。在相應放大之前進行同步處理，可避免新加入複本的重複序列化。

* 從主伺服器刪除模型資料庫時，不會自動從查詢集區中的複本刪除。 您必須使用 [AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell 命令來執行同步處理作業，此命令會從複本的共用 blob 儲存位置移除該資料庫的檔案，然後在查詢集區中的複本上刪除模型資料庫。 若要判斷模型資料庫是否存在於查詢集區中的複本上，而不是在主伺服器上，請確定 [ **處理伺服器與查詢集** 區] 設定為 **[是]**。 然後使用 SSMS 連接到使用辨識符號的主伺服器， `:rw` 以查看資料庫是否存在。 然後連接到不含辨識符號的查詢集區中的複本 `:rw` ，以查看相同的資料庫是否也存在。 如果資料庫存在於查詢集區中的複本上，而不是在主伺服器上，請執行同步處理作業。   

* 在主伺服器上重新命名資料庫時，需要額外的步驟，以確保資料庫已正確地同步處理至任何複本。 重新命名之後，請使用 [AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) 命令來指定 `-Database` 具有舊資料庫名稱的參數，以執行同步處理。 此同步處理會從任何複本移除具有舊名稱的資料庫和檔案。 然後使用新的資料庫名稱，執行另一個指定參數的同步 `-Database` 處理。 第二個同步處理會將新命名的資料庫複製到第二組檔案，並產生任何複本。 您無法使用入口網站中的 [同步處理模型] 命令來執行這些同步處理。

### <a name="synchronization-mode"></a>同步處理模式

依預設，查詢複本會完整解除凍結，而不是以累加方式進行。 解除凍結會在階段進行。 它們會一次卸離並連接兩個 (假設至少有三個複本) ，以確保在任何指定時間，至少會有一個複本保持線上狀態。 在某些情況下，用戶端可能需要在進行此程式時，重新連接到其中一個線上複本。 藉由使用 [ **ReplicaSyncMode** ] 設定，您現在可以指定平行進行查詢複本同步處理。 平行同步處理提供下列優點： 

- 大幅減少同步處理時間。 
- 在同步處理過程中，跨複本的資料比較可能是一致的。 
- 由於資料庫在整個同步處理常式的所有複本上都會保持連線，因此用戶端不需要重新連接。 
- 記憶體中的快取只會隨著變更的資料以累加方式更新，這可能比完全解除凍結模型更快。 

#### <a name="setting-replicasyncmode"></a>設定 ReplicaSyncMode

使用 SSMS 在 Advanced Properties 中設定 ReplicaSyncMode。 可能的值包括： 

- `1` (預設) ：解除凍結中的完整複本資料庫 (增量) 。 
- `2`：平行的優化同步處理。 

![RelicaSyncMode 設定](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

設定 **ReplicaSyncMode = 2**時，視需要更新的快取數量而定，查詢複本可能會耗用額外的記憶體。 為了讓資料庫保持在線上且可供查詢使用，視資料的變更量而定，作業最多可能需要將複本上 *的記憶體加倍* ，因為舊的和新的區段會同時保留在記憶體中。 複本節點具有與主要節點相同的記憶體配置，且主要節點上通常會有額外的記憶體來進行重新整理作業，因此複本可能不太可能會用盡記憶體。 此外，常見的案例是在主要節點上以累加方式更新資料庫，因此需要兩倍的記憶體。 如果同步處理作業遇到記憶體不足的錯誤，則會使用預設的技巧重試， (一次附加/卸離兩個) 。 

### <a name="separate-processing-from-query-pool"></a>與查詢集區分開處理

若要讓處理和查詢作業達到最佳效能，您可以選擇將處理伺服器和查詢集區區隔開來。 當您分隔時，只會將新的用戶端連接指派給查詢集區中的查詢複本。 如果處理作業短時間即可完成，您可以選擇只在執行處理和同步處理作業的期間內區隔處理伺服器與查詢集區，且隨後就將它重新納入查詢集區中。 將處理伺服器與查詢集區分開，或將它新增回查詢集區時，最多可能需要五分鐘的時間才能完成作業。

## <a name="monitor-qpu-usage"></a>監視 QPU 使用量

若要判斷您的伺服器是否需要相應放大，請使用計量在 Azure 入口網站中 [監視您的伺服器](analysis-services-monitor.md) 。 如果您的 QPU 經常超出最大值，表示對應模型的查詢數目超出方案的 QPU 限制。 查詢執行緒集區佇列中的查詢數目超過可用 QPU 時，查詢集區作業佇列長度計量也會增加。 

另一個要監看的良好計量是平均 QPU by ServerResourceType。 此標準會將主伺服器的平均 QPU 與查詢集區進行比較。 

![查詢 scale out 計量](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**設定 QPU by ServerResourceType**

1. 在 [計量] 折線圖中，按一下 [ **新增度量**]。 
2. 在 **[資源**] 中選取您的伺服器，然後在 [計量**命名空間**] 中選取 [ **Analysis Services 標準計量**]，然後在 [**度量**] 中選取 [ **QPU**]，然後在 [**匯總**] 中選取 [ **Avg** 
3. 按一下 [套用 **分割**]。 
4. 在 [ **值**] 中，選取 [ **ServerResourceType**]。  

### <a name="detailed-diagnostic-logging"></a>詳細的診斷記錄

使用 Azure 監視器記錄，以更詳細地診斷相應放大的伺服器資源。 有了記錄，您就可以使用 Log Analytics 查詢，依伺服器和複本細分 QPU 和記憶體。 若要深入瞭解，請參閱 [Analysis Services 診斷記錄](analysis-services-logging.md#example-queries)中的範例查詢。


## <a name="configure-scale-out"></a>設定擴充

### <a name="in-azure-portal"></a>Azure 入口網站

1. 在入口網站中，按一下 [ **向外擴充**]。使用滑杆來選取查詢複本伺服器的數目。 所選擇的複本數目不包括現有的伺服器。  

2. 在 [Separate the processing server from the querying pool]\(區隔處理伺服器與查詢集區\)**** 中，選取 [是] 以將處理伺服器從查詢伺服器排除。 使用預設連接字串 (沒有) 的用戶端 [連接](#connections) `:rw` 會重新導向至查詢集區中的複本。 

   ![擴充滑桿](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 按一下 [儲存]**** 以佈建您的新查詢複本伺服器。 

當您第一次設定伺服器相應放大時，主伺服器上的模型會自動與查詢集區中的複本同步處理。 當您第一次將相應放大設定為一或多個複本時，自動同步處理只會發生一次。 對相同伺服器上的複本數目進行後續變更 *將不會觸發另一個自動同步*處理。 即使您將伺服器設定為零個複本，然後再向外延展至任意數目的複本，也不會再次進行自動同步處理。 

## <a name="synchronize"></a>同步處理 

您必須以手動方式或使用 REST API 來執行同步處理作業。

### <a name="in-azure-portal"></a>Azure 入口網站

在 [概觀]**** > [模型] > [同步處理模型]**** 中。

![同步處理圖示](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

使用**同步**作業。

#### <a name="synchronize-a-model"></a>同步處理模型   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>取得同步處理狀態  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

傳回狀態碼：


|程式碼  |描述  |
|---------|---------|
|-1     |  無效       |
|0     | Replicating        |
|1     |  解除凍結       |
|2     |   已完成       |
|3     |   Failed      |
|4     |    敲定     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用 PowerShell 之前，請 [安裝或更新最新的 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 

若要執行同步處理，請使用 [同步-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)。

若要設定查詢複本的數目，請使用 [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定選擇性的 `-ReadonlyReplicaCount` 參數。

若要將處理伺服器與查詢集區分開，請使用 [AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)。 指定 `-DefaultConnectionMode` 要使用的選擇性參數 `Readonly` 。

若要深入瞭解，請參閱 [使用服務主體搭配 Az. microsoft.analysisservices.sharepoint.integration.dll 模組](analysis-services-service-principal.md#azmodule)。

## <a name="connections"></a>連接

在伺服器的 [概觀] 頁面中，有兩個伺服器名稱。 如果您還沒有為伺服器設定擴充，則這兩個伺服器名稱會以相同方式運作。 一旦設定了伺服器擴充，即必須根據連線類型指定適當的伺服器名稱。 

針對 Power BI Desktop、Excel 和自訂應用程式等終端使用者用戶端連線，請使用 **伺服器名稱**。 

針對 SSMS、Visual Studio 和 PowerShell、Azure 函數應用程式和 AMO 中的連接字串，請使用 **管理伺服器名稱**。 管理伺服器名稱包含特殊 `:rw` (讀寫) 限定詞。 所有處理作業都會發生在 (主要) 管理伺服器上。

![伺服器名稱](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>擴大、相應減少與相應放大

您可以在具有多個複本的伺服器上變更定價層。 相同的定價層會套用至所有複本。 調整規模作業會一次關閉所有複本，然後在新的定價層上顯示所有複本。

## <a name="troubleshoot"></a>疑難排解

**問題：** 使用者收到錯誤 ** \<Name of the server> 在連接模式 ' ReadOnly ' 中找不到伺服器 ' ' 實例。**

**解決方案：****從 [查詢集**區] 選項中選取個別的處理伺服器時，使用預設連接字串 (沒有) 的用戶端連接 `:rw` 會重新導向至查詢集區複本。 如果查詢集區中的複本因為尚未完成同步處理而未上線，則重新導向的用戶端連線可能會失敗。 若要避免連線失敗，則在執行同步處理時，查詢集區中必須有至少兩部伺服器。 每部伺服器會個別同步，而其他伺服器則維持線上狀態。 如果您選擇在處理期間查詢集區中不要有處理中的伺服器，可以選擇從集區中移除該伺服器以供處理，然後在處理完成之後，但在同步處理之前，將它加回集區。 使用記憶體和 QPU 計量來監視同步處理狀態。



## <a name="related-information"></a>相關資訊

[監視伺服器計量](analysis-services-monitor.md)   
[管理 Azure Analysis Services](analysis-services-manage.md)