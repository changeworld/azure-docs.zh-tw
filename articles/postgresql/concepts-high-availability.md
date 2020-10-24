---
title: 高可用性-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文提供適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中高可用性的相關資訊
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/15/2020
ms.openlocfilehash: aa9f38b2cefa60a0c3341c1317cf45fbcb735301
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485438"
---
# <a name="high-availability-in-azure-database-for-postgresql--single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的高可用性–單一伺服器
適用於 PostgreSQL 的 Azure 資料庫–單一伺服器服務可透過 [99.99%](https://azure.microsoft.com/support/legal/sla/postgresql) 執行時間的 SLA) ，提供保證的高可用性服務等級協定 (SLA。 適用於 PostgreSQL 的 Azure 資料庫在規劃的事件（例如，使用者初始化規模計算作業）期間提供高可用性，而且也會在發生非計畫的事件（例如基礎硬體、軟體或網路失敗）時提供高可用性。 適用於 PostgreSQL 的 Azure 資料庫可以從最重要的情況快速復原，以確保在使用此服務時幾乎不會有任何應用程式停機時間。

適用於 PostgreSQL 的 Azure 資料庫適用于執行需要高執行時間的任務關鍵性資料庫。 以 Azure 架構為基礎，此服務具有固有的高可用性、冗余和復原功能，可從規劃和未計畫的中斷中減少資料庫停機時間，而不需要設定任何其他元件。 

## <a name="components-in-azure-database-for-postgresql--single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的元件–單一伺服器

| **元件** | **說明**|
| ------------ | ----------- |
| <b>于 postgresql 資料庫伺服器 | 適用於 PostgreSQL 的 Azure 資料庫為資料庫伺服器提供安全性、隔離、資源保護和快速重新開機功能。 這些功能可在發生中斷之後（以秒為單位），協助進行調整和資料庫伺服器復原等作業。 <br/> 資料庫伺服器中的資料修改通常會發生在資料庫交易的內容中。 所有資料庫變更都會以「寫入前記錄」的形式以同步方式記錄 (WAL 在 Azure 儲存體上) （連接到資料庫伺服器）。 在資料庫 [檢查點](https://www.postgresql.org/docs/11/sql-checkpoint.html) 處理過程中，資料庫伺服器記憶體中的資料頁也會排清到儲存體。 |
| <b>遠端儲存體 | 所有于 postgresql 的實體資料檔案和 WAL 檔都會儲存在 Azure 儲存體上，其架構為在區域內儲存三份資料複本，以確保資料的冗余、可用性和可靠性。 儲存層也與資料庫伺服器無關。 您可以從失敗的資料庫伺服器卸離，並在幾秒內重新附加至新的資料庫伺服器。 此外，Azure 儲存體持續監視任何儲存體錯誤。 如果偵測到區塊損毀，則會藉由具現化新的儲存複本來自動修正。 |
| <b>閘道 | 閘道會作為資料庫 proxy，將所有用戶端連線路由傳送至資料庫伺服器。 |

## <a name="planned-downtime-mitigation"></a>規劃的停機時間緩和
適用於 PostgreSQL 的 Azure 資料庫的架構可在規劃的停機作業期間提供高可用性。 

:::image type="content" source="./media/concepts-high-availability/azure-postgresql-elastic-scaling.png" alt-text="Azure 于 postgresql 中的彈性調整":::

1. 在幾秒內擴大和縮小於 postgresql 資料庫伺服器
2. 作為 proxy 來路由傳送用戶端的閘道會連線到適當的資料庫伺服器
3. 您可以在不停機的情況下執行擴充儲存體。 遠端存放可讓您在容錯移轉之後快速卸離/重新連接。
以下是一些規劃的維護案例：

| **案例** | **描述**|
| ------------ | ----------- |
| <b>計算擴大/縮小 | 當使用者執行計算擴大/減少作業時，會使用調整的計算設定來布建新的資料庫伺服器。 在舊的資料庫伺服器中，允許使用中的檢查點完成，用戶端連接已清空，任何未認可的交易都會取消，然後關閉。 然後，存放裝置會從舊的資料庫伺服器卸離，並附加至新的資料庫伺服器。 當用戶端應用程式重試連線，或嘗試建立新的連線時，閘道會將連接要求導向至新的資料庫伺服器。|
| <b>擴充儲存體 | 相應增加儲存體是一項線上作業，不會中斷資料庫伺服器。|
| <b> (Azure) 的新軟體部署 | 新推出的功能或 bug 修正會在服務的規劃維護期間自動發生。 如需詳細資訊，請參閱 [檔](./concepts-monitoring.md#planned-maintenance-notification)集，也請檢查您的 [入口網站](https://aka.ms/servicehealthpm)。|
| <b>次要版本升級 | 適用於 PostgreSQL 的 Azure 資料庫會自動將資料庫伺服器修補為 Azure 所決定的次要版本。 它會在服務的已規劃維護過程中發生。 這會導致短暫的停機時間（以秒為單位），而且資料庫伺服器會自動以新的次要版本重新開機。 如需詳細資訊，請參閱 [檔](./concepts-monitoring.md#planned-maintenance-notification)集，也請檢查您的 [入口網站](https://aka.ms/servicehealthpm)。|


##  <a name="unplanned-downtime-mitigation"></a>非計畫的停機風險降低

非預期的停機時間可能會因為未預期的失敗而發生，包括基礎硬體錯誤、網路問題和軟體錯誤。 如果資料庫伺服器意外關閉，則會自動布建新的資料庫伺服器（以秒為單位）。 遠端存放會自動附加至新的資料庫伺服器。 于 postgresql 引擎會使用 WAL 和資料庫檔案來執行復原作業，並開啟資料庫伺服器，以允許用戶端連接。 未認可的交易會遺失，而且必須由應用程式重試。 雖然無法避免非計畫的停機時間，適用於 PostgreSQL 的 Azure 資料庫可在資料庫伺服器和儲存層自動執行復原作業，而不需要人為介入，藉此減少停機時間。 


:::image type="content" source="./media/concepts-high-availability/azure-postgresql-built-in-high-availability.png" alt-text="Azure 于 postgresql 中的彈性調整":::

1. 具有快速調整功能的 Azure 于 postgresql 伺服器。
2. 作為 proxy 的閘道，可將用戶端連線路由至適當的資料庫伺服器
3. 具有三個複本的 Azure 儲存體，以提供可靠性、可用性和冗余。
4. 遠端存放也會在伺服器容錯移轉之後啟用快速卸離/重新連接。
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>未計畫的停機時間：失敗案例和服務復原
以下是一些失敗案例，以及適用於 PostgreSQL 的 Azure 資料庫如何自動復原：

| **案例** | **自動復原** |
| ---------- | ---------- |
| <B>資料庫伺服器失敗 | 如果資料庫伺服器因為某些基礎硬體錯誤而關閉，則會中斷使用中的連接，而且會中止任何傳遞交易。 系統會自動部署新的資料庫伺服器，並將遠端資料存放區附加至新的資料庫伺服器。 完成資料庫復原之後，用戶端可以透過閘道連接到新的資料庫伺服器。 <br /> <br /> 復原時間 (RTO) 取決於各種因素，包括發生錯誤時的活動（例如大型交易），以及資料庫伺服器啟動程式期間要執行的復原量。 <br /> <br /> 使用於 postgresql 資料庫的應用程式必須以其偵測和重試中斷的連接和失敗交易的方式來建立。  當應用程式重試時，閘道會以透明方式將連接重新導向至新建立的資料庫伺服器。 |
| <B>儲存體失敗 | 應用程式不會看到任何儲存體相關問題的影響，例如磁片故障或實體區塊損毀。 當資料儲存在3個複本中時，資料複本會由存活的儲存區提供服務。 封鎖損毀會自動校正。 如果遺失資料複本，則會自動建立新的資料複本。 |

以下是一些需要使用者動作來復原的失敗案例：

| **案例** | **復原方案** |
| ---------- | ---------- |
| <b> 區域失敗 | 區域失敗是罕見的事件。 但是，如果您需要防止區域失敗，則可以在其他區域中設定一或多個讀取複本，以進行嚴重損壞修復 (DR) 。  (請參閱 [這篇文章](./howto-read-replicas-portal.md) ，瞭解如何建立及管理讀取複本，以取得詳細資料) 。 發生區域層級失敗時，您可以手動將另一個區域上設定的讀取複本升級為生產資料庫伺服器。 |
| <b> 邏輯/使用者錯誤 | 從使用者錯誤（例如不小心卸載的資料表或不正確的資料）復原，牽涉到在發生錯誤之前的時間之前，藉由還原和復原資料，來執行 [時間點恢復](./concepts-backup.md) (PITR) 。<br> <br>  如果您只想要還原一部分的資料庫或特定的資料表，而不是資料庫伺服器中的所有資料庫，您可以在新的實例中還原資料庫伺服器，) 透過 [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)將資料表匯出 (s，然後使用 [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) 將這些資料表還原至您的資料庫。 |



## <a name="summary"></a>摘要

適用於 PostgreSQL 的 Azure 資料庫可提供資料庫伺服器的快速重新開機功能、重複的儲存空間，以及從閘道有效率的路由。 針對額外的資料保護，您可以將備份設定為異地複寫，也可以在其他區域中部署一或多個讀取複本。 利用固有的高可用性功能，適用於 PostgreSQL 的 Azure 資料庫保護您的資料庫不受最常見的中斷影響，並提供領先業界的財務支援 [99.99% 的執行時間 SLA](https://azure.microsoft.com/support/legal/sla/postgresql)。 所有的可用性和可靠性功能都能讓 Azure 成為執行任務關鍵性應用程式的理想平臺。

## <a name="next-steps"></a>後續步驟
- 深入瞭解 [Azure 區域](../availability-zones/az-overview.md)
- 了解如何[處理暫時性連線錯誤](concepts-connectivity.md)
- 了解如何[使用讀取複本來複寫資料](howto-read-replicas-portal.md)