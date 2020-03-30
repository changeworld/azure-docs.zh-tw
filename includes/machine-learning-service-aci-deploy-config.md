---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486027"
---
文檔中的`deploymentconfig.json`條目映射到[AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)的參數。 下表描述了 JSON 文檔中的實體與方法的參數之間的映射：

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 對於 ACI，該值必須`ACI`為 。 |
| `containerResourceRequirements` | NA | CPU 和記憶體實體的容器。 |
| &emsp;&emsp;`cpu` | `cpu_cores` | 要分配的 CPU 內核數。 違約`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 為此 Web 服務分配的記憶體量（以 GB 表示）。 預設`0.5` |
| `location` | `location` | 要將此 Web 服務部署到的 Azure 區域。 如果未指定，將使用工作區位置。 有關可用區域的更多詳細資訊，請參閱此處[：ACI 區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | 是否為此 Web 服務啟用 auth。 預設值為"錯誤" |
| `sslEnabled` | `ssl_enabled` | 是否為此 Web 服務啟用 SSL。 預設為 False。 |
| `appInsightsEnabled` | `enable_app_insights` | 是否為此 Web 服務啟用 AppInsights。 預設值為"錯誤" |
| `sslCertificate` | `ssl_cert_pem_file` | 啟用 SSL 時所需的證書檔 |
| `sslKey` | `ssl_key_pem_file` | 啟用 SSL 時所需的金鑰檔 |
| `cname` | `ssl_cname` | 啟用 SSL 的 cname |
| `dnsNameLabel` | `dns_name_label` | 評分終結點的 dns 名稱標籤。 如果未指定，將為評分終結點生成唯一的 dns 名稱標籤。 |

以下 JSON 是用於 CLI 的示例部署配置：

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