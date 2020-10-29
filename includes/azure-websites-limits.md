---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 800dc50f82fa47228f1a88a143c5b515168812a6
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755773"
---
| 資源 | 免費 | 共用 | 基本 | 標準 | 進階 (v3) | 隔離 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| 每個 [Azure App Service 方案](../articles/app-service/overview-hosting-plans.md)<sup>1</sup>的 [Web、Mobile 或 API Apps](https://azure.microsoft.com/services/app-service/) |10 |100 |無限制<sup>2</sup> |無限制<sup>2</sup> |無限制<sup>2</sup> |無限制<sup>2</sup>|
| [App Service 計劃](../articles/app-service/overview-hosting-plans.md) |每個區域 10 個 |每個資源群組 10 個 |每個資源群組 100 個 |每個資源群組 100 個 |每個資源群組 100 個 |每個資源群組 100 個|
| 計算執行個體類型 |共用 |共用 |專用<sup>3</sup> |專用<sup>3</sup> |專用<sup>3</sup></p> |專用<sup>3</sup>|
| [擴充](../articles/app-service/manage-scale-up.md) (執行個體數目上限) |1 個共用 |1 個共用 |3 個專用<sup>3</sup> |10 個專用<sup>3</sup> |30 個專用<sup>3</sup>|100 個專用<sup>4</sup>|
| 儲存體<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> 若超過 250 GB，請提交支援要求。 |1 TB<sup>5</sup> <br/><br/> 可用的儲存體配額是 999 GB。 |
| CPU 時間 (5 分鐘)<sup>6</sup> |3 分鐘 |3 分鐘 |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a>|
| CPU 時間 (天)<sup>6</sup> |60 Minuten |240 Minuten |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |無限制，以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費</a> |
| 記憶體 (1 小時) |每個 App Service 方案 1,024 MB |每個應用程式 1,024 MB |N/A |N/A |N/A |N/A |
| 頻寬 |165 MB |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |無限制，套用 [資料傳輸費率](https://azure.microsoft.com/pricing/details/data-transfers/) |
| 應用程式架構 |32 位元 |32 位元 |32 位元/64 位元 |32 位元/64 位元 |32 位元/64 位元 |32 位元/64 位元 |
| 每個執行個體的 Web 通訊端<sup>7</sup> |5 |35 |350 |無限制 |無限制 |無限制 |
| IP 連線 | 600 | 600 | 取決於執行個體大小<sup>8</sup> | 取決於執行個體大小<sup>8</sup> | 取決於執行個體大小<sup>8</sup> | 16,000 |
| 並行 [偵錯工具連接數](../articles/app-service/troubleshoot-dotnet-visual-studio.md) (每個應用程式) |1 |1 |1 |5 |5 |5 |
| 每一訂用帳戶的 App Service 憑證<sup>9</sup>| 不支援 | 不支援 |10 |10 |10 |10 |
| 每個應用程式的自訂網域</a> |0 (僅限 azurewebsites.net 子網域)|500 |500 |500 |500 |500 |
| 自訂網域 [SSL 支援](../articles/app-service/configure-ssl-certificate.md) |不支援，依預設可使用 \*.azurewebsites.net 的萬用字元憑證|不支援，依預設可使用 \*.azurewebsites.net 的萬用字元憑證|無限制的 SNI SSL 連線 |包含無限制的 SNI SSL 和 1 個 IP SSL 連線 |包含無限制的 SNI SSL 和 1 個 IP SSL 連線 | 包含無限制的 SNI SSL 和 1 個 IP SSL 連線|
| 混合式連線 | | | 每個方案 5 個 | 每個方案 25 個 | 每個應用程式 200 個 | 每個應用程式 200 個 |
| [虛擬網路整合](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| 整合式負載平衡器 | |X |X |X |X |X<sup>10</sup> |
| [存取限制](../articles/app-service/networking-features.md#access-restrictions) | 每一應用程式 512 個規則 | 每一應用程式 512 個規則 | 每一應用程式 512 個規則 | 每一應用程式 512 個規則 | 每一應用程式 512 個規則 | 每一應用程式 512 個規則 |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [排定的備份](../articles/app-service/manage-backup.md) | | | | 排程每 2 小時備份，每天最多 12 次備份 (手動 + 排程) | 排程每小時備份，每天最多 50 次備份 (手動 + 排程) | 排程每小時備份，每天最多 50 次備份 (手動 + 排程) |
| [Autoscale](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [端點監視](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| 每個應用程式的[預備位置](../articles/app-service/deploy-staging-slots.md)| | | |5 |20 |20 |
| [在生產環境中測試](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [診斷記錄](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [驗證和授權](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [App Service 受控憑證 (公開預覽)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>應用程式和儲存體配額是依每個 App Service 方案為準，除非另有指示。  
<sup>2</sup>您可以在這些電腦上裝載的實際應用程式數目，會視應用程式的活動、電腦執行個體的大小，及對應的資源使用率而定。  
<sup>3</sup>專用的執行個體可有不同的大小。 如需詳細資訊，請參閱 [App Service 價格](https://azure.microsoft.com/pricing/details/app-service/)。  
<sup>4</sup>可應要求提高數量。  
<sup>5</sup>儲存體限制是跨相同 App Service 方案中所有應用程式的目前總大小。 單一資源群組和區域中所有 App Service 方案上的所有應用程式內容大小總計不能超過 500GB。  
<sup>6</sup>這些資源都會受到專用執行個體 (執行個體大小和執行個體數目) 上的實體資源限制。  
<sup>7</sup>如果您將基本層的一個應用程式調整為兩個執行個體，則其中每個執行個體有 350 個並行連線。 針對標準層和其以上版本，Web 通訊端沒有理論上的限制，但其他因素可能會限制 Web 通訊端的數目。 例如，允許的並行要求數上限 (由 `maxConcurrentRequestsPerCpu`所定義) 是：每個小型 VM 7,500 個、每個中型 VM 15,000 個 (7,500 x 2 個核心) 和每個大型 VM 75,000 個 (18,750 x 4 個核心)。  
<sup>8</sup>IP 連線數會以執行個體為基礎，並且取決於執行個體的大小：每個 B1/S1/P1V3 執行個體 1,920 個、每個 B2/S2/P2V3 執行個體3,968 個、每個 B3/S3/P3V3 執行個體 8,064 個。  
<sup>9</sup>您可以透過支援要求將每個訂用帳戶的 App Service 憑證配額限制增加到最多 200 個。  
<sup>10</sup>App Service 隔離式 SKU 能夠透過 Azure Load Balancer 在內部達成負載平衡 (ILB)，因此網際網路未提供公用連線。 因此，ILB 隔離式 App Service 的某些功能必須從具有 ILB 網路端點直接存取權的電腦才能使用。  
<sup>11</sup>在您的 App Service 執行個體中，以背景工作的方式隨選、依照排程或連續執行自訂可執行檔和/或指令碼。 若要連續執行 WebJobs，「永遠開啟」是必要選項。 可在 App Service 執行個體中執行的 WebJobs 數目沒有預先定義的限制。 但根據應用程式程式碼嘗試執行的動作，會有一些實際限制。

<sup>12</sup>不支援裸網域。 只發出標準憑證 (無法使用萬用字元憑證)。 限制為每個自訂網域只能有一個免費憑證。
