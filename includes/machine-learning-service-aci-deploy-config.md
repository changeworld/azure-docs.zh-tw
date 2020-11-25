---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 190da8fc98f3a03499188ab173f058d15cd2dafe
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028272"
---
`deploymentconfig.json` 文件中的專案會對應至 [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py) 的參數。 下表描述 JSON 文件中的實體之間的對應以及方法的參數：

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 若為 ACI，則值必須為 `ACI`。 |
| `containerResourceRequirements` | NA | CPU 和記憶體實體的容器。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 要配置的 CPU 核心數目。 依預設，`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 要為此 Web 服務配置的記憶體 (GB) 數量。 預設值，`0.5` |
| `location` | `location` | 要作為部署此 Webservice 的 Azure 區域。 如果未指定，則將會使用工作區位置。 如需可用區域的詳細資訊，請參閱：[ACI 區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | 是否要啟用此 Webservice 的驗證。 預設為 False |
| `sslEnabled` | `ssl_enabled` | 是否要為此 Webservice 啟用 SSL。 預設為 False。 |
| `appInsightsEnabled` | `enable_app_insights` | 是否要為此 Webservice 啟用 AppInsights。 預設為 False |
| `sslCertificate` | `ssl_cert_pem_file` | 若已啟用 SSL，則需要憑證檔案 |
| `sslKey` | `ssl_key_pem_file` | 啟用 SSL 時所需的金鑰檔案 |
| `cname` | `ssl_cname` | 如果 SSL 已啟用，則為 cname |
| `dnsNameLabel` | `dns_name_label` | 評分端點的 DNS 名稱標籤。 若未指定，就會為評分端點產生唯一的 DNS 名稱標籤。 |

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