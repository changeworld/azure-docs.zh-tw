---
title: 使用 Azure Migrate 建立遷移計畫 |Microsoft Docs
description: 提供使用 Azure Migrate 建立遷移計畫的指引。
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: rajosh
ms.openlocfilehash: 8f37814e29ce0089c26e235123768296efc2c0b0
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504901"
---
# <a name="build-migration-plan-with-azure-migrate"></a>使用 Azure Migrate 建立移轉計劃

遵循本文，使用 [Azure Migrate](migrate-services-overview.md)建立 Azure 的遷移計畫。 

## <a name="define-cloud-migration-goals"></a>定義雲端遷移目標

開始之前，您必須瞭解並評估移至雲端的 [動機](/azure/cloud-adoption-framework/strategy/motivations) ，才能促成成功的業務成果。 如 [雲端採用架構](/azure/cloud-adoption-framework)中所述，有許多觸發程式和結果。   

**商務活動** | **遷移結果**
--- | ---
資料中心的退出 | 成本 
合併、收購或撤資 | 減少廠商/技術複雜度
減少資本支出 | 追求內部營運的最佳化
終止對任務關鍵性技術的支援 | 提升業務靈活性
對法規合規性變更的回應 | 為了準備新技術能力
新的資料主權需求 | 調整以符合市場需求
減少中斷和 IT 穩定性的改進 | 調整以符合地理需求

識別您的動機可協助您釘選策略性的遷移目標。 下一步是識別並規劃針對您的工作負載量身打造的遷移路徑。 [Azure Migrate：伺服器評](migrate-services-overview.md#azure-migrate-server-assessment-tool)量工具可協助您評估內部部署工作負載，並提供指引和工具來協助您進行遷移。

## <a name="understand-your-digital-estate"></a>瞭解您的數位資產

首先，請識別您的內部部署基礎結構、應用程式和相依性。 這可協助您識別要遷移至 Azure 的工作負載，並收集優化的成本預測。 伺服器評量工具可協助您識別您使用的工作負載、工作負載之間的相依性，以及工作負載優化。

### <a name="workloads-in-use"></a>使用中的工作負載

Azure Migrate 使用輕量 Azure Migrate 設備來執行內部部署 VMware Vm、Hyper-v Vm、其他虛擬化電腦和實體伺服器的無代理程式探索。 連續探索會收集電腦設定資訊和效能中繼資料，以及應用程式資料。 以下是設備從內部部署機器收集到的內容： 

- 機器、磁片和 NIC 中繼資料。

- 已安裝的應用程式、角色和功能。

- 效能資料，包括 CPU 和記憶體使用量、磁片 IOPS 和輸送量。

收集資料之後，您可以匯出應用程式清查清單來尋找應用程式，以及在您的電腦上執行的 SQL Server 實例。 您可以使用 Azure Migrate：資料庫評估工具來瞭解 SQL Server 的就緒程度。

 ![入口網站上的應用程式清查](./media/concepts-migration-planning/application-inventory-portal.png)

 ![應用程式清查匯出](./media/concepts-migration-planning/application-inventory-export.png)

除了使用伺服器評定工具探索到的資料之外，您還可以使用您的設定管理資料庫 (CMDB) 資料來建立伺服器和資料庫資產的觀點，以及瞭解您的伺服器在業務單位、應用程式擁有者、地理位置等之間的散發方式。這可協助您決定要優先進行遷移的工作負載。 

### <a name="dependencies-between-workloads"></a>工作負載之間的相依性

在伺服器探索之後，您可以 [分析](concepts-dependency-visualization.md)相依性、將跨伺服器相依性視覺化，並加以識別，以及將相依的伺服器移至 Azure 的優化策略。 視覺效果有助於瞭解特定電腦是否正在使用中，或者是否可解除委任，而不是被遷移。  分析相依性有助於確保不會留下任何內容，並在遷移期間意外中斷。 在您的應用程式清查和相依性分析完成後，您可以建立高度信賴的伺服器群組，並開始進行評估。

 ![相依性對應](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>優化和調整大小

Azure 可讓您彈性地調整隨時間調整的雲端容量，並讓您有機會將配置給伺服器的 CPU 和記憶體資源優化。 在您身分識別的伺服器上建立評量，可協助您瞭解工作負載效能歷程記錄。 這對於正確調整 Azure VM Sku 的大小和 Azure 中的磁片建議非常重要。

## <a name="assess-migration-readiness"></a>評定遷移的就緒程度


### <a name="readinesssuitability-analysis"></a>就緒/適用性分析

您可以匯出評量報告，並篩選這些類別以瞭解 Azure 的就緒程度：

- **適用于 azure** ：機器可以依原樣遷移至 Azure，而不需要進行任何變更。 
- 有 **條件地準備好使用 azure** ：機器可以遷移至 azure，但需要較小的變更，以符合評量中所提供的補救指導方針。
- **未準備好用於 azure** ：機器無法依原樣遷移至 Azure。 在遷移之前，必須先根據補救指導方針修正問題。 
- **就緒狀態不明** ： Azure Migrate 無法判斷電腦是否就緒，因為中繼資料不足。

您可以使用資料庫評估來評估 SQL Server 資料資產的就緒程度，以遷移至 Azure SQL Database 或 Azure SQL 受控實例。 評量會顯示每個 SQL server 實例的遷移就緒狀態百分比。 此外，針對每個實例，您可以在 Azure 中看到建議的目標、可能的遷移封鎖器、中斷的變更計數、Azure SQL DB 或 Azure SQL VM 的就緒程度，以及相容性層級。 您可以更深入瞭解遷移封鎖程式的影響，以及修正它們的建議。

 ![資料庫評量](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>大小調整建議

將機器標示為準備好進行 Azure 之後，伺服器評量會進行大小調整建議，以識別您電腦的 Azure VM SKU 和磁片類型。 您可以根據效能歷程記錄來取得大小調整建議 (以便在遷移) 時優化資源，或根據內部部署機器設定，而不需要效能歷程記錄。 在資料庫評估中，您可以看到資料庫 SKU、定價層和計算層級的建議。  

### <a name="get-compute-costs"></a>取得計算成本

Azure Migrate 評量中以效能為基礎的大小調整選項，可協助您將 Vm 調整為適當大小，並應作為在 Azure 中優化工作負載的最佳做法。 除了適當調整大小之外，還有其他幾個選項可協助您節省 Azure 成本： 

- **保留實例** ：使用 [保留實例 (RI)](https://azure.microsoft.com/pricing/reserved-vm-instances/)，相較于隨 [用隨付定價](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)，您可以大幅降低成本。
- **Azure Hybrid Benefit** ：透過 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)，您可以將內部部署 Windows Server 授權與使用中的軟體保證或 Linux 訂用帳戶整合到 Azure，並結合保留實例選項。
- **Enterprise 合約** ： azure [ENTERPRISE 合約 (EA)](../cost-management-billing/manage/ea-portal-agreements.md) 可提供 azure 訂用帳戶和服務的節省費用。
- **優惠** ：有多個 [Azure 優惠](https://azure.microsoft.com/support/legal/offer-details/)。 例如， [隨用隨付開發/測試](https://azure.microsoft.com/pricing/dev-test/)或 [Enterprise 開發/測試供應](https://azure.microsoft.com/offers/ms-azr-0148p/)專案，以針對開發/測試 vm 提供較低的費率
- **VM 執行時間** ：您可以每個月檢查幾天，以及 Azure vm 的每天執行時數。 當電腦不在使用中時，將其關閉可能會降低您的成本 (不適用於 RIs) 。
- **目的地區域** ：您可以在不同區域中建立評量，以找出遷移至特定區域是否可能更符合成本效益。 

### <a name="visualize-data"></a>顯現資料

您可以在入口網站中使用 Azure 就緒資訊和每月成本散發) 來查看伺服器評定報告 (。 您也可以匯出評量，並使用額外的視覺效果擴充您的遷移計畫。 您可以使用不同的屬性組合來建立多個評量，並選擇最適合您企業的屬性集。  

 ![評量概觀](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>評估差距/封鎖

當您瞭解您想要遷移的應用程式和工作負載時，請找出您想要遷移的應用程式和工作負載，並尋找您的應用程式與基礎結構之間的任何作業相依性。 這種分析可協助您規劃符合復原時間目標 (RTO) 的遷移，並確保資料遺失的程度降到零。 在遷移之前，建議您先檢查並減輕可能會封鎖伺服器/SQL database 遷移的任何相容性問題或不支援的功能。 Azure Migrate Server 評量報告和 Azure Migrate 資料庫評量可協助您進行此工作。 

### <a name="prioritize-workloads"></a>排定工作負載的優先順序

收集清查的相關資訊之後，您可以識別要先遷移的應用程式和工作負載。 開發「套用並學習」方法，以系統化且可控制的方式遷移應用程式，讓您可以在開始進行完整規模的遷移之前先消除任何瑕疵問題。

若要設定遷移順序的優先順序，您可以使用策略因素，例如複雜性、遷移時間、業務緊急、生產/非生產考慮、合規性、安全性需求、應用程式知識等等。 

一些建議：

- 設定 **快速獲勝的優先順序** ：使用評量報告來識別低度的水果（包括已完全就緒的伺服器和資料庫），並需要最少的努力遷移至 Azure。 下表摘要說明一些進行此作業的方式。

    **State** | **動作**
    --- | ---
    **Azure 就緒 Vm** | 匯出評量報告，並篩選具有 *適用于 Azure* 之狀態的所有電腦。 這可能是您使用 [Azure Migrate：伺服器遷移](migrate-services-overview.md#azure-migrate-server-migration-tool) 工具隨即轉移至 Azure 的第一組電腦。
    **終止支援的作業系統** | 匯出評量報告，並篩選所有執行 Windows Server 2008 R2/Windows Server 2008 的電腦。 這些作業系統是在終止支援的時候，只有 Azure 在您將其遷移至 Azure 時，才會提供三年的免費安全性更新。 如果您結合 Azure Hybrid Benefit 並使用 RIs，節省的成本可能會高出許多。
    **SQL Server 遷移** | 您可以使用「Azure Migrate：資料庫移轉工具」，利用資料庫評估建議來遷移可供 Azure SQL Database 的資料庫。 使用 Azure Migrate：伺服器遷移工具來遷移適用于 Azure SQL VM 的資料庫。
    **終止支援軟體** | 匯出應用程式清查，並篩選可能達到終止支援的任何軟體/延伸模組。 排定這些應用程式的遷移優先順序。
    **未布建的機器** | 匯出評量報告，並篩選低 CPU 使用率 (% ) 和記憶體使用量 (% ) 的電腦。  遷移至正確大小的 Azure VM，並節省使用量過低之資源的成本。
    **過度布建的機器** | 匯出評量報告，並篩選具有高 CPU 使用率的電腦 (% ) 和記憶體使用量 (% ) 。  藉由將這些機器遷移至 Azure，來解決容量限制、防止 overstrained 電腦中斷，以及提高效能。 在 Azure 中，請使用自動調整功能來滿足需求。<br/><br/> 分析評量報告以調查儲存體限制。 分析磁片 IOPS 和輸送量，以及建議的磁片類型。

- **從小規模開始，然後變大** ：先移動應用程式和工作負載，以大幅降低風險和複雜性，以培養您的遷移策略。 分析 Azure Migrate 評量建議和您的 CMDB 存放庫，以找出並遷移可能適合試驗遷移的開發/測試工作負載。 當您開始遷移生產工作負載時，從試驗遷移的意見反應和學習會很有説明。  
- **符合** ： Azure 會根據廣泛和深度的供應專案，維護業界最大的合規性組合。 使用合規性需求來排定遷移的優先順序，讓應用程式和工作負載符合您的國家、地區和業界特定標準和法律。 尤其是對於處理商務關鍵性程式、保存機密資訊或受嚴格管制的產業的組織而言，更是如此。 在這些類型的組織中，標準和法規為數眾多，而且可能經常變更，因此難以趕上。  

## <a name="finalize-the-migration-plan"></a>完成遷移計畫

在完成您的遷移計畫之前，請確定您考慮並減輕其他潛在的阻礙，如下所示： 

- **網路需求** ：評估網路頻寬和延遲條件約束，這可能會導致無法預期的延遲，以及遷移複寫速度的中斷情形。
- **測試/遷移後** 調整：允許時間緩衝區針對已遷移的應用程式進行效能和使用者接受度測試，或設定/調整遷移後的應用程式，例如更新資料庫連接字串、設定網頁伺服器、執行剪下轉移/清除等。
- **許可權** ：檢查建議的 Azure 許可權，以及遷移所需的伺服器/資料庫存取角色和許可權。
- **訓練** ：準備您的組織進行數位轉型。 穩固的訓練基礎對於成功的組織變更很重要。 查看 [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF)的免費訓練，包括 Azure 基本概念、解決方案架構和安全性的課程。 鼓勵您的團隊探索 [Azure 認證](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)。  
- **實作為支援** ：如有需要，請取得您的實施支援。 許多組織都選擇外部協助來支援其雲端遷移。 若要快速且自信地移動至 Azure，請考慮使用 [Azure 專家受控服務提供者](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)或 [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF)。  


建立有效的雲端遷移計畫，其中包含您想要遷移的應用程式、應用程式/資料庫可用性、停機條件約束，以及遷移里程碑的詳細資訊。 此計畫會考慮資料複製將花費的時間，並包含用於遷移後測試的實際緩衝區和剪下的活動。 

遷移後的測試計劃應包含功能性、整合、安全性和效能測試和使用案例，以確保遷移的應用程式如預期般運作，而且所有資料庫物件和資料關聯性都會成功傳輸至雲端。  

建立遷移藍圖，並宣告維護時段，以最短到零的停機時間遷移您的應用程式和資料庫，並在遷移期間限制潛在的營運和業務衝擊。  

## <a name="migrate"></a>移轉

建議您先在 Azure Migrate 中執行測試遷移，再開始進行完整規模的遷移。 測試遷移可協助您估計所需的時間，並調整您的遷移計畫。 它能讓您找出任何潛在的問題，並在完整遷移之前加以修正。

當您準備好進行遷移時，請使用 Azure Migrate：伺服器遷移工具和 Azure 資料移轉服務 (DMS) ，以進行端對端追蹤，以提供順暢且整合的遷移體驗。

- 使用伺服器遷移工具，您可以遷移內部部署 Vm 和伺服器，或位於其他私人或公用雲端的 Vm (包括 AWS、GCP) ，大約零停機時間。
- Azure DMS 提供完全受控的服務，其設計目的是為了讓您能夠從多個資料庫來源順暢地遷移到 Azure 資料平臺，以最短的停機時間。  

## <a name="next-steps"></a>後續步驟

- 調查 Azure 雲端採用架構中的 [雲端遷移旅程](/azure/architecture/cloud-adoption/getting-started/migrate)   。
- [快速流覽](migrate-services-overview.md)Azure Migrate，並觀看快速[入門影片](https://youtu.be/wFfq3YPxYHE)。
- 深入瞭解如何評估 Vm 以遷移至 [Azure vm](concepts-assessment-calculation.md)。
