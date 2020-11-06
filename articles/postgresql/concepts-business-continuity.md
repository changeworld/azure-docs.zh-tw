---
title: 商務持續性-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明使用適用於 PostgreSQL 的 Azure 資料庫時 (時間點還原、資料中心中斷、異地還原、複本) 的商務持續性。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: cf3c07f32f15ff176974219bd8143a1ea315c945
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423040"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>使用適用於 PostgreSQL 的 Azure 資料庫-單一伺服器的商務持續性總覽

本概觀說明適用於 PostgreSQL 的 Azure 資料庫針對商務持續性和災害復原所提供的功能。 了解有哪些選項可讓您從可能導致資料遺失或造成資料庫和應用程式無法使用的干擾性事件中復原。 了解當使用者或應用程式錯誤影響資料完整性、Azure 區域中斷，或您的應用程式需要維護時該如何處理。

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>可用來提供商務持續性的功能

當您開發商務持續性計劃時，您必須了解應用程式在干擾性事件之後完全復原所需的最大可接受時間 - 這是您的復原時間目標 (RTO)。 您也必須了解在干擾性事件之後，應用程式在復原時可容許遺失的最近資料更新 (時間間隔) 最大數量 - 這是您的復原點目標 (RPO)。

適用於 PostgreSQL 的 Azure 資料庫供應商務持續性功能，包括可起始異地還原的地理區域冗余備份，以及在不同區域中部署讀取複本的功能。 每個都有不同的復原時間和潛在資料遺失特性。 使用 [異地還原](concepts-backup.md) 功能時，會使用從另一個區域複寫的備份資料來建立新的伺服器。 還原和復原所需的整體時間，取決於資料庫的大小以及要復原的記錄量。 建立伺服器的整體時間會因幾分鐘到數小時而異。 使用 [讀取複本](concepts-read-replicas.md)時，主資料庫的交易記錄會以非同步方式串流至複本。 如果由於區域層級或區域層級的錯誤而導致主資料庫中斷，容錯移轉至複本會提供較短的 RTO，並減少資料遺失的情況。

> [!NOTE]
> 主要和複本之間的延遲取決於網站之間的延遲、要傳輸的資料量，以及最重要的是主伺服器的寫入工作負載。 大量寫入工作負載可能會產生顯著的延遲。 
>
> 由於用於讀取複本的複寫是非同步本質，因此 **不應該** 將它們視為高可用性 (HA) 解決方案，因為較高的延遲可能表示 RTO 和 RPO 較高。 針對延遲在工作負載尖峰和非尖峰時間之間保持較小的工作負載，讀取複本可作為 HA 替代方案。 否則，讀取複本適用于適用于就緒的繁重工作負載，以及 (嚴重損壞修復) DR 案例的大量讀取規模。

下表將比較 **一般工作負載** 案例中的 RTO 和 RPO：

| **功能** | **Basic** | **一般用途** | **記憶體最佳化** |
| :------------: | :-------: | :-----------------: | :------------------: |
| 從備份進行時間點還原 | 保留期間內的任何還原點 | 保留期間內的任何還原點 | 保留期間內的任何還原點 |
| 從異地複寫備份進行異地還原 | 不支援 | RTO-變化 <br/>RPO < 1 小時 | RTO-變化 <br/>RPO < 1 小時 |
| 讀取複本 | RTO-分鐘 * <br/>RPO < 5 分鐘 * | RTO-分鐘 * <br/>RPO < 5 分鐘 *| RTO-分鐘 * <br/>RPO < 5 分鐘 *|

 \* RTO 和 RPO 在某些情況下 **可能會更高** ，視各種因素而定，包括網站之間的延遲、要傳輸的資料量，以及重要的主資料庫寫入工作負載。 

## <a name="recover-a-server-after-a-user-or-application-error"></a>在使用者或應用程式錯誤之後復原伺服器

您可以使用服務的備份，在各種干擾性事件發生之後復原伺服器。 使用者可能會不小心刪除某些資料、不小心卸除重要的資料表，或甚至是卸除整個資料庫。 應用程式可能會因為應用程式缺陷或其他原因，而不慎以不正確的資料覆寫正確的資料。

您可以執行 **時間點還原** ，將伺服器的複本建立到已知的良好時間點。 此時間點必須在您為伺服器設定的備份保留期間內。 資料還原至新的伺服器之後，您可以將原始伺服器取代為還原的新伺服器，或從還原的伺服器將所需的資料複製到原始伺服器。

> [!IMPORTANT]
> 已刪除的伺服器 **無法** 還原。 如果您刪除伺服器，所有屬於該伺服器的資料庫也會一併刪除，且無法復原。 使用 [Azure 資源鎖定](../azure-resource-manager/management/lock-resources.md) 來協助防止意外刪除您的伺服器。

## <a name="recover-from-an-azure-data-center-outage"></a>從 Azure 資料中心中斷復原

雖然很罕見，但 Azure 資料中心也可能會有中斷的時候。 發生中斷時，可能只會讓營運中斷幾分鐘，但也可能會持續幾小時。

其中一個選項，是在資料中心中斷結束後等待區域資料中心中斷重新上線。 這適用於可承受伺服器離線一段時間的應用程式，例如在開發環境中。 當資料中心發生中斷時，您不知道中斷的最長時間，因此只有在您的伺服器不需要一段時間時，此選項才會運作。

## <a name="geo-restore"></a>異地復原

異地還原功能會使用地理區域冗余備份來還原伺服器。 這些備份會裝載于您伺服器的 [配對區域](../best-practices-availability-paired-regions.md)中。 您可以從這些備份還原到任何其他區域。 異地還原會使用備份中的資料來建立新的伺服器。 深入瞭解 [備份和還原概念文章](concepts-backup.md)中的異地還原。

> [!IMPORTANT]
> 只有使用異地備援備份儲存體來佈建伺服器時，才可進行異地還原。 如果您希望將現有伺服器從本機備援切換到異地備援備份，您必須先透過現有伺服器的 pg_dump 進行傾印後，再將其還原至已設定為異地備援備份的新建伺服器。

## <a name="cross-region-read-replicas"></a>跨區域讀取複本
您可以使用跨區域讀取複本來增強您的商務持續性和嚴重損壞修復規劃。 讀取複本會使用於 postgresql 的實體複寫技術以非同步方式更新，而且可能會延遲主要複本。 深入瞭解讀取複本、可用區域，以及如何從 [讀取複本概念文章](concepts-read-replicas.md)進行容錯移轉。 

## <a name="faq"></a>常見問題集
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>適用於 PostgreSQL 的 Azure 資料庫儲存客戶資料的位置為何？
根據預設，適用於 PostgreSQL 的 Azure 資料庫不會在其部署所在的區域中移動或儲存客戶資料。 不過，客戶可以選擇性地選擇啟用 [異地複寫備份](concepts-backup.md#backup-redundancy-options) ，或建立 [跨區域讀取複本](concepts-read-replicas.md#cross-region-replication) ，以便將資料儲存在另一個區域中。


## <a name="next-steps"></a>後續步驟
- 深入了解[適用於 PostgreSQL 的 Azure 資料庫中的自動備份](concepts-backup.md)。 
- 了解如何使用 [Azure 入口網站](howto-restore-server-portal.md)或 [Azure CLI](howto-restore-server-cli.md) 來進行還原。
- 了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。
