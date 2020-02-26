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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590753"
---
使用保留容量節省 Azure 磁碟儲存體的使用量。 Azure 磁碟儲存體保留與 Azure 保留的虛擬機器執行個體結合，可讓您降低虛擬機器（VM）的總成本。 保留折扣會自動套用至所選保留範圍中的相符磁片。 由於此自動應用程式的原因，您不需要將保留指派給受控磁片來取得折扣。

根據磁片使用量，會每小時套用一次折扣。 未使用的保留容量不會執行。 Azure 磁碟儲存體保留折扣不適用於非受控磁片、ultra 磁片或分頁 blob 耗用量。

## <a name="determine-your-storage-needs"></a>判斷您的儲存體需求

購買保留之前，請先決定您的儲存體需求。 目前，Azure 磁碟儲存體保留僅適用于選取的 Azure premium SSD Sku。 Premium SSD 的 SKU 會決定磁片的大小和效能。

在判斷您的儲存體需求時，請不要只以容量為基礎來考慮磁片。 例如，您不能保留 P40 磁片，並使用它來支付兩個較小的 P30 磁片。 購買保留時，您只會針對每個 SKU 的磁片總數購買保留。

磁片保留是根據磁片 SKU 來進行。 因此，保留耗用量是以磁片 Sku 的單位為基礎，而不是所提供的大小。

例如，假設您保留一個 P40 磁片，其中有2個 TiB 的已布建儲存體容量。 也假設您只配置兩個 P30 磁片。 在此情況下，P40 保留區不會考慮 P30 的耗用量，而且您必須支付 P30 磁片的隨用隨付費率。
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>購買考量

考慮購買磁片保留時，建議您採用下列做法：

- 分析您的使用量資訊，以協助判斷您應該購買的保留。 請確定您追蹤的是磁片 Sku 中的使用量，而不是已布建或已使用的磁片容量。
- 檢查您的磁片保留，以及您的 VM 保留專案。 我們強烈建議您為 VM 使用量和磁片使用量進行保留，以提供最大的節約。 您可以從判斷正確的 VM 保留開始，然後評估磁片保留。 一般來說，您會有每個工作負載的標準設定。 例如，SQL Server 的伺服器可能有兩個 P40 資料磁片和一個 P30 作業系統磁片。
  
  這種模式可協助您判斷可能購買的保留數量。 這種方法可以簡化評估程式，並確保您的 VM 和磁片都有對齊的計畫。 此方案包含訂用帳戶或區域等考慮。

## <a name="purchase-restrictions"></a>購買限制

保留折扣目前無法供下列專案使用：

- 非受控磁片或分頁 blob。
- 標準 Ssd 或標準硬碟（Hdd）。
- 進階 SSD 的 Sku 小於 P30： P1、P2、P3、P4、P6、P10、P15 和 P20 SSD Sku。
- Azure Government、Azure 德國或 Azure 中國區域中的磁片。

在罕見的情況下，Azure 會因為區域中的低容量，而將新的保留購買範圍限制為磁片 Sku 的子集。

## <a name="buy-a-disk-reservation"></a>購買磁片保留

您可以透過[Azure 入口網站](https://portal.azure.com/)購買 Azure 磁碟儲存體保留。 您可以事先或按月付款來支付保留費用。 如需以每月付款購買的詳細資訊，請參閱[每月付款的購買保留](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)。

請遵循下列步驟來購買保留容量：

1. 移至 Azure 入口網站中的 [[購買保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)] 窗格。

1. 選取 [ **Azure 受控磁碟**] 以購買保留。

    ![購買保留的窗格](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. 指定下表所述的必要值：

   |元素  |描述  |
   |---------|---------|
   |**範圍**   |  有多少訂用帳戶可以使用與保留相關的計費權益。 這個值也會指定將保留套用至特定訂用帳戶的方式。 <br/><br/> 如果您選取 [**共用**]，保留折扣會套用至計費內容內每個訂用帳戶中 Azure 儲存體容量。 計費內容取是以您註冊 Azure 的方式為基礎。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 對於隨用隨付客戶，共用範圍包含帳戶管理員所建立之隨用隨付費率的所有個別訂閱。  <br/><br/>  如果您選取 [**單一訂**用帳戶]，保留折扣會套用至所選訂用帳戶中 Azure 儲存體容量。 <br/><br/> 如果您選取 [**單一資源群組**]，保留折扣會套用至所選訂用帳戶中的 Azure 儲存體容量，以及該訂用帳戶的選取資源群組。 <br/><br/> 購買保留區之後，您可以變更保留範圍。  |
   |**訂用帳戶**  | 您用來支付 Azure 儲存體保留費用的訂用帳戶。 所選訂用帳戶的付款條件會用於收費成本。 訂用帳戶必須是下列其中一種類型：<br/><ul><li> Enterprise 合約（供應專案號碼 MS-AZR-0017P-Ms-azr-0017p 和 MS-MS-AZR-0017P-Ms-azr-0148p）。 針對 Enterprise 訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。</li><br/><li>使用隨用隨付費率的個別訂用帳戶（供應專案號碼 MS-AZR-0017P-Ms-azr-0003p 和 MS-MS-AZR-0017P-Ms-azr-0023p）。 針對使用隨用隨付費率的個別訂用帳戶，費用會依訂用帳戶的信用卡或發票付款方法計費。</li></ul>    |
   | **磁碟** | 您想要建立的 SKU。 |
   | **區域** | 保留作用中的區域。 |
   | **計費頻率** | 帳戶收取保留費用的頻率。 選項包括 [**每月**] 和 [**提前**]。 |

    ![選取您想要購買之產品的窗格。 png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 在您指定保留的值之後，Azure 入口網站會顯示成本。 入口網站也會顯示 [隨用隨付計費] 的折扣百分比。 選取 **[下一步**] 以繼續前往 [**購買保留**] 窗格。

1. 在 [**購買保留**] 窗格中，您可以命名您的保留區，並選取您想要進行的保留總數量。 保留的數目會對應到磁片數目。 例如，如果您想要保留一百個磁片，請輸入**Quantity**值**100**。

1. 檢查保留的總成本。

    ![[購買保留] 窗格](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

購買保留專案之後，它會自動套用至任何符合保留條款的現有磁碟儲存體資源。 如果您尚未建立任何磁碟儲存體資源，則會在您建立符合保留條款的資源時套用保留。 不論是哪一種情況，保留期限都會在成功購買後立即開始。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退費

在某些限制下，您可以取消、交換或退款保留。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退費](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)。

## <a name="expiration-of-a-reservation"></a>保留期的到期日

當保留期限到期時，您在該保留下使用的任何 Azure 磁碟儲存體容量都會依照隨用隨付費率計費。 保留不會自動更新。

您會在保留期限到期前30天收到電子郵件通知，並在到期日再次出現。 若要繼續利用保留所提供的節省成本，請在到期日之後續訂。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 保留項目？](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [瞭解保留折扣如何套用至 Azure 磁碟儲存體](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
