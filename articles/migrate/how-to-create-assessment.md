---
title: 使用 Azure Migrate Server 評估來建立 Azure VM 評估 |Microsoft Docs
description: 說明如何使用 Azure Migrate Server 評估工具來建立 Azure VM 評估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: ec95cde1f023b4d034c2fae9cc5a54744ccdc9a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85549810"
---
# <a name="create-an-azure-vm-assessment"></a>建立 Azure VM 評量

本文說明如何為內部部署 VMware Vm 或具有 Azure Migrate： Server 評估的 Hyper-v Vm 建立 Azure VM 評估。

[Azure Migrate](migrate-services-overview.md)可協助您遷移至 Azure。 Azure Migrate 能提供集中式的中樞，以追蹤針對 Azure 的內部部署基礎結構、應用程式與資料的探索、評量及移轉。 該中樞能提供 Azure 工具以進行評量和移轉，也提供協力廠商獨立軟體廠商 (ISV) 供應項目。 

## <a name="before-you-start"></a>在您開始使用 Intune 之前

- 請確定您已[建立](how-to-add-tool-first-time.md)Azure Migrate 專案。
- 如果您已經建立專案，請確定您已[新增](how-to-assess.md)Azure Migrate：伺服器評估工具。
- 若要建立評量，您需要設定適用于[VMware](how-to-set-up-appliance-vmware.md)或[hyper-v](how-to-set-up-appliance-hyper-v.md)的 Azure Migrate 設備。 設備會探索內部部署機器，並將中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。 [深入了解](migrate-appliance.md)。


## <a name="azure-vm-assessment-overview"></a>Azure VM 評估總覽
有兩種類型的調整大小準則，您可以使用 Azure Migrate：伺服器評估來建立 Azure VM 評估。

**評量** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 以所收集效能資料為基礎的評估 | **建議的 VM 大小**：以 CPU 和記憶體使用量資料為基礎。<br/><br/> **建議的磁碟類型 (標準或進階受控磁碟**)：以內部部署磁碟的 IOPS 和輸送量為基礎。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的 VM 大小**：以內部部署 VM 大小為基礎<br/><br> **建議的磁碟類型**：以您為評估選取的儲存體類型設定為基礎。

[深入了解](concepts-assessment-calculation.md)評估。

## <a name="run-an-assessment"></a>執行評估

執行評估，如下所示：

1. 檢閱適用於建立評估的[最佳做法](best-practices-assessment.md)。
2. 在 [伺服器] 索引標籤的 **[Azure Migrate：伺服器評量]** 磚中，按一下 [評估]。

    ![評定](./media/how-to-create-assessment/assess.png)

3. 在 [**評估伺服器**] 中，選取 [Azure VM] 做為 [評估類型]，選取 [探索來源] 並指定評量名稱。

    ![評量基本概念](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. 按一下 [檢視全部] 來檢閱評估屬性。

    ![評量屬性](./media/how-to-create-assessment//view-all.png)

5. 按 **[下一步]** 以**選取要評估的機器**。 在 [選取或建立群組] 中，選取 [新建]，然後指定群組名稱。 群組會將一或多個 VM 收集在一起以進行評估。
6. 在 [將機器新增至群組] 中，選取要新增至群組的 VM。
7. 按 **[下一步]** 以**查看 [+ 建立評估**]，以查看評量詳細資料。
8. 按一下 [建立評估] 以建立群組，然後執行評估。

    ![建立評估](./media/how-to-create-assessment//assessment-create.png)

9. 評量建立好之後，可在 [伺服器] >  **[Azure Migrate：伺服器評量]**  > [評量] 中加以檢視。
10. 按一下 [匯出評估]，將其下載為 Excel 檔案。



## <a name="review-an-azure-vm-assessment"></a>審查 Azure VM 評估

Azure VM 評估會說明：

- **Azure 移轉整備程度**：VM 是否適合移轉至 Azure。
- **每月成本預估**：在 Azure 中執行 VM 的預估每月計算和儲存體成本。
- **每月儲存體成本預估**：移轉之後的磁碟儲存體預估成本。

### <a name="view-an-azure-vm-assessment"></a>查看 Azure VM 評估

1. 在 [移轉目標] >  [伺服器] 中，按一下 [Azure Migrate：伺服器評量] 中的 [評量]。
2. 在 [評量] 中，按一下評量來加以開啟。

    ![評量摘要](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>檢閱 Azure 移轉整備程度

1. 在 [Azure 移轉整備程度] 中，確認 VM 是否已準備好移轉至 Azure。
2. 檢查 VM 狀態：
    - **可供 Azure 使用**：Azure Migrate 會針對評估中的 VM 建議 VM大小和成本估計值。
    - **有條件的備妥**：顯示問題和建議的補救措施。
    - **未備妥供 Azure 使用**：顯示問題和建議的補救措施。
    - **移轉整備程度未知**：當 Azure Migrate 因資料可用性問題而無法評估整備程度時，便會使用此狀態。

3. 按一下 [Azure 移轉整備程度] 狀態。 您可以檢視 VM 的整備程度詳細資料，並向下切入以查看 VM 詳細資料，包括計算、儲存體和網路設定。



### <a name="review-cost-details"></a>檢閱成本詳細資料

此檢視會顯示在 Azure 中執行 VM 的計算和儲存體預估成本。

1. 檢閱每月的計算和儲存體成本。 系統會針對評估群組中的所有 VM 匯總成本。

    - 系統會根據機器的大小建議和其磁碟與屬性來預估成本。
    - 系統會顯示計算和儲存體的每月預估成本。
    - 以 IaaS VM 的形式來執行內部部署 VM 時，才能估計成本。 Azure Migrate 伺服器評估不會考慮 PaaS 或 SaaS 的成本。

2. 您可以檢閱每月的儲存體成本估計值。 此檢視會顯示評估群組的彙總儲存體成本，並分割為不同類型的儲存體磁碟。
3. 您可以向下切入以查看特定 VM 的詳細資料。


### <a name="review-confidence-rating"></a>檢閱信賴評等

當您執行以效能為基礎的評估時，系統會對評估指派信賴評等。

![信賴評等](./media/how-to-create-assessment/confidence-rating.png)

- 會給予 1 星 (最低) 到 5 星 (最高) 的評等。
- 信賴評等可協助您預估評估作業所提供的大小建議是否可靠。
- 信賴評等會以計算評估所需的資料點可用性為基礎。

評估的信賴評等如下所示。

**資料點可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星




## <a name="next-steps"></a>後續步驟

- 瞭解如何使用相依性[對應](how-to-create-group-machine-dependencies.md)來建立高度信賴群組。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
