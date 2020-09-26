---
title: 備份與還原–超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 保護資料免于意外損毀或刪除
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 2d781ca7293d4bd95ae62eadc50295ca14c2d381
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314925"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫-超大規模 (Citus) 中的備份與還原

適用於 PostgreSQL 的 Azure 資料庫–超大規模 (Citus) 會自動建立每個節點的備份，並將其儲存在本機的多餘儲存體中。 您可以使用備份，將超大規模 (Citus) 叢集還原至指定的時間。 備份和還原可保護資料免於意外損毀或刪除，是商務持續性策略中不可或缺的一部分。

## <a name="backups"></a>備份

至少一天一次，適用於 PostgreSQL 的 Azure 資料庫會取得資料檔案和資料庫交易記錄的快照集備份。 這些備份可讓您將伺服器還原至保留期限內的任何時間點。  (所有叢集的保留期限目前為35天。 ) 所有備份都使用 AES 256 位加密進行加密。

在支援可用性區域的 Azure 區域中，備份快照集會儲存在三個可用性區域中。 只要至少有一個可用性區域處於線上狀態，超大規模 (Citus) 叢集就可以還原。

備份檔案無法匯出。 它們只能用於適用於 PostgreSQL 的 Azure 資料庫中的還原作業。

### <a name="backup-storage-cost"></a>備份儲存體成本

如需目前的備份儲存體定價，請參閱適用於 PostgreSQL 的 Azure 資料庫-超大規模 (Citus) [定價頁面](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)。

## <a name="restore"></a>還原

在適用於 PostgreSQL 的 Azure 資料庫中，還原超大規模 (Citus) 叢集會從原始節點的備份建立新的叢集。 

> [!IMPORTANT]
>您只能在相同的訂用帳戶和資源群組中，以及使用不同的叢集名稱，來還原超大規模 (Citus) 叢集。


> [!IMPORTANT]
> 無法還原已刪除的超大規模 (Citus) 叢集。 如果您刪除叢集，所有屬於該叢集的節點都會刪除，而且無法復原。 為了保護叢集資源、部署後、意外刪除或非預期的變更，系統管理員可以利用 [管理鎖定](/azure/azure-resource-manager/management/lock-resources)。

### <a name="point-in-time-restore-pitr"></a>時間點還原 (PITR) 

您可以將叢集還原至過去35天內的任何時間點。
時間點還原適用於多種案例。 例如，當使用者不小心刪除資料時，會卸載重要的資料表或資料庫，或應用程式不小心以錯誤的資料覆寫良好的資料。

還原程式會在與原始的相同 Azure 區域、訂用帳戶和資源群組中建立新的叢集。 叢集具有原始的設定：相同數目的節點、虛擬核心數目、儲存體大小、使用者角色、于 postgresql 版本，以及 Citus 延伸模組的版本。

系統不會保留原始伺服器群組的防火牆設定和于 postgresql 伺服器參數，而是將它們重設為預設值。 防火牆會防止所有連接。 您將需要在還原之後手動調整這些設定。

> [!IMPORTANT]
> 您必須開啟支援要求，才能執行超大規模 (Citus) 叢集的時間點還原。

### <a name="post-restore-tasks"></a>還原後工作

從任一復原機制還原之後，您應該執行下列步驟，讓您的使用者和應用程式恢復運作：

* 如果新伺服器就會取代原始伺服器，則將用戶端和用戶端應用程式重新導向至新伺服器
* 確定有適當的伺服器層級防火牆和 VNet 規則可供使用者連接。 這些規則不會從原始伺服器群組複製。
* 視需要調整于 postgresql 伺服器參數。 這些參數不會從原始伺服器群組複製。
* 確定有適當的登入和資料庫層級權限
* 依適當情況設定警示

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Azure 可用性區域](/azure/availability-zones/az-overview)。
* 在超大規模 (Citus) 伺服器群組上設定 [建議的警示](/azure/postgresql/howto-hyperscale-alert-on-metric#suggested-alerts) 。
