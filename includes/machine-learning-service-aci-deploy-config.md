---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79486027"
---
檔中的專案會對應至 AciWebservice 的參數。 [deploy_configuration。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py) `deploymentconfig.json` 下表描述 JSON 檔中的實體與方法的參數之間的對應：

| JSON 實體 | 方法參數 | 說明 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 若為 ACI，此值必須`ACI`是。 |
| `containerResourceRequirements` | NA | CPU 和記憶體實體的容器。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 要配置的 CPU 核心數目。 設置`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 要為此 web 服務配置的記憶體數量（以 GB 為單位）。 預設`0.5` |
| `location` | `location` | 要部署此 Webservice 的 Azure 區域。 如果未指定，則會使用工作區位置。 如需可用區域的詳細資訊，請參閱： [ACI 區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | 是否要啟用此 Webservice 的驗證。 預設為 False |
| `sslEnabled` | `ssl_enabled` | 是否要為此 Webservice 啟用 SSL。 預設為 False。 |
| `appInsightsEnabled` | `enable_app_insights` | 是否要為此 Webservice 啟用 AppInsights。 預設為 False |
| `sslCertificate` | `ssl_cert_pem_file` | 若已啟用 SSL，則需要憑證檔案 |
| `sslKey` | `ssl_key_pem_file` | 啟用 SSL 時所需的金鑰檔 |
| `cname` | `ssl_cname` | 如果 SSL 已啟用，則為的 cname |
| `dnsNameLabel` | `dns_name_label` | 評分端點的 dns 名稱標籤。 如果未指定，則會產生評分端點的唯一 dns 名稱標籤。 |

下列 JSON 是與 CLI 搭配使用的範例部署設定：

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```