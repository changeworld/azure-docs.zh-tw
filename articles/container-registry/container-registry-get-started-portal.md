---
title: 快速入門 - 在入口網站中建立登錄
description: 快速了解如何使用 Azure 入口網站在 Azure Container Registry 中建立私人 Docker 登錄。
ms.topic: quickstart
ms.date: 06/11/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 82f9a6b02832b718d5b4e7b662c590f1992af595
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752825"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立私人容器登錄

Azure Container Registry 是 Azure 中的私人 Docker 登錄，您可以在其中儲存並管理私人 Docker 容器映像及相關成品。 在本快速入門中，您會使用 Azure 入口網站建立容器登錄。 然後，使用 Docker 命令將容器映像推送到登錄中，最後從您的登錄中提取映像並加以執行。

若要登入登錄以使用容器映像，您在進行此快速入門時必須執行 Azure CLI (建議使用 2.0.55 版或更新版本)。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

您也必須在本機上安裝 Docker。 Docker 提供可輕鬆在 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系統上設定 Docker 的套件。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-container-registry"></a>建立容器登錄庫

選取 [建立資源]**** > [容器]**** > [容器登錄]****。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="瀏覽至入口網站中的容器登錄":::

在 [基本資料]**** 索引標籤中，輸入**資源群組**和**登錄名稱**的值。 登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 在此快速入門中，請在 `West US` 位置中建立名為 `myResourceGroup` 的新資源群組，並選取 [基本] 作為 [SKU]****。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="在入口網站中建立容器登錄":::

接受其餘設定的預設值。 然後，選取 [檢閱 + 建立]****。 檢閱設定之後，選取 [建立]****。

您在本快速入門中會建立「基本」** 登錄，這是正在學習 Azure Container Registry 的開發人員所適用的成本最佳化選項。 如需可用服務層級 (SKU) 的詳細資訊，請參閱[容器登錄服務層][container-registry-skus]。

當 [部署成功]**** 訊息出現時，請在入口網站中選取容器登錄。 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="入口網站中的容器登錄概觀":::

記下 [登入伺服器]**** 的值。 當您使用 Docker 推送和提取映像時，您會在下列步驟中使用此值。

## <a name="log-in-to-registry"></a>登入登錄

推送和提取容器映像之前，您必須先登入登錄執行個體。 在本機電腦上[登入 Azure CLI][get-started-with-azure-cli]，然後執行 [az acr login][az-acr-login] 命令。 (透過 Azure CLI 登入時僅指定登錄名稱。 請勿包含 'azurecr.io' 尾碼。)

```azurecli
az acr login --name <registry-name>
```

完成後，此命令會傳回 `Login Succeeded`。 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>列出容器映像

若要列出您登錄中的映像，請瀏覽至入口網站中的登錄並選取 [存放庫]****，然後選取您使用 `docker push` 建立的 **hello-world**存放庫。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="列出入口網站中的容器映像":::

選取 **hello-world** 存放庫，您會在 [標記]**** 之下看到標記 `v1` 的映像。

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>清除資源

若要清除資源，請在入口網站中瀏覽至 **myResourceGroup** 資源群組。 載入資源群組後，按一下 [刪除資源群組]**** 以移除資源群組、容器登錄，以及儲存於該處的容器映像。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="在入口網站中刪除資源群組":::


## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure 入口網站建立 Azure Container Registry、推送容器映像，以及從登錄中提取映像並加以執行。 請繼續進行 Azure 容器登錄教學課程，以深入了解 ACR。

> [!div class="nextstepaction"]
> [Azure Container Registry 教學課程][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry 工作教學課程][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
