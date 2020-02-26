---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 83bf357fff8fd3cfb8c0e69af92b36710287898f
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586697"
---
| 資源 | 免費 | 共用 | 基本 | 標準 | 進階 (v2) | 隔離 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| 每個[Azure App Service 方案](../articles/app-service/overview-hosting-plans.md)<sup>1</sup>的[Web、行動或 API 應用程式](https://azure.microsoft.com/services/app-service/) |10 |100 |無限制<sup>2</sup> |無限制<sup>2</sup> |無限制<sup>2</sup> |無限制<sup>2</sup>|
| [App Service 計劃](../articles/app-service/overview-hosting-plans.md) |每個區域 10 個 |每個資源群組 10 個 |每個資源群組 100 個 |每個資源群組 100 個 |每個資源群組 100 個 |每個資源群組 100 個|
| 計算執行個體類型 |共用 |共用 |專用<sup>3</sup> |專用<sup>3</sup> |專用<sup>3</sup></p> |專用<sup>3</sup>|
| [相應](../articles/app-service/manage-scale-up.md)放大（最大實例） |1 個共用 |1 個共用 |3 個專用<sup>3</sup> |10 個專用<sup>3</sup> |30專用<sup>3</sup>|100 個專用<sup>4</sup>|
| 儲存體<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU 時間（5分鐘）<sup>6</sup> |3 分鐘 |3 分鐘 |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a>|
| CPU 時間 (天)<sup>6</sup> |60 Minuten |240 Minuten |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |
| 記憶體 (1 小時) |1024 MB/App Service 方案 |每個應用程式 1024 MB |N/A |N/A |N/A |N/A |
| 頻寬 |165 MB |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |
| 應用程式架構 |32 位元 |32 位元 |32 位元/64 位元 |32 位元/64 位元 |32 位元/64 位元 |32 位元/64 位元 |
| 每個實例的 Web 通訊端<sup>7</sup> |5 |35 |350 |無限制 |無限制 |無限制 |
| IP 連線 | 600 | 600 | 取決於實例大小<sup>8</sup> | 取決於實例大小<sup>8</sup> | 取決於實例大小<sup>8</sup> | 16,000 |
| 並行 [偵錯工具連接數](../articles/app-service/troubleshoot-dotnet-visual-studio.md) (每個應用程式) |1 |1 |1 |5 |5 |5 |
| App Service 每個訂用帳戶的憑證<sup>9</sup>| 不支援 | 不支援 |10 |10 |10 |10 |
| 每個應用程式的自訂網域</a> |0 (僅限 azurewebsites.net 子網域)|500 |500 |500 |500 |500 |
| 自訂網域 [SSL 支援](../articles/app-service/configure-ssl-certificate.md) |不支援，預設會提供 *. azurewebsites.net 的萬用字元憑證。|不支援，預設會提供 *. azurewebsites.net 的萬用字元憑證。|無限制的 SNI SSL 連線 |包含無限制的 SNI SSL 和 1 個 IP SSL 連線 |包含無限制的 SNI SSL 和 1 個 IP SSL 連線 | 包含無限制的 SNI SSL 和 1 個 IP SSL 連線|
| 每個計畫的混合式連接 | | | 5 | 25 | 200 | 200 |
| 整合式負載平衡器 | |X |X |X |X |X<sup>10</sup> |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [排定的備份](../articles/app-service/manage-backup.md) | | | | 排定的備份每隔2小時，最多每日12次備份（手動 + 排程） | 排定的備份每小時，最多每天50備份（手動 + 排程） | 排定的備份每小時，最多每天50備份（手動 + 排程） |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [Webjob](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [端點監視](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [預備位置](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>應用程式和儲存體配額是依每個 App Service 方案為準，除非另有指示。  
<sup>2</sup>您可以在這些電腦上裝載的實際應用程式數目，會視應用程式的活動、電腦執行個體的大小，及對應的資源使用率而定。  
<sup>3</sup>專用的執行個體可有不同的大小。 如需詳細資訊，請參閱 [App Service 價格](https://azure.microsoft.com/pricing/details/app-service/)。  
<sup>4</sup>要求時可以有更多的。  
<sup>5</sup>儲存體限制是相同 App service 方案中所有應用程式的總內容大小。 單一資源群組和區域中所有 App service 方案的所有應用程式的內容大小總計不能超過500GB。  
<sup>6</sup>這些資源都會受到專用執行個體 (執行個體大小和執行個體數目) 上的實體資源限制。  
<sup>7</sup>如果您將基本層的一個應用程式調整為兩個執行個體，則其中每個執行個體有 350 個並行連線。 針對標準層和以上版本，web 通訊端沒有理論上的限制，但其他因素可能會限制 web 通訊端的數目。 例如，允許的最大並行要求數（由 `maxConcurrentRequestsPerCpu`所定義）為：每個小型 VM 7500、每中型 VM 15000 （7500 x 2 核心）和每個大型 VM 75000 （18750 x 4 核心）。  
<sup>8</sup>IP 連線數目上限為每個實例，且取決於實例大小：1920每 B1/S1/P1V2 實例，每個 B2/S2/P2V2 實例3968，8064每個 B3/S3/P3V2 實例。  
<sup>9</sup>每個訂用帳戶的 App Service 憑證配額限制可以透過支援要求增加為200的最大限制。  
<sup>10</sup>App Service 隔離式方案 Sku 可以透過 Azure Load Balancer 進行內部負載平衡（ILB），因此沒有網際網路的公用連線能力。 因此，ILB 隔離式 App Service 的某些功能必須從具有 ILB 網路端點直接存取權的電腦才能使用。  
<sup>11</sup>在您的 App Service 實例內，以背景工作的形式依需求、按照排程或連續執行自訂可執行檔和/或腳本。 若要連續執行 WebJobs，「永遠開啟」是必要選項。 在 App Service 實例中可執行檔 Webjob 數目沒有預先定義的限制。 有一些實際的限制取決於應用程式程式碼嘗試執行的動作。  
