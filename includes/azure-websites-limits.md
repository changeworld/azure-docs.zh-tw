---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 505e2d8eec20853fba3743b40cbe289585d14d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78305021"
---
| 資源 | 免費 | 共用 | 基本 | 標準 | 進階 (v2) | 隔離 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| 每個[Azure 應用服務方案](../articles/app-service/overview-hosting-plans.md)<sup>1</sup>的[Web、移動或 API 應用](https://azure.microsoft.com/services/app-service/) |10 |100 |無限制<sup>2</sup> |無限制<sup>2</sup> |無限制<sup>2</sup> |無限制<sup>2</sup>|
| [應用服務方案](../articles/app-service/overview-hosting-plans.md) |每個區域 10 個 |每個資源群組 10 個 |每個資源群組 100 個 |每個資源群組 100 個 |每個資源群組 100 個 |每個資源群組 100 個|
| 計算執行個體類型 |共用 |共用 |專用<sup>3</sup> |專用<sup>3</sup> |專用<sup>3</sup></p> |專用<sup>3</sup>|
| [橫向擴展](../articles/app-service/manage-scale-up.md)（最大實例） |1 個共用 |1 個共用 |3 個專用<sup>3</sup> |10 個專用<sup>3</sup> |30 專用<sup>3</sup>|100 個專用<sup>4</sup>|
| 儲存體<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU 時間（5 分鐘）<sup>6</sup> |3 分鐘 |3 分鐘 |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付款</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付款</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付款</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付款</a>|
| CPU 時間 (天)<sup>6</sup> |60 Minuten |240 Minuten |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付款</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付款</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付款</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付款</a> |
| 記憶體 (1 小時) |每個應用服務方案 1，024 MB |每個應用 1，024 MB |N/A |N/A |N/A |N/A |
| 頻寬 |165 MB |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |
| 應用程式架構 |32 位元 |32 位元 |32 位元/64 位元 |32 位元/64 位元 |32 位元/64 位元 |32 位元/64 位元 |
| 每個<sup>實例</sup>7 的 Web 通訊端 |5 |35 |350 |無限制 |無限制 |無限制 |
| IP 連接 | 600 | 600 | 取決於實例大小<sup>8</sup> | 取決於實例大小<sup>8</sup> | 取決於實例大小<sup>8</sup> | 16,000 |
| 並行 [偵錯工具連接數](../articles/app-service/troubleshoot-dotnet-visual-studio.md) (每個應用程式) |1 |1 |1 |5 |5 |5 |
| 每個訂閱的應用服務證書<sup>9</sup>| 不支援 | 不支援 |10 |10 |10 |10 |
| 每個應用程式的自訂網域</a> |0 (僅限 azurewebsites.net 子網域)|500 |500 |500 |500 |500 |
| 自訂網域 [SSL 支援](../articles/app-service/configure-ssl-certificate.md) |不支援，預設情況下，*.azurewebsites.net的萬用字元證書可用|不支援，預設情況下，*.azurewebsites.net的萬用字元證書可用|無限制的 SNI SSL 連線 |包含無限制的 SNI SSL 和 1 個 IP SSL 連線 |包含無限制的 SNI SSL 和 1 個 IP SSL 連線 | 包含無限制的 SNI SSL 和 1 個 IP SSL 連線|
| 每個計畫的混合連接 | | | 5 | 25 | 200 | 200 |
| 集成負載等化器 | |X |X |X |X |X<sup>10</sup> |
| [始終打開](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [計畫備份](../articles/app-service/manage-backup.md) | | | | 計畫備份每 2 小時一次，每天最多 12 次備份（手動 + 計畫） | 每小時計畫備份，每天最多 50 次備份（手動 + 計畫） | 每小時計畫備份，每天最多 50 次備份（手動 + 計畫） |
| [自動縮放](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [網路作業](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [端點監視](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| 每個應用[暫存插槽](../articles/app-service/deploy-staging-slots.md)| | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>應用程式和儲存體配額是依每個 App Service 方案為準，除非另有指示。  
<sup>2</sup>您可以在這些電腦上裝載的實際應用程式數目，會視應用程式的活動、電腦執行個體的大小，及對應的資源使用率而定。  
<sup>3</sup>專用的執行個體可有不同的大小。 如需詳細資訊，請參閱 [App Service 價格](https://azure.microsoft.com/pricing/details/app-service/)。  
<sup>4</sup>可應要求提供更多服務。  
<sup>5</sup>存儲限制是同一應用服務方案中所有應用的總內容大小。 單個資源組和地區中所有應用服務方案的所有應用的總內容大小不能超過 500GB。  
<sup>6</sup>這些資源都會受到專用執行個體 (執行個體大小和執行個體數目) 上的實體資源限制。  
<sup>7</sup>如果您將基本層的一個應用程式調整為兩個執行個體，則其中每個執行個體有 350 個並行連線。 對於標準層及以上，Web 通訊端沒有理論限制，但其他因素可能會限制 Web 通訊端的數量。 例如，允許的最大併發請求（由`maxConcurrentRequestsPerCpu`定義）為：每個小型 VM 7，500 個，每個中型 VM（7，500 x 2 個內核）15，000 個，每個大型 VM（18，750 x 4 個內核）75，000 個。  
<sup>8</sup>最大 IP 連接是每個實例，取決於實例大小：每個 B1/S1/P1V2 實例 1，920 個，每個 B2/S2/P2V2 實例 3，968 個，每個 B3/S3/P3V2 實例 8，064 個。  
<sup>9</sup>每個訂閱的應用服務證書配額限制可以通過支援請求增加到最大限制 200。  
<sup>10</sup>應用服務隔離 SKU 可以通過 Azure 負載等化器實現內部負載平衡 （ILB），因此沒有來自 Internet 的公共連接。 因此，ILB 隔離式 App Service 的某些功能必須從具有 ILB 網路端點直接存取權的電腦才能使用。  
<sup>11</sup>按需、按計劃運行自訂可執行檔和/或腳本，或作為應用服務實例中的背景工作連續運行。 若要連續執行 WebJobs，「永遠開啟」是必要選項。 在應用服務實例中運行的 Web 作業數量沒有預定義限制。 有一些實際的限制取決於應用程式代碼正在嘗試做什麼。  
