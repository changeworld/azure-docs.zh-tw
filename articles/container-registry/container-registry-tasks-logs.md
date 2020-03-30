---
title: 查看任務運行日誌 - 任務
description: 如何查看和管理由 ACR 任務生成的運行日誌。
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246964"
---
# <a name="view-and-manage-task-run-logs"></a>查看和管理工作運行日誌

[Azure 容器註冊表任務中](container-registry-tasks-overview.md)運行的每個任務都會生成日誌輸出，您可以檢查日誌輸出以確定任務步驟是否成功運行。 

本文介紹如何查看和管理工作運行日誌。

## <a name="view-streamed-logs"></a>查看流式日誌

手動觸發任務時，日誌輸出將直接資料流到主控台。 例如，當您使用[az acr 生成](/cli/azure/acr#az-acr-build)[、az acr 運行](/cli/azure/acr#az-acr-run)或 az [acr 任務運行](/cli/azure/acr/task#az-acr-task-run)命令手動觸發任務時，您將看到日誌輸出資料流到主控台。 

以下示例[az acr 運行](/cli/azure/acr#az-acr-run)命令手動觸發運行從同一註冊表提取的容器的任務：

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

流式日誌：

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

## <a name="view-stored-logs"></a>查看存儲的日誌 

Azure 容器註冊表存儲所有任務的運行日誌。 您可以在 Azure 門戶中查看存儲的運行日誌。 或者，使用[az acr 任務日誌](/cli/azure/acr/task#az-acr-task-logs)命令查看選定的日誌。 預設情況下，日誌將保留 30 天。

如果任務自動觸發（例如由原始程式碼更新），則訪問存儲的日誌是查看運行日誌*的唯*一方法。 自動任務觸發器包括原始程式碼提交或拉取請求、基本映射更新和計時器觸發器。

要查看門戶中的運行日誌，請查看：

1. 導航到容器註冊表。
1. 在 **"服務**"中，選擇 > **"任務運行**"。 **Tasks**
1. 選擇**運行 ID**以查看運行狀態並運行日誌。 日誌包含與流式日誌相同的資訊（如果生成了）。

![查看任務運行登錄門戶](./media/container-registry-tasks-logs/portal-task-run-logs.png)

要使用 Azure CLI 查看日誌，請運行[az acr 任務日誌](/cli/azure/acr/task#az-acr-task-logs)並指定運行 ID、任務名稱或生成任務創建的特定映射。 如果指定了任務名稱，該命令將顯示上次創建運行的日誌。

以下示例使用 ID *cf4*輸出運行的日誌：

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>備用日誌存儲

您可能希望在本地檔案系統上存儲任務運行日誌，或使用替代存檔解決方案（如 Azure 存儲）。

例如，創建本地*任務日誌*目錄，並將[az acr 任務日誌](/cli/azure/acr/task#az-acr-task-logs)的輸出重定向到本地檔：

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

還可以將本地日誌檔保存到 Azure 存儲。 例如，使用[Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) [、Azure 門戶](../storage/blobs/storage-quickstart-blobs-portal.md)或其他方法將檔上載到存儲帳戶。


## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 容器註冊表任務](container-registry-tasks-overview.md)

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
