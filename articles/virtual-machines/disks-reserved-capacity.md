---
title: 使用保留優化 Azure 磁碟儲存體的成本
description: 瞭解如何購買 Azure 磁碟儲存體保留區，以節省 premium SSD 受控磁片的成本。
author: roygara
ms.author: rogarana
ms.date: 01/30/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: db6575894904e6ced2d4be48fec5961f5b8b8a54
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602640"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>使用 Azure 磁碟保留來節省成本

使用保留容量來節省您的 Azure 磁碟儲存體使用量。 Azure 磁碟儲存體保留結合 Azure 保留的虛擬機器執行個體可讓您降低虛擬機器的總 (VM) 成本。 保留折扣會自動套用至所選保留範圍內的相符磁片。 因為此自動應用程式的原因，您不需要將保留指派給受控磁片來取得折扣。

每小時會根據磁片使用量套用折扣。 未使用的保留容量不會執行。 Azure 磁碟儲存體保留折扣不適用於非受控磁片、ultra 磁片或分頁 blob 耗用量。

## <a name="determine-your-storage-needs"></a>判斷您的儲存體需求

購買保留之前，請先判斷您的儲存體需求。 目前，Azure 磁碟儲存體保留僅適用于選取的 Azure premium SSD Sku。 Premium SSD 的 SKU 決定磁片的大小和效能。

判斷您的儲存體需求時，請不要將磁片視為只以容量為基礎。 例如，您不能有 P40 磁片的保留，並使用它來支付兩個較小的 P30 磁片。 購買保留時，您只會針對每個 SKU 購買磁片總數的保留。

磁片保留是針對每個磁片 SKU 進行。 因此，保留耗用量是根據磁片 Sku 的單位，而不是所提供的大小。

例如，假設您保留一個 P40 磁片，其中有2個 TiB 的已布建儲存體容量。 也假設您只配置兩個 P30 磁片。 在該情況下，P40 保留不會考慮 P30 耗用量，而是以隨用隨付費率支付 P30 磁片的費用。
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>購買考量

考慮購買磁片時，建議您採用下列做法：

- 分析您的使用量資訊，以協助判斷您應該購買的保留。 請務必追蹤磁片 Sku 中的使用量，而不是已布建或使用的磁片容量。
- 檢查您的磁片保留區和 VM 保留區。 我們強烈建議您為 VM 使用量和磁片使用量進行保留，以節省最多費用。 您可以從判斷正確的 VM 保留開始，然後評估磁片保留區。 一般來說，您的每個工作負載都有一個標準設定。 例如，SQL Server 服務器可能有兩個 P40 資料磁片和一個 P30 作業系統磁片。
  
  這種模式可協助您判斷您可能購買的保留量。 這種方法可以簡化評估程式，並確保您的 VM 和磁片都有對齊的方案。 此方案包含訂用帳戶或區域等考慮。

## <a name="purchase-restrictions"></a>購買限制

下列專案目前無法使用保留折扣：

- 非受控磁片或分頁 blob。
- 標準 Ssd 或標準硬碟磁片磁碟機 (Hdd) 。
- 進階 SSD 的 Sku 小於 P30： P1、P2、P3、P4、P6、P10、P15 和 P20 SSD Sku。
- Azure Government、Azure 德國或 Azure 中國區域中的磁片。

在罕見的情況下，因為區域中的容量較低，Azure 會將新保留的購買限制為磁片 Sku 的子集。

## <a name="buy-a-disk-reservation"></a>購買磁片保留

您可以透過 [Azure 入口網站](https://portal.azure.com/)購買 Azure 磁碟儲存體的保留。 您可以預先支付或使用每月付款來支付保留費用。 如需購買每月付款的詳細資訊，請參閱 [使用每月付款來購買保留](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments)。

遵循下列步驟來購買保留容量：

1. 移至 Azure 入口網站中的 [ [購買保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) ] 窗格。

1. 選取 **Azure 受控磁碟** 來購買保留。

    ![購買保留的窗格](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. 指定下表所述的必要值：

   |元素  |描述  |
   |---------|---------|
   |**範圍**   |  有多少個訂用帳戶可以使用與保留相關的計費權益。 此值也會指定保留如何套用至特定訂用帳戶。 <br/><br/> 如果您選取 [ **共用**]，保留折扣會套用至計費內容內每個訂用帳戶中 Azure 儲存體的容量。 計費內容取是以您註冊 Azure 的方式為基礎。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付的客戶，共用範圍包含所有個別訂用帳戶，並具有帳戶管理員所建立的隨用隨付費率。  <br/><br/>  如果您選取 **單一訂** 用帳戶，保留折扣會套用至所選訂用帳戶中 Azure 儲存體的容量。 <br/><br/> 如果您選取 [ **單一資源群組**]，保留折扣會套用至所選訂用帳戶中的 Azure 儲存體容量，以及該訂用帳戶的所選資源群組中。 <br/><br/> 您可以在購買保留之後變更保留範圍。  |
   |**訂用帳戶**  | 您用來支付 Azure 儲存體保留的訂用帳戶。 所選訂用帳戶上的付款方法會用來收費成本。 訂用帳戶必須是下列其中一種類型：<br/><ul><li> Enterprise 合約 (供應專案號碼 MS-AZR-0003P->ms-azr-0017p 和 MS MS-AZR-0003P-Ms-azr-0148p) 。 若為企業訂用帳戶，費用會從註冊的 Azure 預付款扣除 (先前稱為預付金) 餘額或收取超額部分的費用。</li><br/><li>採用隨用隨付費率的個別訂用帳戶 (供應專案號碼 MS-AZR-0003P-Ms-azr-0003p 和 MS MS-AZR-0003P Ms-azr-0023p) 。 針對具有隨用隨付費率的個別訂用帳戶，費用會以訂用帳戶的信用卡或發票付款方法計費。</li></ul>    |
   | **磁碟** | 您要建立的 SKU。 |
   | **區域** | 保留生效的區域。 |
   | **計費頻率** | 帳戶支付保留的頻率。 選項包括 **每月****和預付**。 |

    ![選取您要 purchase.png 之產品的窗格](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 指定保留的值之後，Azure 入口網站會顯示成本。 入口網站也會顯示隨用隨付計費的折扣百分比。 選取 **[下一步** ] 以繼續前往 [ **購買保留** ] 窗格。

1. 在 [ **購買保留** ] 窗格中，您可以為您的保留命名，然後選取您想要進行的總保留數量。 保留數目對應至磁片數目。 例如，如果您想要保留一百個磁片，請輸入 **Quantity** 值 **100**。

1. 檢查保留的總成本。

    ![[購買保留] 窗格](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

購買保留之後，它會自動套用至符合保留條款的任何現有磁碟儲存體資源。 如果您尚未建立任何磁碟儲存體資源，則會在您建立符合保留條款的資源時套用保留。 在任一種情況下，保留期限會在成功購買之後立即開始。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

您可以在某些限制內取消、交換或退款保留。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="expiration-of-a-reservation"></a>保留的到期日

保留到期時，您在該保留下使用的任何 Azure 磁碟儲存體容量都會依隨用隨付費率計費。 保留不會自動更新。

您將會在保留到期前30天收到電子郵件通知，並于到期日再次收到通知。 若要繼續利用保留所提供的節省成本，請在到期日之後續訂。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 保留項目？](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [瞭解如何將您的保留折扣套用至 Azure 磁碟儲存體](../cost-management-billing/reservations/understand-disk-reservations.md)