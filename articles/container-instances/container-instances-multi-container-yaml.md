---
title: 教學課程-部署多容器群組-YAML
description: 在本教學課程中，您將瞭解如何使用 YAML 檔案與 Azure CLI，在 Azure 容器實例中部署具有多個容器的容器群組。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cce98ec56ee1d84c087150ba486b9482515b46f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74533599"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>教學課程：使用 YAML 檔案部署多容器群組

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure 容器執行個體支援使用[容器群組](container-instances-container-groups.md)將多個容器部署至單一主機。 當建立應用程式側車以進行記錄、監視或任何其他設定（其中服務需要第二個附加的進程）時，容器群組會很有用。

在本教學課程中，您會遵循使用 Azure CLI 部署[YAML](container-instances-reference-yaml.md)檔案，以執行簡單的雙容器側車設定的步驟。 YAML 檔案提供精簡的格式來指定實例設定。 您會了解如何：

> [!div class="checklist"]
> * 設定 YAML 檔案
> * 部署容器群組
> * 查看容器的記錄

> [!NOTE]
> 多容器群組目前僅限於 Linux 容器。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>設定 YAML 檔案

若要使用 Azure CLI 中的[az container create][az-container-create]命令來部署多容器群組，您必須在 YAML 檔中指定容器群組設定。 然後將 YAML 檔案當做參數傳遞給命令。

一開始先將下列 YAML 複製到名為 **deploy-aci.yaml** 的新檔案中。 在 Azure Cloud Shell 中，您可以使用 Visual Studio Code 在您的工作目錄中建立檔案：

```
code deploy-aci.yaml
```

此 YAML 檔案會定義名為 "myContainerGroup"，且含有兩個容器 (一個公用 IP 位址和兩個公開連接埠) 的容器群組。 容器會從公用 Microsoft 映射進行部署。 群組中第一個容器會執行網際網路對向的 Web 應用程式。 第二個容器 sidecar 會透過容器群組的區域網路，定期對第一個容器中執行的 Web 應用程式執行 HTTP 要求。

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

若要使用私用容器映射登錄，請`imageRegistryCredentials`將屬性新增至容器群組，並針對您的環境修改值：

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>部署容器群組

使用[az group create][az-group-create]命令來建立資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az container create][az-container-create] 命令部署容器群組，並以引數的形式傳遞 YAML 檔案：

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
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

使用 [az container logs][az-container-logs] 命令，檢視容器的記錄輸出。 `--container-name` 引數會指定要從中提取記錄的容器。 在此範例中， `aci-tutorial-app`會指定容器。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

輸出：

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

若要查看側車容器的記錄，請執行指定`aci-tutorial-sidecar`容器的類似命令。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

輸出：

```console
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

如您所見，Sidecar 會透過群組的區域網路定期地向主要 Web 應用程式提出 HTTP 要求，以確保它正在執行。 此側車範例可以擴充，以在收到的 HTTP 回應碼不`200 OK`是時觸發警示。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 YAML 檔案在 Azure 容器實例中部署多容器群組。 您已了解如何︰

> [!div class="checklist"]
> * 設定多容器群組的 YAML 檔
> * 部署容器群組
> * 查看容器的記錄

您也可以使用[Resource Manager 範本](container-instances-multi-container-group.md)來指定多個容器群組。 當您需要使用容器群組來部署其他 Azure 服務資源時，可以針對案例輕鬆地調整 Resource Manager 範本。

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
