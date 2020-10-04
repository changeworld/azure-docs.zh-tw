---
title: 備份與還原-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 瞭解自動備份及還原您的適用於 PostgreSQL 的 Azure 資料庫伺服器-單一伺服器。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 0c1b0b5ac0c5c71dc5c98cb91d86f879a82809bc
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708449"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的備份與還原-單一伺服器

適用於 PostgreSQL 的 Azure 資料庫會自動建立伺服器備份，並將其儲存在使用者設定的本地備援或異地備援儲存體中。 備份可以用來將伺服器還原至某個時間點。 備份和還原可保護資料免於意外損毀或刪除，是商務持續性策略中不可或缺的一部分。

## <a name="backups"></a>備份

適用於 PostgreSQL 的 Azure 資料庫會取得資料檔案和交易記錄檔的備份。 根據支援的儲存體大小上限，我們會將完整和差異備份 (4 TB 的最大儲存體伺服器) 或快照集備份 (最多 16 TB 的儲存體伺服器) 。 在您設定的備份保留期限內，這些備份可讓您將伺服器還原至任何時間點。 預設的備份保留期限是七天。 可選擇設定的期限最多為 35 天。 所有備份皆會使用 AES 256 位元加密進行加密。

這些備份檔案無法匯出。 備份只能用於適用於 PostgreSQL 的 Azure 資料庫中的還原作業。 您可以使用 [pg_dump](howto-migrate-using-dump-and-restore.md) 來複製資料庫。

### <a name="backup-frequency"></a>備份頻率

#### <a name="servers-with-up-to-4-tb-storage"></a>具有最多 4 TB 儲存空間的伺服器

針對最多支援 4 TB 儲存體的伺服器，完整備份會每週進行一次。 差異備份會一天進行兩次。 交易記錄備份會每五分鐘執行一次。


#### <a name="servers-with-up-to-16-tb-storage"></a>最多 16 TB 儲存體的伺服器

在 [Azure 區域](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage)的子集中，所有新布建的伺服器最多可支援 16 TB 的儲存體。 這些大型儲存體伺服器上的備份是以快照集為基礎。 建立伺服器之後，會立即排程第一次完整快照集備份。 第一次完整快照集備份會保留為伺服器的基礎備份。 後續的快照集備份只是差異備份。 差異快照集備份不會依固定排程執行。 一天會執行三個差異快照集備份。 交易記錄備份會每五分鐘執行一次。 

### <a name="backup-retention"></a>備份保留

備份會根據伺服器上的備份保留期限設定來保留。 您可以選取7到35天的保留期間。 預設保留期間為7天。 您可以使用 [Azure 入口網站](https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal#set-backup-configuration) 或 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-restore-server-cli#set-backup-configuration)更新備份設定，在伺服器建立或更新版本期間設定保留期限。 

備份保留期限會控制可往回多少時間來擷取時間點還原，因為這會以可用的備份為基礎。 您也可以從還原的觀點，將備份保留期限視為修復時段。 在備份保留期限內執行時間點還原所需的所有備份都會保留在備份儲存體中。 例如-如果備份保留期限設定為7天，則會將修復視窗視為過去7天。 在此案例中，會保留在過去7天還原伺服器所需的所有備份。 備份保留期間為七天：
- 具有最多 4 TB 儲存體的伺服器最多可保留2個完整的資料庫備份、所有差異備份，以及自最早的完整資料庫備份之後所執行的交易記錄備份。
-   具有最多 16 TB 儲存體的伺服器會保留完整的資料庫快照集、所有差異快照集，以及過去8天內的交易記錄備份。

### <a name="backup-redundancy-options"></a>備份備援選項

適用於 PostgreSQL 的 Azure 資料庫可讓您在一般用途和記憶體最佳化層中，彈性地選擇本地備援或異地備援備份儲存體。 當備份儲存在異地備援備份儲存體中時，這些備份不會只儲存在裝載您伺服器的區域內，也會複寫至[配對的資料中心](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 這樣能提供更好的保護性和功能，當發生災害時，您就可以在不同區域中還原伺服器。 「基本」層只會提供本地備援的備份儲存體。

> [!IMPORTANT]
> 您只可在伺服器建立期間，為備份設定本地備援或異地備援儲存體。 伺服器佈建完成之後，您無法變更備份儲存體備援選項。

### <a name="backup-storage-cost"></a>備份儲存體成本

適用於 PostgreSQL 的 Azure 資料庫可提供高達 100% 的已佈建伺服器儲存體作為備份儲存體，且不須支付額外費用。 使用的任何額外備份儲存體都會依每月 GB 收費。 例如，如果您布建的伺服器具有 250 GB 的儲存體，您可以免費使用 250 GB 的額外儲存體來進行伺服器備份。 超過 250 GB 的備份所耗用的儲存體會依 [定價模式](https://azure.microsoft.com/pricing/details/postgresql/)收費。

您可以使用 Azure 入口網站中可用 Azure 監視器的「 [備份儲存體](concepts-monitoring.md) 」計量來監視伺服器所耗用的備份儲存體。 [備份儲存體使用的計量] 代表所有完整資料庫備份、差異備份和記錄備份所使用的儲存體總和（根據針對伺服器所設定的備份保留期限而定）。 備份的頻率是服務管理的，而且稍早說明。 不論資料庫大小總計，伺服器上頻繁的交易活動可能會導致備份儲存體使用量增加。 針對異地複寫儲存體，備份儲存體使用量會是本機冗余儲存體的兩倍。 

控制備份儲存體成本的主要方法是設定適當的備份保留期限，然後選擇適當的備份冗余選項，以符合您所需的復原目標。 您可以從7到35天的範圍內選取保留期限。 一般用途和經記憶體優化的伺服器可以選擇要備份的異地多餘儲存體。

## <a name="restore"></a>還原

在適用於 PostgreSQL 的 Azure 資料庫中，還原執行作業會從原始伺服器的備份中建立新的伺服器。

有兩種類型的還原可使用：

- **時間點還原** 適用于 [備份冗余] 選項，並在與源伺服器相同的區域中建立新的伺服器。
- 只有當您將伺服器設定為異地多餘的儲存體，並可讓您將伺服器還原到不同的區域時，才可使用**異地還原**。

預估的復原時間取決於數個因素，包括資料庫大小、交易記錄大小、網路頻寬，以及在相同區域中同時進行復原的資料庫總數。 復原時間通常不到 12 小時。

> [!IMPORTANT]
> 已刪除的伺服器**無法**還原。 如果您刪除伺服器，所有屬於該伺服器的資料庫也會一併刪除，且無法復原。 若要在部署後避免伺服器資源遭到意外刪除或非預期的變更，系統管理員可以利用[管理鎖定](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)。

### <a name="point-in-time-restore"></a>時間點還原

與備份備援選項無關，您可以在備份保留期限內地任何時間點執行還原。 新伺服器會建立在與原始伺服器相同的 Azure 區域中。 其使用原始伺服器的組態來建立，包含定價層、計算世代、虛擬核心數目、儲存體大小、備份保留期限，以及備份備援選項。

時間點還原適用於多種案例。 例如，使用者不小心刪除資料、卸除重要的資料表或資料庫，或是因為應用程式缺失，使應用程式不小心用不正確的資料覆寫正確資料。

您可能需要等候下一個交易記錄備份執行時，才能還原至前五分鐘內的時間點。

### <a name="geo-restore"></a>異地復原

如果您已將伺服器設定為使用異地備援備份，您可以將伺服器還原到另一個可使用服務的 Azure 區域中。 最多可支援 4 TB 儲存體的伺服器可以還原至異地配對區域，或最多支援 16 TB 儲存體的任何區域。 對於支援高達 16 TB 儲存體的伺服器，您也可以在支援 16 TB 伺服器的任何區域中還原異地備份。 如需支援的區域清單，請參閱 [Azure Database For PostgeSQL 定價層](concepts-pricing-tiers.md) 。

當您的伺服器因為裝載伺服器區域中的事件而無法使用時，異地還原就是預設的復原選項。 如果區域中的大規模意外導致您無法使用資料庫應用程式，則您可以從異地備援備份，將伺服器還原到任何其他區域中的伺服器。 在建立備份及將它複寫至不同區域之間會有延遲。 此延遲可能最長達一小時，因此當發生災害時，最多可能會遺失最長達一小時的資料。

在異地還原期間，可以進行變更的伺服器設定包括計算世代、vCore、備份保留期間及備份備援選項。 不支援變更定價層 (基本、一般用途或記憶體最佳化) 或儲存體大小。

### <a name="perform-post-restore-tasks"></a>執行還原之後的工作

從其中任何一種復原機制還原之後，您應執行下列工作，讓您的使用者和應用程式回復正常執行狀態︰

- 如果新伺服器就會取代原始伺服器，則將用戶端和用戶端應用程式重新導向至新伺服器
- 確定有適當的伺服器層級防火牆和 VNet 規則可供使用者連接。 這些規則不會從源伺服器複製。
- 確定有適當的登入和資料庫層級權限
- 依適當情況設定警示

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用 [Azure 入口網站](howto-restore-server-portal.md)進行還原。
- 瞭解如何使用 [Azure CLI](howto-restore-server-cli.md)進行還原。
- 若要深入了解商務持續性，請參閱 [商務持續性概觀](concepts-business-continuity.md)。
