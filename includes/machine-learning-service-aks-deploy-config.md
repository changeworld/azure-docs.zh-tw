---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485947"
---
文檔中的`deploymentconfig.json`條目映射到[AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)的參數。 下表描述了 JSON 文檔中的實體與方法的參數之間的映射：

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 對於 AKS，該值必須`aks`為 。 |
| `autoScaler` | NA | 包含用於自動縮放的配置元素。 請參閱自動縮放器表。 |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 是否為 Web 服務啟用自動縮放。 `numReplicas`  = 如果`0` `True`，否則， `False`. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 自動縮放此 Web 服務時要使用的最小容器數。 預設值`1`。 |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 自動縮放此 Web 服務時要使用的最大容器數。 預設值`10`。 |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 自動縮放器嘗試擴展此 Web 服務的頻率。 預設值`1`。 |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | 自動縮放程式應嘗試為此 Web 服務維護的目標利用率（以百分比計）。 預設值`70`。 |
| `dataCollection` | NA | 包含用於資料收集的配置元素。 |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | 是否為 Web 服務啟用模型資料收集。 預設值`False`。 |
| `authEnabled` | `auth_enabled` | 是否為 Web 服務啟用金鑰身份驗證。 和`tokenAuthEnabled``authEnabled`不能`True`都是 。 預設值`True`。 |
| `tokenAuthEnabled` | `token_auth_enabled` | 是否為 Web 服務啟用權杖身份驗證。 和`tokenAuthEnabled``authEnabled`不能`True`都是 。 預設值`False`。 |
| `containerResourceRequirements` | NA | CPU 和記憶體實體的容器。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 為此 Web 服務分配的 CPU 內核數。 違約`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 為此 Web 服務分配的記憶體量（以 GB 表示）。 預設`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | 是否為 Web 服務啟用應用程式見解日誌記錄。 預設值`False`。 |
| `scoringTimeoutMs` | `scoring_timeout_ms` | 用於強制對 Web 服務的呼叫進行評分的超時。 預設值`60000`。 |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 此 Web 服務每個節點的最大併發請求數。 預設值`1`。 |
| `maxQueueWaitMs` | `max_request_wait_time` | 在返回 503 錯誤之前，請求在 e 佇列中停留的最大時間（以毫秒為單位）。 預設值`500`。 |
| `numReplicas` | `num_replicas` | 為此 Web 服務分配的容器數。 無預設值。 如果未設置此參數，則預設情況下將啟用自動縮放器。 |
| `keys` | NA | 包含金鑰的配置元素。 |
| &emsp;&emsp;`primaryKey` | `primary_key` | 用於此 Web 服務的主要 auth 鍵 |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | 用於此 Web 服務的第二個 auth 鍵 |
| `gpuCores` | `gpu_cores` | 為此 Web 服務分配的 GPU 內核數（每個容器副本）。 預設值為 1。 僅支援整數值。 |
| `livenessProbeRequirements` | NA | 包含符合活動性探測要求的配置元素。 |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | 執行活動探測的頻率（以秒為單位）。 預設為 10 秒。 最小值為 1。 |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 啟動活動探測之前容器啟動後的秒數。 預設值為 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 活動探測超時後的秒數。預設值為 2 秒。 最小值為 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | 在失敗後，活度探測被視為成功的連續成功最小。 預設值為 1。 最小值為 1。 |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | 當 Pod 啟動且活動探測失敗時，Kubernetes 將在放棄之前嘗試失敗閾值時間。 預設值為 3。 最小值為 1。 |
| `namespace` | `namespace` | Web 服務部署到的 Kubernetes 命名空間。 最多 63 個小寫字母數位（"a'-'z"，"0"-'9'）和連字號 （'-'） 字元。 第一個和最後一個字元不能是連字號。 |

以下 JSON 是用於 CLI 的示例部署配置：

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
