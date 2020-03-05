---
title: 快速入門 - 將 Docker 容器部署至容器執行個體 - Azure CLI
description: 在本快速入門中，您可以使用 Azure CLI 快速地部署在隔離式 Azure 容器執行個體中執行的容器化的 Web 應用程式
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom:
- seo-python-october2019
- seodec18
- mvc
ms.openlocfilehash: e5cad7d9141963e5062423545f7e5b94f0575152
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252193"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>快速入門：使用 Azure CLI 部署容器執行個體

使用 Azure 容器執行個體在 Azure 中簡潔且快速地執行無伺服器 Docker 容器。 當您不需要像 Azure Kubernetes Service 的完整容器協調流程平台時，請視需要將應用程式部署至容器執行個體。

在本快速入門中，您會使用 Azure CLI 部署隔離式 Docker 容器，並使用完整網域名稱 (FQDN) 讓其應用程式可供使用。 執行單一部署命令的幾秒之後，您可以瀏覽至容器中執行的應用程式：

![在瀏覽器中檢視部署至 Azure Container Instances 的應用程式][aci-app-browser]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶][azure-account]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成此快速入門。 如果您想要在本機使用，建議使用 2.0.55 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 容器執行個體和所有 Azure 資源相同，都必須部署到資源群組中。 資源群組可讓您組織和管理相關的 Azure 資源。

首先，使用下列 [az group create][az-group-create] 命令，在 *eastus* 位置中建立一個名為 *myResourceGroup* 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>建立容器

有了資源群組之後，現在您可以在 Azure 中執行容器。 若要使用 Azure CLI 建立容器執行個體，請在 [az container create][az-container-create] 命令中提供資源群組名稱、容器執行個體名稱和 Docker 容器映像。 在此快速入門中，您可以使用公用 `mcr.microsoft.com/azuredocs/aci-helloworld` 映像。 此映像會封裝以 Node.js 撰寫並提供靜態 HTML 網頁的小型 Web 應用程式。

您可以指定一或多個要開啟的連接埠和 (或) DNS 名稱標籤，以將您的容器公開至網際網路。 在本快速入門中，您會部署附有 DNS 名稱標籤的一個容器，以便公開觸達 Web 應用程式。

執行和下列類似的命令，以啟動容器執行個體。 設定 `--dns-name-label` 值，其在您建立執行個體所在的 Azure 區域中必須是唯一的。 如果出現「DNS 名稱標籤無法使用」錯誤訊息，請嘗試使用不同的 DNS 名稱標籤。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

您應該會在幾秒內獲得 Azure CLI 的回應，指出部署已。 請使用 [az container show][az-container-show] 命令來檢查其狀態：

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

當您執行命令時，就會顯示容器的完整網域名稱 (FQDN) 及其佈建狀態。

```output
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

如果容器的 `ProvisioningState` 為 [成功]  ，請在瀏覽器中移至其 FQDN。 如果您看到如下的網頁，恭喜您！ 您已將 Docker 容器中執行的應用程式成功部署至 Azure。

![在瀏覽器中檢視部署至 Azure Container Instances 的應用程式][aci-app-browser]

如果一開始應用程式並未顯示，您可能需要等候幾秒鐘讓 DNS 傳播完成，然後再次嘗試重新整理瀏覽器。

## <a name="pull-the-container-logs"></a>提取容器記錄

如果您要對容器或其執行的應用程式進行疑難排解 (或只是要檢視其輸出)，請先檢視容器執行個體的記錄。

使用 [az container logs][az-container-logs] 命令提取容器執行個體記錄：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

輸出會顯示容器的記錄，且應該會顯示您在瀏覽器中檢視應用程式時產生的 HTTP GET 要求。

```output
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>附加輸出資料流

除了檢視記錄外，您還可以將您的本機標準輸出和標準錯誤資料流附加至容器的這些項目。

首先，請執行 [az container attach][az-container-attach] 命令，將本機主控台附加至容器的輸出資料流：

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

在附加之後，重新整理瀏覽器幾次以產生一些額外的輸出。 作業完成後，請使用 `Control+C` 將主控台中斷連結。 您應該會看到類似以下的輸出：

```output
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>清除資源

當容器使用完畢後，請使用 [az container delete][az-container-delete] 命令來移除容器：

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

若要確認否已將容器刪除，請執行 [az container list](/cli/azure/container#az-container-list) 命令：

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

**mycontainer** 不應該出現在命令的輸出中。 如果您的資源群組中沒有任何其他容器，則不會顯示任何輸出。

當您使用完 *myResourceGroup* 資源群組和其中包含的所有資源後，請使用 [az group delete][az-group-delete] 命令加以刪除：

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用公用 Microsoft 映像建立 Azure 容器執行個體。 如果您想要建置容器映像，並從私人的 Azure 容器登錄進行部署，請繼續進行 Azure Container Instances 教學課程。

> [!div class="nextstepaction"]
> [Azure 容器執行個體教學課程](./container-instances-tutorial-prepare-app.md)

若要試用在 Azure 上的協調流程系統中執行容器的選項，請參閱 [Azure Kubernetes Service (AKS)][container-service] 快速入門。

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
