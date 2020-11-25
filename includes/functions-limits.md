---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 826fccad043b067ce86d5f56eaebc6ee48b532d1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025722"
---
| 資源 |[取用方案](../articles/azure-functions/functions-scale.md#consumption-plan)|[進階方案](../articles/azure-functions/functions-scale.md#premium-plan)|[專用方案](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|預設[逾時持續時間](../articles/azure-functions/functions-scale.md#timeout) (分鐘) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|最大[逾時持續時間](../articles/azure-functions/functions-scale.md#timeout) (分鐘) |10 | 無限制<sup>7</sup> | 無限制<sup>2</sup> | 無限制 | 無限制 |
| 連出連線上限 (每個執行個體) | 600 個作用中 (總計 1200) | 無限制 | 無限制 | 無限制 | 無限制 |
| 要求大小上限 (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | 取決於叢集 |
| 查詢字串長度上限<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | 取決於叢集 |
| 要求 URL 長度上限<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | 取決於叢集 |
|每個執行個體的 [ACU](../articles/virtual-machines/acu.md) | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [AKS 定價](https://azure.microsoft.com/pricing/details/container-service/) |
| 記憶體上限 (每個執行個體的 GB) | 1.5 | 3.5-14 | 1.75-14 | 3.5 - 14 | 支援任何節點 |
| 每個方案的函式應用程式 |100 |100 |無限制<sup>4</sup> | 無限制 | 無限制 |
| [App Service 方案](../articles/app-service/overview-hosting-plans.md) | 每個[區域](https://azure.microsoft.com/global-infrastructure/regions/) 100 個 |每個資源群組 100 個 |每個資源群組 100 個 | - | - |
| 儲存體<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | n/a |
| 每個應用程式的自訂網域</a> |500<sup>6</sup> |500 |500 | 500 | n/a |
| 自訂網域 [SSL 支援](../articles/app-service/configure-ssl-bindings.md) |包含無限制的 SNI SSL 連線 | 包含無限制的 SNI SSL 和 1 個 IP SSL 連線 |包含無限制的 SNI SSL 和 1 個 IP SSL 連線 | 包含無限制的 SNI SSL 和 1 個 IP SSL 連線 | n/a |

<sup>1</sup>根據預設，App Service 方案中 Functions 1.x 執行階段的逾時不受限制。  
<sup>2</sup> 需要將 App Service 方案設定為 [Always On](../articles/azure-functions/functions-scale.md#always-on)。 以標準[費率](https://azure.microsoft.com/pricing/details/app-service/)付費。  
<sup>3</sup> 這些限制[設定於主機](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)。  
<sup>4</sup> 您可以裝載的實際函式應用程式數目，會視應用程式的活動、電腦執行個體的大小，及對應的資源使用率而定。  
<sup>5</sup> 儲存體限制是在暫存儲存體中相同 App Service 方案中所有應用程式的內容總大小。 取用方案會使用 Azure 檔案儲存體作為暫存儲存體。  
<sup>6</sup> 當您的函式應用程式裝載於[取用方案](../articles/azure-functions/functions-scale.md#consumption-plan)時，僅支援 CNAME 選項。 對於[進階方案](../articles/azure-functions/functions-scale.md#premium-plan)或 [App Service 方案](../articles/azure-functions/functions-scale.md#app-service-plan)中的函式應用程式，您可以使用 CNAME 或 A 記錄對應自訂網域。  
<sup>7</sup> 保證最多60 分鐘。  
<sup>8</sup>背景工作角色是裝載客戶應用程式的角色。 背景工作角色可以三個固定的大小提供：一個 vCPU/3.5 GB RAM；兩個 vCPU/7 GB 的 RAM；四個 vCPU/14 GB RAM。