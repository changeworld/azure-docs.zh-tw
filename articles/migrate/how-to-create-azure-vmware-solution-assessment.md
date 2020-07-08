---
title: 使用 Azure Migrate Server 評估來建立 AVS 評量 |Microsoft Docs
description: 說明如何使用 Azure Migrate Server 評估工具來建立 AVS 評量
author: rashi-ms
ms.service: azure-migrate
ms.topic: article
ms.date: 06/26/2020
ms.author: rajosh
ms.openlocfilehash: 9dd6dba9b517c26277cc7f7e3c87df81b1c95648
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567199"
---
# <a name="create-an-azure-vmware-solution-avs-assessment"></a>建立 Azure VMware 解決方案（AVS）評估

本文說明如何使用 Azure Migrate： Server 評估來建立內部部署 VMware Vm 的 Azure VMware 解決方案（AVS）評量。

[Azure Migrate](migrate-services-overview.md)可協助您遷移至 Azure。 Azure Migrate 能提供集中式的中樞，以追蹤針對 Azure 的內部部署基礎結構、應用程式與資料的探索、評量及移轉。 該中樞能提供 Azure 工具以進行評量和移轉，也提供協力廠商獨立軟體廠商 (ISV) 供應項目。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

- 請確定您已[建立](how-to-add-tool-first-time.md)Azure Migrate 專案。
- 如果您已經建立專案，請確定您已[新增](how-to-assess.md)Azure Migrate：伺服器評估工具。
- 若要建立評估，您必須設定[VMware](how-to-set-up-appliance-vmware.md)的 Azure Migrate 設備，這會探索內部部署機器，並將中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。 [深入了解](migrate-appliance.md)。
- 您也可以匯入逗號分隔值（CSV）格式[的伺服器中繼資料](tutorial-assess-import.md)。


## <a name="azure-vmware-solution-avs-assessment-overview"></a>Azure VMware 解決方案（AVS）評定總覽

您可以使用 Azure Migrate：伺服器評量來建立的評量有兩種。

**評量類型** | **詳細資料**
--- | --- 
**Azure VM** | 將內部部署伺服器遷移至 Azure 虛擬機器的評量。 <br/><br/> 您可以使用此評量類型，評估您的內部部署[VMware vm](how-to-set-up-appliance-vmware.md)、 [hyper-v vm](how-to-set-up-appliance-hyper-v.md)和[實體伺服器](how-to-set-up-appliance-physical.md)，以遷移至 Azure。[深入瞭解](concepts-assessment-calculation.md)
**Azure VMware 解決方案 (AVS)** | 將您的內部部署伺服器遷移至[Azure VMware 解決方案（AVS）](https://docs.microsoft.com/azure/azure-vmware/introduction)的評量。 <br/><br/> 您可以使用此評量類型，評估您的內部部署[Vmware vm](how-to-set-up-appliance-vmware.md) ，以遷移至 Azure VMware 解決方案（AVS）。[深入瞭解](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Azure VMware 解決方案（AVS）評估目前為預覽狀態，而且只能針對 VMware Vm 建立。


有兩種類型的調整準則可讓您用來建立 Azure VMware 解決方案（AVS）評量：

**評量** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 根據收集到的內部部署 Vm 效能資料進行評量。 | **建議的節點大小**：根據 CPU 和記憶體使用量資料，以及您為評估選取的節點類型、儲存體類型和 FTT 設定。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的節點大小**：根據內部部署 VM 大小，以及您為評估選取的節點類型、儲存體類型和 FTT 設定。


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>執行 Azure VMware 解決方案（AVS）評估

執行 Azure VMware 解決方案（AVS）評估，如下所示：

1. 檢閱適用於建立評估的[最佳做法](best-practices-assessment.md)。

2. 在 [伺服器] 索引標籤的 **[Azure Migrate：伺服器評量]** 磚中，按一下 [評估]。

    ![評定](./media/how-to-create-assessment/assess.png)

3. 在 [**評估伺服器**] 中，選取 [Azure VMware 解決方案（AVS）] 做為 [評估類型]，選取 [探索來源] 並指定評量名稱。

    ![評量基本概念](./media/how-to-create-avs-assessment/assess-servers-avs.png)

4. 按一下 [檢視全部] 來檢閱評估屬性。

    ![AVS 評量屬性](./media/how-to-create-avs-assessment/avs-view-all.png)

5. 按 **[下一步]** 以**選取要評估的機器**。 在 [選取或建立群組] 中，選取 [新建]，然後指定群組名稱。 群組會將一或多個 VM 收集在一起以進行評估。

6. 在 [將機器新增至群組] 中，選取要新增至群組的 VM。

7. 按 **[下一步]** 以**查看 [+ 建立評估**]，以查看評量詳細資料。

8. 按一下 [建立評估] 以建立群組，然後執行評估。

    ![建立 AVS 評量](./media/how-to-create-avs-assessment/avs-assessment-create.png)

9. 評量建立好之後，可在 [伺服器] >  **[Azure Migrate：伺服器評量]**  > [評量] 中加以檢視。

10. 按一下 [匯出評估]，將其下載為 Excel 檔案。


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>檢查 Azure VMware 解決方案（AVS）評量

Azure VMware 解決方案（AVS）評估描述：

- **Azure Vmware 解決方案（AVS）就緒**：內部部署 vm 是否適合遷移至 Azure vmware 解決方案（AVS）。
- **Avs 節點數目**：執行 vm 所需的估計 AVS 節點數目。
- 各種**AVS 節點的使用率**：規劃所有節點的 CPU、記憶體和儲存體使用率。
- **每月成本估計**：執行內部部署 vm 的所有 Azure VMware 解決方案（AVS）節點的預估每月成本。


### <a name="view-an-assessment"></a>檢視評估

1. 在 [移轉目標] >  [伺服器] 中，按一下 [Azure Migrate：伺服器評量] 中的 [評量]。

2. 在 [評量] 中，按一下評量來加以開啟。

    ![AVS 評估摘要](./media/how-to-create-avs-assessment/avs-assessment-summary.png)

### <a name="review-azure-vmware-solution-avs-readiness"></a>審查 Azure VMware 解決方案（AVS）準備就緒

1. 在 [ **Azure 準備就緒**] 中，確認 vm 是否已準備好遷移至 AVS。

2. 檢查 VM 狀態：
    - **適用于 AVS**：機器可以在不進行任何變更的情況下，依現有方式遷移至 AZURE （AVS）。 它會在具有完整 AVS 支援的 AVS 中啟動。
    - **備妥條件**： vm 可能會有目前 vSphere 版本的相容性問題，以及在 AVS 中可達成 vm 的完整功能之前，需要可能的 VMware 工具和或其他設定。
    - **尚未準備好用於 avs**： VM 將不會在 avs 中啟動。 例如，如果內部部署 VMware VM 已連接外部裝置（例如 cd-rom），VMotion 作業將會失敗（如果使用 VMware VMotion）。
    - 已**就緒不明**：因為從內部部署環境收集的中繼資料不足，所以 Azure Migrate 無法判斷機器是否已就緒。

3. 請參閱建議的工具：
    - **VMWARE HCX 或 Enterprise**：針對 vmware 機器，vmware 混合式雲端擴充功能（HCX）解決方案是建議的遷移工具，可將內部部署工作負載遷移至 Azure VMware 解決方案（AVS）私人雲端。 [深入了解](https://docs.microsoft.com/azure/azure-vmware/hybrid-cloud-extension-installation)。
    - **未知**：對於透過 CSV 檔案匯入的機器，預設的遷移工具是未知的。 但對於 VMware 機器，建議您使用 VMWare 混合式雲端擴充功能（HCX）解決方案。 

4. 按一下 [ **AVS 準備就緒**] 狀態。 您可以檢視 VM 的整備程度詳細資料，並向下切入以查看 VM 詳細資料，包括計算、儲存體和網路設定。



### <a name="review-cost-details"></a>檢閱成本詳細資料

此視圖會顯示在 Azure VMware 解決方案（AVS）中執行 Vm 的預估成本。

1. 檢查每月總成本。 系統會針對評估群組中的所有 VM 匯總成本。 

    - 成本預估是以考慮所有 Vm 的資源需求所需的 AVS 節點數目為依據。
    - 因為 Azure VMware 解決方案（AVS）的定價是每個節點，所以總成本沒有計算成本和儲存體成本分佈。
    - 成本估計是用來執行 AVS 中的內部部署 Vm。 Azure Migrate 伺服器評估不會考慮 PaaS 或 SaaS 的成本。
    
2. 您可以檢閱每月的儲存體成本估計值。 此檢視會顯示評估群組的彙總儲存體成本，並分割為不同類型的儲存體磁碟。

3. 您可以向下切入以查看特定 VM 的詳細資料。


### <a name="review-confidence-rating"></a>檢閱信賴評等

當您執行以效能為基礎的評估時，系統會對評估指派信賴評等。

![信賴評等](./media/how-to-create-assessment/confidence-rating.png)

- 會給予 1 星 (最低) 到 5 星 (最高) 的評等。
- 信賴評等可協助您預估評估作業所提供的大小建議是否可靠。
- 信賴評等會以計算評估所需的資料點可用性為基礎。
- 針對以效能為基礎的大小調整，伺服器評估中的 AVS 評量需要 CPU 和 VM 記憶體的使用量資料。 會收集下列效能資料，但不會用於針對 AVS 評量的調整大小建議：
  - 連接至 VM 的每個磁片的磁片 IOPS 和輸送量資料。
  - 針對連接至 VM 的每個網路介面卡，處理以效能為基礎之大小調整的網路 i/o。

評估的信賴評等如下所示。

**資料點可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星

[深入瞭解](concepts-azure-vmware-solution-assessment-calculation.md)效能資料 


## <a name="next-steps"></a>後續步驟

- 瞭解如何使用相依性[對應](how-to-create-group-machine-dependencies.md)來建立高度信賴群組。
- [深入瞭解](concepts-azure-vmware-solution-assessment-calculation.md)如何計算 AVS 評估。
