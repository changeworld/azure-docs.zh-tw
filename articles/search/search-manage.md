---
title: 入口網站中的服務管理
titleSuffix: Azure Cognitive Search
description: 使用 Azure 入口網站，在 Microsoft Azure 上管理 Azure 認知搜尋服務（託管的雲端搜尋服務）。
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: 45f9c56fce0c843d9f1ed069abf7d1ed6e2fa604
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565910"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Azure 入口網站中的 Azure 認知搜尋服務管理

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [入口網站](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure 認知搜尋是完全受控的雲端式搜尋服務，用來在自訂應用程式中建立豐富的搜尋體驗。 本文章涵蓋服務管理  工作，可供您在 [Azure 入口網站](https://portal.azure.com)中針對已佈建的搜尋服務執行。 服務管理原先的設計是輕量級的，限定於下列工作︰

* 使用 [中間頁**使用量**] 連結來檢查存放裝置。
* 使用中間頁**監視**連結檢查查詢磁片區和延遲，以及是否已節流要求。
* 使用左邊的 [**金鑰**] 頁面來管理存取權。
* 使用左邊的 [調整**規模**] 頁面來調整容量。

在入口網站中執行的相同工作也可以透過[管理 api](https://docs.microsoft.com/rest/api/searchmanagement/)和[Az. Search PowerShell 模組](search-manage-powershell.md)以程式設計方式處理。 系統管理工作會在入口網站和程式設計介面之間完整呈現。 沒有特定的系統管理工作只能在一個樣式中使用。

Azure 認知搜尋會運用其他 Azure 服務，以進行更深入的監視和管理。 唯一的是，使用搜尋服務儲存的資料就是內容（索引、索引子和資料來源定義，以及其他物件）。 向入口網站頁面回報的計量會在變換30天的週期內從內部記錄提取。 針對使用者控制的記錄保留和其他事件，您將需要[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/)。 

## <a name="fixed-service-properties"></a>已修正服務屬性

搜尋服務的幾個層面會在服務布建時決定，而且之後無法變更：

* 服務名稱（您無法重新命名服務）
* 服務位置（您目前無法將完整的服務移至另一個區域）
* 複本和分割區計數上限（由層、基本或標準決定）

如果您使用基本的來啟動，且其最大值為1個數據分割，而您現在需要更多磁碟分割，您將需要在較高的層級[建立新的服務](search-create-service-portal.md)，並在新的服務上重新建立內容。 

## <a name="administrator-rights"></a>系統管理員權限

Azure 訂用帳戶管理員或共同管理員可以佈建或解除委任服務本身。

關於端點的存取權，可存取服務 URL 和 api 金鑰的任何人都可以存取內容。 如需金鑰的詳細資訊，請參閱[管理 api 金鑰](search-security-api-keys.md)。

* 服務的唯讀存取是查詢許可權，通常是透過提供 URL 和查詢 api 金鑰，授與用戶端應用程式。
* 讀寫存取提供新增、刪除或修改伺服器物件的功能，包括 api 金鑰、索引、索引子、資料來源和排程。藉由提供 URL 和系統管理員 API 金鑰，即可授與讀寫存取權。

服務布建儀器的許可權是透過角色指派來授與。 [角色型存取（RBAC）](../role-based-access-control/overview.md)是根據布建 Azure 資源[Azure Resource Manager](../azure-resource-manager/management/overview.md)建立的授權系統。 

在 Azure 認知搜尋的內容中， [RBAC 角色指派](search-security-rbac.md)會決定誰可以執行工作，不論他們是使用[入口網站](search-manage.md)、 [PowerShell](search-manage-powershell.md)還是[管理 REST api](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)：

* 建立或刪除服務
* 調整服務規模
* 刪除或重新產生 API 金鑰
* 啟用診斷記錄（建立服務）
* 啟用流量分析（建立服務）

> [!TIP]
> 使用 Azure 範圍的機制，您可以鎖定訂用帳戶或資源，以防止使用者具有系統管理員許可權的意外或未經授權刪除您的搜尋服務。 如需詳細資訊，請參閱[鎖定資源以防止非預期的刪除](../azure-resource-manager/management/lock-resources.md)。

## <a name="logging-and-system-information"></a>記錄與系統資訊

在基本層和以上版本中，Microsoft 會針對每個服務等級協定（SLA）監視所有 Azure 認知搜尋服務是否有99.9% 可用性。 如果服務很慢或要求輸送量低於 SLA 閾值，支援團隊會檢閱他們可用的記錄檔並解決問題。

Azure 認知搜尋會利用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/)來收集和儲存索引和查詢活動。 搜尋服務本身只會儲存其內容（索引、索引子定義、資料來源定義、技能集定義、同義字對應）。 快取和記錄的資訊會以非服務的方式儲存，通常會在 Azure 儲存體帳戶中。 如需記錄索引和查詢工作負載的詳細資訊，請參閱[收集和分析記錄資料](search-monitor-logs.md)。

就服務的一般資訊而言，僅使用 Azure 認知搜尋本身內建的功能，您可以透過下列方式取得資訊：

* 使用 [服務**總覽**] 頁面，透過通知、屬性和狀態訊息。
* 使用[PowerShell](search-manage-powershell.md)或[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/)來[取得服務屬性](https://docs.microsoft.com/rest/api/searchmanagement/services)。 在程式設計層沒有提供任何新資訊或作業。 介面存在，讓您可以撰寫腳本。

## <a name="monitor-resource-usage"></a>監視資源使用量

在儀表板中，僅能針對服務儀表板中顯示的資訊，以及一些透過查詢服務取得的度量進行資源監視。 在服務儀表板上的使用量區段中，您可以快速判斷資料分割資源層級是否適合您的應用程式。 如果您想要捕獲並保存已記錄的事件，您可以布建外部資源，例如 Azure 監視。 如需詳細資訊，請參閱[監視 Azure 認知搜尋](search-monitor-usage.md)。

使用搜尋服務 REST API，您可以透過程式設計方式取得檔和索引的計數： 

* [取得索引統計資料](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [文件計數](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>災害復原和服務中斷

雖然我們可以搶救您的資料，但如果叢集或資料中心層級發生中斷，Azure 認知搜尋不會提供服務的立即容錯移轉。 如果叢集在資料中心內失敗，作業小組將會偵測並處理以還原服務。 您在服務還原期間將會遇到停止運作的狀況，但可以根據[服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 要求服務信用額度作為無法使用服務的補償。 

如果在遇到 Microsoft 控制之外的災難性失敗時，需要持續的服務，您可以在不同區域[佈建額外的服務](search-create-service-portal.md)並實作異地複寫策略，以確保索引在所有服務之間皆具有完整備援。

使用[索引子](search-indexer-overview.md)來填入及重新整理索引的客戶，可以透過使用相同資料來源的地理特定索引子來處理災害復原。 在不同區域中的兩個服務 (每個都執行一個索引子)，可以從相同相同的資料來源建立索引，以達到異地備援的目的。 如果您是從也是異地多餘的資料來源編制索引，請注意 Azure 認知搜尋索引子只能執行累加式編制索引（從新的、已修改或已刪除的檔合併更新）與主要複本。 在容錯移轉事件中，請務必將索引子重新指向新的主要複本。 

若不使用索引子，您可以使用應用程式程式碼將物件和資料平行推送到不同的搜尋服務。 如需詳細資訊，請參閱[Azure 認知搜尋中的效能和優化](search-performance-optimization.md)。

## <a name="backup-and-restore"></a>備份與還原

因為 Azure 認知搜尋並非主要的資料儲存體解決方案，所以我們不提供自助備份和還原的正式機制。 不過，您可以使用此[Azure 認知搜尋 .net 範例](https://github.com/Azure-Samples/azure-search-dotnet-samples)存放庫中的**索引備份-還原**範例程式碼，將您的索引定義和快照集備份至一系列 JSON 檔案，然後視需要使用這些檔案來還原索引。 此工具也可以在服務層之間移動索引。

否則，如果您不小心刪除索引，則用來建立和填入索引的應用程式程式碼就是 [原本就還原] 選項。 若要重建索引，請先將索引刪除 (假設它存在)，在服務中重新建立索引，然後從您的主要資料存放區擷取資料以重新載入。

## <a name="scale-up-or-down"></a>擴大或縮小規模

每個搜尋服務都會以一個複本和一個資料分割的最小值開始執行。 如果您註冊的階層[支援更多容量](search-limits-quotas-capacity.md)，**請按一下左側流覽窗格中的**[調整]，以調整資源使用量。

當您透過任何資源加入處理能力時，服務即會自動使用這些資源。 您無須再執行其他動作，但在新資源產生作用前，會有些許的延遲。 佈建其他資源需要 15 分鐘或更久的時間。

### <a name="add-replicas"></a>新增複本

系統會藉由新增複本來增加每秒查詢 (QPS) 數量或達到高可用性。 每個複本都有一個索引的副本，因此再新增一個複本就會轉譯出可用來處理服務查詢要求的索引。 高可用性至少需要3個複本（如需詳細資訊，請參閱[調整容量](search-capacity-planning.md)）。

有許多複本的搜尋服務可透過大量索引來達到查詢要求的負載平衡。 假設有一定等級的查詢量，當有愈多服務要求可用的索引副本時，查詢輸送量的速度就會愈快。 如果您有查詢延遲的經驗，可以期待線上有其他複本時，對效能所造成的正面影響。

雖然當您新增複本時會提高輸送量，但並不會以您新增至服務之複本的正好兩倍或三倍來計算提高程度。 所有搜尋應用程式皆會因為影響查詢效能的外部因素而受影響。 複雜的查詢和網路延遲是造成查詢回應次數變化的兩個因素。

### <a name="add-partitions"></a>新增資料分割

新增複本較常見，但當儲存體受到限制時，您可以新增分割區以取得更多容量。 您布建服務的層級會決定是否可以新增資料分割。 基本層已鎖定于一個分割區。 標準層和更新版本支援額外的資料分割。

分割區會以12的倍數（具體而言是1、2、3、4、6或12）新增。 這是分區化的構件。 索引會建立在 12 個分區中，並且可以全部儲存在 1 個資料分割中，或平均分配在 2、3、4、6 或 12 個資料分割中 (每個資料分割分配一個分區)。

### <a name="remove-replicas"></a>移除複本

高查詢量期間過後，在搜尋的查詢負載量回到標準時 (例如，假日銷售結束後)，您可以使用滑桿減少複本。 無須再執行其他步驟。 降低複本數量會讓出資料中心內的虛擬機器。 相較於先前的情況，現在會在較少的 VM 上執行您的查詢和資料擷取作業。 最低需求是一個複本。

### <a name="remove-partitions"></a>移除資料分割

相較於您無須付出多餘動作就可移除複本，如果您使用的儲存體大於可減少的儲存體，您可能需要完成一些工作。 例如，如果您的解決方案使用三個資料分割，則如果新的儲存空間小於裝載索引所需的空間，則減少為一或兩個資料分割將會產生錯誤。 如您可能預期的，您可以選擇刪除索引或相關索引內的文件來釋出空間，或保持目前設定。

沒有任何偵測方法可告訴您哪個索引分區儲存在哪個特定資料分割中。 每個資料分割提供大約 25 GB 的儲存體，因此您需要將儲存體減少至可讓您擁有的資料分割數量所能容納的大小。 如果您想還原成一個資料分割，所有 12 個分區皆要能符合。

若要協助進行未來規劃，您可能需要檢查儲存體 (使用[取得索引統計資料](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) 以了解實際上您可以使用的空間大小。 

## <a name="next-steps"></a>後續步驟

* 使用[PowerShell](search-manage-powershell.md)進行自動化

* 審查[效能和優化](search-performance-optimization.md)技術

* 審查[安全性功能](search-security-overview.md)來保護內容和作業

* 啟用[診斷記錄](search-monitor-logs.md)來監視查詢和編制索引工作負載
