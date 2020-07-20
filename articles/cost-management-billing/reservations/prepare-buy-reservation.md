---
title: 購買 Azure 保留
description: 了解相關要點可協助您購買 Azure 保留。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: banders
ms.openlocfilehash: 609ca56419ed14f3cf4e449e9b906a75a4ff20ab
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85807776"
---
# <a name="buy-a-reservation"></a>購買保留項目

Azure 保留可協助您藉由承諾多項 Azure 資源一年或三年期的方案來節省成本。 在您輸入承諾用量以購買保留之前，請務必先參閱下列各節，以做好購買的準備。

## <a name="who-can-buy-a-reservation"></a>誰可以購買保留

若要購買方案，您必須具備 Enterprise (MS-AZR-0017P 或 MS-AZR-0148P) 或隨用隨付訂用帳戶 (MS-AZR-0003P 或 MS-AZR-0023P) 或 Microsoft 客戶合約訂用帳戶的訂用帳戶擁有者角色。 雲端解決方案提供者可使用 Azure 入口網站或 [合作夥伴中心](/partner-center/azure-reservations) 來購買 Azure 保留。

Enterprise 合約 (EA) 客戶可藉由在 EA 入口網站中停用 [新增保留執行個體] 選項，以限制對 EA 管理員的購買。 EA 管理員必須是至少一個 EA 訂用帳戶的訂用帳戶擁有者，才能購買保留。 當企業想要讓中央小組購買不同成本中心的保留時，此選項非常有用。 購買之後，中央小組可以將成本中心擁有者新增至保留。 然後，擁有者可以將保留範圍設定為其訂用帳戶。 中央小組不需要擁有購買保留之處的訂用帳戶擁有者存取權。

保留折扣僅適用於與透過 Enterprise、雲端解決方案提供者 (CSP)、Microsoft 客戶合約購買的訂用帳戶相關聯的資源，以及採用隨用隨付費率的個別方案。

## <a name="scope-reservations"></a>設定保留範圍

您可以將保留範圍設定為訂用帳戶或資源群組。 設定保留範圍時會選取保留節省成本的適用之處。 當您將保留範圍設定為資源群組時，保留折扣僅適用於該資源群組，而不會適用於整個訂用帳戶。

### <a name="reservation-scoping-options"></a>保留範圍設定選項

視您的需求而定，您有三個選項可設定保留範圍：

- **單一資源群組範圍** — 只會將保留折扣套用至所選資源群組中的相符資源。
- **單一訂用帳戶範圍** — 會將保留折扣套用至所選訂用帳戶中的相符資源。
- **共用範圍** — 會將保留折扣套用至計費內容中合格訂用帳戶的相符資源。 針對 Enterprise 合約客戶，計費內容為註冊。 針對 Microsoft 客戶合約客戶，計費範圍是帳單設定檔。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是帳戶管理員所建立的所有合格訂用帳戶。

在您的使用量上套用保留折扣時，Azure 會依照下列順序處理保留：

1. 範圍設定為資源群組的保留
2. 單一範圍保留
3. 共用範圍保留

您一律可以在購買保留之後變更範圍。 若要這麼做，請移至保留，按一下 [設定]，然後重新設定保留範圍。 重新設定保留範圍不算是商業交易。 您的保留期限不會變更。 如需有關更新範圍的詳細資訊，請參閱[在購買保留後更新範圍](manage-reserved-vm-instance.md#change-the-reservation-scope)。

![顯示保留範圍變更的範例](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>折扣的訂用帳戶和供應項目類型

保留折扣適用於下列合格訂用帳戶和供應項目類型。

- Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148 P)
- Microsoft 客戶合約訂用帳戶。
- 以隨用隨付費率計費的個別方案 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)
- CSP 訂用帳戶

在具有其他優惠類型的訂用帳戶中執行的資源，將無法收到保留折扣。

## <a name="purchase-reservations"></a>購買保留

您可以從 Azure 入口網站、API、PowerShell、CLI 來購買保留。 當您準備好要購買保留時，請閱讀下列適用的文章：

- [App Service](prepay-app-service-isolated-stamp.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [資料總管](/azure/data-explorer/pricing-reserved-capacity)
- [磁碟儲存體](../../virtual-machines/linux/disks-reserved-capacity.md)
- [專用主機](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [軟體方案](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [適用於 PostgreSQL 的 Azure 資料庫](../../postgresql/concept-reserved-pricing.md)
- [適用於 MySQL 的 Azure 資料庫](../../mysql/concept-reserved-pricing.md)
- [適用於 MariaDB 的 Azure 資料庫](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [虛擬機器](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>購買或每月付款的 Azure 保留

您可以透過每月付款的方式購買 Azure 保留。 不同於需支付全額的預先購買，每月付款選項會將保留的總費用平均分配至期限中的每一個月份。 預付和每月付款的保留總費用是相同的，當您選擇按月支付時，您不需要支付任何額外費用。

如果是使用 Microsoft 客戶合約 (MCA) 購買保留，則您的月付金額可能會依當地貨幣的當月市場匯率而異。

每月付款不適用於：Databricks、SUSE Linux 保留、Red Hat 方案和 Azure Red Hat OpenShift 授權。

### <a name="view-payments-made"></a>檢視已支付的款項

您可以使用 API、使用量資料和成本分析來檢視已支付的款項。 針對每月付款的保留，使用量資料和保留費用 API 中的頻率值會顯示為**定期**。 針對預先付款的保留，此值會顯示為**一次性**。

成本分析會在預設檢視中顯示每月的購買。 在 [費用類型] 上套用 [購買]，以及在 [頻率] 上套用 [定期] 來進行篩選，即可查看所有購買。 若只要檢視保留，請篩選 [保留]。

![在成本分析中顯示保留購買費用的範例](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>交換和退款

就像其他保留一樣，您可以針對以每月計費購買的保留進行退款或交換。 

當您交換每月付款的保留時，新購買項目的總存留期費用應大於退回保留而取消的剩餘款項。 交換並不沒有其他限制或費用。 您可以交換預先付款的保留，改為購買每月計費的新保留。 不過，新保留的存留期值應該大於退回保留的比例分配值。

如果您取消每月付款的保留，已取消的未來付款將會累積到 50,000 美元的退款限制。

如需有關交換和退款的詳細資訊，請參閱 [Azure 保留的自助式交換和退款](exchange-and-refund-azure-reservations.md)。

## <a name="reservation-notifications"></a>保留通知

根據您支付 Azure 訂用帳戶的方式，我們會透過電子郵件，將保留通知傳送給貴組織中的下列使用者。 系統會針對各種事件傳送通知，包括： 

- 購買
- 即將到期的保留期限
- Expiry
- 續約
- 取消
- 範圍變更

針對具有 EA 訂用帳戶的客戶：

- 通知只會傳送給 EA 通知連絡人。
- 使用 RBAC (IAM) 權限新增至保留的使用者，不會收到任何電子郵件通知。

針對具有個別訂用帳戶的客戶：

- 購買者會收到購買通知。
- 在購買時，訂用帳戶計費帳戶擁有者會收到購買通知。
- 帳戶擁有者會收到所有其他通知。

## <a name="next-steps"></a>後續步驟

- [管理 Azure 資源的保留](manage-reserved-vm-instance.md)
