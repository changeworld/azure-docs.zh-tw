---
title: 查看工作執行記錄-工作
description: 如何查看和管理 ACR 工作所產生的執行記錄。
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91871927"
---
# <a name="view-and-manage-task-run-logs"></a>查看和管理工作執行記錄

[Azure Container Registry](container-registry-tasks-overview.md)工作中執行的每項工作都會產生記錄輸出，您可以檢查這些輸出，判斷工作步驟是否已順利執行。 

本文說明如何查看和管理工作執行記錄。

## <a name="view-streamed-logs"></a>查看資料流程記錄

當您手動觸發工作時，記錄輸出會直接串流至主控台。 例如，當您使用 [az acr build](/cli/azure/acr#az-acr-build)、 [az acr run](/cli/azure/acr#az-acr-run)或 [az acr task run](/cli/azure/acr/task#az-acr-task-run) 命令以手動方式觸發工作時，您會看到記錄輸出已串流至主控台。 

下列範例 [az acr run](/cli/azure/acr#az-acr-run) 命令會手動觸發工作，以執行從相同登錄提取的容器：

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

資料流程記錄：

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>查看儲存的記錄 

Azure Container Registry 會儲存所有工作的執行記錄。 您可以在 Azure 入口網站中查看儲存的執行記錄。 或者，使用 [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) 命令來查看選取的記錄檔。 依預設，記錄會保留30天。

如果自動觸發工作（例如，由原始程式碼更新），存取儲存的記錄是查看執行記錄的 *唯一* 方法。 自動工作觸發套裝程式括原始碼認可或提取要求、基底映射更新和計時器觸發程式。

若要在入口網站中查看執行記錄：

1. 瀏覽至您的容器登錄。
1. 在 [**服務**] 中 **，選取 [** 工作  >  **執行**]。
1. 選取 **執行識別碼** 以查看執行狀態和執行記錄。 記錄檔包含與資料流程處理的記錄檔相同的資訊（如果有產生的話）。

![View task run login portal](./media/container-registry-tasks-logs/portal-task-run-logs.png)

若要使用 Azure CLI 來查看記錄，請執行 [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) ，並指定執行識別碼、工作名稱或由組建工作建立的特定映射。 如果指定了工作名稱，命令就會顯示上次建立之執行的記錄檔。

下列範例會輸出執行識別碼 *cf4*的記錄檔：

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>替代記錄儲存體

您可能會想要將工作執行記錄儲存在本機檔案系統上，或使用替代的封存方案（例如 Azure 儲存體）。

例如，建立本機 *tasklogs* 目錄，並將 [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) 的輸出重新導向至本機檔案：

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

您也可以將本機記錄檔儲存至 Azure 儲存體。 例如，使用 [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md)、 [Azure 入口網站](../storage/blobs/storage-quickstart-blobs-portal.md)或其他方法，將檔案上傳至儲存體帳戶。


## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Azure Container Registry 工作](container-registry-tasks-overview.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
