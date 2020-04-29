---
title: 使用 Azure 配對的區域確保商務持續性 & 嚴重損壞修復
description: 使用 Azure 區域配對確保應用程式恢復功能
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79248251"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>業務持續性和災害復原 (BCDR)：Azure 配對的區域

## <a name="what-are-paired-regions"></a>什麼是配對的區域？

Azure 區域包含一組在延遲定義的周邊內部署的資料中心，並透過專用的低延遲網路連線。  這可確保 Azure 區域內的 Azure 服務能提供最佳的效能和安全性。  

Azure 地理位置會定義一個世界中至少包含一個 Azure 區域的區域。 地理位置定義了一個離散市場，通常包含兩個或多個區域，以保留資料的常駐和合規性界限。  [在這裡](https://azure.microsoft.com/global-infrastructure/regions/)尋找 Azure 全球基礎結構的詳細資訊

區域配對是由相同地理位置內的兩個區域所組成。 Azure 會跨區域配對序列化平臺更新（預定的維護），以確保一次只會更新一組中的一個區域。 如果中斷會影響多個區域，則每個配對中至少有一個區域會優先進行復原。

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

某些 Azure 服務會進一步利用配對的區域，以確保業務持續性，並防止資料遺失。  Azure 提供數個[儲存體解決方案](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region)，可利用配對的區域，以確保資料可用性。 例如， [Azure 異地複寫儲存體](./storage/common/storage-redundancy.md#geo-redundant-storage)（GRS）會自動將資料複寫到次要區域，確保即使在主要區域無法復原的情況下，資料仍持久。 

請注意，並非所有的 Azure 服務都會自動複寫資料，而所有 Azure 服務也會自動從失敗的區域切換回其配對。  在這種情況下，復原和複寫必須由客戶進行設定。

## <a name="can-i-select-my-regional-pairs"></a>我可以選取區域配對嗎？

否。 某些 Azure 服務依賴區域配對，例如 Azure 的[多餘儲存體](./storage/common/storage-redundancy.md)。 這些服務不允許您建立新的區域配對。  同樣地，因為 Azure 會針對區域配對來控制預定的維護和復原優先順序，所以您不能定義自己的區域配對來利用這些服務。 不過，您可以建立自己的嚴重損壞修復解決方案，方法是在任意數目的區域中建立服務，並運用 Azure 服務來配對它們。 

例如，您可以使用 Azure 服務（例如[AzCopy](./storage/common/storage-use-azcopy-v10.md) ），將資料備份排程至不同區域中的儲存體帳戶。  客戶可以使用[Azure DNS 和 Azure 流量管理員](./networking/disaster-recovery-dns-traffic-manager.md)，為其應用程式設計具有彈性的架構，以在主要區域遺失的情況下繼續進行。

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>我是否限制在區域配對內使用服務？

否。 雖然指定的 Azure 服務可能會依賴區域配對，但是您可以將其他服務裝載在滿足您業務需求的任何區域中。  Azure GRS 儲存體解決方案可能會將加拿大中部中的資料與加拿大東部中的對等互連，同時使用位於美國東部的計算資源。  

## <a name="must-i-use-azure-regional-pairs"></a>我必須使用 Azure 區域配對嗎？

否。 客戶可以利用 Azure 服務來設計有彈性的服務，而不需依賴 Azure 的區域配對。  不過，我們建議您設定跨區域配對的商務持續性嚴重損壞修復（BCDR），以受益于[隔離](./security/fundamentals/isolation-choices.md)並改善[可用性](./availability-zones/az-overview.md)。 對於支援多個作用中區域的應用程式，我們建議儘可能同時使用相同區域組中的兩個區域。 這可確保應用程式的最佳可用性，並在發生嚴重損壞時減少復原時間。 可能的話，請設計您的應用程式，以獲得[最大的復原能力](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)和輕鬆的嚴重損壞[修復](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)

## <a name="azure-regional-pairs"></a>Azure 區域配對

| [地理位置] | 區域配對 A | 區域配對 B  |
|:--- |:--- |:--- |
| 亞太地區 |東亞（香港特別行政區） | 東南亞（新加坡） |
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
> - 印度西部僅以一個方向配對。 印度西部的次要地區是印度南部，但印度南部的次要區域是印度中部。
> - 巴西南部是唯一的，因為它與地理位置外的區域配對。 巴西南部的次要地區是美國中南部。 美國中南部的次要地區不是巴西南部。


## <a name="an-example-of-paired-regions"></a>配對的區域範例
下圖說明使用區域配對進行嚴重損壞修復的假設應用程式。 綠色數位會反白顯示三項 Azure 服務（Azure 計算、儲存體和資料庫）的跨區域活動，以及它們如何設定為跨區域複寫。 橘色數字則強調跨配對區域部署的獨特優點。

![配對區域優點概觀](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

圖 2 – 假設的 Azure 區域配對

## <a name="cross-region-activities"></a>跨區域活動
如圖 2 所示。

1. **Azure 計算（IaaS）** –您必須事先布建額外的計算資源，以確保在發生嚴重損壞時可在另一個區域中使用資源。 如需詳細資訊，請參閱 [Azure 復原技術指導](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md)。 

2. **Azure 儲存體**-如果您使用受控磁片，請瞭解使用 Azure 備份的[跨區域備份](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines)，並使用 Azure Site Recovery 將[vm](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)從一個區域複寫到另一個區域。 如果您使用的是儲存體帳戶，則在建立 Azure 儲存體帳戶時，預設會設定異地冗余儲存體（GRS）。 使用 GRS 時，系統會在主要區域內將您的資料自動複寫三次，並在配對區域中複寫三次。 如需詳細資訊，請參閱 [Azure 儲存體備援選項](storage/common/storage-redundancy.md)。

3. **Azure SQL Database** –透過 Azure SQL Database 異地複寫，您可以將交易的非同步複寫設定至世界各地的任何區域;不過，我們建議您在配對的區域中部署這些資源，以進行大部分的嚴重損壞修復案例。 如需詳細資訊，請參閱 [Azure SQL Database 中的異地複寫](sql-database/sql-database-geo-replication-overview.md)。

4. **Azure Resource Manager**：Resource Manager 原本就會提供跨區域的元件邏輯隔離。 這表示某個區域中的邏輯失敗不太可能會影響另一個區域。

## <a name="benefits-of-paired-regions"></a>配對區域的優點

5. **實體隔離**–在可能的情況下，Azure 會在區域配對中的資料中心之間至少使用300英里的分隔，雖然這在所有地理位置中並不實際或不可行。 實體資料中心分隔能夠降低自然災害、社會動亂、電力中斷或實體網路中斷同時影響兩個區域的可能性。 隔離會受限於地理位置內的條件約束 (地理位置大小、電源/網路基礎結構可用性、法規等等)。  

6. **平臺提供**的複寫-異地多餘儲存體之類的部分服務會提供自動複寫到配對的區域。

7. **區域復原順序**–萬一發生廣泛的中斷，一個區域的復原會優先于每個配對。 跨配對區域部署的應用程式能夠保障其中一個區域優先復原。 如果在未配對的區域中部署應用程式，就可能會發生復原延遲的情況；最壞的情況是，這兩個選定的區域可能都不會復原。

8. **順序更新**-規劃的 Azure 系統更新會依序（而不是同時）推出至配對的區域，以將停機時間降到最低、錯誤的影響，以及在不佳的更新情況下發生邏輯失敗的情況。

9. **資料**存留-區域位於與其配對相同的地理位置內（巴西南部除外），以符合稅務和執法管轄區用途的資料常駐需求。
