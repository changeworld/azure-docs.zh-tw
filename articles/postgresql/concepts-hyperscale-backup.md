---
title: 備份與還原–超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 保護資料免于意外損毀或刪除
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 22bc3d6efca24a88b28217b2e06ac79d33f16b2e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030074"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫中的備份和還原-超大規模資料庫（Citus）

適用於 PostgreSQL 的 Azure 資料庫–超大規模資料庫（Citus）會自動建立每個節點的備份，並將它們儲存在本機的多餘儲存體中。 備份可以用來將您的超大規模資料庫（Citus）叢集還原到指定的時間。 備份和還原可保護資料免於意外損毀或刪除，是商務持續性策略中不可或缺的一部分。

## <a name="backups"></a>備份

至少一天一次，適用於 PostgreSQL 的 Azure 資料庫會取得資料檔案和資料庫交易記錄的快照集備份。 這些備份可讓您將伺服器還原到保留期限內的任何時間點。 （所有叢集的保留期限目前為35天）。所有備份都會使用 AES 256 位加密進行加密。

在支援可用性區域的 Azure 區域中，備份快照集會儲存在三個可用性區域中。 只要至少有一個可用性區域上線，超大規模資料庫（Citus）叢集便可還原。

無法匯出備份檔案。 它們只能用於適用於 PostgreSQL 的 Azure 資料庫中的還原作業。

### <a name="backup-storage-cost"></a>備份儲存體成本

如需最新的備份儲存體定價，請參閱適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）[定價頁面](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)。

## <a name="restore"></a>還原

在適用於 PostgreSQL 的 Azure 資料庫中，還原超大規模資料庫（Citus）叢集會從原始節點的備份建立新的叢集。 

> [!IMPORTANT]
>您只能在相同的訂用帳戶和資源群組中，以及使用不同的叢集名稱來還原超大規模資料庫叢集。


> [!IMPORTANT]
> 無法還原已刪除的超大規模資料庫（Citus）叢集。 如果您刪除叢集，屬於該叢集的所有節點都會被刪除，而且無法復原。 若要保護叢集資源，部署後不受意外刪除或非預期的變更，系統管理員可以利用[管理鎖定](/azure/azure-resource-manager/management/lock-resources)。

### <a name="point-in-time-restore-pitr"></a>還原時間點（PITR）

您可以將叢集還原到過去35天內的任何時間點。
時間點還原適用於多種案例。 例如，當使用者不小心刪除資料、卸載重要的資料表或資料庫時，或是應用程式不小心以不正確的資料覆寫正確的資料時。

還原程式會在與原始相同的 Azure 區域、訂用帳戶和資源群組中建立新的叢集。 叢集具有原始的設定：相同數目的節點、虛擬核心數目、儲存體大小、使用者角色、于 postgresql 版本，以及 Citus 擴充功能的版本。

系統不會從原始伺服器群組保留防火牆設定和于 postgresql 伺服器參數，而是會將它們重設為預設值。 防火牆會防止所有連線。 您將需要在還原之後手動調整這些設定。

> [!IMPORTANT]
> 您必須開啟支援要求，以執行超大規模資料庫（Citus）叢集的時間點還原。

### <a name="post-restore-tasks"></a>還原後工作

從任一復原機制還原之後，您應該執行下列動作，讓您的使用者和應用程式備份並執行：

* 如果新伺服器就會取代原始伺服器，則將用戶端和用戶端應用程式重新導向至新伺服器
* 請確定已備妥適當的伺服器層級防火牆和 VNet 規則供使用者連接。 這些規則不會從原始伺服器群組複製。
* 視需要調整于 postgresql 伺服器參數。 不會從原始伺服器群組複製參數。
* 確定有適當的登入和資料庫層級權限
* 依適當情況設定警示

## <a name="next-steps"></a>後續步驟

* 瞭解 [Azure 可用性區域](/azure/availability-zones/az-overview)。
* 在超大規模資料庫（Citus）伺服器群組上設定 [建議的警示](/azure/postgresql/howto-hyperscale-alert-on-metric#suggested-alerts)。
