---
title: 成本分析和預算
description: 瞭解如何取得成本分析，並為用來執行 Batch 工作負載的基礎計算資源和軟體授權設定預算。
ms.topic: article
ms.date: 07/19/2019
ms.openlocfilehash: c3c3203882d003d7fbb5f9d5092a1c93886f313a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117483"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch 的成本分析和預算

Azure Batch 本身並不收費，只有用來執行 Batch 工作負載的基礎計算資源和軟體授權。 概括而言，成本會從集區中的虛擬機器（Vm）、從 VM 傳輸資料，或儲存在雲端中的任何輸入或輸出資料產生。 讓我們看看 Batch 的一些重要元件，以瞭解成本的來源、如何設定集區或帳戶的預算，以及一些讓 Batch 工作負載更符合成本效益的技術。

## <a name="batch-resources"></a>Batch 資源

虛擬機器是用來處理批次處理的最重要資源。 針對 Batch 使用 Vm 的成本是根據類型、數量和使用期間計算而得。 VM 計費選項包括 [[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)] 或 [[保留](../cost-management-billing/reservations/save-compute-costs-reservations.md)] （事先付費）。 根據您的計算工作負載，這兩個付款選項都有不同的優點，而這兩個付款模式會以不同的方式影響帳單。

使用[應用程式套件](batch-application-packages.md)將應用程式部署至 Batch-節點（vm）時，您需支付應用程式封裝所耗用的 Azure 儲存體資源費用。 您也需支付任何輸入或輸出檔案的儲存體費用，例如資源檔和其他記錄資料。 一般而言，與 Batch 相關聯的儲存體資料成本遠低於計算資源的成本。 集區中使用**VirtualMachineConfiguration**建立的每個 VM 都有一個使用 Azure 受控磁片的相關 OS 磁片。 Azure 受控磁片有額外的成本，而其他磁片效能層級也會有不同的成本。

Batch 集區會使用網路資源。 特別是， **VirtualMachineConfiguration**集區會使用標準負載平衡器，這需要靜態 IP 位址。 Batch 所使用的負載平衡器會顯示給**使用者訂**用帳戶，但不會顯示在**batch 服務**帳戶中。 標準負載平衡器會針對所有從 Batch 集區 Vm 傳遞的資料產生費用;選取從集區節點（例如取得工作/節點檔案）、工作應用程式套件、資源/輸出檔案和容器映射抓取資料的 Batch Api 將會產生費用。

### <a name="additional-services"></a>其他服務

不包含 Vm 和儲存體的服務可以納入 Batch 帳戶的成本。

通常與 Batch 搭配使用的其他服務可能包括：

- Application Insights
- Data Factory
- Azure 監視器
- 虛擬網路
- 具有圖形應用程式的 Vm

視您搭配 Batch 解決方案使用的服務而定，您可能會產生額外的費用。 請參閱[定價計算機](https://azure.microsoft.com/pricing/calculator/)來判斷每個額外服務的成本。

## <a name="cost-analysis-and-budget-for-a-pool"></a>集區的成本分析和預算

透過 Azure 入口網站，您可以為 Batch 集區或 Batch 帳戶建立預算和支出警示。 預算和警示適用于通知專案關係人超支的任何風險。 可能會有延遲的費用，並稍微超過預算。 在此範例中，我們將會查看個別 Batch 集區的成本分析。

1. 在 [Azure 入口網站中，從左側導覽列選取 [**成本管理 + 帳單**]。
1. 從 [**我的訂閱**] 區段中選取您的訂用帳戶
1. 在左側流覽列的 [**成本管理**] 區段下，移至 [**成本分析**]，這將會顯示如下的視圖：
1. 選取 [**新增篩選**]。 在第一個下拉式選單中，選取 [**資源** ![] [選取資源篩選器]](./media/batch-budget/resource-filter.png)
1. 在第二個下拉式選單中，選取 Batch 集區。 選取集區時，成本分析看起來會類似下列分析。
    ![集區的成本分析](./media/batch-budget/pool-cost-analysis.png)

產生的成本分析會顯示集區的成本，以及貢獻此成本的資源。 在此範例中，集區中使用的 Vm 是成本最高的資源。

若要建立集區的預算，請選取 [**預算：無**]，然後選取 [**建立新預算 >**]。 現在，使用視窗來設定您的集區專用的預算。

如需設定預算的詳細資訊，請參閱[建立和管理 Azure 預算](../cost-management-billing/costs/tutorial-acm-create-budgets.md)。

> [!NOTE]
> Azure Batch 採用 Azure 雲端服務和 Azure 虛擬機器技術。 選擇**雲端服務組態**時，將會根據雲端服務定價結構向您收費。 選擇**虛擬機器組態**時，將會根據虛擬機器定價結構向您收費。 此頁面上的範例會使用**虛擬機器**設定。

## <a name="minimize-cost"></a>將成本降至最低

使用數個 Vm 和 Azure 服務長時間可能會很昂貴。 幸運的是，有一些服務可協助降低您的支出，以及將工作負載效率最大化的策略。

### <a name="low-priority-virtual-machines"></a>低優先順序虛擬機器

低優先順序的 Vm 會利用 Azure 中剩餘的計算容量，降低 Batch 工作負載的成本。 當您在集區中指定低優先順序的 Vm 時，Batch 會使用此多餘的來執行您的工作負載。 使用低優先順序的 Vm 來取代專用的 Vm，可節省大量成本。

若要深入瞭解如何為您的工作負載設定低優先順序 Vm，[請前往使用低優先順序的 vm 搭配 Batch](batch-low-pri-vms.md)。

### <a name="virtual-machine-os-disk-type"></a>虛擬機器 OS 磁片類型

有多個[VM OS 磁片類型](../virtual-machines/windows/disks-types.md)。 大部分的 VM 系列都具有同時支援 premium 和 standard 儲存體的大小。 為集區選取了的 VM 大小時，Batch 會設定 premium SSD OS 磁片。 選取 [非 s] VM 大小時，會使用較便宜的標準 HDD 磁片類型。 例如，premium SSD OS 磁片用於， `Standard_D2s_v3`而標準 HDD os 磁片則用於。 `Standard_D2_v3`

進階 SSD OS 磁片的成本較高，但具有較佳的效能，而且具有 premium 磁片的 Vm 可以比具有標準 HDD OS 磁片的 Vm 稍微快速啟動。 使用 Batch 時，作業系統磁片通常不會使用，因為應用程式和工作檔案位於 Vm 暫存 SSD 磁片上。 因此，在許多情況下，當指定的「VM 大小」時，並不需要支付布建的 premium SSD 成本增加。

### <a name="reserved-virtual-machine-instances"></a>保留的虛擬機器實例

如果您想要使用 Batch 一段很長的時間，您可以使用工作負載的[Azure 保留](../cost-management-billing/reservations/save-compute-costs-reservations.md)來節省 vm 的成本。 保留費率比隨用隨付費率低得多。 未使用保留的虛擬機器實例會以隨用隨付費率計費。 如果您購買保留，則會套用保留折扣，而且您不再以隨用隨付費率計費。

### <a name="automatic-scaling"></a>自動調整

[自動調整](batch-automatic-scaling.md)會根據目前作業的需求，以動態方式調整 Batch 集區中的 vm 數目。 藉由根據作業的存留期調整集區，自動調整可確保 Vm 只有在有作業可以執行時才會相應增加並使用。 當作業完成，或沒有任何作業時，Vm 會自動相應減少以儲存計算資源。 縮放可讓您只使用所需的資源，來降低 Batch 解決方案的整體成本。

如需自動調整的詳細資訊，請參閱[自動調整 Azure Batch 集區中的計算節點](batch-automatic-scaling.md)。

## <a name="next-steps"></a>後續步驟

- 深入瞭解可用於建立和監視 Batch 解決方案的[Batch api 和工具](batch-apis-tools.md)。  

- 深入瞭解[使用 Batch 的低優先順序 vm](batch-low-pri-vms.md)。
