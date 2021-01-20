---
title: 使用 Azure Migrate 評估 VMware VM 以移轉至 Azure VMware 解決方案 (AVS)
description: 了解如何使用 Azure Migrate 伺服器評估來評估 VMware VM 是否可移轉至 AVS。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: e57084dab00210802edbd46e3380313e034eb036
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98566800"
---
# <a name="tutorial-assess-vmware-vms-for-migration-to-avs"></a>教學課程：評估 VMware VM 以移轉至 AVS

在您移轉至 Azure 的過程中，會評估您的內部部署工作負載，以測量雲端整備程度、找出風險，以及預估成本和複雜度。

本文說明如何使用 Azure Migrate 來評估已探索的 VMware 虛擬機器 (VM) 是否可移轉至 Azure VMware 解決方案 (AVS)：伺服器評量工具。 AVS 是一項受管理的服務，可讓您在 Azure 中執行 VMware 平台。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
- 根據電腦中繼資料和設定資訊執行評量。
- 根據效能資料執行評量。

> [!NOTE]
> 教學課程顯示嘗試案例的最快路徑，並且在可行時使用預設選項。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。



## <a name="prerequisites"></a>必要條件

在您遵循此教學課程來評估您的機器是否可移轉至 AVS 之前，請確定已探索到您想要評估的機器：

- 若要使用 Azure Migrate 設備探索機器，請[遵循此教學課程](tutorial-discover-vmware.md)。 
- 若要使用匯入的 CSV 檔案探索機器，請[遵循此教學課程](tutorial-discover-import.md)。


## <a name="decide-which-assessment-to-run"></a>決定要執行的評量


決定您是否想要根據依內部部署或動態效能資料收集的電腦設定資料/中繼資料，使用調整大小準則來執行評量。

**評量** | **詳細資料** | **建議**
--- | --- | ---
**依內部部署** | 根據電腦設定資料/中繼資料進行評估。  | 在 AVS 中，建議的節點大小是根據內部部署 VM 大小，以及您在評估中指定的節點類型、儲存體類型和失敗容許設定。
**以效能為基礎** | 根據所收集的動態效能資料進行評估。 | 在 AVS 中，建議的節點大小是根據 CPU 和記憶體使用量資料，以及您在評估中指定的節點類型、儲存體類型和失敗容許設定。

## <a name="run-an-assessment"></a>執行評估

執行評估，如下所示：

1. 在 [伺服器] 頁面 > [Windows 和 Linux 伺服器] 上，按一下 [評估和遷移伺服器]。

   ![評估和遷移伺服器按鈕的位置](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. 在 [**Azure Migrate：伺服器評量]** 中，按一下 [評估]。

1. 在 [評估伺服器]  >  [評量類型] 中，請選取 [Azure VMware 解決方案 (AVS) (預覽)]。

1. 在 [探索來源] 中：

    - 如果您使用設備探索到機器，請選取 [從 Azure Migrate 設備探索到的機器]。
    - 如果您使用匯入的 CSV 檔案探索到機器，請選取 [匯入的機器]。 
    
1. 按一下 [ **編輯** ] 以檢查評量屬性。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="用於選取評量設定的頁面":::
 

1. 在 [評量屬性] > [目標屬性] 中：

    - 在 [目標位置] 中，指定您要將機器遷移到其中的 Azure 區域。
       - 大小和成本建議是根據您指定的位置。
       - 您目前可以評定四個區域 (澳大利亞東部、美國東部、西歐、美國西部) 
   - **儲存體類型** 預設為 **vSAN**。 這是適用於 AVS 私人雲端的預設儲存體類型。
   - AVS 節點目前不支援 [保留執行個體]。
1. 在 [VM 大小] 中：
    - **節點類型** 預設為 **AV36**。 Azure Migrate 會建議將 VM 遷移至 AVS 所需節點中的節點。
    - 在 [FTT 設定] 的 [ **raid 等級**] 中，選取 [容錯和 Raid 組合失敗]。  選取的 FTT 選項 (結合內部部署 VM 磁片需求) 會決定 AVS 中所需的 vSAN 儲存空間總計。
    - 在 [CPU 超額訂閱] 中，指定與 AVS 節點中的一個實體核心相關聯虛擬核心的比率。 大於 4:1 的超額訂閱可能會導致效能降低，但可用於網頁伺服器類型工作負載。

1. 在 [節點大小] 中： 
    - 在 [調整大小準則] 中，選取是否要根據靜態中繼資料或以效能為基礎的資料進行評量。 如果您使用效能資料：
        - 在 [效能歷程記錄] 中，指出您想要作為評量基礎的資料持續時間
        - 在 [百分位數使用率] 中，指定您想要用於效能範例的百分位數值。 
    - 在 [緩和因數] 中，指出您想要在評量期間使用的緩衝區。 這會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。 例如，如果您使用兩個緩和因數：
    
        **元件** | **有效使用率** | **新增緩和因數 (2.0)**
        --- | --- | ---
        核心 | 2  | 4
        記憶體 | 8 GB | 16 GB  

1. 在 [價格] 中：
    - 在 [供應項目] 中，會顯示您所註冊的 [Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)，伺服器評量會評估該供應項目的成本。
    - 在 [貨幣] 中，選取您帳戶的帳單貨幣。
    - 在 [折扣 (%)] 中，在 Azure 供應項目上新增您獲得的任何訂用帳戶特定折扣。 預設設定為 0%。

1. 如果您進行變更，請按一下 [儲存]。

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="評量屬性":::

1. 在 [評估伺服器] 中，按 [下一步]。

1. 在 [**選取要評估** 評量的電腦]  >   > 指定評量的名稱。 
 
1. 在 [ **選取或建立群組** ] > 選取 [ **建立新** 的]，並指定組名。 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="將 VM 新增至群組":::
 
1. 選取設備，然後選取您想要新增至群組的 VM。 然後按一下 [下一步]  。

1. 在 [檢閱 + 建立評量] 中，檢閱評量詳細資料，然後按一下 [建立評量] 以建立群組並執行評量。

    > [!NOTE]
    > 針對以效能為基礎的評量，建議您在建立評量之前，先等待至少一天後再開始探索。 這可提供更高的信賴度來收集效能資料。 在理想的情況下，在您開始探索之後，請等候您指定的效能持續時間 (天/週/月)，以取得高信賴度。

## <a name="review-an-assessment"></a>檢閱評量

AVS 評量會說明：

- AVS 整備程度：內部部署 VM 是否適合移轉至 Azure VMware 解決方案 (AVS)。
- AVS 節點數目：執行 VM 所需的估計 AVS 節點數目。
- 各種 AVS 節點的使用率：跨所有節點的預估 CPU、記憶體和儲存體使用率。
    - 使用率包括在下列叢集管理負荷中預先進行的分解，例如 vCenter Server、NSX Manager (大型) 、NSX Edge、如果部署 HCX，同時也會使用 HCX 管理員和 IX 設備，在壓縮和重復資料刪除之前耗用 44vCPU (11 CPU) 、75GB RAM 和722GB 儲存體。 
    - 記憶體、重複配置和壓縮目前設定為100% 的記憶體使用率和1.5 重複使用和壓縮，這會是未來版本中使用者定義的輸入，讓使用者能夠微調其所需的大小。
- 每月成本預估：執行內部部署 VM 的所有 Azure VMware 解決方案 (AVS) 節點的預估每月成本。

## <a name="view-an-assessment"></a>檢視評估

若要檢視評估：

1. 在 伺服器 > **Azure Migrate：伺服器評量** 中，按一下 評量 旁邊的數字。

1. 在 [評估] 中，選取評估來加以開啟。 作為範例 (估計和成本僅供範例使用)： 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="AVS 評估摘要":::

1. 檢閱評量摘要。 您也可以編輯評量屬性，或重新計算評量。
 

### <a name="review-readiness"></a>檢閱整備程度

1. 按一下 [Azure 移轉整備程度]。
2. 在 [Azure 移轉整備程度] 中，檢閱 VM 狀態。

    - **可供 AVS 使用**：機器不進行任何變更，即可依原樣移轉至 Azure AVS。 電腦將會在 AVS 中啟動，並提供完整的 AVS 支援。
    - **有條件的備妥**：電腦可能與目前的 vSphere 版本有相容性問題。 其可能需要安裝 VMware 工具或其他設定，才能在 AVS 中擁有完整的功能。
    - **未備妥供 AVS 使用**：VM 將不會在 AVS 中啟動。 例如，如果內部部署 VMware VM 已附加外部裝置 (如 CD-ROM)，且您使用 VMware VMotion，則 VMotion 作業會失敗。
 - **移轉整備程度未知**：Azure Migrate 無法判斷機器是否就緒，因為從內部部署環境收集的中繼資料不足。

3. 請參閱建議的工具。

    - VMware HCX 或 Enterprise：針對 VMware 機器，VMWare 混合式雲端擴充功能 (HCX) 解決方案是建議的遷移工具，可將您的內部部署工作負載遷移至 Azure VMware 解決方案 (AVS) 私人雲端。 進一步瞭解。
    - 未知：針對透過 CSV 檔案匯入的電腦，預設的移轉工具是未知的。 但對於 VMware 機器，建議使用 VMware 混合式雲端擴充功能 (HCX) 解決方案。
4. 按一下 [AVS 移轉整備程度] 狀態。 您可以檢視 VM 的整備程度詳細資料，並向下切入以查看 VM 詳細資料，包括計算、儲存體和網路設定。

### <a name="review-cost-estimates"></a>檢閱成本估計

此評估摘要會顯示在 Azure 中執行 VM 的計算和儲存體預估成本。 

1. 檢閱每月總成本。 系統會針對評估群組中的所有 VM 匯總成本。

    - 成本預估是以考慮所有 VM 的資源需求所需的 AVS 節點數目為基礎。
    - 因為 AVS 的價格是根據每個節點，所以總成本沒有計算成本和儲存體成本分佈。
    - 成本估計是用於執行 AVS 中的內部部署 VM。 Azure Migrate 伺服器評估不會考慮 PaaS 或 SaaS 的成本。

2. 檢閱每月的儲存體估計值。 此檢視會顯示評估群組的彙總儲存體成本，並分割為不同類型的儲存體磁碟。 
3. 您可以向下切入以查看特定 VM 的成本詳細資料。

### <a name="review-confidence-rating"></a>檢閱信賴評等

伺服器評量會將信賴評等指派給以效能為基礎的評量。 評等從一顆星 (最低) 到五顆星 (最高)。

![信賴評等](./media/tutorial-assess-vmware-azure-vmware-solution/confidence-rating.png)

信賴評等可協助您預估評量中的大小建議是否可靠。 此評等會以計算評估所需的資料點可用性作為基礎。

> [!NOTE]
> 如果您根據 CSV 檔案建立評量，則不會指派信賴評等。

信賴評等如下。

**資料點可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星

[深入了解](concepts-assessment-calculation.md#confidence-ratings-performance-based)信賴評等。

## <a name="next-steps"></a>下一步

- 使用[相依性對應](concepts-dependency-visualization.md)尋找電腦相依性。
- 設定[無代理程式](how-to-create-group-machine-dependencies-agentless.md)或[以代理程式為基礎](how-to-create-group-machine-dependencies.md)的相依性對應。
