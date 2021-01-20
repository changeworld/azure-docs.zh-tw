---
title: 預付 Azure 專用主機以節省成本
description: 瞭解如何購買 Azure 專用主機保留實例，以節省您的計算成本。
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: cac7a6dfcd98b9de61fabdb8e43ca1cdfd4023c2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598296"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>使用 Azure 專用主機保留來節省成本

當您認可至 Azure 專用主機的保留實例時，您可以節省成本。 保留折扣會自動套用至符合保留範圍和屬性的執行中專用主機數目。 您不需要將保留指派給專用主機來取得折扣。 購買的保留實例僅涵蓋您使用量的計算部分，並包含軟體授權成本。 請參閱 [虛擬機器的 Azure 專用主機總覽](./dedicated-hosts.md)。

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>購買之前，請先決定適當的專用主機 SKU


購買保留之前，您應該先判斷您需要的專用主機。 定義專用主機的 SKU，代表 VM 系列和類型。 

一開始請先移至 [Windows 虛擬機器](./sizes.md) 或 [Linux](./sizes.md) 的支援大小來識別 VM 系列。

接下來，請檢查 Azure 專用主機是否支援此功能。 [Azure 專用主機定價](https://aka.ms/ADHPricing) 頁面具有專用主機 sku 的完整清單、其 CPU 資訊，以及各種定價選項 (包括保留的實例) 。

您可能會發現數個 Sku 支援不同類型)  (VM 系列。 藉由比較主機 (數目個 vcpu) 的容量來識別最佳 SKU。 請注意，您可以將保留套用至多個支援相同 VM 系列的專用主機 Sku (例如 DSv3_Type1 和 DSv3_Type2) ，但不能跨不同的 VM 系列 (例如 DSv3 和 ESv3) 。



## <a name="purchase-restriction-considerations"></a>購買限制考慮

保留實例適用于大部分的專用主機大小，但有一些例外狀況。

保留折扣不適用於下列各項：

- 雲端保留不適用於德國或中國區域 **中的購買**。

- **配額不足** -具有單一訂用帳戶範圍的保留，必須在新的保留實例的訂用帳戶中有可用的 vCPU 配額。 例如，如果目標訂用帳戶的配額限制為10個 vcpu （DSv3 系列），則您無法購買支援此系列的保留專用主機。 保留的配額檢查包含已在訂用帳戶中部署的 Vm 和專用主機。 您可以 [建立配額增加要求](../azure-portal/supportability/resource-manager-core-quotas-request.md) 以解決此問題。

- **容量限制** -在罕見的情況下，Azure 會限制購買專用主機 sku 子集的新保留，因為區域中的低容量。

## <a name="buy-a-reservation"></a>購買保留項目

您可以在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中購買 Azure 專用主機實例的保留實例。

保留的付款方式可為[預先付款或每月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)。 這些需求適用于購買保留的專用主機實例：

- 您必須是至少一個 EA 訂用帳戶的擁有者角色，或具有隨用隨付費率的訂用帳戶。

- 若為 EA 訂用帳戶，必須在 [ea 入口網站](https://ea.azure.com/)中啟用 [**新增保留實例**] 選項。 或者，如果該設定已停用，則您必須是訂用帳戶的 EA 管理員。

- 針對雲端解決方案提供者 (CSP) 程式，只有系統管理員代表或銷售代表可以購買保留項目。

購買執行個體：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [所有服務] \> [保留]。

3. 選取 [ **新增** ] 以購買新的保留，然後按一下 [ **專用主機**]。

4. 輸入必要欄位。 執行符合您選取之屬性的專用主機實例符合取得保留折扣的資格。 取得折扣的專用主機實例實際數目取決於所選的範圍和數量。

如果您有 EA 合約，可以使用 [ **新增更多] 選項** 來快速新增額外的實例。 此選項不適用於其他訂用帳戶類型。

| **欄位**           | **說明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 訂用帳戶        | 用來支付保留項目的訂用帳戶。 保留項目的費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 enterprise 合約 (供應專案號碼： MS-AZR-0003P->ms-azr-0017p 或 MS-AZR-0003P-Ms-azr-0148p) 或 Microsoft 客戶合約，或是採用隨用隨付費率的個別訂用帳戶 (供應專案號碼： MS-MS-AZR-0003P-Ms-azr-0003p 或 MS-MS-AZR-0003P-Ms-azr-0023p) 。 費用會從 Azure 預付款扣除 (先前稱為預付金) 餘額（如果有的話），或作為超額部分收費。 針對具有隨用隨付費率的訂用帳戶，費用會以訂用帳戶的信用卡或發票付款方法計費。 |
| 影響範圍               | 保留項目範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取：                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 區域              | 保留項目所涵蓋的 Azure 區域。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 專用主機大小 | 專用主控制項實例的大小。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 詞彙                | 一年或三年。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 數量            | 保留項目內所購買的執行個體數目。 Quantity 是可取得帳單折扣的執行中專用主機實例的數目。                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **單一資源群組範圍** — 只會將保留折扣套用至所選資源群組中的相符資源。

- **單一訂用帳戶範圍** — 會將保留折扣套用至所選訂用帳戶中的相符資源。

- **共用範圍** — 會將保留折扣套用至計費內容中合格訂用帳戶的相符資源。 若是 EA 客戶，計費內容為註冊。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是帳戶管理員所建立的所有合格訂用帳戶。

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

若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [使用 Azure 專用主機](./dedicated-hosts.md)

- [專用主機定價](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [管理 Azure 中的保留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [了解保留項目折扣的套用方式](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [了解採用隨用隨付費率的訂用帳戶的保留使用量](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [了解 Enterprise 註冊的保留項目使用量](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [Windows 軟體的成本不包括在保留項目內](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)