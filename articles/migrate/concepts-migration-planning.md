---
title: 使用 Azure Migrate 建立遷移計畫 |Microsoft Docs
description: 提供使用 Azure Migrate 建立遷移計畫的指引。
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: 6629f02178b7c00ccc849e2ccfc3f0a48f419a7f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735554"
---
# <a name="build-migration-plan-with-azure-migrate"></a>使用 Azure Migrate 建立移轉計劃

本文提供快速指南，可協助您使用 [Azure Migrate](migrate-services-overview.md)建立 Azure 的遷移計畫。如果您有其他問題，請查看下列資源：

- 關於 Azure Migrate 的[一般問題](resources-faq.md)
- [Azure Migrate 設備](common-questions-appliance.md)的相關問題
- [伺服器遷移](common-questions-server-migration.md)的相關問題
- 在[Azure Migrate 論壇](https://docs.microsoft.com/answers/topics/azure-migrate.html)中取得問題的解答

## <a name="define-the-goals-of-cloud-migration"></a>定義雲端遷移的目標

在建立遷移計畫之前，請務必瞭解並評估移至雲端的 [動機](/azure/cloud-adoption-framework/strategy/motivations) ，以協助產生更成功的業務成果。 因為 [Azure 的雲端採用架構](/azure/cloud-adoption-framework) 所述，可能會有適用于您企業的不同觸發程式和遷移方法：  

**重大商業事件** | **遷移結果**
--- | ---
資料中心的退出 | 節省成本
合併、收購或撤資 | 降低廠商或技術上的複雜性
減少資本支出 | 追求內部營運的最佳化
終止對任務關鍵性技術的支援 | 提升業務靈活性
對法規合規性變更的回應 | 為了準備新技術能力
新的資料主權需求 | 調整以符合市場需求
減少中斷和 IT 穩定性的改進 | 調整以符合地理需求

您的遷移動機也有助於反映您想要藉由遷移至 Azure 達成的策略性目標和結果。 下一步是識別並規劃針對您的工作負載量身打造的 Azure 遷移路徑。 Azure Migrate：伺服器評量工具可協助您評估內部部署工作負載，並提供指引和工具來協助您進行遷移。

## <a name="understand-your-digital-estate"></a>瞭解您的數位資產

開始瞭解您的內部部署基礎結構、應用程式和相依性，以協助識別您想要遷移至 Azure 的工作負載，並取得優化的成本預測。 伺服器評量工具可協助您回答下列問題：

### <a name="what-workloads-are-in-use"></a>哪些工作負載正在使用中？

使用輕量 Azure Migrate 設備來執行內部部署 VMware Vm、Hyper-v Vm 和實體伺服器的無代理程式探索。 連續探索會收集電腦設定和效能中繼資料，也可以用來取得已安裝應用程式的清查，以及在內部部署機器上執行的角色/功能。 Azure Migrate 設備會收集：

- 機器、磁片和 Nic 的中繼資料詳細資料

- 已安裝的應用程式，包括應用程式和角色/功能  

- 效能資料，包括 CPU 與記憶體使用率、磁片 IOPS 和輸送量

接下來，匯出應用程式清查清單以找出工作負載上執行的所有 SQL Server 實例，並使用 Azure Migrate：資料庫評估工具來瞭解其就緒程度。

 ![入口網站上的應用程式清查](./media/concepts-migration-planning/application-inventory-portal.png)

 ![應用程式清查匯出](./media/concepts-migration-planning/application-inventory-export.png)

除了伺服器評量工具的探索資料，您也可以使用現有的 CMDB 資料來建立您的伺服器和資料庫資產的觀點，並瞭解跨業務單位、應用程式擁有者、地理位置等的伺服器散發，進而有助於優先處理要遷移的工作負載。

### <a name="what-dependencies-exist-between-workloads"></a>工作負載之間有哪些相依性？

一旦探索到伺服器之後，請使用無代理程式相依性對應來視覺化和識別將相依的伺服器移至 Azure 的跨伺服器相依性和優化策略。 視覺效果有助於瞭解特定電腦是否正在使用中，或者是否可將它們解除委任而不是遷移。  請確定您分析了相依性，以確保不會留下任何內容，並避免在遷移期間意外中斷。 當您執行應用程式清查和相依性對應之後，您就可以建立高度信賴的群組，並開始評估您的伺服器。

 ![相依性對應](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>它們的優化和大小是否正確？

因為 Azure 可讓您彈性地調整隨時間調整的雲端容量，所以您可以利用遷移來將配置給伺服器的 CPU 和記憶體資源優化。 在所識別的群組上建立評量，以瞭解工作負載的效能歷程記錄，這對於在 Azure 上 rightsized VM Sku 和磁片建議非常重要。

## <a name="assess-your-readiness-for-migration"></a>評估您的準備以進行遷移

### <a name="readiness-and-suitability-analysis-for-azure"></a>Azure 的就緒和適用性分析
匯出 Azure VM 評量報告，並依下列準備類別篩選，以瞭解 Azure 的 VM 就緒程度：

- **適用于 azure** ：您可以在不進行任何變更的情況下，依原樣將這些機器遷移至 azure  

- **有條件地準備好用於 azure** ：您可以將這些機器遷移至 azure，但您需要在這些伺服器上根據評量中提供的補救指導方針進行輕微變更

- **尚未準備好進行 azure** ：您無法依原樣將這些機器遷移至 Azure，而且需要根據補救指導方針修正問題，再進行遷移

- **就緒狀態不明** ：由於中繼資料不足，Azure Migrate 無法判斷電腦是否就緒

您可以使用資料庫評量來評估將 SQL Server 資料資產遷移至 Azure SQL Database 或 Azure SQL 受控實例的準備工作。 您可以看到每個 SQL server 實例的遷移準備就緒狀態百分比。 此外，針對每個實例，您可以在 Azure 中看到建議的目標、可能的遷移封鎖器、重大變更計數、Azure SQL DB/Azure SQL VM 的就緒程度，以及相容性層級。 您可以更深入瞭解遷移封鎖程式的影響，以及修正它們的建議。

 ![資料庫評量](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>大小調整建議

電腦標示為適用于 Azure 之後，伺服器評量會進行調整大小建議，以識別 Vm 的 Azure VM 和磁片 SKU。 您可以根據效能歷程 (，選擇查看大小調整建議，讓您在遷移) 時優化資源，或根據其內部部署設定，而不考慮效能歷程記錄。 針對資料庫，您可以在資料庫評量中查看有關資料庫 SKU、定價層和計算層級的建議。  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>計算評量以取得在 Azure 中執行工作負載的預估成本

評量中以 *效能為基礎* 的適當調整大小選項可讓您將 Azure 的工作負載優化。 除了擔心之外，還有一些其他途徑有助於節省成本：

- **保留** 實例：使用保留實例時，您可以大幅降低成本，相較于隨用隨付定價，在 Windows 和 Linux (vm 上使用1年期或3年期的隨用隨付定價) 

- **Azure 混合式優點** ：您可以將 Windows Server 內部部署授權與軟體保證整合到 Azure，並將它與保留實例選項結合

- **Enterprise 合約供應專案 (EA)** ： Enterprise 合約提供適用于您訂用帳戶的內建節省費用

- **優惠** ：有多個 Azure 供應專案，例如隨用隨付開發/測試和企業開發/測試，可為開發或測試 vm 提供較低的費率

- **VM 執行時間** ：您可以在 Azure vm 執行時，每個月以天為單位和每日的時間來提及，以降低您的成本 (不適用於 RI) 

- **目的地區域** ：您可以在不同區域中建立多個評量，以進行比較。

- 以 **效能為基礎的建議** ：最佳做法是嘗試使用 Rightsized 的 Azure VM 建議，以協助您節省雲端成本

### <a name="visualize-data"></a>顯現資料

您可以在入口網站中看到有 Azure 就緒和每月成本分配的伺服器評定報告，以及匯出評定以對探索和評量資料套用更多視覺效果，讓遷移計畫更豐富。 您可以針對不同的屬性組合建立多個評量，並選擇最適合您企業的屬性集。  

 ![評量概觀](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>評估差距和潛在阻礙

在判斷要遷移的應用程式和基礎結構時，請找出這些應用程式的停機條件約束，並在您的應用程式和基礎結構之間尋找任何作業的相依性。 此分析可協助您規劃符合復原時間目標 (RTO) 的遷移，並確保不會遺失任何資料。 在您遷移之前，建議您先檢查並減輕任何相容性問題或不受支援的功能，這些問題可能會導致無法透過伺服器評量報告和 Azure Migrate：資料庫評估建議來遷移您的伺服器和 SQL 資料庫。

### <a name="first-workloads-to-target-and-approach"></a>目標和方法的第一個工作負載

既然您已經擁有進行遷移決策的所有重要資訊，您應該優先處理應該先遷移哪些應用程式和工作負載。 開發「套用並學習」方法，以系統化且可控制的方式遷移您的預定應用程式，讓您可以在進行完整規模的遷移之前，先登機此策略中的任何瑕疵。 您也可以使用策略因素，例如複雜性和時間來遷移、業務緊迫、生產/非生產環境、合規性和安全性需求、應用程式知識等，以優先處理要遷移的應用程式群組。

以下是一些建議的遷移策略：

- **排定快速獲勝的優先順序** ：您可以使用評量報告來識別低度的水果（包括已完全就緒的伺服器和資料庫），並需要最少的努力遷移至 Azure：
    - Azure 移轉已就緒：匯出評量報告，並篩選所有「適用于 Azure」的電腦。 這可以是您可以隨即轉移 Azure Migrate：伺服器遷移工具的第一組電腦。
    - 作業系統終止支援：匯出評量報告，並篩選執行 Windows Server 2008 和 Windows Server 2008 R2 作業系統的所有電腦。 這些 Sku 已結束支援，而且只有 Azure 會在您將其遷移至 Azure 時，提供您3年的免費安全性更新。 當您結合、Azure Hybrid Benefit 和使用保留實例時，儲存可能會更高。
    - SQL Server 遷移：使用資料庫評估建議，以使用 Azure Migrate 來遷移適用于 Azure SQL 資料庫的資料庫：資料庫移轉和準備使用 Azure SQL VM 的資料庫（使用 Azure Migrate：伺服器遷移）。
    - 軟體終止支援：匯出應用程式清查，並篩選可能即將終止支援的任何軟體/延伸模組。 您應優先處理這些應用程式。
    - 過度布建的 Vm：匯出評定報告，並篩選出 CPU 使用率低 (% ) 和記憶體使用量 (% ) 的電腦。  您可以使用這個機會來遷移至 Azure 中的 rightsized VM，並省下您針對使用量過低之資源的費用。
    - 容量限制：匯出您的評定報告，並篩選出 CPU 使用率高 (% ) 和記憶體使用量 (% ) 的電腦。  您可以藉由將 Vm 遷移至 Azure 並利用自動調整功能來滿足需求，以防止 overstrained 的 Vm 中斷並提高效能。 您也可以查看評量報告，藉由分析磁片 IOPS 和輸送量，並找出最符合您需求的建議磁片類型，來瞭解您的儲存體限制。

- **從小規模開始，然後再** 開始：開始移動最少風險且較不復雜的應用程式和工作負載，以在您的遷移策略中建立信心。 您也可以與組織的 CMDB 存放庫交集 Azure Migrate 評量建議，以在試驗遷移中尋找並遷移開發/測試環境工作負載。 當您遷移生產工作負載時，可以使用這些試驗中的學習。  

- **符合您的法規/產業需求** ： Azure 會在廣泛和深度的供應專案中，維持產業的最大合規性組合。 您可以使用這種方式來排定遷移至 Azure 的優先順序，並符合您的國家、地區和業界特定標準和法律。 尤其是對於處理業務關鍵或保存機密資訊的組織來說，或在嚴格管制的產業中，為數眾多和特定案例中的標準和法規可能經常變更，因此難以趕上。  

## <a name="finalize-the-migration-planandprepare-formigration"></a>完成遷移計畫並準備進行遷移

在完成您的遷移計畫之前，請確定這些重要的遷移考慮不會阻礙至您的遷移規劃：

- 評估網路頻寬和延遲的條件約束，這可能會導致未預期的延遲，並中斷遷移複寫速度。

- 在遷移的應用程式上執行效能和使用者接受度測試的時間緩衝區，或執行任何遷移後的應用程式調整，例如更新資料庫連接字串和 web 伺服器設定、執行轉換和清除等等。

- 請參閱建議的 Azure 許可權，以及您的伺服器/資料庫存取角色和遷移所需的許可權模型。

- 準備您的組織，並確保員工與數位轉型的一致。 穩固的訓練基礎對於成功的組織變更很重要。 查看 [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF)提供的免費訓練，包括 Azure 基礎結構、解決方案架構和安全性的課程。 鼓勵您的團隊也能探索 [Azure 認證](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)   。  

- 如有需要，請取得您的實施支援。 許多組織都選擇外部協助來支援其雲端遷移。 若要快速且自信地使用個人化協助來移動到 Azure，請考慮使用 [Azure 專家受控服務提供者](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)   或 [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF)。  

建立有效的雲端遷移計畫，其中包含您想要遷移的應用程式清單/群組、應用程式和資料庫可用性和停機條件約束，以及所需的遷移里程碑等詳細資訊。 您的遷移計畫也應該考慮資料複製將採用的時間長度，並包含用於遷移後測試和轉換活動的明智緩衝區。 遷移後測試必須包含功能性、整合、安全性和效能測試使用案例，以確保已遷移的應用程式如預期般運作，而且所有資料庫物件和資料關聯性都已成功轉移至雲端。  

您可以使用此分析來建立遷移藍圖，並宣告維護時段，以最短到零的停機時間遷移您的應用程式和資料庫，並在遷移期間限制潛在的作業/業務影響。  

建議您一律先測試並繼續使用 Azure Migrate 的 *測試遷移* 功能，再將完整規模的遷移至 Azure 開始。 這種實際的資料將協助您估計所需的實際時間，並對您的遷移計畫進行必要的調整。 測試遷移也可讓您在實際的遷移發生之前，探索任何可能的遷移計畫問題，並加以修正。  

當您準備好遷移之後，請使用 Azure Migrate 的 *伺服器遷移工具* 和 Azure Migrate 的 *資料移轉服務* ，透過端對端追蹤進行順暢且整合的遷移體驗。 伺服器遷移工具支援在客戶的資料中心或任何其他私用或公用雲端（包括 AWS、GCP 等）中，遷移內部部署裝載的 Vm 和伺服器，並大約零停機。 Azure 資料庫移轉服務是一個完全受控的服務，能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低。  

> [!NOTE]
> 若為 VMware Vm，伺服器評量會使用 vCenter Server 中為 VM 指定的作業系統來處理「客體作業系統」分析。 針對在 VMware 上執行的 Linux Vm，它目前不會識別虛擬作業系統的確切核心版本。

## <a name="next-steps"></a>後續步驟

- 調查 Azure 雲端採用架構中的 [雲端遷移旅程](/azure/architecture/cloud-adoption/getting-started/migrate)   。
- Azure Migrate[入門](https://youtu.be/wFfq3YPxYHE)。
- 建立 [VMware vm](./tutorial-assess-vmware-azure-vm.md) 或 [hyper-v vm](tutorial-assess-hyper-v.md)的評量。
