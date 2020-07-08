---
title: 高可用性-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文提供適用於 PostgreSQL 的 Azure 資料庫單一伺服器中高可用性的相關資訊
author: sr-pg20
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/15/2020
ms.openlocfilehash: 564aa030c442331fbcd965c87da3bfbc03d00d79
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85105877"
---
# <a name="high-availability-in-azure-database-for-postgresql--single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的高可用性–單一伺服器
適用於 PostgreSQL 的 Azure 資料庫–單一伺服器服務可在[99.99%](https://azure.microsoft.com/support/legal/sla/postgresql)的執行時間中，以財務支援的服務等級協定（SLA）提供保證的高可用性等級。 適用於 PostgreSQL 的 Azure 資料庫在規劃的事件（例如，使用者初始化的規模計算作業）期間提供高可用性，同時也會在發生基礎硬體、軟體或網路失敗之類的非計畫事件時進行。 適用於 PostgreSQL 的 Azure 資料庫可以快速地從最重要的情況中復原，確保在使用此服務時，幾乎不會有任何應用程式停機時間。

適用於 PostgreSQL 的 Azure 資料庫適用于執行需要很長執行時間的要徑任務資料庫。 此服務以 Azure 架構為基礎，具有固有的高可用性、冗余和復原功能，可減少規劃和未計畫的中斷資料庫停機時間，而不需要設定任何其他元件。 

## <a name="components-in-azure-database-for-postgresql--single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的元件–單一伺服器

| **元件** | **說明**|
| ------------ | ----------- |
| <b>于 postgresql 資料庫伺服器 | 適用於 PostgreSQL 的 Azure 資料庫為資料庫伺服器提供安全性、隔離、資源保護和快速重新開機功能。 這些功能可協助在中斷發生後的作業（以秒為單位）進行調整和資料庫伺服器復原操作。 <br/> 資料庫伺服器中的資料修改通常會發生在資料庫交易的內容中。 所有資料庫變更都會以同步方式記錄在 Azure 儲存體上的寫入記錄（WAL）格式，也就是附加到資料庫伺服器。 在資料庫[檢查點](https://www.postgresql.org/docs/11/sql-checkpoint.html)處理過程中，資料庫伺服器記憶體中的資料頁也會排清至儲存體。 |
| <b>遠端儲存體 | 所有于 postgresql 的實體資料檔和 WAL 檔案都會儲存在 Azure 儲存體上，其架構是用來在區域內儲存三份資料複本，以確保資料的冗余、可用性和可靠性。 儲存層也與資料庫伺服器無關。 它可以從失敗的資料庫伺服器卸離，並在幾秒鐘內重新附加到新的資料庫伺服器。 此外，Azure 儲存體會持續監視是否有任何儲存體錯誤。 如果偵測到區塊損毀，則會藉由具現化新的存放複本來自動修正。 |
| <b>閘道 | 閘道會做為資料庫 proxy，將所有用戶端連接路由傳送到資料庫伺服器。 |

## <a name="planned-downtime-mitigation"></a>規劃的停機時間緩和
適用於 PostgreSQL 的 Azure 資料庫的架構是為了在規劃的停機作業期間提供高可用性。 

![Azure 于 postgresql 中彈性調整的觀點](./media/concepts-high-availability/azure-postgresql-elastic-scaling.png)

以下是一些預定進行的維護案例：

| **案例** | **描述**|
| ------------ | ----------- |
| <b>計算相應增加/減少 | 當使用者執行計算相應增加/減少作業時，會使用調整的計算設定來布建新的資料庫伺服器。 在舊的資料庫伺服器中，可以完成作用中的檢查點，用戶端連接已耗盡，任何未認可的交易都會取消，然後關閉。 然後會從舊的資料庫伺服器卸離存放裝置，並將其附加至新的資料庫伺服器。 當用戶端應用程式重試連接，或嘗試建立新的連線時，閘道會將連接要求導向至新的資料庫伺服器。|
| <b>相應增加儲存體 | 相應增加存放裝置是一種線上操作，不會中斷資料庫伺服器。|
| <b>新的軟體部署（Azure） | 新功能首度發行或 bug 修正會在服務預定進行的維護過程中自動發生。 如需詳細資訊，請參閱[檔](https://docs.microsoft.com/azure/postgresql/concepts-monitoring#planned-maintenance-notification)，並檢查您的[入口網站](https://aka.ms/servicehealthpm)。|
| <b>次要版本升級 | 適用於 PostgreSQL 的 Azure 資料庫會自動將資料庫伺服器修補到 Azure 所決定的次要版本。 它會在服務的預定維護過程中發生。 這會導致短暫停機時間（以秒為單位），而資料庫伺服器會自動以新的次要版本重新開機。 如需詳細資訊，請參閱[檔](https://docs.microsoft.com/azure/postgresql/concepts-monitoring#planned-maintenance-notification)，並檢查您的[入口網站](https://aka.ms/servicehealthpm)。|


##  <a name="unplanned-downtime-mitigation"></a>未計畫的停機緩和

未預期的停機可能會因為無法預見的失敗而發生，包括基礎硬體錯誤、網路問題和軟體錯誤。 如果資料庫伺服器意外關閉，則會自動布建新的資料庫伺服器（以秒為單位）。 遠端存放會自動附加至新的資料庫伺服器。 于 postgresql 引擎會使用 WAL 和資料庫檔案來執行復原作業，並開啟資料庫伺服器以允許用戶端連接。 未認可的交易會遺失，且必須由應用程式重試。 雖然無法避免非計畫的停機時間，適用於 PostgreSQL 的 Azure 資料庫會在資料庫伺服器和儲存層自動執行復原作業，而不需要人為介入，藉此減少停機時間。 


![Azure 于 postgresql 中高可用性的觀點](./media/concepts-high-availability/azure-postgresql-built-in-high-availability.png)

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>非計畫的停機時間：失敗案例和服務復原
以下是一些失敗案例，以及適用於 PostgreSQL 的 Azure 資料庫如何自動復原：

| **案例** | **自動復原** |
| ---------- | ---------- |
| <B>資料庫伺服器失敗 | 如果資料庫伺服器因為某些基礎硬體錯誤而關閉，則會卸載作用中的連接，並中止任何傳遞的交易。 系統會自動部署新的資料庫伺服器，並將遠端資料儲存區附加至新的資料庫伺服器。 完成資料庫復原之後，用戶端就可以透過閘道連接到新的資料庫伺服器。 <br /> <br /> 使用於 postgresql 資料庫的應用程式必須以其偵測和重試中斷連接和失敗交易的方式建立。  當應用程式重試時，閘道會以透明方式將連接重新導向至新建立的資料庫伺服器。 |
| <B>儲存體失敗 | 應用程式不會看到任何與儲存體相關的問題（例如磁片故障或實體區塊損毀）都會有任何影響。 當資料儲存在3個複本中時，資料的複本會由存活的儲存空間提供服務。 會自動校正封鎖損毀。 如果遺失資料的複本，就會自動建立新的資料複本。 |

以下是一些需要使用者動作才能復原的失敗案例：

| **案例** | **復原方案** |
| ---------- | ---------- |
| <b>區域失敗 | 區域的失敗是罕見的事件。 不過，如果您需要防止區域失敗，您可以在其他區域中設定一或多個讀取複本，以進行嚴重損壞修復（DR）。 （如需詳細資訊，請參閱[這篇](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal)關於建立和管理讀取複本的文章）。 在區域層級失敗的情況下，您可以手動將另一個區域上設定的讀取複本升級為實際執行的資料庫伺服器。 |
| <b>邏輯/使用者錯誤 | 從使用者錯誤復原，例如不小心卸載資料表或錯誤更新的資料，會包含執行時間[點恢復](https://docs.microsoft.com/azure/postgresql/concepts-backup)（PITR），方法是還原並復原資料，直到發生錯誤之前的時間為止。<br> <br>  如果您只想還原資料庫或特定資料表的子集，而不是資料庫伺服器中的所有資料庫，您可以在新的實例中還原資料庫伺服器、透過[pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)匯出資料表，然後使用[pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html)將這些資料表還原至您的資料庫。 |



## <a name="summary"></a>摘要

適用於 PostgreSQL 的 Azure 資料庫提供資料庫伺服器的快速重新開機功能、多餘的儲存空間，以及從閘道有效率地路由傳送。 如需其他資料保護，您可以將備份設定為異地複寫，並在其他區域中部署一或多個讀取複本。 利用固有的高可用性功能，適用於 PostgreSQL 的 Azure 資料庫保護您的資料庫免于發生最常見的中斷，並提供領先業界、財務支援[99.99% 的執行時間 SLA](https://azure.microsoft.com/support/legal/sla/postgresql)。 這些可用性和可靠性功能都能讓 Azure 成為執行任務關鍵性應用程式的理想平臺。

## <a name="next-steps"></a>後續步驟
- 瞭解[Azure 區域](../availability-zones/az-overview.md)
- 了解如何[處理暫時性連線錯誤](concepts-connectivity.md)
- 了解如何[使用讀取複本來複寫資料](howto-read-replicas-portal.md)