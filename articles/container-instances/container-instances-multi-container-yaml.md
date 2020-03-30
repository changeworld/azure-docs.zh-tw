---
title: 教程 - 部署多容器組 - YAML
description: 在本教程中，您將瞭解如何將 YAML 檔與 Azure CLI 一起使用，在 Azure 容器實例中部署具有多個容器的容器組。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cce98ec56ee1d84c087150ba486b9482515b46f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533599"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>教程：使用 YAML 檔部署多容器組

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [資源管理器](container-instances-multi-container-group.md)
>

Azure 容器執行個體支援使用[容器群組](container-instances-container-groups.md)將多個容器部署至單一主機。 容器組在構建應用程式側車以用於記錄、監視或服務需要第二個附加進程的任何其他配置時非常有用。

在本教程中，您可以按照以下步驟，通過使用 Azure CLI 部署[YAML 檔](container-instances-reference-yaml.md)來運行簡單的雙容器側車配置。 YAML 檔提供了指定實例設置的簡明格式。 您會了解如何：

> [!div class="checklist"]
> * 配置 YAML 檔
> * 部署容器群組
> * 查看容器的日誌

> [!NOTE]
> 多容器群組目前僅限於 Linux 容器。

如果沒有 Azure 訂閱，請先創建[一個免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。"

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>配置 YAML 檔

要在 Azure CLI 中部署具有[az 容器創建][az-container-create]命令的多容器組，必須在 YAML 檔中指定容器組配置。 然後將 YAML 檔作為參數傳遞給命令。

一開始先將下列 YAML 複製到名為 **deploy-aci.yaml** 的新檔案中。 在 Azure 雲外殼中，可以使用視覺化工作室代碼在工作目錄中創建檔：

```
code deploy-aci.yaml
```

此 YAML 檔案會定義名為 "myContainerGroup"，且含有兩個容器 (一個公用 IP 位址和兩個公開連接埠) 的容器群組。 容器從公共 Microsoft 映射部署。 群組中第一個容器會執行網際網路對向的 Web 應用程式。 第二個容器 sidecar 會透過容器群組的區域網路，定期對第一個容器中執行的 Web 應用程式執行 HTTP 要求。

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

要使用專用容器映射註冊表，請將`imageRegistryCredentials`屬性添加到容器組，並針對環境修改值：

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>部署容器群組

使用[az 組創建命令創建資源組][az-group-create]：

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

使用 [az container logs][az-container-logs] 命令，檢視容器的記錄輸出。 `--container-name` 引數會指定要從中提取記錄的容器。 在此示例中，指定`aci-tutorial-app`容器。

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

要查看側車容器的日誌，運行指定`aci-tutorial-sidecar`該容器的類似命令。

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

如您所見，Sidecar 會透過群組的區域網路定期地向主要 Web 應用程式提出 HTTP 要求，以確保它正在執行。 如果此側車示例收到 以外的`200 OK`HTTP 回應代碼，則可以展開以觸發警報。

## <a name="next-steps"></a>後續步驟

在本教程中，您使用 YAML 檔在 Azure 容器實例中部署多容器組。 您已了解如何︰

> [!div class="checklist"]
> * 為多容器組配置 YAML 檔
> * 部署容器群組
> * 查看容器的日誌

您還可以使用[資源管理器範本](container-instances-multi-container-group.md)指定多容器組。 當您需要使用容器組部署其他 Azure 服務資源時，資源管理器範本可以很容易地適應這些方案。

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
