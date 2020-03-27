---
title: 準備購買 Azure 保留
description: 購買 Azure 保留之前，請先了解相關要點。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235659"
---
# <a name="prepare-to-buy-a-reservation"></a>準備購買保留

Azure 保留可協助您藉由承諾多項 Azure 資源一年或三年期的方案來節省成本。 在您輸入承諾用量以購買保留之前，請務必先參閱下列各節，以做好購買的準備。

## <a name="who-can-buy-a-reservation"></a>誰可以購買保留

若要購買方案，您必須具備 Enterprise (MS-AZR-0017P 或 MS-AZR-0148P) 或隨用隨付訂用帳戶 (MS-AZR-0003P 或 MS-AZR-0023P) 或 Microsoft 客戶合約訂用帳戶的訂用帳戶擁有者角色。 雲端解決方案提供者可使用 Azure 入口網站或 [合作夥伴中心](/partner-center/azure-reservations) 來購買 Azure 保留。

Enterprise 合約 (EA) 客戶可藉由在 EA 入口網站中停用 [新增保留執行個體]  選項，以限制對 EA 管理員的購買。 EA 管理員必須是至少一個 EA 訂用帳戶的訂用帳戶擁有者，才能購買保留。 當企業想要讓中央小組購買不同成本中心的保留時，此選項非常有用。 購買之後，中央小組可以將成本中心擁有者新增至保留。 然後，擁有者可以將保留範圍設定為其訂用帳戶。 中央小組不需要擁有購買保留之處的訂用帳戶擁有者存取權。

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

單一資源群組可根據您設定保留範圍的方式，從多個保留取得保留折扣。

您一律可以在購買保留之後變更範圍。 若要這麼做，請移至保留，按一下 [設定]  ，然後重新設定保留範圍。 重新設定保留範圍不算是商業交易。 您的保留期限不會變更。 如需有關更新範圍的詳細資訊，請參閱[在購買保留後更新範圍](manage-reserved-vm-instance.md#change-the-reservation-scope)。

![顯示保留範圍變更的範例](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>購買保留

您可以從 Azure 入口網站、API、PowerShell、CLI 來購買保留。 當您準備好要購買保留時，請閱讀下列適用的文章：

- [App Service](prepay-app-service-isolated-stamp.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [資料總管](../../data-explorer/pricing-reserved-capacity.md)
- [磁碟儲存體](../../virtual-machines/linux/disks-reserved-capacity.md)
- [專用主機](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [軟體方案](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [SQL 資料倉儲](prepay-sql-data-warehouse-charges.md)
- [虛擬機器](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>後續步驟

- [管理 Azure 資源的保留](manage-reserved-vm-instance.md)
