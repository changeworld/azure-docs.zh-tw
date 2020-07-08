---
title: 成本分析和預算
description: 了解如何取得成本分析，並針對用來執行 Batch 工作負載的基礎計算資源和軟體授權設定預算。
ms.topic: how-to
ms.date: 07/19/2019
ms.openlocfilehash: 13c8cc508a4940b5e21570104527c40988879919
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725766"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch 的成本分析和預算

Azure Batch 本身並不收費，只有用來執行 Batch 工作負載的基礎計算資源和軟體授權需要收費。 概括而言，成本是從集區中的虛擬機器 (VM)、從 VM 傳輸資料，或儲存在雲端的任何輸入或輸出資料產生。 讓我們看看 Batch 的一些重要元件，以了解成本的來源、如何設定集區或帳戶的預算，以及一些讓 Batch 工作負載更符合成本效益的技術。

## <a name="batch-resources"></a>Batch 資源

虛擬機器是用於 Batch 處理的最重要資源。 針對 Batch 使用 VM 的成本是根據類型、數量和使用時間計算而得。 VM 計費選項包括[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)或[保留](../cost-management-billing/reservations/save-compute-costs-reservations.md) (預先付款)。 根據您的計算工作負載，這兩個付款選項有不同的優點，而這兩個付款模型將會以不同的方式影響帳單。

使用[應用程式套件](batch-application-packages.md)將應用程式部署到 Batch 節點 (VM) 時，您需要針對應用程式套件使用的 Azure 儲存體資源付費。 您也需要支付任何輸入或輸出檔案的儲存體費用，例如資源檔案和其他記錄資料。 一般而言，與 Batch 相關聯的儲存體資料成本遠低於計算資源的成本。 以 **VirtualMachineConfiguration** 建立之集區中的每部 VM 都有一個使用 Azure 受控磁碟的相關聯 OS 磁碟。 Azure 受控磁碟有額外的成本，而其他磁碟效能層級也會有不同的成本。

Batch 集區會使用網路資源。 特別是，系統會針對 **VirtualMachineConfiguration** 集區使用需要靜態 IP 位址的標準負載平衡器。 **使用者訂用帳戶**帳戶看得到 Batch 所使用的負載平衡器，但 **Batch 服務**帳戶看不到。 標準負載平衡器會針對在 Batch 集區 VM 之間來回傳遞的資料產生費用；選取從集區節點 (例如取得工作/節點檔案)、工作應用程式套件、資源/輸出檔案和容器映像擷取資料的 Batch API 將會產生費用。

### <a name="additional-services"></a>其他服務

不包含 VM 和儲存體的服務可以納入 Batch 帳戶的成本。

通常搭配 Batch 使用的其他服務可能包括：

- Application Insights
- Data Factory
- Azure 監視器
- 虛擬網路
- 具有圖形應用程式的 VM

視您搭配 Batch 解決方案使用的服務而定，您可能會產生額外的費用。 請參閱[定價計算機](https://azure.microsoft.com/pricing/calculator/)，以確定每個額外服務的成本。

## <a name="cost-analysis-and-budget-for-a-pool"></a>集區的成本分析和預算

您可以透過 Azure 入口網站，為 Batch 集區或 Batch 帳戶建立預算和支出警示。 預算和警示適用於通知專案關係人超支的任何風險。 支出警示可能會有延遲，而且稍微超出預算。 在此範例中，我們將會檢視個別 Batch 集區的成本分析。

1. 在 Azure 入口網站中，選取左側導覽列中的 [成本管理 + 計費]。
1. 從 [我的訂用帳戶] 區段中，選取您的訂用帳戶
1. 在左側導覽列的 [成本管理] 區段底下，移至 [成本分析]，這將會顯示如下的檢視：
1. 選取 [新增篩選]。 在第一個下拉式清單中，選取 [資源] ![選取資源篩選](./media/batch-budget/resource-filter.png)
1. 在第二個下拉式清單中，選取 [Batch 集區]。 選取集區時，成本分析看起來類似下列分析。
    ![集區的成本分析](./media/batch-budget/pool-cost-analysis.png)

產生的成本分析會顯示集區的成本，以及造成此成本的資源。 在此範例中，集區中使用的 VM 是成本最高的資源。

若要建立集區的預算，請選取 [預算: 無]，然後選取 [建立新的預算 >]。 現在，使用視窗專門針對您的集區設定預算。

如需有關設定預算的詳細資訊，請參閱[建立和管理 Azure 預算](../cost-management-billing/costs/tutorial-acm-create-budgets.md)。

> [!NOTE]
> Azure Batch 採用 Azure 雲端服務和 Azure 虛擬機器技術。 選擇 [雲端服務組態] 時，將會根據雲端服務定價結構向您收費。 選擇 [虛擬機器組態] 時，將會根據虛擬機器定價結構向您收費。 本頁面上的範例會使用 [虛擬機器組態]。

## <a name="minimize-cost"></a>將成本降至最低

長時間使用數個 VM 和 Azure 服務可能會很昂貴。 幸好，有一些有助於降低支出的服務，以及將工作負載效率發揮到極致的策略。

### <a name="low-priority-virtual-machines"></a>低優先順序的虛擬機器

低優先順序的 VM 會利用 Azure 中剩餘的計算容量，降低 Batch 工作負載的成本。 當您在集區中指定低優先順序的 VM 時，Batch 就會使用剩餘的容量執行工作負載。 使用低優先順序的 VM 取代專用的 VM 可節省大量成本。

若要深入了解如何為您的工作負載設定低優先順序的 VM，請前往[使用低優先順序的 VM 搭配 Batch](batch-low-pri-vms.md)。

### <a name="virtual-machine-os-disk-type"></a>虛擬機器 OS 磁碟類型

[VM OS 磁碟類型](../virtual-machines/windows/disks-types.md)有很多種。 大部分的 VM 系列大小都同時支援進階和標準儲存體。 為集區選取「s」VM 大小時，Batch 會設定進階 SSD OS 磁碟。 選取「非 s」VM 大小時，則會使用較便宜的標準 HDD 磁碟類型。 例如，進階 SSD OS 磁碟用於 `Standard_D2s_v3`，而標準 HDD OS 磁碟則用於 `Standard_D2_v3`。

進階 SSD OS 磁碟較昂貴，但效能更高，而且相較於具有標準 HDD OS 磁碟的 VM，具有進階磁碟 VM 的啟動速度可以稍微快一點。 使用 Batch 時，不會常使用 OS 磁碟，因為應用程式與工作檔案位於 VM 的暫存 SSD 磁碟上。 因此在許多情況下，不需要為指定「s」VM 大小所佈建的進階 SSD 支付更多的成本。

### <a name="reserved-virtual-machine-instances"></a>保留的虛擬機器執行個體

如果您想要長時間使用 Batch，可以為工作負載使用 [Azure 保留](../cost-management-billing/reservations/save-compute-costs-reservations.md)以節省 VM 的成本。 保留費率比隨用隨付費率低很多。 使用不含保留的虛擬機器執行個體會按隨用隨付費率計費。 如果您購買保留，則會使用保留折扣，而且不再按隨用隨付費率向您收費。

### <a name="automatic-scaling"></a>自動調整

[自動調整](batch-automatic-scaling.md)會根據目前作業的需求，以動態方式調整 Batch 集區中的 VM 數目。 自動調整可藉由根據作業的存留期調整集區，確保只有在有作業可以執行時才會擴大並使用 VM。 當作業完成，或沒有任何作業時，會自動縮小 VM 以節省計算資源。 調整可讓您透過僅使用所需的資源來降低 Batch 解決方案的整體成本。

如需有關自動調整的詳細資訊，請參閱[自動調整 Azure Batch 集區中的計算節點](batch-automatic-scaling.md)。

## <a name="next-steps"></a>後續步驟

- 深入了解可用來建置並監視 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。  

- 了解[以低優先順序的 VM 搭配 Batch](batch-low-pri-vms.md)。
