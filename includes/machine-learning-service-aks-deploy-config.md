---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79485947"
---
檔中的專案會對應至 AksWebservice 的參數。 [deploy_configuration。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py) `deploymentconfig.json` 下表描述 JSON 檔中的實體與方法的參數之間的對應：

| JSON 實體 | 方法參數 | 說明 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 若為 AKS，此值必須`aks`是。 |
| `autoScaler` | NA | 包含自動調整的設定元素。 請參閱自動調整程式資料表。 |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 是否要啟用 web 服務的自動調整。 `numReplicas`  = 如果`0`為`True`，則為，否則為`False`。 |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 自動調整此 web 服務時所要使用的容器數目下限。 預設值`1`：。 |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 自動調整此 web 服務時所要使用的容器數目上限。 預設值`10`：。 |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 自動調整程式嘗試調整此 web 服務的頻率。 預設值`1`：。 |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | 自動調整程式應該嘗試為此 web 服務維護的目標使用率（以百分比100為單位）。 預設值`70`：。 |
| `dataCollection` | NA | 包含資料收集的設定元素。 |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | 是否要啟用 web 服務的模型資料收集。 預設值`False`：。 |
| `authEnabled` | `auth_enabled` | 是否要啟用 web 服務的金鑰驗證。 和`tokenAuthEnabled` `authEnabled`都不能`True`是。 預設值`True`：。 |
| `tokenAuthEnabled` | `token_auth_enabled` | 是否要啟用 web 服務的權杖驗證。 和`tokenAuthEnabled` `authEnabled`都不能`True`是。 預設值`False`：。 |
| `containerResourceRequirements` | NA | CPU 和記憶體實體的容器。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 要為此 web 服務配置的 CPU 核心數目。 設置`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 要為此 web 服務配置的記憶體數量（以 GB 為單位）。 預設`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | 是否要啟用 web 服務的 Application Insights 記錄。 預設值`False`：。 |
| `scoringTimeoutMs` | `scoring_timeout_ms` | 對 web 服務評分呼叫強制執行的時間。 預設值`60000`：。 |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 此 web 服務每個節點的並行要求數目上限。 預設值`1`：。 |
| `maxQueueWaitMs` | `max_request_wait_time` | 在傳回503錯誤之前，要求會停留在三個佇列中的時間上限（以毫秒為單位）。 預設值`500`：。 |
| `numReplicas` | `num_replicas` | 要為此 web 服務配置的容器數目。 無預設值。 如果未設定此參數，則預設會啟用自動調整程式。 |
| `keys` | NA | 包含索引鍵的設定元素。 |
| &emsp;&emsp;`primaryKey` | `primary_key` | 要用於此 Webservice 的主要驗證金鑰 |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | 要用於此 Webservice 的次要驗證金鑰 |
| `gpuCores` | `gpu_cores` | 要為此 Webservice 配置的 GPU 核心數（每個容器的複本）。 預設值為 1。 僅支援整數值。 |
| `livenessProbeRequirements` | NA | 包含活動探查需求的設定元素。 |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | 執行活動探查的頻率（以秒為單位）。 預設為10秒。 最小值為1。 |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 在起始活動探查之前，容器啟動後的秒數。 預設為310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 活動探查超時之前的秒數。預設為2秒。 最小值為1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | 成功之後，成功將活動探查視為成功的最小連續成功。 預設值為 1。 最小值為1。 |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | 當 Pod 啟動且活動探查失敗時，Kubernetes 會先嘗試 failureThreshold 時間再放棄。 預設值為3。 最小值為1。 |
| `namespace` | `namespace` | Webservice 部署所在的 Kubernetes 命名空間。 最多63小寫英數位元（' a'-'z-a'-'z '、' 0 '-' 9 '）和連字號（'-'）字元。 第一個和最後一個字元不可以是連字號。 |

下列 JSON 是與 CLI 搭配使用的範例部署設定：

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
