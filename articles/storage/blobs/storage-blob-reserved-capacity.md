---
title: 使用保留容量將 Blob 儲存體的成本優化-Azure 儲存體
description: 瞭解如何購買 Azure 儲存體保留容量，以節省區塊 blob 和 Azure Data Lake Storage Gen2 資源的成本。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/01/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c06bbc412a51fc919b862aeb3f62ec58feec89cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84259196"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>使用保留容量將 Blob 儲存體的成本最佳化

您可以節省具有 Azure 儲存體保留容量之 blob 資料的儲存體成本費用。 當您認可一年或三年的保留時，Azure 儲存體保留容量可提供區塊 blob 容量的折扣以及標準儲存體帳戶中 Azure Data Lake Storage Gen2 資料的折扣。 保留會針對保留期限提供固定的儲存容量量。

Azure 儲存體保留容量可以大幅降低區塊 blob 和 Azure Data Lake Storage Gen2 資料的容量成本。 所達到的節省成本取決於您的保留持續時間、選擇要保留的總容量，以及您為儲存體帳戶選擇的儲存體帳戶的存取層和類型。 保留容量會提供計費折扣，且不會影響 Azure 儲存體資源的狀態。

如需 Azure 儲存體保留定價的詳細資訊，請參閱[區塊 Blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)和 [Azure Data Lake Storage Gen 2 定價](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

## <a name="reservation-terms-for-azure-storage"></a>Azure 儲存體的保留條款

下列各節說明 Azure 儲存體保留的條款。

### <a name="reservation-capacity"></a>保留容量

您可以使用 100 TB 的單位來購買 Azure 儲存體保留容量，並在一年或三年期內每月 1 PB。

### <a name="reservation-scope"></a>保留範圍

Azure 儲存體保留容量適用于單一訂用帳戶或多個訂用帳戶 (共用範圍) 。 當範圍設定為單一訂用帳戶時，保留折扣僅適用于選取的訂用帳戶。 當範圍設定為多個訂用帳戶時，保留折扣會在客戶計費內容中的所有訂用帳戶之間共用。

當您購買 Azure 儲存體保留容量時，您可以針對區塊 blob 和 Azure Data Lake Storage Gen2 資料使用您的保留。 保留會套用至您在購買範圍內的使用量，且不能限制為訂用帳戶內的特定儲存體帳戶、容器或物件。

Azure 儲存體保留僅涵蓋儲存在訂用帳戶或共用資源群組中的資料量。 保留中不包含提早刪除、作業、頻寬和資料傳輸費用。 當您購買保留專案時，符合保留屬性的容量費用會以折扣費率計費，而不是以隨用隨付費率計費。 如需 Azure 保留的詳細資訊，請參閱 [什麼是 Azure 保留專案？](/azure/billing/billing-save-compute-costs-reservations)。

### <a name="supported-account-types-tiers-and-redundancy-options"></a>支援的帳戶類型、層級和冗余選項

Azure 儲存體保留容量適用于標準儲存體帳戶中的資源，包括一般用途 v2 (GPv2) 與 Blob 儲存體帳戶。

所有存取層 (經常性存取、非經常性存取和封存) 都支援保留。 如需存取層的詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取、非經常性存取層和封存存取層](storage-blob-storage-tiers.md)。

所有類型的冗余都支援保留。 如需有關冗余選項的詳細資訊，請參閱 [Azure 儲存體冗余](../common/storage-redundancy.md)。

> [!NOTE]
> Azure 儲存體保留容量不適用於 premium 儲存體帳戶、一般用途 v1 (GPv1) 儲存體帳戶、Azure Data Lake Storage Gen1、分頁 blob、Azure 佇列儲存體、Azure 資料表儲存體或 Azure 檔案儲存體。  

### <a name="security-requirements-for-purchase"></a>購買的安全性需求

若要購買保留容量：

- 您必須是至少一個具有隨用隨付費率的企業或個別訂用帳戶的 **擁有** 者角色。
- 針對企業訂用帳戶，必須在 EA 入口網站中啟用 [ **新增保留實例** ]。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
- 針對雲端解決方案提供者 (CSP) 方案，只有系統管理員代理程式或銷售專員可以購買 Azure Blob 儲存體保留容量。

## <a name="determine-required-capacity-before-purchase"></a>購買前請先判斷所需的容量

當您購買 Azure 儲存體保留時，您必須選擇保留的區域、存取層和冗余選項。 您的保留僅適用于儲存在該區域、存取層和冗余層級的資料。 例如，假設您在美國西部為美國西部的資料購買保留，但使用區域冗余儲存體 (ZRS) 。 您無法將相同的保留用於美國東部的資料、封存層中的資料，或異地複寫儲存體 (GRS) 中的資料。 不過，您可以購買另一個保留區來滿足您的其他需求。  

保留目前可用於 100 TB 或 1 PB 區塊，1 PB 區塊的折扣較高。 當您在 Azure 入口網站中購買保留時，Microsoft 可能會根據您先前的使用量提供建議，以協助您判斷應該購買的保留。

## <a name="purchase-azure-storage-reserved-capacity"></a>購買 Azure 儲存體保留容量

您可以透過 [Azure 入口網站](https://portal.azure.com)購買 Azure 儲存體的保留容量。 保留的付款方式可為預先付款或每月付款。 如需購買每月付款的詳細資訊，請參閱 [購買預付或每月付款的 Azure 保留](/azure/billing/billing-monthly-payments-reservations)。

如需找出適合您案例的保留條款的協助，請參閱 [瞭解 Azure 儲存體保留容量折扣](../../cost-management-billing/reservations/understand-storage-charges.md)。

遵循下列步驟來購買保留容量：

1. 流覽至 Azure 入口網站中的 [ [購買保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) ] 窗格。  
1. 選取 **Azure Blob 儲存體** 以購買新的保留。  
1. 請填寫必填欄位，如下表中所述：

    ![顯示如何購買保留容量的螢幕擷取畫面](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |欄位  |說明  |
   |---------|---------|
   |**範圍**   |  指出有多少訂用帳戶可以使用與保留相關的計費權益。 它也會控制保留容量套用至特定訂用帳戶的方式。 <br/><br/> 如果您選取 [ **共用**]，保留折扣會套用至計費內容內任何訂用帳戶中 Azure 儲存體的容量。 計費內容取是以您註冊 Azure 的方式為基礎。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對隨用隨付的客戶，共用範圍包含所有個別訂用帳戶，並具有帳戶管理員所建立的隨用隨付費率。  <br/><br/>  如果您選取 **單一訂**用帳戶，保留折扣會套用至所選訂用帳戶中 Azure 儲存體的容量。 <br/><br/> 如果您選取 [ **單一資源群組**]，保留折扣會套用至所選訂用帳戶中的 Azure 儲存體容量，以及該訂用帳戶內選取的資源群組。 <br/><br/> 您可以在購買保留之後變更保留範圍。  |
   |**訂用帳戶**  | 用來支付 Azure 儲存體保留的訂用帳戶。 所選訂用帳戶上的付款方法會用來收費成本。 訂用帳戶必須是下列其中一種類型： <br/><br/>  Enterprise 合約 (供應專案號碼： MS-AZR-0003P->ms-azr-0017p 或 MS-AZR-0003P-Ms-azr-0148p) ：針對 Enterprise 訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或以超額部分收費。 <br/><br/> 採用隨用隨付費率的個別訂用帳戶 (供應專案號碼： MS-AZR-0003P-Ms-azr-0003p 或 MS-AZR-0003P-Ms-azr-0023p) ：對於採用隨用隨付費率的個別訂用帳戶，費用會以訂用帳戶的信用卡或發票付款方法計費。    |
   | **區域** | 保留生效的區域。 |
   | **存取層** | 保留生效的存取層。 選項包括 *經常性*存取 *、非*經常性 *存取或封存*。 如需存取層的詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取、非經常性存取層和封存存取層](storage-blob-storage-tiers.md)。 |
   | **備援性** | 保留的重複選項。 選項包括 *LRS*、 *ZRS*、 *GRS*、 *GZRS*、 *ra-GRS*和 *RA-GZRS*。 如需有關冗余選項的詳細資訊，請參閱 [Azure 儲存體冗余](../common/storage-redundancy.md)。 |
   | **計費頻率** | 指出帳戶支付保留的頻率。 選項包括 *每月* 或 *提前*。 |
   | **大小** | 保留生效的區域。 |
   |**字詞**  | 一年或三年。   |

1. 選取保留的參數之後，Azure 入口網站會顯示成本。 入口網站也會顯示隨用隨付計費的折扣百分比。

1. 在 [ **購買保留** ] 窗格中，檢查保留的總成本。 您也可以提供保留的名稱。

    ![顯示如何購買保留的螢幕擷取畫面](media/storage-blob-reserved-capacity/purchase-reservations.png)

購買保留之後，它會自動套用到任何現有的 Azure 儲存體區塊 blob 或符合保留條款的 Azure Data Lake Storage Gen2 資源。 如果您尚未建立任何 Azure 儲存體資源，則會在您建立符合保留條款的資源時套用保留。 在任何一種情況下，保留期限會在成功購買之後立即開始。

## <a name="exchange-or-refund-a-reservation"></a>交換或退款保留

您可以交換或退款保留，但有某些限制。 下列各節將說明這些限制。

若要交換或退款保留，請流覽至 Azure 入口網站中的保留詳細資料。 選取 [ **交換** ] 或 [ **退款**]，然後依照指示提交支援要求。 處理要求後，Microsoft 會傳送電子郵件給您，以確認要求是否完成。

如需 Azure 保留原則的詳細資訊，請參閱 [Azure 保留的自助式交換和退款](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

### <a name="exchange-a-reservation"></a>交換保留

交換保留可讓您根據保留的未使用部分，收到按比例計算的退款。 然後，您可以將退款套用至新 Azure 儲存體保留的購買價格。

您可以進行的交換數目沒有任何限制。 此外，也不會有與 exchange 相關聯的費用。 您購買的新保留專案必須等於或大於原始保留的比例值。 Azure 儲存體保留只能針對另一個 Azure 儲存體保留進行交換，而不能針對任何其他 Azure 服務的保留進行交換。

### <a name="refund-a-reservation"></a>退款保留

您可以隨時取消 Azure 儲存體保留。 當您取消時，您將會根據保留的剩餘期限（減去12% 的提前終止費用）來收取按比例計算的退款。 每年的退款上限為 $50000。

取消保留會立即終止保留，並將剩餘的月份傳回給 Microsoft。 剩餘的比例餘額減去費用，將會退款給您的原始購買形式。

## <a name="expiration-of-a-reservation"></a>保留的到期日

保留到期時，您在該保留下使用的任何 Azure 儲存體容量都會以隨用隨付費率計費。 保留不會自動更新。

您將會在保留到期前30天收到電子郵件通知，並在到期日出現。 若要繼續利用保留所提供的節省成本，請在到期日之後續訂。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 保留項目？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解保留折扣如何套用至 Azure 儲存體](../../cost-management-billing/reservations/understand-storage-charges.md)
