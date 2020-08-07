---
title: Azure 保留的自助式交換和退費
description: 了解如何進行 Azure 保留的交換或退款。 您必須擁有保留訂單的擁有者存取權，才能進行保留的交換和退費。
author: yashesvi
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: f1323d21ee59386220a35cc31b17cab3441f0e0d
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87457629"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure 保留的自助式交換和退費

Azure 保留可提供彈性，以協助您滿足不斷演變的需求。 您可以用保留來交換相同類型的其他保留。 例如，您可以交換虛擬機器保留，以購買任何其他 VM 大小或區域的另一個保留。 同樣地，您可以交換 SQL PaaS 資料庫保留，以購買任何 SQL PaaS 資料庫類型或區域的另一個保留。 您也可以將保留退款，但是在 12 個月的滾動期間內，計費範圍 (例如 EA、Microsoft 客戶合約和 Microsoft 合作夥伴合約) 中已取消的保留承諾用量總數不可超過 50,000 美元。 Azure Databricks 保留容量、Azure VMware solution by CloudSimple 保留、Azure Red Hat Open Shift 保留、Red Hat 方案和 SUSE Linux 方案不符合退款資格。

自助式交換和取消功能不適用於美國政府 Enterprise 合約客戶。 可支援其他美國政府訂用帳戶類型，包括隨用隨付和雲端解決方案提供者雲端解決方案提供者 (CSP)。

> [!NOTE]
> - **您必須擁有保留訂單的擁有者存取權，才能對現有的保留進行交換和退款**。 您可以[新增或變更可以管理保留的使用者](https://docs.microsoft.com/azure/cost-management-billing/reservations/manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation)。
> - Microsoft 目前不收取保留退款的提早終止費用。 對於未來的退款，我們可能會收取費用。 我們目前尚未訂定啟用此費用的日期。

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>如何交換現有的保留或將其退款

您可以從 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)交換您的保留。

1. 選取要退款的保留，然後選取 [交換]。  
    [![此範例影像顯示要退還的保留](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. 選取您要購買的 VM 產品，並輸入數量。 請確定新購買的金額總計高於退還的金額總計。 [購買之前請確認大小正確](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)。  
    [![此範例影像顯示要透過交換來購買的 VM 產品](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. 檢閱並完成交易。  
    [![此範例影像顯示要透過交換來購買的 VM 產品，並完成退還](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

若要退還保留，請移至 [保留詳細資料]，然後選取 [退款]。

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Exchange 非進階儲存體換成進階儲存體

針對不支援進階儲存體的 VM 大小，您可以將所購買的保留換成可支援進階儲存體的對應 VM 大小。 例如，將 F1 換成 F1s。 若要進行交換，請移至 [保留詳細資料]，然後選取 [交換]。 交換並不會重設保留執行個體的期限，也不會產生新的交易。 

## <a name="how-transactions-are-processed"></a>交易的處理方式

首先，Microsoft 會取消現有保留，並依比例退還該保留的款項。 如果有交換，則會處理新的購買。 Microsoft 會根據帳戶類型和付款方式，使用下列其中一種方法來處理退款：

### <a name="enterprise-agreement-customers"></a>Enterprise 合約客戶

款項會新增至交換和退款的預付金 (如果原本是使用交換或退款來購買的話)。 如果使用所購買保留的預付金期限已失效，則會將點數新增至您目前的 Enterprise 合約預付金期限。 點數的有效期是從退款日期起算的 90 天內。 未使用的點數會在 90 天結束時到期。

如果原始購買是以超額部分進行的，則購買保留的原始發票和所有後續的發票都會重新開立並調整。 Microsoft 會針對退款發出貸項憑單。

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>隨用隨付發票付款和 CSP 計畫

原始保留的購買發票會取消，然後產生新發票以便退款。 若要交換，則新發票會顯示退款和新的購買。 系統會針對此購買來調整退款金額。 如果您只退還保留，則按比例計算的金額會留在 Microsoft，並針對未來購買的保留進行調整。 如果您以隨用隨付費率購買保留後轉換為 CSP，您可以在退還保留後重新購買，而不會產生違約金。

### <a name="pay-as-you-go-credit-card-customers"></a>隨用隨付信用卡客戶

原始發票會取消，並產生新的發票。 款項會退還到原本購買時所用的信用卡。 如果您已換卡，請[連絡支援人員](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="cancel-exchange-and-refund-policies"></a>取消、交換和退款原則

Azure 有下列取消、交換和退款原則。

**交換原則**

- 您可以退還多個現有保留，以購買相同類型的新保留。 您無法交換不同類型的保留。 例如，您無法退還 VM 保留以購買 SQL 保留。 您可以透過交換來變更保留屬性，例如系列、版本、SKU、區域、數量和期間。
- 只有保留擁有者可以處理交換。 [了解如何新增或變更可以管理保留的使用者](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
- 交換會以退款和重新購買的形式來處理；針對取消和新的保留購買會產生不同的交易。 針對您折抵的保留，系統會按比例計算保留的退款金額。 新的購買則會收取完整費用。 按比例計算的保留金額是所退還保留的按日、按比例計算殘值。
- 即使用來購買保留的 Enterprise 合約已到期並更新為新合約，您還是可以交換或退還保留。
- 新保留的存留期承諾用量應大於或等於退還保留的剩餘承諾用量。 範例：針對每個月 100 美元，並在第 18 次付款後交換的三年保留期，新保留的存留期承諾用量應為 1,800 美元或更高的金額 (每月付費或預付)。
- 在交換時所購買的新保留會有新的期限 (從交換時起算)。
- 交換沒有任何懲罰或年度限制。

**退款原則**

- 我們目前不收取提前終止費用，但未來可能會針對取消收取 12% 的提早終止費用。
- 對於帳單設定檔或單一註冊，在 12 個月的滾動期間內，已取消的承諾用量總數不可超過 50,000 美元。 例如，針對每個月 100 美元，並在第 18 個月退款的三年保留期，取消的承諾用量為 1,800 美元。 退款之後，您新的可用退款限制將是 48,200 美元。 自退款日起算的 365 天後，48,200 美元的限制將會增加 1,800 美元，而您的新額度將會是 50,000 美元。 帳單設定檔或 EA 註冊的任何其他保留取消都將會使用相同的額度，且適用相同的補充邏輯。
- 對於帳單設定檔或 EA 註冊，Azure 不會處理在 12 個月滾動期間內將超過 50,000 美元限制的任何退款。
- 退款會根據您購買價格或目前保留價格的最低價格來計算。
- 只有保留訂單擁有者可以處理退款。 [了解如何新增或變更可以管理保留的使用者](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](manage-reserved-vm-instance.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
    - [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
    - [管理 Azure 中的保留](manage-reserved-vm-instance.md)
    - [了解保留項目折扣的套用方式](../manage/understand-vm-reservation-charges.md)
    - [了解隨用隨付訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](reserved-instance-windows-software-costs.md)
    - [CSP 計畫中的 Azure 保留](/partner-center/azure-reservations)
