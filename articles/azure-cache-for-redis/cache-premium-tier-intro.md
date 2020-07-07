---
title: Azure Cache for Redis 進階層簡介
description: 了解如何建立和管理進階層 Azure Cache for Redis 執行個體的 Redis 持續性、Redis 叢集和 VNET 支援
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74121663"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Azure Cache for Redis 進階層簡介
Azure Cache for Redis 是一種分散式受控快取，可提供超快速的資料存取，藉此協助您建置具高度延展性且快速回應的應用程式。 

新的高階層是可供企業立即使用的層，其中包括所有標準層功能及其他優點，例如更佳的效能、更大的工作負載、災害復原、匯入/匯出和增強的安全性。 請繼續閱讀，以深入了解高階快取層的其他功能。

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>效能優於標準或基本層。
**效能優於標準或基本層。**  高階層中的快取是部署在擁有較快處理器的硬體上，因此效能優於基本或標準層。 高階層快取的輸送量較高，延遲性較低。 

**相較於標準層，高階層中相同大小的快取，其輸送量較高。**  例如：53 GB P4 (高階層) 快取的輸送量是每秒 250K 個要求，相較之下，C6 (標準層) 則只有 150K 個。

如需高階快取的大小、輸送量和頻寬的詳細資訊，請參閱[Azure Cache For REDIS 常見問題](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis 資料永續性
高階層可讓您將快取資料保存在 Azure 儲存體帳戶中。 在基本/標準快取中，所有資料都只儲存在記憶體中。 如果基礎結構發生問題，資料可能會遺失。 建議您使用高階層中的 Redis 資料永續性功能，以增加資料遺失時的復原能力。 Azure Cache for Redis 會在[Redis 持續](https://redis.io/topics/persistence)性中提供 RDB 和 AOF （即將推出）選項。 

如需設定持續性的相關指示，請參閱 [如何設定進階 Azure Cache for Redis 的持續性](cache-how-to-premium-persistence.md)。

## <a name="redis-cluster"></a>Redis 叢集
如果您想要建立大於 53 GB 的快取，或想跨多個 Redis 節點共用資料，可以使用高階層中的 Redis 叢集。 每個節點均包含一個 Azure 所管理的主要/複本快取組，可提供高可用性。 

**Redis 叢集可提供最大的擴充能力和輸送量。** 當您增加叢集中的分區 (節點) 數目時，輸送量會呈線性增加。 例如 如果建立具有 10 個分區的 P4 叢集，則可用的輸送量為 250K *10 = 每秒 250 萬個要求。 如需進階快取的大小、輸送量和頻寬等方面的詳細資訊，請參閱 [Azure Cache for Redis 常見問題集](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)。

若要開始使用叢集，請參閱[如何設定進階 Azure Cache for Redis 的叢集](cache-how-to-premium-clustering.md)。

## <a name="enhanced-security-and-isolation"></a>增強的安全性和隔離
您可透過公用網際網路存取基本或標準層中建立的快取。 對快取的存取權會受到存取金鑰的限制。 若使用高階層，您可以進一步確保只有指定網路中的用戶端可以存取快取。 您可以在 [Azure 虛擬網路 (VNet)](https://azure.microsoft.com/services/virtual-network/) 中部署 Azure Cache for Redis。 您可以使用 VNet 的所有功能，例如子網路、存取控制原則和其他功能，進一步限制對 Redis 的存取權。

如需詳細資訊，請參閱[如何設定進階 Azure Cache for Redis 的虛擬網路支援](cache-how-to-premium-vnet.md)。

## <a name="importexport"></a>匯入/匯出
「匯入/匯出」是 Azure Cache for Redis 的資料管理作業，可讓您從進階快取將「Azure Cache for Redis 資料庫」(RDB) 快照集匯入和匯出至 Azure 儲存體帳戶中的分頁 Blob，以將資料匯入 Azure Cache for Redis 或從 Azure Cache for Redis 匯出資料。 這可讓您在不同的 Azure Cache for Redis 執行個體之間移轉，或在使用前將資料填入快取。

匯入可以用來從執行雲端或環境的任何 Redis 伺服器 (包含在 Linux、Windows 上執行的 Redis，或任何雲端提供者，例如 Amazon Web Services 等) 引入 Redis 相容 RDB 檔案。 匯入資料是使用預先填入資料建立快取的輕鬆方式。 在進行匯入程序的期間，Azure Cache for Redis 會從 Azure 儲存體將 RDB 檔案載入記憶體，然後將金鑰插入快取中。

匯出可讓您將儲存在 Azure Cache for Redis 中的資料匯出至與 Redis 相容的 RDB 檔案。 您可以使用這項功能，將資料從一個 Azure Cache for Redis 執行個體移到另一個執行個體，或移到另一個 Redis 伺服器。 在進行匯出程序的期間，會在裝載 Azure Cache for Redis 伺服器執行個體的 VM 上建立暫存檔案，然後將檔案上傳至指定的儲存體帳戶。 當匯出作業完成時的狀態為成功或失敗時，都會刪除暫存檔案。

如需詳細資訊，請參閱 [如何將資料匯入 Azure Cache for Redis 與從中匯出資料](cache-how-to-import-export-data.md)。

## <a name="reboot"></a>重新啟動
進階層可讓您依需求重新啟動快取的一或多個節點。 這可讓您測試應用程式，以便在發生失敗時加以復原。 您可以重新啟動下列節點。

* 快取的主要節點
* 快取的次要節點
* 快取的主要和次要節點
* 搭配使用高階快取與叢集時，您可以在快取中重新開機個別分區的主要、次要或兩個節點

如需詳細資訊，請參閱[重新啟動](cache-administration.md#reboot)和[重新啟動常見問題集](cache-administration.md#reboot-faq)。

>[!NOTE]
>重新啟動功能現在已對所有的 Azure Cache for Redis 層啟用。
>
>

## <a name="schedule-updates"></a>更新排程
排程更新功能可讓您指定適用於快取的維護期間。 若指定了維護期間，即會在此期間進行任何 Redis 伺服器更新。 若要指定維護期間，請選取所需的天數，並指定每一天的維護期間開始小時。 請注意，維護期間時間是 UTC。 

如需詳細資訊，請參閱[排程更新](cache-administration.md#schedule-updates)和[排程更新常見問題集](cache-administration.md#schedule-updates-faq)。

> [!NOTE]
> 在排程維護期間，只會進行 Redis 伺服器更新。 維護期間不適用於 Azure 更新或 VM 作業系統的更新。
> 
> 

## <a name="geo-replication"></a>異地複寫

**異地複寫**提供一個機制，可連結兩個進階層 Azure Cache for Redis 執行個體。 其中一個快取被指定為主要連結快取，而另一個則為次要連結快取。 次要連結快取會變成唯讀，而寫入主要快取的資料會複寫至次要連結快取。 這項功能可用來跨 Azure 區域複寫快取。

如需詳細資訊，請參閱[如何設定 Azure Cache for Redis 的異地複寫](cache-how-to-geo-replication.md)。


## <a name="to-scale-to-the-premium-tier"></a>調整為進階層
若要調整為進階層，只需選擇 [變更定價層] **** 刀鋒視窗中的其中一個進階層。 您也可以使用 PowerShell 和 CLI 來將快取調整為進階層。 如需逐步指示，請參閱[如何調整 Azure Cache for Redis](cache-how-to-scale.md) 和[如何將調整作業自動化](cache-how-to-scale.md#how-to-automate-a-scaling-operation)。

## <a name="next-steps"></a>後續步驟
建立快取並探索高階層的新功能。

* [如何設定進階 Azure Cache for Redis 的持續性](cache-how-to-premium-persistence.md)
* [如何設定進階 Azure Cache for Redis 的虛擬網路支援](cache-how-to-premium-vnet.md)
* [如何設定進階 Azure Cache for Redis 的 Redis 叢集功能](cache-how-to-premium-clustering.md)
* [如何將資料匯入 Azure Cache for Redis 與從中匯出資料](cache-how-to-import-export-data.md)
* [如何管理 Azure Cache for Redis](cache-administration.md)

