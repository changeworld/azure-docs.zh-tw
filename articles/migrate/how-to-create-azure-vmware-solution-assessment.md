---
title: 使用 Azure Migrate 伺服器評量建立 AVS 評量 |Microsoft Docs
description: 說明如何使用 Azure Migrate Server 評定工具建立 AVS 評量
author: rashi-ms
ms.service: azure-migrate
ms.topic: article
ms.date: 06/26/2020
ms.author: rajosh
ms.openlocfilehash: 192780f1340b09cd2579e31f4023acb101d0e1f9
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358078"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>建立 (AVS) 評量的 Azure VMware 解決方案

本文說明如何使用 Azure Migrate：伺服器評量，為內部部署 VMware Vm 建立 Azure VMware 解決方案 (AVS) 評量。

[Azure Migrate](migrate-services-overview.md) 可協助您遷移至 Azure。 Azure Migrate 能提供集中式的中樞，以追蹤針對 Azure 的內部部署基礎結構、應用程式與資料的探索、評量及移轉。 該中樞能提供 Azure 工具以進行評量和移轉，也提供協力廠商獨立軟體廠商 (ISV) 供應項目。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

- 請確定您已 [建立](how-to-add-tool-first-time.md) Azure Migrate 專案。
- 如果您已建立專案，請確定您已 [新增](how-to-assess.md) Azure Migrate：伺服器評定工具。
- 若要建立評量，您必須設定 [VMware](how-to-set-up-appliance-vmware.md)的 Azure Migrate 設備，以探索內部部署電腦，並將中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。 [深入了解](migrate-appliance.md)。
- 您也可以 [將伺服器中繼資料](tutorial-assess-import.md) 以逗號分隔值匯入 (CSV) 格式。


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Azure VMware Solution (AVS) 評量總覽

您可以使用 Azure Migrate：伺服器評量來建立的評量有兩種。

**評量類型** | **詳細資料**
--- | --- 
**Azure VM** | 評估內部部署伺服器，並將其遷移至 Azure 虛擬機器。 <br/><br/> 您可以使用此評量類型來評定您的內部部署 [VMware vm](how-to-set-up-appliance-vmware.md)、 [hyper-v vm](how-to-set-up-appliance-hyper-v.md)和 [實體伺服器](how-to-set-up-appliance-physical.md) ，以遷移至 Azure。[深入瞭解](concepts-assessment-calculation.md)
**Azure VMware 解決方案 (AVS)** | 評估內部部署伺服器，並將其遷移至 [Azure VMware 解決方案 (AVS)](../azure-vmware/introduction.md)。 <br/><br/> 您可以使用此評量類型，評量內部部署 [VMware VM](how-to-set-up-appliance-vmware.md) 以移轉至 Azure VMware 解決方案 (AVS)。[深入了解](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Azure VMware 解決方案 (AVS) 評量目前為預覽狀態，而且只能針對 VMware Vm 建立。


您可以使用兩種類型的大小調整準則來建立 Azure VMware 解決方案 (AVS) 評量：

**評量** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 以內部部署 Vm 收集的效能資料為基礎的評量。 | **建議的節點大小**：根據 CPU 和記憶體使用率資料，以及您針對評量選取的節點類型、儲存體類型和 FTT 設定。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的節點大小**：根據內部部署 VM 大小，以及您為評量選取的節點類型、儲存體類型和 FTT 設定。


## <a name="run-an-azure-vmware-solution-avs-assessment"></a> (AVS) 評量執行 Azure VMware 解決方案

執行 Azure VMware 解決方案 (AVS) 評量，如下所示：

1. 檢閱適用於建立評估的[最佳做法](best-practices-assessment.md)。

2. 在 [伺服器] 索引標籤的 **[Azure Migrate：伺服器評量]** 磚中，按一下 [評估]。

    ![螢幕擷取畫面顯示在 [評量工具] 底下選取 [評定] Azure Migrate 伺服器。](./media/how-to-create-assessment/assess.png)

3. 在 [ **評估伺服器**] 中，選取 [Azure VMware SOLUTION (AVS) ] 的評量類型，選取探索來源並指定評定名稱。

    ![評量基本概念](./media/how-to-create-avs-assessment/assess-servers-avs.png)

4. 按一下 [檢視全部] 來檢閱評估屬性。

    ![AVS 評量屬性](./media/how-to-create-avs-assessment/avs-view-all.png)

5. 按一下 [下一步] 以**選取要評量的機器**。 在 [選取或建立群組] 中，選取 [新建]，然後指定群組名稱。 群組會將一或多個 VM 收集在一起以進行評估。

6. 在 [將機器新增至群組] 中，選取要新增至群組的 VM。

7. 按一下 下一步 以**檢閱+ 建立評量**來檢閱評量詳細資料。

8. 按一下 [建立評估] 以建立群組，然後執行評估。

    ![建立 AVS 評量](./media/how-to-create-avs-assessment/avs-assessment-create.png)

9. 評量建立好之後，可在 [伺服器] >  **[Azure Migrate：伺服器評量]**  > [評量] 中加以檢視。

10. 按一下 [匯出評估]，將其下載為 Excel 檔案。


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>複習 (AVS) 評量的 Azure VMware 解決方案

 (AVS) 評量的 Azure VMware 解決方案描述：

- **Azure VMware 解決方案 (avs) 準備**：內部部署 vm 是否適合遷移至 Azure VMware SOLUTION (AVS) 。
- **Avs 節點數目**：執行 vm 所需的最少 avs 節點數目。
- **跨 AVS 節點的使用率**：所有節點的預計 CPU、記憶體和儲存體使用率。
- **每月成本估計**：所有 Azure VMware 解決方案的每月預估成本 (AVS) 執行內部部署 vm 的節點。


### <a name="view-an-assessment"></a>檢視評估

1. 在 [移轉目標] >  [伺服器] 中，按一下 [Azure Migrate：伺服器評量] 中的 [評量]。

2. 在 [評量] 中，按一下評量來加以開啟。

    ![AVS 評估摘要](./media/how-to-create-avs-assessment/avs-assessment-summary.png)

### <a name="review-azure-vmware-solution-avs-readiness"></a>複習 Azure VMware Solution (AVS) 準備

1. 在 **Azure 就緒**狀態中，確認 vm 是否已準備好遷移至 AVS。

2. 檢查 VM 狀態：
    - **適用于 avs**：機器可以依原樣遷移至 AZURE (AVS) ，而不需要任何變更。 它會在具有完整 AVS 支援的 AVS 中開始。
    - **備妥條件**： vm 可能會有目前 vSphere 版本的相容性問題，而且需要有可能的 VMware 工具和或其他設定，才能在 AVS 中達成完整的虛擬機器功能。
    - **尚未準備好用於 avs**： VM 將不會在 avs 中啟動。 例如，如果內部部署 VMware VM 有連接的外部裝置（例如 cd-rom），則 VMotion 作業將會失敗 (如果使用 VMware VMotion) 。
    - **就緒狀態不明**： Azure Migrate 因為從內部部署環境收集的中繼資料不足，所以無法判斷電腦是否就緒。

3. 複習建議的工具：
    - **VMWARE HCX 或企業**：若為 vmware 機器，vmware 混合式雲端擴充 (HCX) 解決方案是建議的遷移工具，可將內部部署工作負載遷移至 Azure VMware 解決方案 (AVS) 私用雲端。 [深入了解](../azure-vmware/hybrid-cloud-extension-installation.md)。
    - **未知**：針對透過 CSV 檔案匯入的電腦，預設的移轉工具是未知的。 針對 VMware 機器，建議使用 VMware 混合式雲端擴充功能 (HCX) 解決方案。 

4. 按一下 [ **AVS 就緒** 狀態]。 您可以檢視 VM 的整備程度詳細資料，並向下切入以查看 VM 詳細資料，包括計算、儲存體和網路設定。



### <a name="review-cost-details"></a>檢閱成本詳細資料

此視圖會顯示在 Azure VMware Solution (AVS) 中執行 Vm 的預估成本。

1. 查看每月總成本。 系統會針對評估群組中的所有 VM 匯總成本。 

    - 成本預估是根據所有 Vm 的資源需求總計所需的 AVS 節點數目。
    - 因為 Azure VMware 解決方案 (AVS) 的定價是每個節點，所以總成本沒有計算成本和儲存體成本分配。
    - 成本估計適用于在 AVS 中執行內部部署 Vm。 Azure Migrate 伺服器評估不會考慮 PaaS 或 SaaS 的成本。
    
2. 您可以檢閱每月的儲存體成本估計值。 此檢視會顯示評估群組的彙總儲存體成本，並分割為不同類型的儲存體磁碟。

3. 您可以向下切入以查看特定 VM 的詳細資料。


### <a name="review-confidence-rating"></a>檢閱信賴評等

當您執行以效能為基礎的評估時，系統會對評估指派信賴評等。

![信賴評等](./media/how-to-create-assessment/confidence-rating.png)

- 會給予 1 星 (最低) 到 5 星 (最高) 的評等。
- 信賴評等可協助您預估評估作業所提供的大小建議是否可靠。
- 信賴評等會以計算評估所需的資料點可用性為基礎。
- 針對以效能為基礎的大小調整，伺服器評量中的 AVS 評定需要 CPU 和 VM 記憶體的使用量資料。 下列效能資料會收集，但不會用於 AVS 評量的大小調整建議：
  - 連接至 VM 的每個磁片的磁片 IOPS 和輸送量資料。
  - 針對連接至 VM 的每個網路介面卡，處理以效能為基礎的大小調整的網路 i/o。

評估的信賴評等如下所示。

**資料點可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星

[深入瞭解](concepts-azure-vmware-solution-assessment-calculation.md) 效能資料 


## <a name="next-steps"></a>後續步驟

- 瞭解如何使用相依性 [對應](how-to-create-group-machine-dependencies.md) 來建立高信賴度群組。
- [深入瞭解](concepts-azure-vmware-solution-assessment-calculation.md) 如何計算 AVS 評定。
