---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371656"
---
提交到 Azure 保留 VM 實例時，可以節省資金。 保留項目折扣會自動套用到符合保留項目範圍和屬性之執行中虛擬機器的數目。 您無需為虛擬機器分配預留來獲得折扣。 預留實例購買僅涵蓋 VM 使用的計算部分。 對於 Windows VM，使用儀錶分為兩個單獨的儀錶。 有一個計算儀錶，它和Linux儀錶相同，還有一個Windows IP儀錶。 購買時看到的費用僅適用于計算成本。 費用不包括 Windows 軟體成本。 有關軟體成本的詳細資訊，請參閱[Azure 預留 VM 實例中未包括的軟體成本](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)。

## <a name="determine-the-right-vm-size-before-you-buy"></a>購買之前，請先決定正確的 VM 大小

在購買預留之前，應確定所需的 VM 的大小。 以下各節將説明您確定正確的 VM 大小。

### <a name="use-reservation-recommendations"></a>使用預訂建議

您可以使用預訂建議來説明確定應購買的預訂。

- 在 Azure 門戶中購買 VM 預留實例時，將顯示購買建議和建議的數量。
- Azure 顧問為各個訂閱提供購買建議。  
- 您可以使用 API 獲取共用範圍和單個訂閱作用域的購買建議。 有關詳細資訊，請參閱[為企業客戶的預留實例購買建議 API。](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)
- 對於企業協定 （EA） 和 Microsoft 客戶協定 （MCA） 客戶[，Azure 消耗洞察 Power BI 內容包](/power-bi/service-connect-to-azure-consumption-insights)提供了共用和單個訂閱範圍的採購建議。

### <a name="services-that-get-vm-reservation-discounts"></a>取得 VM 保留折扣的服務

您的 VM 保留可套用至從多個服務發出的 VM 使用量，而不只是針對您的 VM 部署。 取得保留折扣的資源會根據執行個體大小彈性設定而變更。

#### <a name="instance-size-flexibility-setting"></a>執行個體大小彈性設定

執行個體大小彈性設定會決定哪些服務可取得保留執行個體折扣。

無論是開啟或關閉設定，保留折扣都會在 *ConsumedService* 為 `Microsoft.Compute` 時，自動套用至任何相符的 VM 使用量。 因此，請檢查您的使用量資料是否有 ConsumedService** 值。 部分範例包括：

- 虛擬機器
- 虛擬機器擴展集
- 容器服務
- Azure Batch 部署 (在使用者訂用帳戶模式中)
- Azure Kubernetes Service (AKS)
- Service Fabric

若設為開啟，保留折扣會在 *ConsumedService* 為下列任何項目時，自動套用至相符的 VM 使用量：

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

檢查使用量資料中的 ConsumedService** 值，以判斷使用量是否符合保留折扣的資格。

如需執行個體大小彈性的詳細資訊，請參閱[利用保留的 VM 執行個體獲得虛擬機器大小彈性](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。

### <a name="analyze-your-usage-information"></a>分析使用方式資訊

分析使用方式資訊，以説明確定應購買哪些預訂。 使用方式資料在使用方式檔和 API 中可用。 將它們一起使用以確定購買哪個預訂。 檢查每天使用率高的 VM 實例，以確定要購買的預留數量。 避免使用`Meter`資料中的子`Product`類別和欄位。 它們不區分使用高級存儲的 VM 大小。 如果您使用這些欄位來確定用於預留購買的 VM 大小，則可能會購買錯誤的大小。 然後，您將得不到您期望的預訂折扣。 相反，請參閱使用檔`AdditionalInfo`或使用方式 API 中的欄位以確定正確的 VM 大小。

您的使用方式檔按計費週期和每日使用方式顯示您的費用。 有關下載使用方式檔的資訊，請參閱[查看和下載 Azure 使用方式和費用](../articles/cost-management-billing/understand/download-azure-daily-usage.md)。 然後，通過使用使用方式檔資訊，您可以[確定要購買哪些預留](../articles/cost-management-billing/reservations/determine-reservation-purchase.md)。

### <a name="purchase-restriction-considerations"></a>購買限制注意事項

保留 VM 實例可用於大多數 VM 大小，但有一些例外。 預訂折扣不適用於以下 VM：

- **VM 系列**- A 系列、Av2 系列或 G 系列。

- **預覽或促銷 VM** - 預覽或使用促銷儀錶的任何 VM 系列或大小。

- **雲**- 預訂不適用於在德國或中國地區購買。

- **配額不足**- 限定為單個訂閱的預留必須在新 RI 的訂閱中具有 vCPU 配額。 例如，如果目標訂用帳戶的配額限制為 10 個 D 系列的 vCPU，您便無法購買一個適用於 11 個 Standard_D1 執行個體的保留項目。 保留的配額檢查包含已在訂用帳戶中部署的 VM。 例如，如果訂用帳戶的配額為 10 個 D 系列的 vCPU，且已部署 2 個 standard_D1 執行個體，則您可以在此訂用帳戶中購買一個適用於 10 個 standard_D1 執行個體的保留項目。 您可以創建[報價增加請求](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md)以解決此問題。

- **容量限制**- 在極少數情況下，Azure 會限制購買 VM 大小的子集的新預留，因為區域中的容量較低。

## <a name="buy-a-reserved-vm-instance"></a>購買保留 VM 執行個體

您可以在[Azure 門戶](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中購買保留的 VM 實例。 保留的付款方式可為[預先付款或每月付款](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)。
這些要求適用于購買保留的 VM 實例：

- 您必須在至少一個 EA 訂閱或具有即用即付費率的訂閱中擔任擁有者角色。
- 對於 EA 訂閱，必須在[EA 門戶](https://ea.azure.com/)中啟用 **"添加預留實例"** 選項。 或者，如果該設定已停用，則您必須是訂用帳戶的 EA 管理員。
- 針對雲端解決方案提供者 (CSP) 程式，只有系統管理員代表或銷售代表可以購買保留項目。

若要購買執行個體：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 選擇**所有服務** > **預訂**。
1. 選擇 **"添加"** 以購買新的預訂，然後按一下 **"虛擬機器**"。
1. 輸入必要欄位。 符合您選取之屬性的執行中 VM 執行個體符合取得保留項目折扣的資格。 取得折扣的 VM 執行個體實際數目取決於選取的範圍和數量。

如果您有 EA 協定，則可以使用 **"添加更多"選項**快速添加其他實例。 該選項不適用於其他訂閱類型。


| 欄位      | 描述|
|------------|--------------|
|訂用帳戶|用來支付保留項目的訂用帳戶。 訂閱上的付款條件將收取預訂費用。 訂閱類型必須是企業協定（產品/服務編號：MS-AZR-0017P 或 MS-AZR-0148P）或 Microsoft 客戶協定或具有即用即付費率的個人訂閱（產品編號：MS-AZR-0003P 或 MS-AZR-0023P）。 費用從貨幣承諾餘額中扣除（如有）或按超額收取。 對於具有即用即付費率的訂閱，費用將計入訂閱上的信用卡或發票付款條件。|    
|影響範圍       |預留的範圍可以涵蓋一個訂閱或多個訂閱（共用範圍）。 如果您選取： <ul><li>**單一資源群組範圍** — 只會將保留折扣套用至所選資源群組中的相符資源。</li><li>**單一訂用帳戶範圍** — 會將保留折扣套用至所選訂用帳戶中的相符資源。</li><li>**共用範圍** — 會將保留折扣套用至計費內容中合格訂用帳戶的相符資源。 對於 EA 客戶，計費上下文是註冊。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是帳戶管理員所建立的所有合格訂用帳戶。</li></ul>|
|區域    |保留涵蓋的 Azure 區域。|    
|VM 大小     |VM 執行個體的大小。|
|已針對下列項目最佳化     |預設情況下，可以選擇 VM 實例大小靈活性。 按一下 **"高級設置**"更改實例大小靈活性值，以將預留折扣應用於同一[VM 大小組中的其他 VM。](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md) 容量優先順序會讓資料中心容量供您的部署優先使用。 它增加了您對在需要時啟動 VM 實例的能力的信心。 保留項目範圍是單一訂用帳戶時，才可使用容量優先順序。 |
|詞彙        |一年或三年。|
|數量    |保留項目內所購買的執行個體數目。 數量是可以取得帳單折扣之執行中 VM 執行個體的數目。 例如，如果在美國東部運行 10Standard_D2 VM，則將數量指定為 10，以最大化所有正在運行的 VM 的好處。 |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>使用資料和預訂利用率

對於獲得保留折扣的使用量，您的使用量資料有效價格會是零。 您可以看到哪個 VM 實例為每個預留提供了預留折扣。

有關預訂折扣如何顯示在使用方式資料中的詳細資訊，請參閱[瞭解企業註冊的 Azure 預留使用方式](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)（如果您是 EA 客戶）。 如果您有單獨的訂閱，請參閱[瞭解即用即付訂閱的 Azure 預留使用方式](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="change-a-reservation-after-purchase"></a>購買後更改預訂

購買之後，您可以對保留進行下列類型的變更：

- 更新保留範圍
- 實例大小靈活性（如果適用）
- 擁有權

您還可以將預留拆分為較小的塊，並將已拆分的預留合併。 任何更改都不會導致新的商業交易或更改預訂的結束日期。

購買後不能直接進行以下類型的更改：

- 現有保留區域
- SKU
- 數量
- Duration

但是，如果要進行更改，可以*交換*預訂。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有疑問或需要幫助，[請創建支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 要瞭解如何管理預留，請參閱[管理 Azure 預留](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
    - [什麼是 Azure 保留項目？](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [管理 Azure 中的預留](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [了解保留項目折扣的套用方式](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [了解採用隨用隨付費率的訂用帳戶的保留使用量](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)
