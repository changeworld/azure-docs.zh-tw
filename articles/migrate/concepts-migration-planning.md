---
title: 使用 Azure Migrate 建立遷移計畫 |Microsoft Docs
description: 提供有關使用 Azure Migrate 建立遷移計畫的指引。
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: a7d22a6a2f7100826c865beec0d1cff09fc39660
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043235"
---
# <a name="build-migration-plan-with-azure-migrate"></a>使用 Azure Migrate 建立移轉計劃

這篇文章提供快速指南，協助您使用[Azure Migrate](migrate-services-overview.md)來建立 Azure 的遷移計畫。如果您有其他問題，請檢查下列資源：

- 關於 Azure Migrate 的[一般問題](resources-faq.md)
- [Azure Migrate 設備](common-questions-appliance.md)的相關問題
- [伺服器遷移](common-questions-server-migration.md)的相關問題
- 在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)中獲得解答

## <a name="define-the-goals-of-cloud-migration"></a>定義雲端遷移的目標

建立遷移計畫之前，請務必瞭解並評估要移至雲端的[動機](https://docs.microsoft.com/azure/cloud-adoption-framework/strategy/motivations)，以協助產生更成功的商務成果。 當[Azure 的雲端採用架構](https://docs.microsoft.com/azure/cloud-adoption-framework)說明時，可能會有不同的觸發程式和遷移方法適用于您的企業：  

**重大商業事件** | **遷移結果**
--- | ---
資料中心的退出 | 節省成本
合併、收購或 divestiture | 降低廠商或技術上的複雜性
縮減資本費用 | 追求內部營運的最佳化
終止對任務關鍵性技術的支援 | 提高企業的靈活性
法規合規性變更的回應 | 為了準備新技術能力
新的資料主權需求 | 調整以符合市場需求
降低中斷並改善 IT 穩定性 | 調整以符合地理需求

您的遷移動機也可以協助您反映出您想要藉由遷移至 Azure 來達成的策略性目標和結果。 下一個步驟是識別並規劃針對您的工作負載量身打造的 Azure 遷移路徑。 Azure Migrate：伺服器評定工具可協助您評估內部部署工作負載，並提供指引和工具來協助您進行遷移。

## <a name="understand-your-digital-estate"></a>瞭解您的數位資產

先瞭解您的內部部署基礎結構、應用程式和相依性，以協助識別您想要遷移至 Azure 的工作負載，並取得優化的成本預測。 伺服器評估工具可協助您回答下列問題：

### <a name="what-workloads-are-in-use"></a>使用哪些工作負載？

使用輕量 Azure Migrate 設備來執行內部部署 VMware Vm、Hyper-v Vm 和實體伺服器的無代理程式探索。 連續探索會收集電腦設定和效能中繼資料，也可用來取得已安裝應用程式的清查，以及在您的內部部署電腦上執行的角色/功能。 Azure Migrate 設備會收集：

- 機器、磁片和 Nic 的中繼資料詳細資料

- 已安裝的應用程式，包括應用程式和角色/功能  

- 效能資料，包括 CPU 和記憶體使用率、磁片 IOPS 和輸送量

接下來，匯出應用程式清查清單，找出工作負載上執行的所有 SQL Server 實例，並使用 Azure Migrate：資料庫評估工具來瞭解其就緒程度。

 ![入口網站上的應用程式清查](./media/concepts-migration-planning/application-inventory-portal.png)

 ![應用程式清查匯出](./media/concepts-migration-planning/application-inventory-export.png)

除了伺服器評估工具的探索資料之外，您還可以使用現有的 CMDB 資料來打造您的伺服器和資料庫資產的觀點，並瞭解跨業務單位、應用程式擁有者、地理位置等的伺服器散發，進而協助您排定要遷移的工作負載優先順序。

### <a name="what-dependencies-exist-between-workloads"></a>工作負載之間有哪些相關性？

探索到伺服器之後，使用無代理程式相依性對應來視覺化和識別跨伺服器相依性，以及將相互依存的伺服器移至 Azure 的優化策略。 視覺效果有助於瞭解特定電腦是否正在使用中，或者是否可以解除委任，而不是進行遷移。  請務必分析相依性，以確保不會留下任何內容，並避免在遷移期間發生意外的中斷。 當您執行應用程式清查和相依性對應之後，您就可以建立高度信賴群組，並開始評估伺服器。

 ![相依性對應](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>它們的優化和大小是否正確？

因為 Azure 提供彈性來調整一段時間的雲端容量，所以您可以將配置給伺服器的 CPU 和記憶體資源優化，以進行遷移。 在之前識別的群組上建立評量，以瞭解工作負載的效能歷程記錄，這對於在 Azure 上 rightsized VM Sku 和磁片建議非常重要。

## <a name="assess-your-readiness-for-migration"></a>評估您是否已準備好進行遷移

### <a name="readiness-and-suitability-analysis-for-azure"></a>適用于 Azure 的準備和適用性分析
匯出 Azure VM 評估報告，並依照下列準備類別進行篩選，以瞭解 Azure 的 VM 就緒程度：

- **適用于 azure**：您可以在不進行任何變更的情況下，將這些機器依現有方式遷移至 azure  

- **有條件地準備好 azure**.. 您可以將這些機器遷移至 azure，但您必須根據評估中提供的補救指導方針，在這些伺服器上進行些許變更。

- **尚未準備好用於 azure**：您無法依需求將這些機器遷移至 azure，而且需要根據補救指引來修正問題，然後再進行遷移

- 已**就緒不明**：因為中繼資料不足，所以 Azure Migrate 無法判斷機器是否已就緒

您可以使用資料庫評量，評估將 SQL Server 資料資產遷移至 Azure SQL Database 或 Azure SQL 受控實例的就緒程度。 您可以查看每個 SQL server 實例的遷移就緒狀態百分比。 此外，針對每個實例，您可以在 Azure 中看到建議的目標、可能的遷移封鎖程式、重大變更計數、Azure SQL DB/Azure SQL VM 的準備就緒，以及相容性層級。 您可以更深入瞭解遷移封鎖程式的影響，以及修正這些問題的建議。

 ![資料庫評量](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>大小調整建議

電腦標示為 [準備好用於 Azure] 之後，伺服器評估會進行大小調整建議，以識別 Vm 的 Azure VM 和磁片 SKU。 您可以選擇根據效能歷程記錄查看大小調整建議（以便在遷移時將資源優化），或根據其內部部署設定，而不考慮效能歷程記錄。 針對資料庫，您可以在資料庫評估中查看資料庫 SKU、定價層和計算層級的相關建議。  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>計算評估以取得在 Azure 中執行工作負載的預估成本

評量中以*效能為基礎*的適當調整大小選項，可讓您將 Azure 的工作負載優化。 除了規模小型化優化之外，還有一些其他的途徑可協助節省成本：

- **保留實例**：使用保留實例，您可以大幅降低成本，相較于隨用隨付定價，在 Windows 和 Linux （vm）上使用1年期或3年期的條款

- **Azure 混合式優點**.. 您可以將具有軟體保證的 Windows Server 內部部署授權帶入 azure，並將它與保留實例選項結合

- **Enterprise 合約供應專案（EA）**： Enterprise 合約提供適用于您訂用帳戶的內建節省費用

- 供應**專案：有**多個 Azure 供應專案，例如，隨用隨付開發/測試和企業開發/測試，為開發或測試 vm 提供較低的費率

- **VM 執行時間**：您可以在 Azure vm 執行以降低成本（不適用於 RI）時，提及每月的天數和每日小時數

- **目的地區域**：您可以在不同的區域中建立多個評量，以進行比較（如果遷移到地理位置中的特定區域可能更符合成本效益）

- 以**效能為基礎的建議**：最佳作法是嘗試使用 Rightsized 的 Azure VM 建議，以協助您節省雲端成本

### <a name="visualize-data"></a>顯現資料

您可以在入口網站中看到具有 Azure 就緒和每月成本分佈的伺服器評估報告，以及匯出評量，以對探索和評估資料套用更多視覺效果，讓遷移計畫更豐富。 您可以針對不同的屬性組合建立多個評量，並選擇最適合您企業的屬性集。  

 ![評量概觀](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>評估差距和潛在的阻礙

在決定要遷移的應用程式和基礎結構時，請識別這些應用程式的停機限制，並尋找應用程式與基礎結構之間的任何操作相依性。 此分析可協助您規劃符合復原時間目標（RTO）的遷移，並確保不會遺失資料的最小值。 在您遷移之前，建議您先檢查並減輕任何相容性問題或不支援的功能，以協助您在伺服器評估報告和 Azure Migrate：資料庫評估建議時，阻止遷移伺服器和 SQL 資料庫。

### <a name="first-workloads-to-target-and-approach"></a>目標和方法的第一個工作負載

既然您已擁有所有重要的資訊來進行遷移決策，您應該優先考慮應該遷移哪些應用程式和工作負載。 開發「套用並學習」的方法，以系統化且可控制的方式遷移您想要的應用程式，讓您可以在登機進行完整規模的遷移之前，消除此策略中的任何瑕疵。 您也可以使用策略因素（例如複雜性和時間）來遷移、營運緊急情況、生產/非生產環境、合規性和安全性需求、應用程式知識等等，以設定要遷移的應用程式群組的優先順序。

一些建議的遷移策略如下：

- 設定**快速 wins 的優先順序**：您可以使用評量報告來識別低度的水果，包括已完全準備好的伺服器和資料庫，而且需要最少的投入量來遷移至 Azure：
    - Azure 移轉已就緒：匯出評量報告，並篩選「準備好 Azure」的所有機器。 這可以是您可以隨即轉移 Azure Migrate：伺服器遷移工具的第一個電腦群組。
    - 作業系統終止支援：匯出您的評量報告，並篩選所有執行 Windows Server 2008 和 Windows Server 2008 R2 作業系統的機器。 這些 Sku 支援終止，只有 Azure 在您將其遷移至 Azure 時，才會提供您免費的3年安全性更新。 當您結合、Azure Hybrid Benefit 和使用保留實例時，儲存可能會更高。
    - SQL Server 遷移 .. 使用資料庫評估建議來遷移適用于 Azure SQL database 的資料庫，請使用 Azure Migrate：資料庫移轉和準備好用於 Azure SQL VM 的資料庫（使用 Azure Migrate：伺服器遷移）。
    - 軟體結束支援：匯出您的應用程式清查，並篩選任何可能達到終止支援的軟體/延伸模組。 您應該設定這些應用程式的優先順序。
    - 過度布建的 Vm：匯出評量報告，並篩選出 CPU 使用率低（%）的電腦和記憶體使用率（%）。  您可以使用此機會遷移至 Azure 中的 rightsized VM，並省下您為使用量過低的資源付費。
    - 容量限制：匯出您的評定報告，並篩選出高 CPU 使用率（%）的機器和記憶體使用率（%）。  您可以藉由將 overstrained Vm 遷移至 Azure，並利用自動調整功能來滿足需求，以避免其中斷並提升效能。 您也可以查看評估報告以瞭解您的儲存體條件約束，方法是分析磁片 IOPS 和輸送量，並找出最符合您需求的建議磁片類型。

- **從小規模開始，然後變大**：從移動具有最低風險且較不復雜的應用程式和工作負載開始，以在您的遷移策略中打造信心。 您也可以將您的 Azure Migrate 評量建議與組織的 CMDB 存放庫交集，以在試驗遷移中尋找及遷移開發/測試環境工作負載。 在遷移生產工作負載時，可以使用這些試驗中的學習。  

- **符合您的法規/產業需求**： Azure 會維護業界最大的合規性組合，並涵蓋廣度和深度的供應專案。 您可以使用這種方式來排定遷移至 Azure 的優先順序，並符合您的國家、地區和業界特定的標準和法律。 這特別適用于處理業務關鍵或保存機密資訊，或是在嚴格規範的產業中，而在某些情況下，為數眾多的標準和法規可能經常變更，因此難以趕上。  

## <a name="finalize-the-migration-planandprepare-formigration"></a>完成遷移計畫並準備進行遷移

在完成您的遷移計畫之前，請確定這些重要的遷移考慮不會阻礙您的遷移規劃：

- 評估網路頻寬和延遲的限制，這可能會造成未預期的延遲，並中斷了遷移複寫速度。

- 緩衝處理已遷移應用程式的效能和使用者接受度測試，或執行任何遷移後的應用程式調整，例如更新資料庫連接字串和 web 伺服器設定、執行轉換和清除等。

- 請參閱建議的 Azure 許可權，以及您的伺服器/資料庫存取角色和遷移所需的許可權模型。

- 準備您的組織，並確保員工與數位轉型一致。 穩固的訓練基礎對於成功的組織變更非常重要。 查看 [Microsoft Learn](https://docs.microsoft.com/learn/azure/?ocid=CM_Discovery_Checklist_PDF)提供的免費訓練，包括 Azure 基本概念、解決方案架構和安全性課程。 鼓勵您的團隊同時探索 [Azure 認證](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)   。  

- 如有需要，請取得您的執行支援。 許多組織都選擇可支援其雲端遷移的外部協助。 若要透過個人化協助快速且自信地移至 Azure，請考慮 [Azure 專家管理的服務提供者](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)   或 [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF)。  

建立有效的雲端遷移計畫，其中包含您想要遷移之應用程式清單/群組的詳細資訊、應用程式和資料庫可用性與停機時間限制，以及所需的遷移里程碑。 您的遷移計畫也應該納入資料複製所需的時間，並包含用於遷移後測試和轉換活動的明智緩衝區。 遷移後測試必須包含功能、整合、安全性和效能測試使用案例，以確保遷移的應用程式如預期般運作，而且所有資料庫物件和資料關聯性都已成功轉移到雲端。 遷移後測試必須包含功能、整合、安全性和效能測試使用案例，以確保遷移的應用程式如預期般運作，而且所有資料庫物件和資料關聯性都已成功轉移到雲端。  

使用此分析來建立遷移藍圖，並宣告維護期間，以最少的停機時間遷移您的應用程式和資料庫，並在遷移期間限制潛在的營運/業務影響。  

我們建議您一律先測試並繼續使用 Azure Migrate 的 *測試遷移*功能，然後再開始至 Azure 的完整規模遷移。 這種實際資料可協助您估計相關的實際時間，並對您的遷移計畫進行必要的調整。 測試遷移也讓您有機會找出遷移計畫的任何潛在問題，並在實際的遷移進行之前加以修正。  

當您準備好進行遷移時，請使用 Azure Migrate 的*伺服器遷移工具*和 Azure Migrate 的*資料移轉服務*，取得順暢且整合的遷移體驗與端對端追蹤。 伺服器遷移工具支援在客戶的資料中心或任何其他私人或公用雲端（包括 AWS、GCP 等）中，遷移裝載于內部部署的 Vm 和伺服器，大約零停機時間。 Azure 資料庫移轉服務是一個完全受控的服務，能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低。  

> [!NOTE]
> 針對 VMware Vm，伺服器評估會使用在 vCenter Server 中為 VM 指定的作業系統來處理虛擬機器作業系統分析。 對於在 VMware 上執行的 Linux Vm，它目前不會識別虛擬作業系統的確切核心版本。

## <a name="next-steps"></a>後續步驟

- 調查 Azure 雲端採用架構中的 [雲端遷移旅程](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate)   。
- [開始](https://youtu.be/wFfq3YPxYHE)使用 Azure Migrate。
- 建立[VMware vm](tutorial-assess-vmware.md)或[hyper-v vm](tutorial-assess-hyper-v.md)的評量。
