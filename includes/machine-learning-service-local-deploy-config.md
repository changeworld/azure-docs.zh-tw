---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477267"
---
`deploymentconfig.json` 文件中的項目會對應至 [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py) 的參數。 下表描述 JSON 文件中的實體之間的對應以及方法的參數：

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 若為本機目標，該值必須為 `local`。 |
| `port` | `port` | 本機連接埠上公開服務的 HTTP 端點。 |

此 JSON 是與 CLI 搭配使用的範例部署設定：

```json
{
    "computeType": "local",
    "port": 32267
}
```
