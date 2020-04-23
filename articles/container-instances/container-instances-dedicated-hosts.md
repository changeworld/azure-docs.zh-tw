---
title: 部署在專用主機上
description: 使用專用主機實現 Azure 容器實例工作負載的真正主機級隔離
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025028"
---
# <a name="deploy-on-dedicated-hosts"></a>在專用主機上部署

"專用"是一個 Azure 容器實例 (ACI) sku,它為安全運行容器提供隔離且專用的計算環境。 使用專用 sKU 會導致每個容器組在 Azure 資料中心中具有專用物理伺服器,確保完全隔離工作負載以説明滿足組織的安全和合規性要求。 

專用 sKU 適用於需要從物理伺服器角度隔離工作負載的容器工作負載。

## <a name="prerequisites"></a>Prerequisites

* 任何訂閱使用專用 sKU 的預設限制為 0。 如果要將此 sKU 用於生產容器部署,請創建[Azure 支援請求][azure-support]以增加限制。

## <a name="use-the-dedicated-sku"></a>使用專用 sKU

> [!IMPORTANT]
> 使用專用 sKU 僅在當前推出的最新 API 版本 (2019-12-01) 中可用。在部署範本中指定此 API 版本。
>

從 API 版本 2019-12-01 開始`sku`,部署範本的容器組屬性部分下有一個屬性,這是 ACI 部署所必需的。 目前,可以將此屬性用作 ACI 的 Azure 資源管理器部署範本的一部分。 瞭解有關使用教學中的樣本部署 ACI 資源的更多資訊[:使用資源管理員樣本部署多容器群組](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)。 

該`sku`屬性可以具有以下值之一:
* `Standard`- 標準 ACI 部署選擇,仍保證虛擬機器管理程式級安全性 
* `Dedicated`- 用於與容器群組的專用物理主機進行工作負載層隔離

## <a name="modify-your-json-deployment-template"></a>修改 JSON 部署樣本

在部署樣本中,修改或新增以下屬性:
* 在`resources`下`apiVersion`,`2019-12-01`設定為 。
* 在容器群組屬性下,新增`sku`值的屬性`Dedicated`。

下面是使用專用 sKU 的容器組部署樣本的資源部分的範例代碼段:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

下面是部署執行單個容器實體的範例容器群組的完整樣本:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>部署容器群組

如果在桌面上創建並編輯了部署範本檔,則可以通過將檔拖入其中將其上傳到雲端殼目錄。 

使用 [az group create][az-group-create] 命令來建立資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az group deployment create][az-group-deployment-create] 命令來部署範本。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

在幾秒內，您應該會從 Azure 收到首次回應。 成功部署將在專用主機上進行。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
