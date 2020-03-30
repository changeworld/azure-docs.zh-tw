---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590753"
---
使用預留容量保存 Azure 磁片存儲使用方式。 Azure 磁片存儲預留與 Azure 預留虛擬機器實例相結合，可降低虛擬機器 （VM） 總成本。 預留折扣將自動應用於所選預留範圍內的匹配磁片。 由於此自動應用程式，您無需將預留分配給託管磁片以獲得折扣。

折扣每小時應用一次，具體取決於磁片使用方式。 未使用的預留容量不會保留。 Azure 磁片存儲預留折扣不適用於非託管磁片、超級磁片或頁面 Blob 消耗。

## <a name="determine-your-storage-needs"></a>確定您的存儲需求

在購買預訂之前，請確定您的存儲需求。 目前，Azure 磁片存儲預留僅適用于選定的 Azure 高級 SSD SSD SKU。 高級 SSD 的 SKU 確定磁片的大小和性能。

在確定存儲需求時，不要僅僅考慮基於容量的磁片。 例如，不能預訂 P40 磁片，而無法使用該預留來為兩個較小的 P30 磁片付費。 購買預訂時，您只為每個 SKU 購買磁片總數的預留。

每個磁片 SKU 進行磁片預留。 因此，預留消耗量基於磁片 SKU 的單位，而不是提供的大小。

例如，假設您保留一個 P40 磁片，該磁片具有 2 TiB 的預配存儲容量。 還假定您只分配兩個 P30 磁片。 在這種情況下，P40 預留不包括 P30 消耗量，您在 P30 磁片上支付即用即付費率。
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>購買考量

在考慮購買磁片時，我們建議採用以下做法：

- 分析使用方式資訊，以説明確定應購買哪些預訂。 請確保跟蹤磁片 SKU 中的使用方式，而不是預配或使用的磁片容量。
- 檢查磁片保留以及 VM 預留。 我們強烈建議為 VM 使用和磁片使用方式進行預留，以最大限度地節省成本。 您可以先確定正確的 VM 預留，然後評估磁片預留。 通常，您將為每個工作負載提供標準配置。 例如，SQL Server 服務器可能具有兩個 P40 資料磁片和一個 P30 作業系統磁片。
  
  這種模式可以説明您確定可能購買的預留金額。 此方法可以簡化評估過程，並確保 VM 和磁片都有一致的計畫。 該計畫包含訂閱或區域等注意事項。

## <a name="purchase-restrictions"></a>購買限制

預訂折扣當前不適用於以下事項：

- 非託管磁片或頁面 blob。
- 標準 SSD 或標準硬碟 （HDD）。
- 小於 P30 的高級 SSD SKU：P1、P2、P3、P4、P6、P10、P15 和 P20 SSD SKU。
- Azure 政府、Azure 德國或 Azure 中國區域中的磁片。

在極少數情況下，Azure 會將購買新預留限制為磁片 SKU 的子集，因為區域中的容量較低。

## <a name="buy-a-disk-reservation"></a>購買磁片預訂

您可以通過[Azure 門戶](https://portal.azure.com/)購買 Azure 磁片存儲預留。 您可以提前或按月付款支付預訂費用。 有關使用每月付款進行購買的詳細資訊，請參閱[每月付款購買預訂](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)。

按照以下步驟購買預留容量：

1. 轉到 Azure 門戶中的["購買預留"](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)窗格。

1. 選擇**Azure 託管磁片**以購買預留。

    ![用於購買預訂的窗格](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. 指定下表中描述的所需值：

   |元素  |描述  |
   |---------|---------|
   |**Scope**   |  有多少訂閱可以使用與預訂關聯的計費權益。 此值還指定如何將預留應用於特定訂閱。 <br/><br/> 如果選擇 **"共用**"，則預留折扣將應用於計費上下文中每個訂閱中的 Azure 存儲容量。 計費內容取是以您註冊 Azure 的方式為基礎。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 對於即用即付客戶，共用範圍包括帳戶管理員創建的所有個人訂閱，即用即用即付費率。  <br/><br/>  如果選擇 **"單一訂閱**"，則預留折扣將應用於所選訂閱中的 Azure 存儲容量。 <br/><br/> 如果選擇 **"單個資源組**"，則預留折扣將應用於所選訂閱中以及該訂閱的選定資源組中的 Azure 存儲容量。 <br/><br/> 您可以在購買預訂後更改預訂範圍。  |
   |**訂閱**  | 用於為 Azure 存儲預留付費的訂閱。 所選訂閱的付款條件用於收取費用。 訂閱必須是以下類型之一：<br/><ul><li> 企業協定（產品編號 MS-AZR-0017P 和 MS-AZR-0148P）。 針對 Enterprise 訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。</li><br/><li>個人訂閱，即用即付費率（產品編號 MS-AZR-0003P 和 MS-AZR-0023P）。 對於具有即用即付費率的個人訂閱，費用將計入訂閱上的信用卡或發票付款條件。</li></ul>    |
   | **磁片** | 要創建的 SKU。 |
   | **地區** | 保留生效的區域。 |
   | **計費頻率** | 帳戶的預訂費用。 選項包括**每月**和**預付**。 |

    ![用於選擇要購買的產品的窗格。](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 指定預留值後，Azure 門戶將顯示成本。 該門戶還顯示與即用即付計費的折扣百分比。 選擇 **"下一步**"以繼續"**購買預訂"** 窗格。

1. 在 **"購買預訂"** 窗格中，您可以命名預訂並選擇要預訂的總數。 預留數映射到磁片數。 例如，如果要保留一百個磁片，請輸入**數量**值**100**。

1. 查看預訂的總成本。

    !["購買預訂"窗格](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

購買預留後，它將自動應用於與預訂條款匹配的任何現有磁片存儲資源。 如果您尚未創建任何磁片存儲資源，則每當創建與保留條款匹配的資源時，保留都會適用。 在這兩種情況下，預訂期限在成功購買後立即開始。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以在某些限制範圍內取消、交換或退款預訂。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)。

## <a name="expiration-of-a-reservation"></a>預訂到期

當預留過期時，您在該預留下使用的任何 Azure 磁片存儲容量都將以即用即付費率計費。 保留不會自動更新。

您將在預訂到期前 30 天收到電子郵件通知，並在到期日期再次收到通知。 要繼續利用預訂提供的成本節約，請不遲于到期日期續訂。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有疑問或需要幫助，[請創建支援請求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 保留項目？](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解如何將您的保留折扣套用至 Azure 磁碟儲存體](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
