---
title: 教學課程 - 在程式碼認可上建置映像
description: 在本教學課程中，您會了解如何設定「Azure Container Registry 工作」，以在您將原始程式碼認可至 Git 存放庫時，自動在雲端觸發容器映像建置。
ms.topic: tutorial
ms.date: 05/04/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 6882cb683e0bd8b76bb1207e628e43f24c7b5987
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252124"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>教學課程：於認可原始程式碼時在雲端自動執行容器映像建置

除了[快速工作](container-registry-tutorial-quick-task.md)之外，ACR 工作還能在您將原始程式碼認可至 Git 存放庫時，自動化雲端中的 Docker 容器映像建置。 ACR 工作支援的 Git 內容包括公用或私人 GitHub 或 Azure 存放庫。

> [!NOTE]
> 目前，ACR 工作不支援 GitHub Enterprise 存放庫中的認可或提取要求觸發程序。

在本教學課程中，當您將原始程式碼認可至 Git 存放庫時，ACR 工作會建置並推送在 Dockerfile 中指定的單一容器映像。 若要建立[多步驟工作](container-registry-tasks-multi-step.md)，以在認可程式碼時，使用 YAML 檔案來定義建置、推送及選擇性測試多個容器的步驟，請參閱[教學課程：於認可原始程式碼時在雲端執行多步驟容器工作流程](container-registry-tutorial-multistep-task.md)。 如需 ACR 工作的概觀，請參閱[使用 ACR 工作自動化作業系統和架構修補](container-registry-tasks-overview.md)

本教學課程內容：

> [!div class="checklist"]
> * 建立工作
> * 測試工作
> * 檢視工作狀態
> * 透過程式碼認可觸發工作

本教學課程假設您已完成[上一個教學課程](container-registry-tutorial-quick-task.md)中的步驟。 如果您尚未完成上一個教學課程的[必要條件](container-registry-tutorial-quick-task.md#prerequisites)一節中的步驟，請先加以完成，再繼續操作。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您想要在本機使用 Azure CLI，必須安裝 Azure CLI **2.0.46** 版或更新版本，並使用 [az login][az-login] 登入。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級 CLI，請參閱[安裝 Azure CLI][azure-cli]。

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-the-build-task"></a>建立建置工作

現在，您已完成啟用 ACR 工作以讀取認可狀態以及在存放庫中建立 Webhook 所需的步驟，接下來可以建立工作，以在認可至存放庫時觸發容器映像建置。

首先，請在這些殼層環境變數中填入您的環境適用的值。 此步驟並不是必要動作，但可簡化在本教學課程中執行多行 Azure CLI 命令的作業。 若未填入這些環境變數，則必須手動取代命令範例中出現的每個值。

[![內嵌啟動](https://shell.azure.com/images/launchcloudshell.png "啟動 Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

現在，請執行下列 [az acr task create][az-acr-task-create] 命令以建立工作：

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> 如果您先前已在預覽期間使用 `az acr build-task` 命令建立工作，則必須使用 [az acr task][az-acr-task] 命令重新建立這些工作。

此工作會指定只要有程式碼認可至 `--context` 所指定之存放庫中的「主要」  分支，ACR 工作即會從該分支中的程式碼建置容器映像。 系統會使用 `--file` 所指定、位於存放庫根目錄中的 Dockerfile 來建置映像。 `--image` 引數會針對映像標記的版本部分指定 `{{.Run.ID}}` 的參數化值，以確保建置的映像會與特定的組建相互關聯，並加上唯一標記。

成功執行的 [az acr task create][az-acr-task-create] 命令會產生如下的輸出：

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2018-09-14T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-build-task"></a>測試建置工作

現在，您已有定義組建的工作。 若要測試組建管線，請執行 [az acr task run][az-acr-task-run] 命令以手動觸發建置：

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

根據預設，`az acr task run` 命令會在您執行命令時將記錄輸出串流處理至主控台。

```console
$ az acr task run --registry $ACR_NAME --name taskhelloworld

2018/09/17 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2018/09/17 22:51:00 Setting up Docker configuration...
2018/09/17 22:51:02 Successfully set up Docker configuration
2018/09/17 22:51:02 Logging in to registry: myregistry.azurecr.io
2018/09/17 22:51:03 Successfully logged in
2018/09/17 22:51:03 Executing step: build
2018/09/17 22:51:03 Obtaining source code and scanning for dependencies...
2018/09/17 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 5f574fcf5816
Step 3/5 : RUN cd /src && npm install
 ---> Running in b1bca3b5f4fc
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.078s
Removing intermediate container b1bca3b5f4fc
 ---> 44457db20dac
Step 4/5 : EXPOSE 80
 ---> Running in 9e6f63ec612f
Removing intermediate container 9e6f63ec612f
 ---> 74c3e8ea0d98
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:11 Executing step: push
2018/09/17 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2018/09/17 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2018/09/17 22:51:21 Populating digests for step id: build...
2018/09/17 22:51:22 Successfully populated digests for step id: build
2018/09/17 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: da2 was successful after 27s
```

## <a name="trigger-a-build-with-a-commit"></a>透過認可觸發建置

現在您已藉由手動執行測試了工作，接下來請透過原始程式碼的變更自動加以觸發。

首先，請確定您位於[存放庫][sample-repo]的本機複本所在的目錄中：

```console
cd acr-build-helloworld-node
```

接著，執行下列命令以建立新檔案，然後將其認可並推送至 GitHub 上的存放庫分支：

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

在您執行 `git push` 命令時，系統可能會要求您提供 GitHub 認證。 請提供您的 GitHub 使用者名稱，並輸入您先前為密碼建立的個人存取權杖 (PAT)。

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

在您將認可推送至存放庫後，ACR 工作所建立的 Webhook 即會在 Azure Container Registry 中引發並啟動建置。 請顯示目前所執行工作的記錄，以確認並監視建置進度：

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

輸出會類似於下列內容，顯示目前正在執行 (或最後執行) 的工作：

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: da4

[...]

Run ID: da4 was successful after 38s
```

## <a name="list-builds"></a>列出建置

若要查看 ACR 工作已為登錄完成的工作流程執行，請執行 [az acr task list-runs][az-acr-task-list-runs] 命令：

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

此命令的輸出應類似於下列內容。 ACR 工作已執行的流程執行會顯示出來，且最新工作的 [觸發程序] 資料行中會出現「Git 認可」：

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用工作，在將原始程式碼認可至 Git 存放庫時於 Azure 中自動觸發容器映像建置。 請進入下一個教學課程，以了解如何建立會在容器映像的基底映像更新時觸發建置的工作。

> [!div class="nextstepaction"]
> [在基底映像更新時自動執行建置](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-login]: /cli/azure/reference-index#az-login



