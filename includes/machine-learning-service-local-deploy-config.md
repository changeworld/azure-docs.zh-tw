---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477267"
---
文檔中的`deploymentconfig.json`條目映射到[LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)的參數。 下表描述了 JSON 文檔中的實體與方法的參數之間的映射：

| JSON 實體 | 方法參數 | 描述 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 對於本地目標，該值必須為`local`。 |
| `port` | `port` | 要在其中公開服務的 HTTP 終結點的本地埠。 |

此 JSON 是用於 CLI 的示例部署配置：

```json
{
    "computeType": "local",
    "port": 32267
}
```
