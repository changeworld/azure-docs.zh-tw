---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198338"
---
| 資源 | [消費計畫](../articles/azure-functions/functions-scale.md#consumption-plan) | [保費計畫](../articles/azure-functions/functions-scale.md#premium-plan) | [應用服務方案](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| 相應放大 | 事件驅動 | 事件驅動 | [手動/自動縮放](../articles/app-service/manage-scale-up.md) | 
| 最大實例 | 200 | 100 | 10-20 |
|預設[超時持續時間](../articles/azure-functions/functions-scale.md#timeout)（分鐘） |5 | 30 |30<sup>2</sup> |
|最大[超時持續時間](../articles/azure-functions/functions-scale.md#timeout)（最小） |10 | 無界<sup>8</sup> | 無界<sup>3</sup> |
| 最大出站連接（每個實例） | 600 活動（總計 1200） | 無限制 | 無限制 |
| 最大請求大小 （MB）<sup>4</sup> | 100 | 100 | 100 |
| 最大查詢字串長度<sup>4</sup> | 4096 | 4096 | 4096 |
| 最大請求 URL 長度<sup>4</sup> | 8192 | 8192 | 8192 |
| 每個實例[的 ACU](../articles/virtual-machines/windows/acu.md) | 100 | 210-840 | 100-840 |
| 最大記憶體（每個實例的 GB） | 1.5 | 3.5-14 | 1.75-14 |
| 每個計畫的函數應用 |100 |100 |無界<sup>5</sup> |
| [應用服務方案](../articles/app-service/overview-hosting-plans.md) | 每個區域 100[個](https://azure.microsoft.com/global-infrastructure/regions/) |每個資源群組 100 個 |每個資源群組 100 個 |
| 存儲<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| 每個應用程式的自訂網域</a> |500<sup>7</sup> |500 |500 |
| 自訂網域 [SSL 支援](../articles/app-service/configure-ssl-bindings.md) |包括未綁定 SNI SSL 連線 | 包含無限制 SNI SSL 和 1 個 IP SSL 連線 |包含無限制 SNI SSL 和 1 個 IP SSL 連線 | 

<sup>1</sup>有關各種應用服務方案選項的特定限制，請參閱[應用服務方案限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。  
<sup>2</sup>預設情況下，應用服務方案中的函數 1.x 運行時的超時是無界的。  
<sup>3</sup>要求將應用服務方案設置為["始終打開](../articles/azure-functions/functions-scale.md#always-on)"。 以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)支付。  
<sup>4</sup>這些限制在[主機中設置](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)。  
<sup>5</sup>您可以承載的函數應用的實際數量取決於應用的活動、電腦實例的大小以及相應的資源利用率。  
<sup>6</sup>存儲限制是同一應用服務方案中所有應用的臨時存儲中的總內容大小。 消耗計畫使用 Azure 檔進行臨時存儲。  
<sup>7</sup>當函數應用託管在[消耗計畫中](../articles/azure-functions/functions-scale.md#consumption-plan)時，僅支援 CNAME 選項。 對於[高級計畫](../articles/azure-functions/functions-scale.md#premium-plan)或[應用服務方案中的](../articles/azure-functions/functions-scale.md#app-service-plan)功能應用，可以使用 CNAME 或 A 記錄映射自訂域。  
<sup>8</sup>保證長達 60 分鐘。