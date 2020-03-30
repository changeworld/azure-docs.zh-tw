---
title: 使用預留容量優化 Blob 存儲的成本 - Azure 存儲
description: 瞭解如何購買 Azure 存儲預留容量，以節省塊 Blob 和 Azure 資料存儲湖存儲 Gen2 資源的成本。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e73686629de8481f6a37e5bfafc9b723206b4853
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351037"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>使用保留容量將 Blob 儲存體的成本最佳化

您可以使用 Azure 存儲預留容量為 Blob 資料節省存儲成本。 當您提交一年或三年的預留時，Azure 存儲預留容量可為標準存儲帳戶中的塊 Blob 和 Azure 資料存儲庫存儲 Gen2 資料的容量提供折扣。 預訂提供預訂期限的固定存儲容量。

Azure 存儲預留容量可以顯著降低塊 Blob 和 Azure 資料存儲湖存儲 Gen2 資料的容量成本。 實現的成本節約取決於預訂的持續時間、您選擇預訂的總容量以及您為存儲帳戶選擇的冗余訪問層和類型。 預留容量提供計費折扣，不會影響 Azure 存儲資源的狀態。

如需 Azure 儲存體保留定價的詳細資訊，請參閱[區塊 Blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)和 [Azure Data Lake Storage Gen 2 定價](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

## <a name="reservation-terms-for-azure-storage"></a>Azure 存儲的預留條款

以下各節介紹 Azure 存儲預留的術語。

### <a name="reservation-capacity"></a>預訂容量

您可以購買 Azure 存儲預留容量，單位為 100 TB，每月購買 1 PB，為期一年或三年。

### <a name="reservation-scope"></a>預訂範圍

Azure 存儲預留容量可用於單個訂閱或多個訂閱（共用範圍）。 當限定為單個訂閱時，保留折扣將僅應用於選定的訂閱。 當範圍限定為多個訂閱時，預留折扣將在客戶的計費上下文中在這些訂閱中共用。

購買 Azure 存儲預留容量時，可以使用預留塊 Blob 和 Azure 資料湖存儲 Gen2 資料。 預留適用于您在購買範圍內的使用，不能僅限於訂閱中的特定存儲帳戶、容器或物件。 無法跨多個訂閱拆分預留。

Azure 存儲預留僅涵蓋存儲在訂閱或共用資源組中的資料量。 保留中不包括早期刪除、操作、頻寬和資料傳輸費用。 購買預訂後，將按折扣費率而不是即付即用費率收取與預訂屬性匹配的容量費用。 有關 Azure 預留的詳細資訊，請參閱[什麼是 Azure 預留？](/azure/billing/billing-save-compute-costs-reservations)

### <a name="supported-account-types-tiers-and-redundancy-options"></a>受支援的帳戶類型、層和冗余選項

Azure 存儲預留容量可用於標準存儲帳戶中的資源，包括通用 v2 （GPv2） 和 Blob 存儲帳戶。

所有訪問層（熱、酷和存檔）都支援預訂。 有關訪問層的詳細資訊，請參閱 Azure [Blob 存儲：熱、酷和存檔訪問層](storage-blob-storage-tiers.md)。

所有類型的冗余都支援進行預留。 有關冗余選項的詳細資訊，請參閱[Azure 存儲冗余](../common/storage-redundancy.md)。

> [!NOTE]
> Azure 存儲預留容量不適用於高級存儲帳戶、通用 v1 （GPv1） 存儲帳戶、Azure 資料湖存儲第 1 代、頁面 Blob、Azure 佇列存儲、Azure 表存儲或 Azure 檔。  

### <a name="security-requirements-for-purchase"></a>購買的安全要求

要購買預留容量：

- 您必須在至少一個企業或個人訂閱中處於 **"擁有者**"角色，該訂閱具有即用即付費率。
- 對於企業訂閱，必須在 EA 門戶中啟用 **"添加預留實例**"。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
- 對於雲解決方案供應商 （CSP） 計畫，只有管理員代理或銷售代理才能購買 Azure Blob 存儲預留容量。

## <a name="determine-required-capacity-before-purchase"></a>購買前確定所需容量

購買 Azure 存儲預留時，必須選擇預留的區域、訪問層和冗余選項。 您的預訂僅適用于存儲在該區域、訪問層和冗余級別的資料。 例如，假設您使用區域冗余存儲 （ZRS） 為熱層購買美國西部資料預留。 對於美國東部的資料、存檔層中的資料或異地冗余存儲 （GRS） 中的資料，不能使用相同的預留。 但是，您可以購買其他預訂，以滿足您的其他需求。  

現在，100 TB 或 1 PB 模組的預訂提供，1 個 PB 模組的折扣更高。 在 Azure 門戶中購買預留時，Microsoft 可能會根據以前的使用方式為您提供建議，以説明確定應購買哪個預留。

## <a name="purchase-azure-storage-reserved-capacity"></a>購買 Azure 存儲預留容量

您可以通過[Azure 門戶](https://portal.azure.com)購買 Azure 存儲預留容量。 保留的付款方式可為預先付款或每月付款。 有關使用每月付款進行購買的詳細資訊，請參閱[使用預付或每月付款購買 Azure 預訂](/azure/billing/billing-monthly-payments-reservations)。

有關確定適合方案的預留條款的説明，請參閱[瞭解 Azure 存儲預留容量折扣](../../cost-management-billing/reservations/understand-storage-charges.md)。

按照以下步驟購買預留容量：

1. 導航到 Azure 門戶中的["購買預留"](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)窗格。  
1. 選擇**Azure Blob 存儲**以購買新預留。  
1. 請填寫必填欄位，如下表中所述：

    ![顯示如何購買預留容量的螢幕截圖](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |欄位  |描述  |
   |---------|---------|
   |**Scope**   |  指示有多少訂閱可以使用與預訂關聯的計費權益。 它也會控制保留容量套用至特定訂用帳戶的方式。 <br/><br/> 如果選擇 **"共用**"，則預留折扣將應用於計費上下文中任何訂閱中的 Azure 存儲容量。 計費內容取是以您註冊 Azure 的方式為基礎。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 對於即用即付客戶，共用範圍包括帳戶管理員創建的所有個人訂閱，即用即用即付費率。  <br/><br/>  如果選擇 **"單一訂閱**"，則預留折扣將應用於所選訂閱中的 Azure 存儲容量。 <br/><br/> 如果選擇 **"單個資源組**"，則預留折扣將應用於所選訂閱中的 Azure 存儲容量以及該訂閱中的選定資源組。 <br/><br/> 您可以在購買預訂後更改預訂範圍。  |
   |**訂閱**  | 用於為 Azure 存儲預留付費的訂閱。 所選訂閱的付款條件用於收取費用。 訂閱必須是以下類型之一： <br/><br/>  企業協定（優惠編號：MS-AZR-0017P 或 MS-AZR-0148P）：對於企業訂閱，費用從註冊的貨幣承諾餘額中扣除，或按超額收取。 <br/><br/> 具有即用即付費率的個人訂閱（優惠編號：MS-AZR-0003P 或 MS-AZR-0023P）：對於具有即用即付費率的個人訂閱，費用由訂閱上的信用卡或發票付款條件計費。    |
   | **地區** | 保留生效的區域。 |
   | **存取層** | 保留生效的接入層。 選項包括*熱*、*酷*或*存檔*。 有關訪問層的詳細資訊，請參閱 Azure [Blob 存儲：熱、酷和存檔訪問層](storage-blob-storage-tiers.md)。 |
   | **備援性** | 預留的冗余選項。 選項包括*LRS、ZRS、GRS*和*RA-GZRS*。 *LRS* *GRS* 有關冗余選項的詳細資訊，請參閱[Azure 存儲冗余](../common/storage-redundancy.md)。 |
   | **計費頻率** | 指示帳戶為預訂計費的頻率。 選項包括*每月*或*預付*。 |
   | **大小** | 保留生效的區域。 |
   |**術語**  | 一年或三年。   |

1. 選擇預留的參數後，Azure 門戶將顯示成本。 該門戶還顯示與即用即付計費的折扣百分比。

1. 在 **"購買預訂"** 窗格中，查看預訂的總成本。 您還可以提供預訂的名稱。

    ![顯示如何購買預訂的螢幕截圖](media/storage-blob-reserved-capacity/purchase-reservations.png)

購買預留後，它將自動應用於任何與預留條款匹配的現有 Azure 存儲塊 Blob 或 Azure 資料湖存儲 Gen2 資源。 如果尚未創建任何 Azure 存儲資源，則每當創建與保留條款匹配的資源時，都將應用預留。 在這兩種情況下，預訂期限在成功購買後立即開始。

## <a name="exchange-or-refund-a-reservation"></a>交換或退還預訂

您可以兌換或退款預訂，但有一定的限制。 這些限制在以下各節仲介紹。

要交換或退還預留，請導航到 Azure 門戶中的預留詳細資訊。 選擇 **"兌換**"或 **"退款**"，然後按照說明提交支援請求。 處理請求後，Microsoft 將向您發送一封電子郵件以確認請求的完成。

有關 Azure 預留策略的詳細資訊，請參閱[Azure 預留的自助服務交換和退款](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

### <a name="exchange-a-reservation"></a>交換預訂

通過交換預訂，您可以根據預訂中未使用的部分獲得按比例退款。 然後，您可以將退款應用於新的 Azure 存儲預留的購買價格。

您可以進行的交流數量沒有限制。 此外，不收取與交易所相關的費用。 您購買的新預訂的價值必須等於或大於原始預訂的按比例抵免額。 Azure 存儲預留只能換用於其他 Azure 存儲預留，而不能為任何其他 Azure 服務進行預留。

### <a name="refund-a-reservation"></a>退還預訂

您可以隨時取消 Azure 存儲預留。 取消後，您將根據預訂的剩餘期限按比例退款，減去 12% 的提前終止費。 每年的最高退款額為 50，000 美元。

取消預訂會立即終止預訂，並將剩餘月份返還給 Microsoft。 剩餘的按比例餘額（減去費用）將退還至您原始購買形式。

## <a name="expiration-of-a-reservation"></a>預訂到期

當預訂到期時，您根據該預留使用的任何 Azure 存儲容量都將以即用即付費率計費。 保留不會自動更新。

您將在預訂到期前 30 天收到電子郵件通知，並在到期日期再次收到通知。 要繼續利用預訂提供的成本節約，請不遲于到期日期續訂。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有疑問或需要幫助，[請創建支援請求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 保留項目？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解保留折扣如何套用至 Azure 儲存體](../../cost-management-billing/reservations/understand-storage-charges.md)
