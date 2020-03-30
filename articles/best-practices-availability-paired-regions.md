---
title: 使用 Azure 配對區域確保業務連續性&災害復原
description: 使用 Azure 區域配對確保應用程式恢復能力
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248251"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>業務持續性和災害復原 (BCDR)：Azure 配對的區域

## <a name="what-are-paired-regions"></a>什麼是配對的區域？

Azure 區域由部署在延遲定義的週邊部署並通過專用低延遲網路連接的一組資料中心組成。  這可確保 Azure 區域中的 Azure 服務提供最佳的性能和安全性。  

Azure 地理環境定義包含至少一個 Azure 區域的世界區域。 地理位置定義一個離散市場，通常包含兩個或多個區域，以保留資料駐留性和合規性邊界。  在此處查找有關 Azure[全球基礎結構的詳細資訊](https://azure.microsoft.com/global-infrastructure/regions/)

區域對由同一地理位置內的兩個區域組成。 Azure 跨區域對序列化平臺更新（計畫維護），確保每個對中只有一個區域一次更新。 如果中斷影響多個區域，則每個對中至少有一個區域將優先進行恢復。

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

某些 Azure 服務進一步利用配對區域，以確保業務連續性並防止資料丟失。  Azure 提供了多個[存儲解決方案](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region)，這些解決方案利用配對區域來確保資料可用性。 例如[，Azure 異地冗余存儲](./storage/common/storage-redundancy.md#geo-redundant-storage)（GRS） 會自動將資料複製到次要區域，確保即使在主區域不可恢復的情況下，資料也是持久的。 

請注意，並非所有 Azure 服務都自動複製資料，也不是所有 Azure 服務都會自動從失敗區域回退到其對。  在這種情況下，恢復和複製必須由客戶配置。

## <a name="can-i-select-my-regional-pairs"></a>我可以選擇區域對嗎？

否。 某些 Azure 服務依賴于區域對，例如 Azure 的[冗余存儲](./storage/common/storage-redundancy.md)。 這些服務不允許創建新的區域配對。  同樣，由於 Azure 控制區域對的計畫維護和恢復優先順序，因此無法定義自己的區域對以利用這些服務。 但是，您可以通過在任意數量的區域中構建服務並利用 Azure 服務將它們配對來創建自己的災害復原解決方案。 

例如，可以使用 Azure 服務（如[AzCopy）](./storage/common/storage-use-azcopy-v10.md)將資料備份計畫到其他區域中的存儲帳戶。  使用[Azure DNS 和 Azure 流量管理器](./networking/disaster-recovery-dns-traffic-manager.md)，客戶可以為其應用程式設計一個彈性體系結構，這些體系結構將在主區域丟失後存活下來。

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>我僅限於使用區域對中的服務嗎？

否。 雖然給定的 Azure 服務可能依賴于區域對，但您可以在滿足業務需求的任何區域中託管其他服務。  Azure GRS 存儲解決方案可能會將加拿大中部的資料與加拿大東部的對等體資料配對，同時使用位於美國東部的計算資源。  

## <a name="must-i-use-azure-regional-pairs"></a>我必須使用 Azure 區域對嗎？

否。 客戶可以利用 Azure 服務來構建彈性服務，而無需依賴 Azure 的區域對。  但是，我們建議您跨區域對配置業務連續性災害復原 （BCDR），以便從[隔離](./security/fundamentals/isolation-choices.md)中獲益並提高[可用性](./availability-zones/az-overview.md)。 對於支援多個作用中區域的應用程式，我們建議儘可能同時使用相同區域組中的兩個區域。 這可確保應用程式的最佳可用性，並在發生災難時最大限度地減少恢復時間。 盡可能設計應用程式，以實現[最大的恢復能力和](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)[災害復原](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)的簡便性。

## <a name="azure-regional-pairs"></a>Azure 區域對

| [地理位置] | 區域對 A | 區域對 B  |
|:--- |:--- |:--- |
| 亞太地區 |東亞（香港） | 東南亞（新加坡） |
| 澳大利亞 |澳大利亞東部 |澳大利亞東南部 |
| 澳大利亞 |澳大利亞中部 |澳大利亞中部 2 |
| 巴西 |巴西南部 |美國中南部 |
| Canada |加拿大中部 |加拿大東部 |
| 中國 |中國北部 |中國東部|
| 中國 |中國北部 2 |中國東部 2|
| 歐洲 |北歐（愛爾蘭） |西歐（荷蘭） |
| 法國 |法國中部|法國南部|
| 德國 |德國中部 |德國東北部 |
| 印度 |印度中部 |印度南部 |
| 印度 |印度西部 |印度南部 |
| 日本 |日本東部 |日本西部 |
| 南韓 |南韓中部 |南韓南部 |
| 北美洲 |美國東部 |美國西部 |
| 北美洲 |美國東部 2 |美國中部 |
| 北美洲 |美國中北部 |美國中南部 |
| 北美洲 |美國西部 2 |美國中西部 |
| 挪威 | 挪威東部 | 挪威西部 |
| 南非 | 南非北部 |南非西部 |
| 瑞士 | 瑞士北部 |瑞士西部 |
| 英國 |英國西部 |英國南部 |
| 阿拉伯聯合大公國 | 阿拉伯聯合大公國北部 | 阿拉伯聯合大公國中部
| 美國國防部 |US DoD 東部 |US DoD 中部 |
| 美國政府 |US Gov 亞利桑那州 |US Gov 德克薩斯州 |
| 美國政府 |US Gov 愛荷華州 |US Gov 維吉尼亞州 |
| 美國政府 |US Gov 維吉尼亞州 |US Gov 德克薩斯州 |

> [!Important]
> - 西印度只朝一個方向配對。 印度西部的次要地區是印度南部，但印度南部的次要區域是印度中部。
> - 巴西南部是獨一無二的，因為它與地理以外的區域配對。 巴西南部的第二個地區是美國中南部。 美國中南部的次生地區不是巴西南部。


## <a name="an-example-of-paired-regions"></a>配對的區域範例
下圖演示了使用區域對進行災害復原的假設應用程式。 綠色數位突出顯示三個 Azure 服務的跨區域活動（Azure 計算、存儲和資料庫），以及如何將它們配置為跨區域複製。 橘色數字則強調跨配對區域部署的獨特優點。

![配對區域優點概觀](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

圖 2 – 假設的 Azure 區域配對

## <a name="cross-region-activities"></a>跨區域活動
如圖 2 所示。

1. **Azure 計算 （IaaS）** – 您必須提前預配其他計算資源，以確保資源在災難期間在另一個區域可用。 如需詳細資訊，請參閱 [Azure 復原技術指導](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md)。 

2. **Azure 存儲**- 如果使用託管磁片，請使用 Azure[備份瞭解跨區域備份](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines)，以及通過 Azure 網站恢復[將 VM](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)從一個區域複製到另一個區域。 如果使用存儲帳戶，則預設情況下在創建 Azure 存儲帳戶時配置異地冗余存儲 （GRS）。 使用 GRS 時，系統會在主要區域內將您的資料自動複寫三次，並在配對區域中複寫三次。 如需詳細資訊，請參閱 [Azure 儲存體備援選項](storage/common/storage-redundancy.md)。

3. **Azure SQL 資料庫**– 使用 Azure SQL 資料庫異地複製，可以將事務的非同步複製配置為世界上任何區域;因此，可以將事務非同步複製配置為世界上任何區域。但是，我們建議您在配對區域中部署這些資源，以便執行大多數災害復原方案。 如需詳細資訊，請參閱 [Azure SQL Database 中的異地複寫](sql-database/sql-database-geo-replication-overview.md)。

4. **Azure Resource Manager**：Resource Manager 原本就會提供跨區域的元件邏輯隔離。 這表示某個區域中的邏輯失敗不太可能會影響另一個區域。

## <a name="benefits-of-paired-regions"></a>配對區域的優點

5. **物理隔離**– 如果可能，Azure 更喜歡區域對中的資料中心之間至少分離 300 英里，儘管這在所有地理位置都不可行或可能。 實體資料中心分隔能夠降低自然災害、社會動亂、電力中斷或實體網路中斷同時影響兩個區域的可能性。 隔離會受限於地理位置內的條件約束 (地理位置大小、電源/網路基礎結構可用性、法規等等)。  

6. **平臺提供的複製**- 某些服務（如異地冗余存儲）提供對配對區域的自動複製。

7. **區域恢復順序**– 在發生廣泛中斷時，每個對中優先恢復一個區域。 跨配對區域部署的應用程式能夠保障其中一個區域優先復原。 如果在未配對的區域中部署應用程式，就可能會發生復原延遲的情況；最壞的情況是，這兩個選定的區域可能都不會復原。

8. **順序更新**– 計畫 Azure 系統更新按順序（不是同時）推出到配對區域，以最大程度地減少停機時間、Bug 的影響，並在極少數情況下發生錯誤更新時出現邏輯故障。

9. **資料駐留**– 區域位於與其對（巴西南部除外）相同的地理位置內，以滿足稅務和執法管轄目的的資料居住要求。
