---
title: 在專用主機上部署
description: 使用專用主機，針對您的 Azure 容器實例工作負載達成真正的主機層級隔離
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 967d2da61ffdfa9d1723bcab589deb2277d4041e
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825611"
---
# <a name="deploy-on-dedicated-hosts"></a>在專用主機上部署

「專用」是 (ACI) sku 的 Azure 容器實例，可提供隔離且專用的計算環境，以安全地執行容器。 使用專用 sku 會導致每個容器群組在 Azure 資料中心具有專用的實體伺服器，以確保完整的工作負載隔離，以協助您符合組織的安全性和合規性需求。 

專用 sku 適用于需要從實體伺服器觀點來隔離工作負載的容器工作負載。

## <a name="prerequisites"></a>必要條件

* 使用專用 sku 之任何訂用帳戶的預設限制為0。 如果您想要將此 sku 用於生產容器部署，請建立 [Azure 支援要求][azure-support] 來提高限制。

## <a name="use-the-dedicated-sku"></a>使用專用 sku

> [!IMPORTANT]
> 使用專用 sku 僅適用于目前推出的最新 API 版本 (2019-12-01) 。在您的部署範本中指定此 API 版本。
>

從 API 版本2019-12-01 開始，在 `sku` 部署範本的 [容器群組屬性] 區段下有一個可供 ACI 部署所需的屬性。 目前，您可以使用此屬性作為 ACI 的 Azure Resource Manager 部署範本的一部分。 在 [教學課程：使用 Resource Manager 範本部署多容器群組](./container-instances-multi-container-group.md)中，深入瞭解如何使用範本部署 ACI 資源。 

`sku`屬性可以具有下列其中一個值：
* `Standard` -標準 ACI 部署選擇，仍然可保證虛擬程式層級安全性 
* `Dedicated` -用於容器群組專用實體主機的工作負載層級隔離

## <a name="modify-your-json-deployment-template"></a>修改您的 JSON 部署範本

在您的部署範本中，修改或新增下列屬性：
* 在底下 `resources` ，將設定 `apiVersion` 為 `2019-12-01` 。
* 在容器群組屬性下，新增 `sku` 具有值的屬性 `Dedicated` 。

以下是使用專用 sku 的容器群組部署範本資源區段的範例程式碼片段：

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

以下是完整的範本，可部署執行單一容器實例的範例容器群組：

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

如果您在桌面上建立和編輯部署範本檔案，您可以將檔案拖曳到 Cloud Shell 目錄，將它上傳到您的目錄。 

使用 [az group create][az-group-create] 命令來建立資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az deployment group create][az-deployment-group-create] 命令部署範本。

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

在幾秒內，您應該會從 Azure 收到首次回應。 成功的部署會在專用主機上進行。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
