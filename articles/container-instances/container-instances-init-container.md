---
title: 執行 init 容器
description: 在 Azure 容器實例中執行 init 容器，以在應用程式容器執行之前，在容器群組中執行設定工作。
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954276"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>針對容器群組中的設定工作執行 init 容器

Azure 容器實例支援容器群組中的*init 容器*。 在應用程式容器或容器啟動之前，Init 容器會執行到完成。 類似于[Kubernetes init 容器](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)，請使用一或多個 init 容器來執行應用程式容器的初始化邏輯，例如設定帳戶、執行安裝腳本或設定資料庫。

本文說明如何使用 Azure Resource Manager 範本來設定具有 init 容器的容器群組。

## <a name="things-to-know"></a>須知事項

* **API 版本**-您至少需要 Azure 容器實例 API 版本2019-12-01，才能部署 init 容器。 `initContainers`在[YAML](container-instances-multi-container-yaml.md)檔案或[Resource Manager 範本](container-instances-multi-container-group.md)中使用屬性進行部署。
* **執行的順序**-Init 容器會依照範本中指定的順序，在其他容器之前執行。 根據預設，您最多可以為每個容器群組指定59個 init 容器。 至少有一個非 init 容器必須在群組中。
* **主機環境**-Init 容器會在與群組中其餘容器相同的硬體上執行。
* **資源**-您未指定 init 容器的資源。 它們會被授與容器群組可用的資源總計，例如 Cpu 和記憶體。 當 init 容器執行時，群組中不會執行其他容器。
* **支援的屬性**-Init 容器可以使用群組屬性，例如磁片區、秘密和受控識別。 不過，如果為容器群組設定了埠或 IP 位址，它們就不能使用它們。 
* **重新開機原則**-每個 init 容器都必須成功結束，群組中的下一個容器才會啟動。 如果 init 容器未成功結束，其重新開機動作取決於針對群組所設定的[重新開機原則](container-instances-restart-policy.md)：

    |群組中的原則  |Init 中的原則  |
    |---------|---------|
    |永遠     |OnFailure         |
    |OnFailure     |OnFailure         |
    |永不     |永不         |
* **費用**-容器群組會產生初始容器第一次部署的費用。

## <a name="resource-manager-template-example"></a>Resource Manager 範本範例

一開始先將下列 JSON 複製到名為 `azuredeploy.json` 的新檔案中。 範本會設定一個具有一個 init 容器和兩個應用程式容器的容器群組：

* *Init1*容器會從 Docker Hub 執行[busybox](https://hub.docker.com/_/busybox)映射。 它會睡眠60秒，然後將命令列字串寫入[emptyDir 磁片](container-instances-volume-emptydir.md)區中的檔案。
* 這兩個應用程式容器都會執行 Microsoft `aci-wordcount` 容器映射：
    * *Hamlet 文字*容器會以其預設設定執行 wordcount 應用程式，並在 Shakespeare 的 play *hamlet 文字*中計算文字頻率。
    * *Juliet*應用程式容器會從 emptDir 磁片區讀取命令列字串，以執行 wordcount 應用程式，而不是 Shakespeare 的*Romeo 和 juliet*。

如需使用影像的詳細資訊和範例 `aci-wordcount` ，請參閱[在容器實例中設定環境變數](container-instances-environment-variables.md)。

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
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>部署範本

使用 [az group create][az-group-create] 命令來建立資源群組。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用[az deployment group create][az-deployment-group-create]命令來部署範本。

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

在具有 init 容器的群組中，部署時間會隨著 init 容器或容器完成所需的時間而增加。


## <a name="view-container-logs"></a>檢視容器記錄

若要確認 init 容器已成功執行，請使用[az container logs][az-container-logs]命令來查看應用程式容器的記錄輸出。 `--container-name` 引數會指定要從中提取記錄的容器。 在此範例中，請提取*hamlet 文字*和*juliet*容器的記錄，其會顯示不同的命令輸出：

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

輸出：

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

輸出：

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>後續步驟

Init 容器可協助您執行應用程式容器的設定和初始化工作。 如需有關執行以工作為基礎之容器的詳細資訊，請參閱[使用重新開機原則執行容器](container-instances-restart-policy.md)化工作。

Azure 容器實例提供其他選項來修改應用程式容器的行為。 範例包括：

* [在容器實例中設定環境變數](container-instances-environment-variables.md)
* [在容器實例中設定命令列，以覆寫預設的命令列操作](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
