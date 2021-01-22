---
title: 成本分析和預算
description: 瞭解如何取得成本分析、設定預算，以及降低用來執行 Batch 工作負載的基礎計算資源和軟體授權成本。
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679303"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch 的成本分析和預算

使用 Azure Batch 本身不會產生任何費用，不過，用來執行 Batch 工作負載的基礎計算資源和軟體授權可能會產生費用。 從虛擬機器 (Vm) 集區中的 Vm、從 VM 傳送的資料，或儲存在雲端中的任何輸入或輸出資料，都可能產生成本。 本主題將協助您瞭解成本的來源、如何設定 Batch 集區或帳戶的預算，以及降低 Batch 工作負載成本的方法。

## <a name="batch-resources"></a>Batch 資源

虛擬機器是用於 Batch 處理的最重要資源。 針對 Batch 使用 VM 的成本是根據類型、數量和使用時間計算而得。 VM 計費選項包括[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)或[保留](../cost-management-billing/reservations/save-compute-costs-reservations.md) (預先付款)。 這兩個付款選項各有不同的權益，視您的計算工作負載而定，會以不同的方式影響帳單

使用[應用程式套件](batch-application-packages.md)將應用程式部署到 Batch 節點 (VM) 時，您需要針對應用程式套件使用的 Azure 儲存體資源付費。 您也需要支付任何輸入或輸出檔案的儲存體費用，例如資源檔案和其他記錄資料。 一般而言，與 Batch 相關聯的儲存體資料成本遠低於計算資源的成本。 使用 [虛擬機器](nodes-and-pools.md#virtual-machine-configuration) 設定建立的集區中的每個 VM 都有相關聯的 OS 磁片，其使用 Azure 受控磁片。 Azure 受控磁碟有額外的成本，而其他磁碟效能層級也會有不同的成本。

Batch 集區會使用網路資源。 尤其是在 **VirtualMachineConfiguration** 集區中，會使用標準負載平衡器，而這需要靜態 IP 位址。 **使用者訂用帳戶** 帳戶看得到 Batch 所使用的負載平衡器，但 **Batch 服務** 帳戶看不到。 標準負載平衡器會針對在 Batch 集區 VM 之間來回傳遞的資料產生費用；選取從集區節點 (例如取得工作/節點檔案)、工作應用程式套件、資源/輸出檔案和容器映像擷取資料的 Batch API 將會產生費用。

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

在 Azure 入口網站中，您可以為 Batch 集區或 Batch 帳戶建立預算和支出警示。 預算和警示很適合用來通知專案關係人超支的任何風險，不過可能會導致消費警示的延遲，以及稍微超過預算。

在此範例中，我們將會檢視個別 Batch 集區的成本分析。

1. 在 [Azure 入口網站] 中，輸入或選取 [ **成本管理 + 計費** ]。
1. 在 [ **計費範圍** ] 區段中選取您的訂用帳戶。
1. 在 [成本管理] 底下，選取 [成本分析]。
1. 選取 [新增篩選]。 在第一個下拉式清單中，選取 [**資源**]
1. 在第二個下拉式清單中，選取 [Batch 集區]。 選取集區時，您會看到集區的成本分析，類似此處所示的範例。
    ![顯示 Azure 入口網站中集區成本分析的螢幕擷取畫面。](./media/batch-budget/pool-cost-analysis.png)

產生的成本分析會顯示集區的成本，以及造成此成本的資源。 在此範例中，集區中使用的 VM 是成本最高的資源。

若要建立集區的預算，請選取 [ **預算：無**]，然後選取 [ **建立新的預算 >**]。 現在，使用視窗來 [設定](../cost-management-billing/costs/tutorial-acm-create-budgets.md) 您的集區專用的預算。

> [!NOTE]
> Azure Batch 採用 Azure 雲端服務和 Azure 虛擬機器技術。 選擇 [雲端服務組態] 時，將會根據雲端服務定價結構向您收費。 選擇 [虛擬機器組態] 時，將會根據虛擬機器定價結構向您收費。 此頁面上的範例會使用 **虛擬機器** 設定，建議用於大部分的 Batch 集區。

## <a name="minimize-cost"></a>將成本降至最低

長時間使用數個 VM 和 Azure 服務可能會很昂貴。 請考慮使用這些策略，將工作負載的效率最大化，並降低成本。

### <a name="low-priority-virtual-machines"></a>低優先順序的虛擬機器

[低優先順序的 vm](batch-low-pri-vms.md) 會利用 Azure 中剩餘的計算容量，以降低 Batch 工作負載的成本。 當您在集區中指定低優先順序的 VM 時，Batch 就會使用剩餘的容量執行工作負載。 當您使用低優先順序的 Vm，而不是專用 Vm 時，可能會大幅節省成本。

### <a name="virtual-machine-os-disk-type"></a>虛擬機器 OS 磁碟類型

Azure 提供多個 [VM OS 磁片類型](../virtual-machines/disks-types.md)。 大部分的 VM 系列大小都同時支援進階和標準儲存體。 為集區選取了「VM 大小」時，Batch 會設定 premium SSD OS 磁片。 選取 [非 s] VM 大小時，就會使用較便宜的標準 HDD 磁片類型。 例如，進階 SSD OS 磁碟用於 `Standard_D2s_v3`，而標準 HDD OS 磁碟則用於 `Standard_D2_v3`。

進階 SSD 作業系統磁片的成本較高，但效能較高。 具有高階磁片的 Vm，其啟動速度會比具有標準 HDD OS 磁片的 Vm 稍微快。 使用 Batch 時，作業系統磁片通常不會使用太多，因為應用程式和工作檔案位於 VM 的暫存 SSD 磁片上。 因此，您通常可以選取「非 s」 VM 大小，以避免支付當指定了「VM 大小」時所布建之 premium SSD 的增加成本。

### <a name="reserved-virtual-machine-instances"></a>保留的虛擬機器執行個體

如果您想要使用批次很長的一段時間，您可以使用適用于您工作負載的 [Azure 保留](../cost-management-billing/reservations/save-compute-costs-reservations.md) 來降低 vm 的成本。 保留費率比隨用隨付費率低很多。 未使用保留的虛擬機器實例會依隨用隨付費率計費。 當您購買保留時，會套用保留折扣。

### <a name="automatic-scaling"></a>自動調整

[自動調整](batch-automatic-scaling.md)會根據目前作業的需求，以動態方式調整 Batch 集區中的 VM 數目。 藉由根據工作的存留期調整集區，自動調整可確保 Vm 會相應增加，而且只有在有要執行的作業時才會使用。 當作業完成時，或沒有任何作業時，Vm 會自動相應減少以節省計算資源。 調整可讓您透過僅使用所需的資源來降低 Batch 解決方案的整體成本。

## <a name="next-steps"></a>後續步驟

- 深入了解可用來建置並監視 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。  
- 瞭解如何搭配 [Batch 使用低優先順序 vm](batch-low-pri-vms.md)。
