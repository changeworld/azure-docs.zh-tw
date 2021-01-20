---
title: 預付 Azure 虛擬機器以節省成本
description: 瞭解如何購買 Azure 保留的虛擬機器執行個體，以節省您的計算成本。
author: vikramdesai01
manager: vikramdesai01
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: vikdesai
ms.openlocfilehash: ffc9c8f3143824dd099aaad3e743398eea4e0f43
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602529"
---
# <a name="save-costs-with-azure-reserved-vm-instances"></a>使用 Azure 保留的 VM 執行個體節省成本



當您認可至 Azure 保留的 VM 實例時，您可以節省成本。 保留項目折扣會自動套用到符合保留項目範圍和屬性之執行中虛擬機器的數目。 您不需要將保留指派給虛擬機器以取得折扣。 購買保留實例僅涵蓋 VM 使用量的計算部分。 針對 Windows Vm，使用量計量會分割成兩個不同的計量。 有一種計算計量，與 Linux 計量和 Windows IP 計量相同。 當您進行購買時所看到的費用，只會用於計算成本。 費用不包含 Windows 軟體成本。 如需軟體成本的詳細資訊，請參閱 [Azure 保留的 VM 執行個體未包含的軟體成本](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)。

## <a name="determine-the-right-vm-size-before-you-buy"></a>購買之前，請先決定正確的 VM 大小

購買保留之前，您應該先判斷您需要的 VM 大小。 下列各節將協助您判斷正確的 VM 大小。

### <a name="use-reservation-recommendations"></a>使用保留建議

您可以使用保留建議來協助判斷您應該購買的保留。

- 購買 Azure 入口網站中的 VM 保留實例時，會顯示購買建議和建議數量。
- Azure Advisor 提供個別訂用帳戶的購買建議。  
- 您可以使用 Api 來取得共用範圍和單一訂用帳戶範圍的購買建議。 如需詳細資訊，請參閱 [企業客戶的保留實例購買建議 api](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)。
- 針對 Enterprise 合約 (EA) 和 Microsoft 客戶合約 (MCA) 客戶，可透過 [Azure 使用量見解 Power BI 內容套件](/power-bi/service-connect-to-azure-consumption-insights)取得共用和單一訂用帳戶範圍的購買建議。

### <a name="services-that-get-vm-reservation-discounts"></a>取得 VM 保留折扣的服務

您的 VM 保留可套用至從多個服務發出的 VM 使用量，而不只是針對您的 VM 部署。 取得保留折扣的資源會根據執行個體大小彈性設定而變更。

#### <a name="instance-size-flexibility-setting"></a>執行個體大小彈性設定

執行個體大小彈性設定會決定哪些服務可取得保留執行個體折扣。

無論是開啟或關閉設定，保留折扣都會在 *ConsumedService* 為 `Microsoft.Compute` 時，自動套用至任何相符的 VM 使用量。 因此，請檢查您的使用量資料是否有 ConsumedService 值。 部分範例包括：

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

檢查使用量資料中的 ConsumedService 值，以判斷使用量是否符合保留折扣的資格。

如需執行個體大小彈性的詳細資訊，請參閱[利用保留的 VM 執行個體獲得虛擬機器大小彈性](reserved-vm-instance-size-flexibility.md)。

### <a name="analyze-your-usage-information"></a>分析您的使用量資訊

分析您的使用量資訊，以協助判斷您應該購買的保留。 使用量資料會在使用方式檔案和 Api 中提供。 一起使用它們來決定要購買的保留。 檢查每天是否有高使用量的 VM 實例，以決定要購買的保留數量。 避免 `Meter` `Product` 使用方式資料中的子類別和欄位。 它們不會區分使用 premium 儲存體的 VM 大小。 如果您使用這些欄位來判斷保留購買的 VM 大小，您可能會購買錯誤的大小。 然後您就不會得到預期的保留折扣。 相反地，請參閱 `AdditionalInfo` 使用量檔案或使用量 API 中的欄位，以判斷正確的 VM 大小。

您的使用量檔案會依計費期間和每日使用量顯示您的費用。 如需下載使用量檔案的相關資訊，請參閱 [查看及下載您的 Azure 使用量和費用](../cost-management-billing/understand/download-azure-daily-usage.md)。 然後，您可以使用使用量檔案資訊來 [決定要購買的保留](../cost-management-billing/reservations/determine-reservation-purchase.md)。

### <a name="purchase-restriction-considerations"></a>購買限制考慮

保留的 VM 實例適用于大多數 VM 大小，但有一些例外狀況。 保留折扣不適用於下列 Vm：

- **VM 系列** A 系列、Av2 系列或 G 系列。

- **預覽或促銷 vm** -任何處于預覽狀態或使用促銷計量的 VM 系列或大小。

- 雲端保留不適用於德國或中國區域 **中的購買**。

- **配額不足** -具有單一訂用帳戶範圍的保留，必須在新 RI 的訂用帳戶中有可用的 vCPU 配額。 例如，如果目標訂用帳戶的配額限制為 10 個 D 系列的 vCPU，您便無法購買一個適用於 11 個 Standard_D1 執行個體的保留項目。 保留的配額檢查包含已在訂用帳戶中部署的 VM。 例如，如果訂用帳戶的配額為 10 個 D 系列的 vCPU，且已部署 2 個 standard_D1 執行個體，則您可以在此訂用帳戶中購買一個適用於 10 個 standard_D1 執行個體的保留項目。 您可以 [建立報價增加要求](../azure-portal/supportability/resource-manager-core-quotas-request.md) 以解決此問題。

- **容量限制** -在罕見的情況下，Azure 會限制購買 VM 大小子集的新保留，因為區域中的低容量。

## <a name="buy-a-reserved-vm-instance"></a>購買保留 VM 執行個體

您可以在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中購買保留的 VM 實例。 保留的付款方式可為[預先付款或每月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)。
這些需求適用于購買保留的 VM 實例：

- 您必須是至少一個 EA 訂用帳戶的擁有者角色，或具有隨用隨付費率的訂用帳戶。
- 若為 EA 訂用帳戶，必須在 [ea 入口網站](https://ea.azure.com/)中啟用 [**新增保留實例**] 選項。 或者，如果該設定已停用，則您必須是訂用帳戶的 EA 管理員。
- 針對雲端解決方案提供者 (CSP) 程式，只有系統管理員代表或銷售代表可以購買保留項目。

購買執行個體：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 [所有服務] > [保留]。
1. 選取 [新增] 以購買新的保留，然後按一下 [虛擬機器]。
1. 輸入必要欄位。 符合您選取之屬性的執行中 VM 執行個體符合取得保留項目折扣的資格。 取得折扣的 VM 執行個體實際數目取決於選取的範圍和數量。

如果您有 EA 合約，可以使用 [ **新增更多] 選項** 來快速新增額外的實例。 此選項不適用於其他訂用帳戶類型。


| 欄位      | 描述|
|------------|--------------|
|訂用帳戶|用來支付保留項目的訂用帳戶。 保留項目的費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 enterprise 合約 (供應專案號碼： MS-AZR-0003P->ms-azr-0017p 或 MS-AZR-0003P-Ms-azr-0148p) 或 Microsoft 客戶合約，或是採用隨用隨付費率的個別訂用帳戶 (供應專案號碼： MS-MS-AZR-0003P-Ms-azr-0003p 或 MS-MS-AZR-0003P-Ms-azr-0023p) 。 費用會從 Azure 預付款扣除 (先前稱為預付金) 餘額（如果有的話），或作為超額部分收費。 針對具有隨用隨付費率的訂用帳戶，費用會以訂用帳戶的信用卡或發票付款方法計費。|    
|影響範圍       |保留範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍) 。 如果您選取： <ul><li>**單一資源群組範圍** — 只會將保留折扣套用至所選資源群組中的相符資源。</li><li>**單一訂用帳戶範圍** — 會將保留折扣套用至所選訂用帳戶中的相符資源。</li><li>**共用範圍** — 會將保留折扣套用至計費內容中合格訂用帳戶的相符資源。 若是 EA 客戶，計費內容為註冊。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是帳戶管理員所建立的所有合格訂用帳戶。</li></ul>|
|區域    |保留所涵蓋的 Azure 區域。|    
|VM 大小     |VM 執行個體的大小。|
|已針對下列項目最佳化     |預設會選取 VM 實例大小彈性。 按一下 [ **Advanced settings** ] 來變更實例大小彈性值，以將保留折扣套用至相同 [VM 大小群組](reserved-vm-instance-size-flexibility.md)中的其他 vm。 容量優先順序會讓資料中心容量供您的部署優先使用。 它能讓您在需要時更有信心地啟動 VM 實例。 保留項目範圍是單一訂用帳戶時，才可使用容量優先順序。 |
|詞彙        |一年或三年。 此外，也有5年的期限僅適用于 HBv2 Vm。|
|數量    |保留項目內所購買的執行個體數目。 數量是可以取得帳單折扣之執行中 VM 執行個體的數目。 例如，如果您在美國東部執行10部 Standard_D2 Vm，則您會將數量指定為10，以最大化所有執行中 Vm 的權益。 |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>使用量資料和保留使用率

對於獲得保留折扣的使用量，您的使用量資料有效價格會是零。 您可以查看哪個 VM 實例收到每個保留的保留折扣。

如需保留折扣如何出現在使用量資料中的詳細資訊，請參閱如果您是 EA 客戶，請 [瞭解您的 Enterprise 註冊的 Azure 保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 。 如果您有個別訂用帳戶，請參閱 [瞭解隨用隨付訂用帳戶的 Azure 保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="change-a-reservation-after-purchase"></a>在購買後變更保留

購買之後，您可以對保留進行下列類型的變更：

- 更新保留範圍
- 實例大小彈性 (（如果適用）) 
- 擁有權

您也可以將保留分割成較小的區塊，併合並已分割的保留。 這些變更都不會造成新的商業交易或變更保留的結束日期。

您無法在購買後直接進行下列類型的變更：

- 現有的保留區域
- SKU
- 數量
- 持續時間

但是，如果您想要進行變更，您可以 *交換* 保留。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](../cost-management-billing/reservations/manage-reserved-vm-instance.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
    - [什麼是 Azure 保留項目？](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [管理 Azure 中的保留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [了解保留項目折扣的套用方式](../cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [了解採用隨用隨付費率的訂用帳戶的保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)