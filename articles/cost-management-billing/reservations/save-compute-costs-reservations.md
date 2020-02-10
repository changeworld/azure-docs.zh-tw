---
title: 什麼是 Azure 保留項目？
description: 了解 Azure Reservations 和定價，以節省虛擬機器、SQL 資料庫、Azure Cosmos DB 及其他資源的成本。
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 10cf5b5a0ec4edda3d3c868cda34352e827604b0
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76841875"
---
# <a name="what-are-azure-reservations"></a>什麼是 Azure 保留項目？

「Azure 保留」可協助您藉由承諾虛擬機器、Azure Blob 儲存體或 Azure Data Lake Storage Gen2、SQL Database 計算容量、Azure 磁碟儲存體、Azure Cosmos DB 輸送量或其他 Azure 資源的一年或三年期方案來節省成本。 承諾可讓您在所用的資源上取得折扣。 對於隨用隨付，Reservations 可以大幅減少 72% 的資源成本。 保留會提供計費折扣，且不會影響資源的執行階段狀態。

您可以預先或每月支付保留費用。 預付和每月付款的保留總費用是相同的，當您選擇按月支付時，您不需要支付任何額外費用。 每月付款僅適用於 Azure 保留，而不是第三方產品。

您可以在 [Azure 入口網站](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)購買保留。

## <a name="why-buy-a-reservation"></a>為何要購買保留？

如果您有使用大量容量或輸送量或者長期執行的虛擬機器、Blob 儲存體資料、Azure Cosmos DB 或 SQL 資料庫，則購買保留可提供給您最符合成本效益的選項。 例如，當您持續在未使用保留的情況下執行某個服務的四個執行個體時，必須以隨用隨付費率支付費用。 當您針對那些資源購買保留時，您會立即取得保留折扣。 那些資源將不再以隨用隨付費率計費。

## <a name="charges-covered-by-reservation"></a>保留所涵蓋的費用

服務方案：

- **保留的虛擬機器執行個體**：保留僅涵蓋虛擬機器計算成本。 它不會涵蓋其他軟體、網路或儲存體費用。
- **Azure 儲存體保留容量** - 保留涵蓋 Blob 儲存體或 Azure Data Lake Gen2 儲存體的標準儲存體帳戶儲存容量。 保留並未涵蓋頻寬或交易速率。
- **Azure 磁碟儲存體保留** - 保留僅涵蓋 P30 大小或更大的進階 SSD。 其中並不涵蓋任何其他磁碟類型或小於 P30 的大小。
- **Azure Cosmos DB 保留容量**：保留涵蓋針對資源所佈建的輸送量。 它未涵蓋儲存體和網路費用。
- **SQL Database 保留的 vCore**：保留僅包含計算成本。 授權為分開計費。
- **SQL 資料倉儲**：保留涵蓋 cDWU 使用量。 但未涵蓋與 SQL 資料倉儲使用量相關的儲存或網路費用。
- **App Service 戳記費用**：保留涵蓋戳記使用量。 但不會套用至背景工作角色，因此任何其他與戳記相關聯的資源也會分開收費。
- 適用於 MySQL 的 Azure 資料庫
- 適用於 PostgreSQL 的 Azure 資料庫
- 適用於 MariaDB 的 Azure 資料庫
- Azure 資料總管


針對 Windows 虛擬機器和 SQL 資料庫，您可以利用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 來涵蓋授權成本。

## <a name="whos-eligible-to-purchase-a-reservation"></a>誰有資格購買保留？

若要購買方案，您必須具備 Enterprise (MS-AZR-0017P 或 MS-AZR-0148P) 或隨用隨付訂用帳戶 (MS-AZR-0003P 或 MS-AZR-0023P) 或 Microsoft 客戶合約訂用帳戶的訂用帳戶擁有者角色。 雲端解決方案提供者可使用 Azure 入口網站或 [合作夥伴中心](/partner-center/azure-reservations) 來購買 Azure 保留。

Enterprise 合約 (EA) 客戶可藉由在 EA 入口網站中停用 [新增保留執行個體]  選項，以限制對 EA 管理員的購買。 EA 管理員必須是至少一個 EA 訂用帳戶的訂用帳戶擁有者，才能購買保留。 當企業想要讓中央小組購買不同成本中心的保留時，此選項非常有用。 購買之後，中央小組可以將成本中心擁有者新增至保留。 然後，擁有者可以將保留範圍設定為其訂用帳戶。 中央小組不需要擁有購買保留之處的訂用帳戶擁有者存取權。

保留折扣僅適用於與透過 Enterprise、雲端解決方案提供者 (CSP)、Microsoft 客戶合約購買的訂用帳戶相關聯的資源，以及採用隨用隨付費率的個別方案。

## <a name="scope-reservations"></a>設定保留範圍

您可以將保留範圍設定為訂用帳戶或資源群組。 設定保留範圍時會選取保留節省成本的適用之處。 當您將保留範圍設定為資源群組時，保留折扣僅適用於該資源群組，而不會適用於整個訂用帳戶。

### <a name="reservation-scoping-options"></a>保留範圍設定選項

設定資源群組範圍時，視您的需求而定，您有三個選項可設定保留範圍：

- **單一資源群組範圍** — 只會將保留折扣套用至所選資源群組中的相符資源。
- **單一訂用帳戶範圍** — 會將保留折扣套用至所選訂用帳戶中的相符資源。
- **共用範圍** — 會將保留折扣套用至計費內容中合格訂用帳戶的相符資源。 針對 Enterprise 合約客戶，計費內容為註冊。 針對 Microsoft 客戶合約客戶，計費範圍是帳單設定檔。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是帳戶管理員所建立的所有合格訂用帳戶。

在您的使用量上套用保留折扣時，Azure 會依照下列順序處理保留：

1. 範圍設定為資源群組的保留
2. 單一範圍保留
3. 共用範圍保留

單一資源群組可根據您設定保留範圍的方式，從多個保留取得保留折扣。

### <a name="scope-a-reservation-to-a-resource-group"></a>將保留範圍設定為資源群組

當您購買保留時，您可以將保留範圍設定為資源群組，或在購買後設定範圍。 您必須是訂用帳戶擁有者，才能將保留範圍設定為資源群組。

若要設定範圍，請移至 Azure 入口網站中的[購買保留](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand)頁面。 選取您想要購買的保留類型。 在 [選取您要購買的產品]  選取表單上，將 [範圍] 值變更為 [單一資源群組]。 然後選取一個資源群組。

![顯示 VM 保留購買選取項目的範例](./media/save-compute-costs-reservations/select-product-to-purchase.png)

系統會針對虛擬機器保留中的資源群組顯示購買建議。 建議是藉由分析過去30天的使用量來計算。 如果具有保留執行個體的資源執行成本比採用隨用隨付費率的資源執行成本更低，則會提出購買建議。 如需保留購買建議的詳細資訊，請參閱[根據使用模式取得保留執行個體購買建議](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations)。

您一律可以在購買保留之後變更範圍。 若要這麼做，請移至保留，按一下 [設定]  ，然後重新設定保留範圍。 重新設定保留範圍不算是商業交易。 您的保留期限不會變更。 如需有關更新範圍的詳細資訊，請參閱[在購買保留後更新範圍](manage-reserved-vm-instance.md#change-the-reservation-scope)。

![顯示保留範圍變更的範例](./media/save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>監視及最佳化保留使用量

您可使用多種方式來監視保留使用量 – 透過 Azure 入口網站、透過 API，或透過使用量資料。 若要查看您有權存取的所有保留，請移至 Azure 入口網站中的 [保留]  。 [保留] 方格會顯示保留的最後記錄使用率百分比。 按一下保留可查看保留的長期使用率。

如果您是 Enterprise 合約客戶或 Microsoft 客戶合約客戶，您也可使用 [API](reservation-apis.md#see-reservation-usage) 以及從[使用量資料](understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks)來取得保留使用率。

如果您注意到資源群組範圍的保留使用率很低，則可將保留範圍更新為單一訂用帳戶，或在計費內容中共用。 您也可以分割保留，並將產生的保留套用至不同的資源群組。

### <a name="other-considerations"></a>其他考量

如果您的資源群組中沒有相符的資源，則保留不會充分利用。 保留不會自動套用至低使用率的不同資源群組或訂用帳戶。

如果您將資源群組從一個訂用帳戶移到另一個，則保留範圍不會自動更新。 如果您刪除資源群組，則範圍不會更新。 您必須[重新設定保留範圍](manage-reserved-vm-instance.md#change-the-reservation-scope)。 否則，保留將不會充分利用。

## <a name="discounted-subscription-and-offer-types"></a>折扣的訂用帳戶和供應項目類型

保留折扣適用於下列合格訂用帳戶和供應項目類型。

- Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148 P)
- Microsoft 客戶合約訂用帳戶。
- 以隨用隨付費率計費的個別方案 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)
- CSP 訂用帳戶

在具有其他優惠類型的訂用帳戶中執行的資源，將無法收到保留折扣。

## <a name="how-is-a-reservation-billed"></a>保留的計費方式為何？

保留會計費至與訂用帳戶繫結的付款方式。 保留成本是從您的預付餘額扣除 (如果有的話)。 當您的預付餘額未涵蓋保留的成本時，您會支付超額部分。 如果您有來自個別方案的訂用帳戶採用隨用隨付費率，您的帳戶所擁有的信用卡會立即被收取預付費用。 每月付款會出現在您的發票上，且您的信用卡會每月扣款。 當您透過發票計費時，您會在下一張發票上看到費用。

## <a name="how-reservation-discount-is-applied"></a>保留折扣的套用方式

保留折扣會套用至符合您於購買保留時所選屬性的資源使用量。 屬性包含相符的 VM、SQL 資料庫、Azure Cosmos DB 或其他資源的執行範圍。 例如，如果您想要針對美國西部區域的四個標準 D2 虛擬機器取得保留折扣，則選取這些 VM 執行所在的訂用帳戶。

保留折扣採「不用則作廢」  的原則。 如果您有任何一小時沒有相符的資源，就會失去該小時的保留數量。 您無法遞轉未使用的保留時數。

當您關閉資源時，保留折扣會自動套用至指定範圍中另一個相符的資源。 如果在指定的範圍內找不到相符的資源，則會「失去」  保留時數。

例如，您稍後可能會建立資源，且具有未充分利用的相符保留。 保留折扣會自動套用至新的相符資源。

如果虛擬機器是在註冊/帳戶內的不同訂用帳戶中執行，則選取共用的範圍。 共用的範圍可讓保留折扣套用到整個訂用帳戶。 您可以在購買保留之後變更範圍。 如需詳細資訊，請參閱[管理 Azure 保留](manage-reserved-vm-instance.md)。

保留折扣僅適用於與 Enterprise、Microsoft 客戶合約、CSP 或採用隨用隨付費率的訂用帳戶相關聯的資源。 在具有其他優惠類型的訂用帳戶中執行的資源，將無法收到保留折扣。

## <a name="when-the-reservation-term-expires"></a>當保留期限到期時

在保留期限結束時，計費折扣會到期，隨後資源便會按照預付型方案的費率來計費。 根據預設，保留不會設定為自動更新。 您可以選擇 [更新] 設定中的選項，以啟用保留的自動更新。 使用自動更新時，將會在現有保留期限到期時購買取代保留。 根據預設，取代保留的屬性與到期保留相同，可選擇性地變更「更新」設定中的計費頻率、期間或數量。 任何擁有保留存取權的使用者，以及用來計費的訂用帳戶，都可以設定更新。  

## <a name="discount-applies-to-different-sizes"></a>折扣會套用於不同的大小

當您購買保留時，折扣可適用於屬性在相同大小群組內的其他執行個體。 這項功能稱為執行個體大小彈性。 折扣涵蓋範圍的彈性取決於您購買保留時所挑選的保留類型和屬性。

服務方案：

- 保留的 VM 執行個體：當您購買保留並選取 [針對執行個體大小彈性最佳化]  時，折扣涵蓋範圍會取決於您選取的 VM 大小。 此保留可適用於相同大小系列群組中的虛擬機器 (VM) 大小。 如需詳細資訊，請參閱[利用保留的 VM 執行個體獲得虛擬機器大小彈性](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。
- Azure 儲存體保留容量：您可以為標準 Azure 儲存體帳戶購買保留容量 (以每個月 100 TiB 或 1 PiB 為單位)。 Azure 儲存體保留容量適用於所有區域中的任何存取層 (經常性、非經常性或封存) 和任何複寫選項 (LRS、GRS 或 ZRS)。
- SQL Database 保留容量：折扣涵蓋範圍取決於您挑選的效能層級。 如需詳細資訊，請參閱[了解如何套用 Azure 保留折扣](understand-reservation-charges.md)。
- Azure Cosmos DB 保留容量：折扣涵蓋範圍取決於所佈建的輸送量。 如需詳細資訊，請參閱[了解如何套用 Azure Cosmos DB 保留折扣](understand-cosmosdb-reservation-charges.md)。

## <a name="reservation-notifications"></a>保留通知

根據您支付 Azure 訂用帳戶的方式，我們會透過電子郵件將保留通知傳送給貴組織中的下列使用者。 系統會針對各種事件傳送通知，包括：

- 購買
- 即將到期的保留期限
- Expiry
- 續約
- 取消
- 範圍變更

針對具有 EA 訂用帳戶的客戶：
- 購買通知會傳送給購買者和 EA 通知連絡人。
- 其他保留生命週期通知只會傳送給 EA 通知連絡人。
- 使用 RBAC (IAM) 權限新增至保留的使用者，不會收到任何電子郵件通知。

針對具有個別訂用帳戶的客戶：
- 購買者會收到購買通知。
- 在購買時，訂用帳戶計費帳戶擁有者會收到購買通知。
- 帳戶擁有者會收到所有其他通知。


## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 使用下列文章來深入了解 Azure 保留項目：
    - [管理 Azure 保留項目](manage-reserved-vm-instance.md)
    - [了解採用隨用隨付費率的訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](reserved-instance-windows-software-costs.md)
    - [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)

- 深入了解服務方案的保留：
    - [具有 Azure 保留 VM 執行個體的虛擬機器](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [具有 Azure Cosmos DB 保留容量的 Azure Cosmos DB 資源](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [具有 Azure SQL Database 保留容量的 SQL Database 計算資源](../../sql-database/sql-database-reserved-capacity.md) 深入了解軟體方案的保留：
    - [Azure 保留中的 Red Hat 軟體方案](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure 保留中的 SUSE 軟體方案](../../virtual-machines/linux/prepay-suse-software-charges.md)
