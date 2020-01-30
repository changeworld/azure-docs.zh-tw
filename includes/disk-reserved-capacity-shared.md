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
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847130"
---
使用保留的容量來儲存您的 premium 固態硬碟（SSD）使用量，並結合保留的虛擬機器實例，您可以降低總 VM 成本。 保留折扣會自動套用至所選保留範圍中的相符磁片，您不需要將保留指派給受控磁片來取得折扣。 折扣會依磁片使用量每小時套用，而且任何未使用的保留容量也不會超過。 受控磁片保留折扣不適用於非受控磁片、ultra 磁片或分頁 blob 耗用量。

## <a name="determine-your-storage-needs"></a>判斷您的儲存體需求

購買保留之前，您應該決定您的儲存體需求為何。 目前，磁片保留僅適用于選取的 premium SSD Sku。 Premium SSD 的 SKU 會決定磁片的大小和效能。 在決定您的儲存體需求時，我們不建議您將磁片視為總容量，您無法將保留用於較大的磁片（例如 P40），並使用它來支付兩個較小的磁片（P30）。 購買保留時，您只會購買每個 SKU 的磁片總數。

磁片保留是根據磁片 SKU 進行，因此保留耗用量是以磁片 Sku 的單位為基礎，而不是提供的大小。 例如，如果您保留1個 P40 2 TiB 布建容量，但只配置2個 P30 磁片，則兩個 P30 耗用量將不會考慮 P40 保留，而且您會支付隨用隨付費率。



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>購買考量

考慮購買磁片保留時，建議您遵循下列最佳作法：

- 分析您的使用量資訊，以協助判斷您應該購買的保留。 請確定您追蹤的是磁片 Sku 中的使用量，而不是已布建或已使用的磁片容量。 
- 檢查您的磁片保留，以及您的 VM 保留專案。 我們強烈建議您為 VM 和磁片使用量進行保留，以達到最大儲存空間。 您可以從決定正確的 VM 保留開始著手，然後據以評估磁片保留。 一般而言，您會有每個工作負載的標準設定，例如，SQL server 可能會有兩個 P40 資料磁片和一個 P30 OS 磁片。 這種模式可協助您判斷可能購買的保留數量。 這種方法可以簡化評估程式，也請確定您在訂用帳戶、區域和其他方面都有對齊的 VM 和磁片計畫。 

## <a name="purchase-restrictions"></a>購買限制

保留折扣目前不適用於下列磁片：
- 非受控磁片/分頁 blob
- 標準 SSD 或標準 HDD
- 進階 SSD 的 Sku 小於 P30 –保留不適用於 P1/P2/P3/P4/P6/P10/P15/P20 進階 SSD Sku。
- 雲端-保留無法在 Azure Gov、德國或中國地區購買。
- 容量限制-在罕見的情況下，Azure 會限制購買磁片 Sku 子集的新保留，因為區域中的低容量。

## <a name="buy-a-disk-reservation"></a>購買磁片保留

您可以透過[Azure 入口網站](https://portal.azure.com/)購買 Azure 磁片保留。 您可以事先或按月付款來支付保留期。 如需以每月付款購買的詳細資訊，請參閱[每月付款的購買保留](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)。

請遵循下列步驟來購買保留容量：

1. 流覽至 Azure 入口網站中的 [[購買保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)] 分頁。
1. 選取 [ **Azure 受控磁碟**] 以購買保留。

    ![disks-reserved-purchase-reservation .png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. 請填寫必填欄位，如下表中所述：

   |欄位  |說明  |
   |---------|---------|
   |**範圍**   |  指出有多少訂用帳戶可以使用與保留相關的計費權益。 它也會控制保留容量套用至特定訂用帳戶的方式。 <br/><br/> 如果您選取 [**共用**]，保留折扣會套用至計費內容內任何訂用帳戶中 Azure 儲存體容量。 計費內容取是以您註冊 Azure 的方式為基礎。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 對於隨用隨付客戶，共用範圍包含帳戶管理員所建立之隨用隨付費率的所有個別訂閱。  <br/><br/>  如果您選取 [**單一訂**用帳戶]，保留折扣會套用至所選訂用帳戶中 Azure 儲存體容量。 <br/><br/> 如果您選取 [**單一資源群組**]，保留折扣會套用至所選訂用帳戶中的 Azure 儲存體容量，以及該訂用帳戶內選取的資源群組。 <br/><br/> 購買保留區之後，您可以變更保留範圍。  |
   |**訂用帳戶**  | 用來支付 Azure 儲存體保留的訂用帳戶。 所選訂用帳戶的付款條件會用於收費成本。 訂用帳戶必須是下列其中一種類型： <br/><br/>  Enterprise 合約（供應專案號碼： MS-AZR-0017P-Ms-azr-0017p 或 MS-AZR-0017P-Ms-azr-0148p）：若為企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 <br/><br/> 使用隨用隨付費率的個別訂用帳戶（供應專案號碼： MS-AZR-0017P-Ms-azr-0003p 或 MS-MS-AZR-0017P-Ms-azr-0023p）：對於使用隨用隨付費率的個別訂用帳戶，費用會依訂用帳戶的信用卡或發票付款方法計費。    |
   | **磁碟** | 您想要建立的 SKU。 |
   | **區域** | 保留作用中的區域。 |
   | **計費頻率** | 指出帳戶收取保留費用的頻率。 選項包括 [*每月*] 或 [*提前*]。 |

    ![premium-ssd-reserved-purchase-selection .png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 在您選取保留的參數之後，Azure 入口網站會顯示成本。 入口網站也會顯示 [隨用隨付計費] 的折扣百分比。 選取 **[下一步]** 以繼續前往 [**購買保留**] 分頁。

1. 在 [**購買保留**] 窗格中，您可以命名您的保留區，並選取您想要進行的保留總數量。 保留的數目會對應到磁片數目。 例如，如果您想要保留一百個磁片，則會將 [**數量**] 變更為100。
1. 檢查保留的總成本。

    ![premium-ssd-reserved-selecting-sku-total-purchase .png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

購買保留之後，它會自動套用至任何符合保留條款的現有 Azure 磁片儲存體資源。 如果您尚未建立任何 Azure 磁片儲存體資源，則會在您建立符合保留條款的資源時套用保留。 不論是哪一種情況，保留期都會在成功購買後立即開始。

## <a name="exchange-or-refund-a-reservation"></a>交換或退款保留

有某些限制，您可以交換或退款保留。

若要交換或退款保留，請流覽至 Azure 入口網站中的保留詳細資料。 選取 [ **Exchange] 或 [退款**]，並遵循指示提交支援要求。 處理要求之後，Microsoft 會傳送電子郵件給您，以確認要求完成。

如需 Azure 保留原則的詳細資訊，請參閱[Azure 保留的自助交換和退款](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

### <a name="exchange-a-reservation"></a>交換保留

交換保留可讓您根據保留的未使用部分，收到按比例計算的退款。 然後，您可以將退款套用至新 Azure 磁片保留的購買價格。
您可以無限次進行交換。 而且，交換不會產生任何費用。 您所購買的新保留專案，其值必須等於或大於原始保留區中按比例計算的信用額度。 Azure 磁片保留區只能針對另一個 Azure 磁片保留交換，而不能用於任何其他 Azure 服務的保留。

### <a name="refund-a-reservation"></a>退款保留

您可以隨時取消 Azure 磁片保留。 如果您取消，您會收到按比例計算的退款，並以剩餘的保留期限減去12% 的提前終止費用。 每年的退款上限為 $50000。
取消保留會立即終止保留區，並將剩餘的月份傳回給 Microsoft。 剩餘的按比例餘額減去費用，將會退款至您的原始購買形式。

## <a name="expiration-of-a-reservation"></a>保留期的到期日

當保留期限到期時，您在該保留下使用的任何 Azure 磁片容量都會依照隨用隨付費率計費。 保留項目不會自動續約。
您會在保留期限前30天收到電子郵件通知，並在到期日再次出現。 若要繼續利用保留所提供的節省成本，請在到期日之後續訂。

## <a name="need-help-contact-us"></a>需要協助嗎？ 連絡我們

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 保留項目？](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [瞭解保留折扣如何套用至 Azure 磁碟儲存體](../articles/cost-management-billing/reservations/understand-disk-reservations.md)