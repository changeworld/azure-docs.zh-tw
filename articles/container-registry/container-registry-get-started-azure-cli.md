---
title: 快速入門 - 建立登錄 - Azure CLI
description: 快速了解如何使用 Azure CLI 建立私用的 Docker 容器登錄。
ms.topic: quickstart
ms.date: 06/12/2020
ms.custom: seodec18, H1Hack27Feb2017, mvc, devx-track-azurecli
ms.openlocfilehash: f4e69616d30c6a7b853c5cc854adee147ebde206
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486539"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>快速入門：使用 Azure CLI 建立私人容器登錄

Azure Container Registry 是用於儲存私用 Docker 容器映像的受控 Docker 容器登錄服務。 本指南詳述如何使用 Azure CLI 建立 Azure Container Registry 執行個體。 然後，使用 Docker 命令將容器映像推送到登錄中，最後從您的登錄中提取映像並加以執行。

進行此快速入門時，您必須執行 Azure CLI (建議使用 2.0.55 版或更新版本)。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

您也必須在本機上安裝 Docker。 Docker 提供可輕鬆在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系統上設定 Docker 的套件。

由於 Azure Cloud Shell 未包含所有必要的 Docker 元件 (`dockerd` 精靈)，因此您無法使用本快速入門中的 Cloud Shell。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create][az-group-create] 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 eastus** 位置建立名為 myResourceGroup** 的資源群組。

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>建立容器登錄庫

您在本快速入門中會建立「基本」** 登錄，這是正在學習 Azure Container Registry 的開發人員所適用的成本最佳化選項。 如需可用服務層級的詳細資訊，請參閱[容器登錄服務層][container-registry-skus]。

使用 [az acr create][az-acr-create] 命令，以建立 ACR 執行個體。 登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 下列範例中使用 *myContainerRegistry007*。 請將此更新為唯一的值。

```azurecli
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry007 --sku Basic
```

建立登錄時，輸出大致如下：

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

請記下輸出中的 `loginServer`，這是完整登錄名稱 (全部小寫)。 在本快速入門的其餘部分，`<registry-name>` 是容器登錄名稱的預留位置，而 `<login-server>` 是登錄登入伺服器名稱的預留位置。

## <a name="log-in-to-registry"></a>登入登錄

推送和提取容器映像之前，您必須先登入登錄。 若要這樣做，請使用 [az acr login][az-acr-login] 命令。

```azurecli
az acr login --name <registry-name>
```

完成後，此命令會傳回 `Login Succeeded` 訊息。

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>列出容器映像

下列範例會列出登錄中的存放庫：

```azurecli
az acr repository list --name <registry-name> --output table
```

輸出：

```
Result
----------------
hello-world
```

下列範例會列出在 **hello-world** 存放庫上的標籤。

```azurecli
az acr repository show-tags --name <registry-name> --repository hello-world --output table
```

輸出：

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete][az-group-delete] 命令移除資源群組、容器登錄，以及儲存於該處的容器映像。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure CLI 建立 Azure Container Registry、將容器映像推送至登錄，以及從登錄中提取映像並加以執行。 請繼續進行 Azure 容器登錄教學課程，以深入了解 ACR。

> [!div class="nextstepaction"]
> [Azure Container Registry 教學課程][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry 工作教學課程][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
