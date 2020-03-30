---
title: 教程 - 部署多容器組 - 範本
description: 在本教程中，瞭解如何將 Azure 資源管理器範本與 Azure CLI 一起使用，以在 Azure 容器實例中部署具有多個容器的容器組。
ms.topic: article
ms.date: 04/03/2019
ms.custom: mvc
ms.openlocfilehash: d2b4e20520cad28c5d62118f6c9d10fcc43ac89e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533630"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>教程：使用資源管理器範本部署多容器組

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [資源管理器](container-instances-multi-container-group.md)

Azure 容器執行個體支援使用[容器群組](container-instances-container-groups.md)將多個容器部署至單一主機。 容器組在構建應用程式側車以用於記錄、監視或服務需要第二個附加進程的任何其他配置時非常有用。

在本教程中，您可以按照以下步驟，通過使用 Azure CLI 部署 Azure 資源管理器範本來運行簡單的雙容器側車配置。 您會了解如何：

> [!div class="checklist"]
> * 配置多容器組範本
> * 部署容器群組
> * 查看容器的日誌

當您需要將其他 Azure 服務資源（例如，Azure 檔共用或虛擬網路）與容器組一起部署時，資源管理器範本可以很容易地適應這些方案。 

> [!NOTE]
> 多容器群組目前僅限於 Linux 容器。 

如果沒有 Azure 訂閱，請先創建[一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。"

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>配置範本

首先將以下 JSON 複製到名為`azuredeploy.json`的新檔中。 在 Azure 雲外殼中，可以使用視覺化工作室代碼在工作目錄中創建檔：

```
code azuredeploy.json
```

此 Resource Manager 範本會定義含有兩個容器、一個公用 IP 位址和兩個已公開連接埠的容器群組。 群組中第一個容器會執行網際網路對向的 Web 應用程式。 第二個容器 (也就是 Sidecar) 會透過群組的區域網路向主要 Web 應用程式提出 HTTP 要求。

```JSON
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
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

若要使用私用容器映像登錄，請使用下列格式將物件新增至 JSON 文件。 如需此設定的範例實作，請參閱 [ACI Resource Manager 範本參考][template-reference]文件。

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>部署範本

使用 [az group create][az-group-create] 命令來建立資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az group deployment create][az-group-deployment-create] 命令來部署範本。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

在幾秒內，您應該會從 Azure 收到首次回應。

## <a name="view-deployment-state"></a>檢視部署狀態

若要檢視部署的狀態，請使用下列 [az container show][az-container-show] 命令：

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

若要檢視執行中的應用程式，請在瀏覽器中瀏覽至其公用 IP 位址。 例如，在此範例輸出中，IP 是 `52.168.26.124`：

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>檢視容器記錄

使用 [az container logs][az-container-logs] 命令，檢視容器的記錄輸出。 `--container-name` 引數會指定要從中提取記錄的容器。 在此示例中，指定`aci-tutorial-app`容器。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

輸出：

```bash
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

要查看側車容器的日誌，運行指定`aci-tutorial-sidecar`該容器的類似命令。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

輸出：

```bash
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

如您所見，Sidecar 會透過群組的區域網路定期地向主要 Web 應用程式提出 HTTP 要求，以確保它正在執行。 如果此側車示例收到 以外的`200 OK`HTTP 回應代碼，則可以展開以觸發警報。

## <a name="next-steps"></a>後續步驟

在本教程中，您使用 Azure 資源管理器範本在 Azure 容器實例中部署多容器組。 您已了解如何︰

> [!div class="checklist"]
> * 配置多容器組範本
> * 部署容器群組
> * 查看容器的日誌

有關其他範本示例，請參閱[Azure 容器實例的 Azure 資源管理器範本](container-instances-samples-rm.md)。

您還可以使用[YAML 檔](container-instances-multi-container-yaml.md)指定多容器組。 由於 YAML 格式的簡潔性，當您的部署僅包含容器實例時，使用 YAML 檔進行部署是一個不錯的選擇。


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
