---
title: 教學課程 - 部署多容器群組 - 範本
description: 在此教學課程中，您將了解如何使用 Azure Resource Manager 範本搭配 Azure CLI，在 Azure 容器執行個體中部署具有多個容器的容器群組。
ms.topic: article
ms.date: 04/03/2019
ms.custom: mvc
ms.openlocfilehash: b08a974cbbdc9e4bdf1594672f82748bfabe88b4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653523"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>教學課程：使用 Resource Manager 範本部署多容器群組

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure 容器執行個體支援使用[容器群組](container-instances-container-groups.md)將多個容器部署至單一主機。 在建置應用程式 Sidecar 以便記錄、監視或進行服務需要另一個附加程序的任何其他設定時，容器群組非常有用。

在此教學課程中，您要依照步驟，使用 Azure CLI 部署 Azure Resource Manager 範本，以執行簡單的雙容器 Sidecar 組態。 您會了解如何：

> [!div class="checklist"]
> * 設定多容器群組範本
> * 部署容器群組
> * 檢視容器記錄

當您需要使用容器群組部署其他 Azure 服務資源 (例如，Azure 檔案共用或虛擬網路) 時，可以隨時調整 Resource Manager 範本以因應各種情況。 

> [!NOTE]
> 多容器群組目前僅限於 Linux 容器。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>設定範本

一開始先將下列 JSON 複製到名為 `azuredeploy.json` 的新檔案中。 在 Azure Cloud Shell 中，您可以使用 Visual Studio Code，在工作目錄中建立檔案：

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
              "port": 80
            },
            {
                "protocol": "tcp",
                "port": 8080
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

使用 [az group deployment create][az-group-deployment-create] 命令部署範本。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

在幾秒內，您應該會從 Azure 收到首次回應。

## <a name="view-deployment-state"></a>檢視部署狀態

若要檢視部署狀態，請使用下列 [az container show][az-container-show] 命令：

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

使用 [az container logs][az-container-logs] 命令，檢視容器的記錄輸出。 `--container-name` 引數會指定要從中提取記錄的容器。 在此範例中，已指定 `aci-tutorial-app` 容器。

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

若要查看 Sidecar 容器的記錄，請執行類似指定 `aci-tutorial-sidecar` 容器的命令。

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

如您所見，Sidecar 會透過群組的區域網路定期地向主要 Web 應用程式提出 HTTP 要求，以確保它正在執行。 您可以擴充此 Sidecar 範例，以便在其收到 `200 OK` 以外的 HTTP 回應碼時觸發警示。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您使用 Azure Resource Manager 範本，在 Azure 容器執行個體中部署多容器群組。 您已了解如何︰

> [!div class="checklist"]
> * 設定多容器群組範本
> * 部署容器群組
> * 檢視容器記錄

如需其他範本範例，請參閱 [Azure 容器執行個體的 Azure Resource Manager 範本](container-instances-samples-rm.md)。

您也可以使用 [YAML 檔案](container-instances-multi-container-yaml.md)，指定多容器群組。 YAML 格式的本質較簡潔，因此當您的部署僅包含容器執行個體時，建議您使用 YAML 檔案進行部署。


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
