---
title: 使用 Azure 遷移伺服器評估創建評估 |微軟文檔
description: 介紹如何使用 Azure 遷移伺服器評估工具創建評估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68229098"
---
# <a name="create-an-assessment"></a>建立評估

本文介紹如何為具有 Azure 遷移的本地 Vm 或超 VM 創建評估：伺服器評估。

[Azure 遷移](migrate-services-overview.md)可説明您遷移到 Azure。 Azure Migrate 能提供集中式的中樞，以追蹤針對 Azure 的內部部署基礎結構、應用程式與資料的探索、評量及移轉。 該中樞能提供 Azure 工具以進行評量和移轉，也提供協力廠商獨立軟體廠商 (ISV) 供應項目。 

## <a name="before-you-start"></a>開始之前

- 請確保[已創建](how-to-add-tool-first-time.md)Azure 遷移專案。
- 如果已創建專案，請確保[已添加](how-to-assess.md)Azure 遷移：伺服器評估工具。
- 要創建評估，需要為[VMware](how-to-set-up-appliance-vmware.md)或[Hyper-V](how-to-set-up-appliance-hyper-v.md)設置 Azure 遷移設備。 設備發現本地電腦，並將中繼資料和效能資料發送到 Azure 遷移：伺服器評估。 [深入了解](migrate-appliance.md)。


## <a name="assessment-overview"></a>評量概觀
可以使用 Azure 遷移創建兩種類型的評估：伺服器評估。

**評量** | **詳細資料** | **資料**
--- | --- | ---
**以效能為基礎** | 以所收集效能資料為基礎的評估 | **建議的 VM 大小**：基於 CPU 和記憶體利用率資料。<br/><br/> **推薦的磁片類型（標準磁片或高級託管磁片）：** 基於 IOPS 和本地磁片的輸送量。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的 VM 大小**：基於本地 VM 大小<br/><br> **建議的磁片類型**：根據為評估選擇的存儲類型設置。

[瞭解有關評估的更多](concepts-assessment-calculation.md)詳細資訊。

## <a name="run-an-assessment"></a>執行評估

執行評估，如下所示：

1. 檢閱適用於建立評估的[最佳做法](best-practices-assessment.md)。
2. 在 **"伺服器"** 選項卡中，在**Azure 遷移：伺服器評估**磁貼中，按一下"**評估**"。

    ![評定](./media/how-to-create-assessment/assess.png)

2. 在 [評估伺服器]**** 中，指定評估的名稱。
3. 按一下 [檢視全部]**** 來檢閱評估屬性。

    ![評量屬性](./media/how-to-create-assessment//view-all.png)

3. 在 [選取或建立群組]**** 中，選取 [新建]****，然後指定群組名稱。 群組會將一或多個 VM 收集在一起以進行評估。
4. 在 [將機器新增至群組]**** 中，選取要新增至群組的 VM。
5. 按一下 [建立評估]**** 以建立群組，然後執行評估。

    ![建立評估](./media/how-to-create-assessment//assessment-create.png)

6. 創建評估後，在**伺服器** > **Azure 遷移：伺服器評估** > **中**查看它。
7. 按一下 [匯出評估]****，將其下載為 Excel 檔案。



## <a name="review-an-assessment"></a>檢閱評量

評量會說明：

- **Azure 就緒**：VM 是否適合遷移到 Azure。
- **每月成本估算**：在 Azure 中運行 VM 的估計每月計算和存儲成本。
- **每月存儲成本估算**：遷移後磁片存儲的估計成本。

### <a name="view-an-assessment"></a>檢視評估

1. 在**遷移目標** >  **伺服器**中，按一下 Azure 遷移中的 **"評估****：伺服器評估**"。
2. 在 [評量]**** 中，按一下評量來加以開啟。

    ![評量摘要](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>檢閱 Azure 移轉整備程度

1. 在 [Azure 移轉整備程度]**** 中，確認 VM 是否已準備好移轉至 Azure。
2. 檢查 VM 狀態：
    - **已為 Azure 做好準備**：Azure 遷移建議在評估中為 VM 提供 VM 大小和成本估算。
    - **準備好條件**：顯示問題和建議的補救。
    - **尚未準備好 Azure**：顯示問題和建議的修正。
    - **就緒未知**：由於資料可用性問題，Azure 遷移無法評估就緒狀態時使用。

2. 按一下 [Azure 移轉整備程度]**** 狀態。 您可以檢視 VM 的整備程度詳細資料，並向下切入以查看 VM 詳細資料，包括計算、儲存體和網路設定。



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

- 瞭解如何使用[依賴項映射](how-to-create-group-machine-dependencies.md)創建高置信度組。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
