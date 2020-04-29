---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477267"
---
檔中的專案會對應至 LocalWebservice 的參數。 [deploy_configuration。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py) `deploymentconfig.json` 下表描述 JSON 檔中的實體與方法的參數之間的對應：

| JSON 實體 | 方法參數 | 說明 |
| ----- | ----- | ----- |
| `computeType` | NA | 計算目標。 若為本機目標，此值必須`local`是。 |
| `port` | `port` | 要在其上公開服務之 HTTP 端點的本機埠。 |

此 JSON 是與 CLI 搭配使用的範例部署設定：

```json
{
    "computeType": "local",
    "port": 32267
}
```
