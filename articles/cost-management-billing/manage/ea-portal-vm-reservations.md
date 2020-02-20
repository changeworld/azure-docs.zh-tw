---
title: Azure EA VM 保留執行個體
description: 本文將摘要說明 VM 保留執行個體的 Azure 保留如何協助您使用企業註冊來節省成本。
author: bandersmsft
ms.author: banders
ms.date: 02/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 3eceef9dc0bc29f7e105ac0b110bd2550413fa61
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200705"
---
# <a name="azure-ea-vm-reserved-instances"></a>Azure EA VM 保留執行個體

本文將摘要說明 VM 保留執行個體的 Azure 保留如何協助您使用企業註冊來節省成本。 如需有關保留的詳細資訊，請參閱[什麼是 Azure 保留？](../reservations/save-compute-costs-reservations.md)。

## <a name="reservation-exchanges-and-refunds"></a>保留交換和退款

您可以用保留來交換相同類型的其他保留。 如果您不再需要保留，也可以退款，每年最多 50,000 美元。 Azure 入口網站可用於對保留進行交換或退款。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](../reservations/exchange-and-refund-azure-reservations.md)。

## <a name="reservation-costs-and-usage"></a>保留的成本和使用量

Enterprise 合約客戶可以在 Azure 入口網站和 REST API 中檢視成本和使用量資料。 透過保留的成本和使用量，您可以：

- 取得保留購買資料。
- 知道哪個訂用帳戶、資源群組或資源使用了保留。
- 針對保留使用量進行退款。
- 計算保留節省成本。
- 取得保留的低使用量資料。
- 攤銷保留成本。

如需保留成本和使用量資料的詳細資訊，請參閱[取得 Enterprise 合約保留成本和使用量](../reservations/understand-reserved-instance-usage-ea.md)。

如需有關價格的相關資訊，請參閱 [Linux 虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或 [Windows 虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)。

## <a name="reserved-instances-api-support"></a>保留執行個體的 API 支援

您可以使用 Azure API 以程式設計方式為您的組織取得 Azure 服務或軟體保留的資訊。 如需詳細資訊，請參閱[適用於 Azure 保留自動化的 API ](../reservations/reservation-apis.md)。

## <a name="azure-reserved-virtual-machine-instances"></a>Azure 保留的虛擬機器執行個體

保留執行個體可讓所有 VM 降低虛擬機器成本高達 72% (超過隨用隨付價格)，若與 Azure Hybrid Benefit 結合，最高可省下 82%。 您可利用一年期或三年期的預付款，更妥善地設定工作負載、預算和預測的優先順序。 您也可以隨著業務需求變更來交換或取消保留。

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>如何購買保留的虛擬機器執行個體

若要購買 Azure 保留的虛擬機器執行個體，Azure Enterprise 註冊管理員必須在 [Azure EA 入口網站](https://ea.azure.com/)中 [註冊]  索引標籤上的 [註冊詳細資料]  區段中，啟用 [保留執行個體]  購買選項。

一旦啟用 EA 註冊以新增保留執行個體後，任何具有 EA 註冊相關聯作用中訂用帳戶的帳戶擁有者，都可以在 [Azure 入口網站](https://aka.ms/reservations)中購買保留的虛擬機器執行個體。 如需詳細資訊，請參閱[預付虛擬機器並且使用保留的虛擬機器執行個體來節省成本](https://go.microsoft.com/fwlink/?linkid=861721)。

### <a name="how-to-view-reserved-instance-purchase-details"></a>如何檢視保留執行個體購買詳細資料

您可以透過 [Azure 入口網站](https://aka.ms/reservations)左側的 [保留]  功能表，或從 [Azure EA 入口網站](https://ea.azure.com/)檢視您的保留執行個體購買詳細資料。 從左側功能表中選取 [報告]  ，然後向下捲動至 [使用量摘要]  索引標籤上的 [依服務顯示的費用]  區段。捲動至區段底部，而您的保留執行個體購買和使用量將會列在結尾，如服務名稱旁邊的「1 年」或「3 年」指名所示，例如：Standard_DS1_v2 eastus 1 年或 Standard_D2s_v3 eastus2 3 年。

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>如何變更與保留執行個體相關聯的訂用帳戶，或將保留執行個體權益轉移到同一個帳戶下的訂用帳戶？

在任何指定的時間，只有一個訂用帳戶可以接收保留執行個體權益。 您可以變更可接收保留執行個體權益的訂用帳戶，其做法如下：

- 登入 [Azure 入口網站](https://aka.ms/reservations)。
- 在相同帳戶之下建立不同訂用帳戶的關聯，以更新已套用的訂用帳戶範圍。

### <a name="how-to-view-reserved-instance-usage-details"></a>如何檢視保留執行個體使用量詳細資料

您可以在 [Azure 入口網站](https://aka.ms/reservations)或 [Azure EA 入口網站](https://ea.azure.com/) (適用於有權檢視帳單資訊的 EA 客戶) 的 [報告]   > [使用量摘要]   > [依費用顯示的費用]  之下，檢視您的保留執行個體使用量詳細資料。 您的保留執行個體可以包含 'Reservation' 的服務名稱進行識別，例如：Reservation-Base VM 或 Virtual Machines Reservation-Windows Svr (1 Core)。

您的使用量詳細資料和進階報告下載 CSV 包含其他保留執行個體使用量資訊。 [其他資訊]  欄位可協助您識別保留執行個體使用量。

如果您並未使用 Azure Hybrid Benefit 來購買 Azure 保留的 VM 執行個體，保留執行個體將會發出兩個計量 (硬體和軟體)。 如果您使用了 Azure Hybrid Benefit 來購買保留執行個體，您在保留執行個體使用量詳細資料中就看不到軟體計量。

### <a name="reserved-instance-billing"></a>保留執行個體計費

對於 Enterprise 客戶 而言，預付金可用於購買 Azure 保留的 VM 執行個體。 如果您的註冊有足夠的預付金餘額來涵蓋保留執行個體購買，此金額將會從您的預付金餘額中扣除，而且您不會收到購買的發票。

在 Azure EA 客戶用完所有預付金的情況下，仍可購買保留執行個體，而這些購買項目將會在下一期超額帳單上開立發票。 保留執行個體超額 (如果有的話) 將會成為一般超額發票的一部分。

### <a name="reserved-instance-expiration"></a>保留執行個體到期

您會在保留和到期前 30 天收到電子郵件通知。 保留到期後，部署的 VM 仍會繼續執行，且會以隨用隨付的費率計費。 如需詳細資訊，請參閱[保留的虛擬機器執行個體供應項目](https://azure.microsoft.com/pricing/reserved-vm-instances/)。

## <a name="next-steps"></a>後續步驟
- 如需有關 Azure 保留的詳細資訊，請參閱[什麼是 Azure 保留？](../reservations/save-compute-costs-reservations.md)。
- 若要深入了解企業保留成本和使用量，請參閱[取得 Enterprise 合約保留成本和使用量](../reservations/understand-reserved-instance-usage-ea.md)。
- 如需有關價格的相關資訊，請參閱 [Linux 虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或 [Windows 虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)。
