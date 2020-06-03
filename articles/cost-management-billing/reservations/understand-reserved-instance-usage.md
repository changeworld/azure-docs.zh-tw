---
title: 個別訂用帳戶的 Azure 保留使用量
description: 學習如何看懂使用量，以了解個別訂用帳戶 (採用隨用隨付費率) 的 Azure 保留套用情形。
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f3890b2c2c9434c7970bd969f6f4620e5d235d96
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84017693"
---
# <a name="understand-azure-reservation-usage-for-your-individual-subscription-with-pay-as-you-go-rates-subscription"></a>了解個別訂用帳戶 (採用隨用隨付費率) 的 Azure 保留使用量

使用[保留頁面](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)的 ReservationId 和 [Azure 帳戶入口網站](https://account.azure.com)的使用量檔案，來評估保留使用量。

如果您是擁有 Enterprise 合約的客戶，請參閱[了解 Enterprise 註冊的保留使用量](understand-reserved-instance-usage-ea.md)。

本文假設保留會套用至單一訂用帳戶。 如果此保留套用至多個訂用帳戶，則保留的權益可能會延伸到多個使用量 CSV 檔案。

## <a name="usage-for-reserved-virtual-machine-instances"></a>保留的虛擬機器執行個體使用量

針對下列幾個區段，假設您在美國東部區域執行 Standard_DS1_v2 Windows VM，且保留的 VM 執行個體資訊看起來會類似下表：

| 欄位 | 值 |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|數量 |1|
|SKU | Standard_DS1_v2|
|區域 | eastus |

已涵蓋虛擬機器的硬體部分，因為部署的虛擬機器符合保留屬性。 若要查看保留的 VM 執行個體未涵蓋哪些 Windows 軟體，請參閱 [Azure 保留 VM 執行個體的 Windows 軟體成本](reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>VM 的 CSV 檔案「說明」區段

CSV 檔案的這個區段會顯示保留的整體使用量。 請對包含 **"Reservation-"** 的 [計量子類別] 欄位套用篩選。 您會看到類似下列螢幕擷取畫面的內容：

![篩選後保留使用量詳細資料與費用的螢幕擷取畫面](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

**Reservation-Base VM** 行有保留所涵蓋的小時總數。 這一行為 $0.00，原因是其在保留的涵蓋範圍內。 **Reservation-Windows Svr (1 核心)** 行涵蓋了 Windows 軟體成本。

### <a name="daily-usage-section-of-csv-file"></a>CSV 檔案的「每日使用量」區段

篩選**其他資訊**並輸入**保留識別碼**。 以下螢幕擷取畫面顯示和此保留相關的欄位。

![每日使用量詳細資料與費用的螢幕擷取畫面](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. [其他資訊] 欄位中的 **ReservationId** 是套用至 VM 的保留。
2. **ConsumptionMeter** 是 VM 的計量識別碼。
3. **Reservation-Base VM** 的 [計量子類別]  行在 [說明] 區段中代表 $0 成本。 執行此 VM 的成本已由保留支付。
4. [計量識別碼]  是保留的計量識別碼。 此計量的成本為 $0。 任何有資格獲得保留折扣的 VM 都會出現此計量識別碼。
5. Standard_DS1_v2 是一種 vCPU 虛擬機器，且是在沒有 Azure Hybrid Benefit 的情況下部署的虛擬機器。 因此，這個計量涵蓋 Windows 軟體的額外費用。 若要尋找對應於 D 系列 1 核心 VM 的計量，請參閱 [Azure 保留 VM 執行個體的 Windows 軟體成本](reserved-instance-windows-software-costs.md)。 如果您擁有 Azure Hybrid Benefit，就不會產生此額外費用。

## <a name="usage-for-sql-database--cosmos-db-reservations"></a>SQL Database 與 Cosmos DB 保留的使用量

下列各節使用 Azure SQL Database 作為範例來說明使用情況報表。 您也可以使用相同步驟來取得 Azure Cosmos DB 的使用情況。

假設您目前在美國東部區域執行 SQL Database Gen 4，且保留資訊看起來類似下表：

| 欄位 | 值 |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|數量 |2|
|Products| SQL Database Gen 4 (2 核心)|
|區域 | eastus |

### <a name="statement-section-of-csv-file"></a>CSV 檔案的「說明」區段

針對 [保留執行個體使用方式]  計量名稱進行篩選，然後選擇所需的 [計量類別目錄]  - [Azure SQL 資料庫] 或 [Azure Cosmos DB]。 您會看到類似下列螢幕擷取畫面的內容：

![SQL Database 保留容量的 CSV 檔案](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

[保留的執行個體使用量]  行有保留所涵蓋的核心時數總數。 這一行的費率為 $0，原因是保留涵蓋該成本。

### <a name="detail-section-of-csv-file"></a>CSV 檔案的「詳細資料」區段

篩選**其他資訊**並輸入**保留識別碼**。 以下螢幕擷取畫面顯示與 SQL Database 保留容量相關的欄位。

![SQL Database 保留容量的 CSV 檔案](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. [其他資訊] 欄位中的 **ReservationId** 是套用至 SQL 資料庫資源的 SQL Database 保留容量。
2. **ConsumptionMeter** 是 SQL Database 資源的計量識別碼。
3. [計量識別碼]  是保留計量。 此計量的成本為 $0。 任何有資格獲得保留折扣的 SQL Database 資源都會在 CSV 檔案中顯示此計量識別碼。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../../azure-sql/database/reserved-capacity-overview.md)
- [管理 Azure 保留項目](manage-reserved-vm-instance.md)
- [了解保留項目折扣的套用方式](../manage/understand-vm-reservation-charges.md)
- [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
- [Windows 軟體成本不包含在 Reservations 內](reserved-instance-windows-software-costs.md)
