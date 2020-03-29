---
title: 為 Azure 專用主機預付費用以節省資金
description: 瞭解如何購買 Azure 專用主機保留實例以節省計算成本。
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207741"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>使用 Azure 專用主機的預留實例節省成本

提交 Azure 專用主機的保留實例時，可以節省資金。 預留折扣將自動應用於與預留範圍和屬性匹配的正在運行的專用主機的數量。 您無需將預留分配給專用主機以獲得折扣。 預留實例購買僅涵蓋您的使用的計算部分，並且確實包括軟體許可成本。 請參閱[虛擬機器的 Azure 專用主機概述](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)。

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>購買前確定正確的專用主機 SKU


在購買預訂之前，您應該確定您需要哪個專用主機。 SKU 是為表示 VM 系列和類型的專用主機定義的。 

首先，通過查看[Windows 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)或[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的支援大小來標識 VM 系列。

接下來，檢查 Azure 專用主機上是否支援它。 [Azure 專用主機定價](https://aka.ms/ADHPricing)頁包含專用主機 SKU 的完整清單、其 CPU 資訊以及各種定價選項（包括預留實例）。

您可能會發現多個 SKU 支援 VM 系列（具有不同類型）。 通過比較主機的容量（vCPU 數量）確定最佳 SKU。 請注意，您將能夠將預留應用於支援同一 VM 系列的多個專用主機 SKU（例如DSv3_Type1和DSv3_Type2），但不能跨不同的 VM 系列（如 DSv3 和 ESv3）。



## <a name="purchase-restriction-considerations"></a>購買限制注意事項

保留實例可用於大多數專用主機大小，但有一些例外。

預訂折扣不適用於以下事項：

- **雲** - 預訂不適用於在德國或中國地區購買。

- **配額不足** - 限定為單個訂閱的預留必須在新保留實例的訂閱中具有 vCPU 配額。 例如，如果目標訂閱的配額限制為 DSv3 系列 10 vCPU，則無法購買支援此系列的預留專用主機。 預留的配額檢查包括已在訂閱中部署的 VM 和專用主機。 您可以創建 [配額增加請求](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) 以解決此問題。

- **容量限制** - 在極少數情況下，Azure 會限制為專用主機 SKU 子集購買新預留，因為區域中的容量較低。

## <a name="buy-a-reservation"></a>購買保留項目

您可以在 [Azure 門戶](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中購買 Azure 專用主機實例的保留實例。

提前 [或按月付款](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations)支付預訂費用。 這些要求適用于購買預留的專用主機實例：

- 您必須在至少一個 EA 訂閱或具有即用即付費率的訂閱中擔任擁有者角色。

- 對於 EA 訂閱，必須在 [EA 門戶](https://ea.azure.com/)中啟用 **"添加預留實例"** 選項。 或者，如果該設定已停用，則您必須是訂用帳戶的 EA 管理員。

- 針對雲端解決方案提供者 (CSP) 程式，只有系統管理員代表或銷售代表可以購買保留項目。

若要購買執行個體：

1. 登入  [Azure 入口網站](https://portal.azure.com/)。

2. 選擇 **所有服務** \> **預訂**。

3. 選擇 **"添加"** 以購買新的預訂，然後按一下 **"專用主機**"。

4. 輸入必要欄位。 運行與您選擇的屬性匹配的專用主機實例有資格獲得預留折扣。 獲得折扣的專用主機實例的實際數量取決於所選的範圍和數量。

如果您有 EA 協定，則可以使用 **"添加更多"選項** 快速添加其他實例。 該選項不適用於其他訂閱類型。

| **領域**           | **描述**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 訂用帳戶        | 用來支付保留項目的訂用帳戶。 訂閱上的付款條件將收取預訂費用。 訂閱類型必須是企業協定（產品/服務編號：MS-AZR-0017P 或 MS-AZR-0148P）或 Microsoft 客戶協定或具有即用即付費率的個人訂閱（產品編號：MS-AZR-0003P 或 MS-AZR-0023P）。 費用從貨幣承諾餘額中扣除（如有）或按超額收取。 對於具有即用即付費率的訂閱，費用將計入訂閱上的信用卡或發票付款條件。 |
| 影響範圍               | 保留項目範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取：                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 區域              | 保留項目所涵蓋的 Azure 區域。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 專用主機大小 | 專用主機實例的大小。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 詞彙                | 一年或三年。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 數量            | 保留項目內所購買的執行個體數目。 數量是運行的專用主機實例的數量，這些實例可以獲得計費折扣。                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **單個資源組範圍** – 僅將預留折扣應用於所選資源組中的匹配資源。

- **單個訂閱範圍** – 將預留折扣應用於所選訂閱中的匹配資源。

- **共用範圍** – 將預留折扣應用於計費上下文中符合條件的訂閱中的匹配資源。 對於 EA 客戶，計費上下文是註冊。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是帳戶管理員所建立的所有合格訂用帳戶。

## <a name="usage-data-and-reservation-utilization"></a>使用資料和預訂利用率

對於獲得保留折扣的使用量，您的使用量資料有效價格會是零。 您可以看到哪個 VM 實例為每個預留提供了預留折扣。

有關預訂折扣如何顯示在使用方式資料中的詳細資訊，請參閱 [瞭解企業註冊的](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) Azure 預留使用方式（如果您是 EA 客戶）。 如果您有單獨的訂閱，請參閱 [瞭解即用即付訂閱的 Azure 預留使用方式](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。

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

但是，如果要進行更改，可以 *交換* 預訂。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以取消、交換保留或進行退費，但有某些限制。 有關詳細資訊，請參閱 [Azure 預留的自助服務交換和退款](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有疑問或需要幫助， [請創建支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

要瞭解如何管理預留，請參閱 [管理 Azure 預留](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)。

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [使用 Azure 專用主機](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [專用主機定價](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [管理 Azure 中的預留](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [了解保留項目折扣的套用方式](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [了解採用隨用隨付費率的訂用帳戶的保留使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [了解 Enterprise 註冊的保留項目使用量](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [Windows 軟體的成本不包括在保留項目內](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)


