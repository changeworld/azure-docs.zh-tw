---
title: Azure 保留折扣的套用方式
description: 本文可協助您了解保留執行個體折扣的一般套用方式。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: fb6e7e3fe3b7b45b503dca66dc5b039a9eceb689
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80135482"
---
# <a name="how-a-reservation-discount-is-applied"></a>保留折扣的套用方式

本文可協助您了解保留執行個體折扣的一般套用方式。 保留折扣會套用至符合您於購買保留時所選屬性的資源使用量。 屬性包含相符的 VM、SQL 資料庫、Azure Cosmos DB 或其他資源的執行範圍。 例如，如果您想要針對美國西部區域的四個標準 D2 虛擬機器取得保留折扣，則選取這些 VM 執行所在的訂用帳戶。

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

## <a name="how-discounts-apply-to-specific-azure-services"></a>如何將折扣套用至特定 Azure 服務

閱讀下列適用於您的文章，以了解折扣如何套用至特定的 Azure 服務：

- [App Service](reservation-discount-app-service-isolated-stamp.md)
- [Azure Cache for Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [適用於 MariaDB 的資料庫](understand-reservation-charges-mariadb.md)
- [適用於 MySQL 的資料庫](understand-reservation-charges-mysql.md)
- [適用於 PostgreSQL 的資料庫](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [資料總管](understand-azure-data-explorer-reservation-charges.md)
- [專用主機](billing-understand-dedicated-hosts-reservation-charges.md)
- [磁碟儲存體](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [軟體方案](understand-suse-reservation-charges.md)
- [Storage](understand-storage-charges.md)
- [SQL Database](understand-reservation-charges.md)
- [SQL 資料倉儲](reservation-discount-azure-sql-dw.md)
- [虛擬機器](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>後續步驟

- [管理 Azure 保留項目](manage-reserved-vm-instance.md)
- [了解採用隨用隨付費率的訂用帳戶的保留使用量](understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](understand-reserved-instance-usage-ea.md)
- [Windows 軟體的成本不包括在保留項目內](reserved-instance-windows-software-costs.md)