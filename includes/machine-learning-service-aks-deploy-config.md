---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 95422ace63c9f79583de5a2c86d777ad4d0a8834
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026085"
---
`deploymentconfig.json` 文件中的專案會對應至 [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py) 的參數。 下表描述 JSON 文件中的實體之間的對應以及方法的參數：

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 若為 AKS，則值必須為 `aks`。 |
| `autoScaler` | NA | 包含自動調整的組態元素。 請參閱自動調整資料表。 |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 是否為 Web 服務啟用自動調整。 如果 `numReplicas` = `0`，則為 `True`：否則為 `False`。 |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 自動調整此 Web 服務時，要使用的容器數目下限。 預設值：`1`。 |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 自動調整此 Web 服務時，要使用的容器數目上限。 預設值：`10`。 |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 自動調整器嘗試調整此 Web 服務的頻率。 預設值：`1`。 |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | 自動調整器應嘗試為此 Web 服務維持的目標使用率 (以百分比為單位，最高 100)。 預設值：`70`。 |
| `dataCollection` | NA | 包含資料收集的組態元素。 |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | 是否要啟用 Web 服務的模型資料收集。 預設值：`False`。 |
| `authEnabled` | `auth_enabled` | 是否要啟用 Web 服務的金鑰驗證。 `tokenAuthEnabled` 和 `authEnabled` 都無法為 `True`。 預設值：`True`。 |
| `tokenAuthEnabled` | `token_auth_enabled` | 是否要啟用 Web 服務的權杖驗證。 `tokenAuthEnabled` 和 `authEnabled` 都無法為 `True`。 預設值：`False`。 |
| `containerResourceRequirements` | NA | CPU 和記憶體實體的容器。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 要為此 Web 服務配置的 CPU 核心數目。 依預設，`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 要為此 Web 服務配置的記憶體 (GB) 數量。 預設值，`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | 是否要啟用 Web 服務的 Application Insights 記錄。 預設值：`False`。 |
| `scoringTimeoutMs` | `scoring_timeout_ms` | 要對 Web 服務評分呼叫強制套用的逾時毫秒數。 預設值：`60000`。 |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 此 Web 服務每個節點的並行要求數上限。 預設值：`1`。 |
| `maxQueueWaitMs` | `max_request_wait_time` | 在傳回 503 錯誤之前，要求會停留在該佇列中的時間上限 (以毫秒為單位)。 預設值：`500`。 |
| `numReplicas` | `num_replicas` | 要為此 Web 服務配置的容器數目。 無預設值。 如果未設定此參數，則預設會啟用自動調整程式。 |
| `keys` | NA | 包含金鑰的組態元素。 |
| &emsp;&emsp;`primaryKey` | `primary_key` | 用於此 Web 服務的主要驗證金鑰 |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | 用於此 Web 服務的次要驗證金鑰 |
| `gpuCores` | `gpu_cores` | 要為此 Web 服務配置的 GPU 核心數 (每個容器複本)。 預設值為 1。 僅支援整數值。 |
| `livenessProbeRequirements` | NA | 包含活躍度探查需求的組態元素。 |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | 執行活躍度探查的頻率 (以秒為單位)。 預設值為 10 秒。 最小值為 1。 |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 在起始活躍度探查之前，容器啟動後的秒數。 預設值為 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 活躍度探查逾時之前所經過的秒數。預設值為 2 秒。 最小值為 1。 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | 失敗之後，會將活躍度探查的最小連續成功次數視為成功。 預設值為 1。 最小值為 1。 |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | 當 Pod 啟動且活躍度探查失敗時，Kubernetes 會在放棄之前先嘗試使用 failureThreshold 時間。 預設值為 3。 最小值為 1。 |
| `namespace` | `namespace` | Web 服務部署所在的 Kubernetes 命名空間。 最多 63 個小寫英數字元 ('a'-'z，'0'-'9') 和連字號 ('-') 字元。 第一個和最後一個字元不可以是連字號。 |

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