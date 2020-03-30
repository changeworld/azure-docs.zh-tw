---
title: 成本分析和預算 - Azure 批次處理
description: 瞭解如何進行成本分析，並為用於運行 Batch 工作負荷的基礎計算資源和軟體許可證設置預算。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022710"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure 批次處理的成本分析和預算

Azure Batch 本身不收取任何費用，只有用於運行 Batch 工作負荷的基礎計算資源和軟體許可證。 在較高級別上，來自池中的虛擬機器 （VM）、從 VM 傳輸的資料傳輸或存儲在雲中的任何輸入或輸出資料會產生成本。 讓我們看一下 Batch 的一些關鍵元件，以瞭解成本來自何處，如何為池或帳戶設置預算，以及一些使 Batch 工作負載更具成本效益的技術。

## <a name="batch-resources"></a>Batch 資源

虛擬機器是批次處理使用的最重要資源。 根據類型、數量和使用期限計算批量使用 VM 的成本。 VM 計費選項包括即[用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)或[預訂](../cost-management-billing/reservations/save-compute-costs-reservations.md)（提前付款）。 兩種付款選項都有不同的權益，具體取決於您的計算工作負載，並且兩種付款模式都會對您的帳單產生不同的影響。

當應用程式使用[應用程式包](batch-application-packages.md)部署到 Batch-節點 （VM） 時，將針對應用程式包使用的 Azure 存儲資源計費。 此外，您還會為存儲任何輸入或輸出檔案（如資源檔和其他日誌資料）收費。 通常，與 Batch 關聯的存儲資料成本遠低於計算資源的成本。 使用**虛擬機器配置**創建的池中的每個 VM 都有一個使用 Azure 管理的磁片的關聯 OS 磁片。 Azure 管理的磁片具有額外的成本，其他磁片性能層也具有不同的成本。

批次處理池使用網路資源。 特別是，對於**虛擬機器配置**池，使用標準負載等化器，這需要靜態 IP 位址。 Batch 使用的負載等化器對**使用者訂閱**帳戶可見，但對於**批次處理服務**帳戶不可見。 標準負載等化器對傳入和從批次處理池 VM 傳遞的所有資料產生費用;選擇從池節點（如獲取任務/節點檔）、任務應用程式包、資源/輸出檔案和容器映射中檢索資料的批次處理 API 將會產生費用。

### <a name="additional-services"></a>其他服務

不包括 VM 和存儲的服務可以考慮批次處理帳戶的成本。

與 Batch 一起使用的其他服務可能包括：

- Application Insights
- Data Factory
- Azure 監視器
- 虛擬網路
- 具有圖形應用程式的 VM

根據您在 Batch 解決方案中使用的服務，您可能會產生額外費用。 請參閱[定價計算機](https://azure.microsoft.com/pricing/calculator/)以確定每個附加服務的成本。

## <a name="cost-analysis-and-budget-for-a-pool"></a>池的成本分析和預算

通過 Azure 門戶，可以為批次處理池或批次處理帳戶創建預算和支出警報。 預算和警報有助於通知利益相關者任何超支的風險。 支出警報可能會延遲，並略高於預算。 在此示例中，我們將查看單個 Batch 池的成本分析。

1. 在 Azure 門戶中，從左側巡覽列中選擇**成本管理 + 計費**。
1. 從"**我的訂閱"** 部分選擇您的訂閱
1. 轉到左側巡覽列"**成本管理**"部分下**的成本分析**，該部分將顯示如下所示的視圖：
1. 選擇 **"添加篩選器**"。 在第一個下拉清單中，選擇 **"資源**![選擇資源篩選器"](./media/batch-budget/resource-filter.png)
1. 在第二個下拉清單中，選擇批次處理池。 選擇池後，成本分析將類似于以下分析。
    ![池的成本分析](./media/batch-budget/pool-cost-analysis.png)

由此產生的成本分析顯示池的成本以及導致此成本的資源。 在此示例中，池中使用的 VM 是成本最高的資源。

要為池創建預算，請選擇 **"預算：無**"，然後選擇 **"創建新的預算>**。 現在，使用該視窗專門為池配置預算。

有關配置預算的詳細資訊，請參閱[創建和管理 Azure 預算](../cost-management-billing/costs/tutorial-acm-create-budgets.md)。

> [!NOTE]
> Azure Batch 採用 Azure 雲端服務和 Azure 虛擬機器技術。 選擇**雲端服務組態**時，將會根據雲端服務定價結構向您收費。 選擇**虛擬機器組態**時，將會根據虛擬機器定價結構向您收費。 此頁上的示例使用**虛擬機器配置**。

## <a name="minimize-cost"></a>最小化成本

長時間使用多個 VM 和 Azure 服務可能成本高昂。 幸運的是，有一些服務可以説明減少您的支出，以及最大限度地提高工作量效率的策略。

### <a name="low-priority-virtual-machines"></a>低優先順序虛擬機器

低優先順序 VM 利用 Azure 中的剩餘計算容量，降低了批次處理工作負載的成本。 在池中指定低優先順序 VM 時，Batch 使用此剩餘部分運行工作負荷。 使用低優先順序 VM 代替專用 VM 可以節省大量成本。

詳細瞭解如何在[使用 Batch 使用低優先順序 VM](batch-low-pri-vms.md)時為工作負載設置低優先順序 VM。

### <a name="virtual-machine-os-disk-type"></a>虛擬機器 OS 磁片類型

有多個[VM OS 磁片類型](../virtual-machines/windows/disks-types.md)。 大多數 VM 系列都有支援高級存儲和標準存儲的大小。 為池選擇"s"VM 大小時，Batch 會配置高級 SSD OS 磁片。 選擇"非 s"VM 大小時，將使用更便宜的標準 HDD 磁片類型。 例如，高級 SSD OS 磁片用於`Standard_D2s_v3`和 標準 HDD OS 磁片`Standard_D2_v3`用於 。

高級 SSD OS 磁片成本更高，但性能更高，具有高級磁片的 VM 的啟動速度可能略快于具有標準 HDD OS 磁片的 VM。 對於 Batch，作業系統磁片的使用通常不多，因為應用程式和任務檔位於 VM 臨時 SSD 磁片上。 因此，在許多情況下，無需為指定"s" VM 大小時預配的優質 SSD 支付增加的成本。

### <a name="reserved-virtual-machine-instances"></a>保留虛擬機器實例

如果打算長時間使用 Batch，則可以通過使用[Azure 預留](../cost-management-billing/reservations/save-compute-costs-reservations.md)來節省 VM 的成本。 預訂率遠低於即用即付費率。 無需預訂使用的虛擬機器實例按即用即付費率收費。 如果您購買預訂，則適用預訂折扣，不再按即用即付費率收費。

### <a name="automatic-scaling"></a>自動調整

根據當前作業的需求，[自動縮放](batch-automatic-scaling.md)動態擴展批次處理池中的 VM 數量。 通過基於作業的存留期縮放池，自動縮放可確保 VM 僅在要執行作業時進行擴展和使用。 作業完成後，或者沒有作業時，VM 將自動縮小以節省計算資源。 通過僅使用所需的資源，縮放可以降低 Batch 解決方案的總體成本。

有關自動縮放的詳細資訊，請參閱[Azure 批次處理池中的自動縮放計算節點](batch-automatic-scaling.md)。

## <a name="next-steps"></a>後續步驟

- 詳細瞭解可用於構建和監視批次處理解決方案的[批次處理 API 和工具](batch-apis-tools.md)。  

- 瞭解[具有批次處理的低優先順序 VM。](batch-low-pri-vms.md)
