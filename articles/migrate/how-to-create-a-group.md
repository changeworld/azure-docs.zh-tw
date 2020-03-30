---
title: 使用 Azure Migrate 分組機器以進行評量 | Microsoft Docs
description: 說明如何在使用 Azure Migrate 服務進行評量之前先分組機器。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68301683"
---
# <a name="create-a-group-for-assessment"></a>創建用於評估的組

本文介紹如何使用 Azure 遷移：伺服器評估創建用於評估的電腦群組。

[Azure 遷移](migrate-services-overview.md)可説明您遷移到 Azure。 Azure Migrate 能提供集中式的中樞，以追蹤針對 Azure 的內部部署基礎結構、應用程式與資料的探索、評量及移轉。 該中樞能提供 Azure 工具以進行評量和移轉，也提供協力廠商獨立軟體廠商 (ISV) 供應項目。 

## <a name="grouping-machines"></a>對電腦進行分組

將電腦分組到組中，以評估它們是否適合遷移到 Azure，並獲取 Azure 大小調整和成本估算。 創建組的方法有幾種：

- 如果您知道需要一起遷移的電腦，則可以在 Azure 遷移中手動創建組。
- 如果不確定需要組合在一起的電腦，則可以使用 Azure 遷移中的依賴項視覺化功能創建組。 

> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果功能。

## <a name="create-a-group-manually"></a>手動創建組

您可以在[創建評估](how-to-create-assessment.md)的同時創建組。

如果要在創建評估之外手動創建組，請執行以下操作：

1. 在 Azure 遷移專案中>**概述**中，按一下"**評估和遷移伺服器**"。 在**Azure 遷移中：伺服器評估**，按一下 **"組**"
    - 如果尚未添加 Azure 遷移：伺服器評估工具，請按一下以添加該工具。 [深入了解](how-to-assess.md)。
    - 如果尚未創建 Azure 遷移專案，[則有關詳細資訊](how-to-add-tool-first-time.md)。

    ![選取群組](./media/how-to-create-a-group/select-groups.png)

2. 按一下 **"組"** 圖示。
3. 在 **"創建組**"中，指定組名稱，並在 **"設備名稱**"中選擇用於電腦發現的 Azure 遷移設備。
1. 從電腦清單中選擇要添加到組>**創建**的電腦。

    ![建立群組](./media/how-to-create-a-group/create-group.png)

現在，您可以在[創建評估](how-to-create-assessment.md)時使用此組。

## <a name="refine-a-group-with-dependency-mapping"></a>使用依賴項映射優化組

依賴關係映射可説明您視覺化跨電腦的依賴項。 當您要評估具有更高置信度的電腦群組時，通常使用依賴項映射。
- 它可以説明您在運行評估之前交叉檢查電腦依賴項。 
- 它還有助於確保不留下任何內容，從而在遷移期間避免意外中斷，從而有助於有效地規劃遷移到 Azure。
- 您可以發現需要一起遷移的相互依賴的系統，並確定正在運行的系統是否仍在為使用者提供服務，或者是停用而不是遷移的候選系統。

如果已[設置依賴項映射](how-to-create-group-machine-dependencies.md)，並且想要優化現有組，則執行以下操作：

1. 在 **"伺服器"** 選項卡中，在**Azure 遷移：伺服器評估**磁貼中，按一下"**組**"。
2. 按一下要優化的組。
    - 如果尚未設置依賴項映射，**則"依賴項"** 列將顯示 **"需要安裝**"狀態。 對於要視覺化依賴項的每個 VM，請按一下 **"需要安裝**"。 在每個 VM 上安裝幾個代理，然後才能映射電腦依賴項。 [深入了解](how-to-create-group-machine-dependencies.md)。

        ![添加依賴項映射](./media/how-to-create-a-group/add-dependency-mapping.png)

    - 如果已設置依賴項映射，請在組頁上按一下 **"查看依賴項**"以打開組依賴項映射。

3. 按一下 **"查看依賴項"** 後，組依賴項映射將顯示以下內容：

    - 入站（用戶端）和出站（伺服器）TCP 連接和從已安裝依賴項代理的組中的所有電腦的連接。
    - 未安裝依賴項代理的從屬電腦按埠號分組。
    - 安裝了依賴項代理的從屬電腦顯示為單獨的框。
    - 在機器內部運行的進程。 展開每個機器框以查看流程。
    - 機器屬性（包括 FQDN、作業系統、MAC 位址）。 按一下每個機器框以查看詳細資訊。

4. 要查看您選擇的時間間隔中的依賴項，請通過指定開始和結束日期或持續時間來修改時間範圍（預設情況下為一小時）。

    > [!NOTE]
    > 時間範圍可達一小時。 如果需要較長的範圍，請使用 Azure[監視器查詢從屬資料](how-to-create-group-machine-dependencies.md)的時間較長。

5. 標識要添加到組或從群組移除的依賴項後，可以修改組。 使用 Ctrl_Click 從組中添加或刪除電腦。

    - 您只能新增已探索到的機器。
    - 添加和刪除電腦會使組的過去評估無效。
    - 修改群組時，可以選擇性地建立新評量。


## <a name="next-steps"></a>後續步驟

瞭解如何設置和使用[依賴項映射](how-to-create-group-machine-dependencies.md)來創建高置信度組。

