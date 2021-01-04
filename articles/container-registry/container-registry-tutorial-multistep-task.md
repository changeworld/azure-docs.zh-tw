---
title: 教學課程 - 多步驟 ACR 工作
description: 在本教學課程中，您會了解如何設定「Azure Container Registry 工作」，以在您將原始程式碼認可至 Git 存放庫時，自動在雲端觸發多步驟工作流程來建置、執行及推送容器映像。
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: c8d1179f1c31642b350ab8757a8d4abf71583bfc
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562883"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>教學課程：於認可原始程式碼時在雲端執行多步驟容器工作流程

除了[快速工作](container-registry-tutorial-quick-task.md)以外，ACR 工作支援多步驟及多容器式的工作流程，這些工作流程可在您將原始程式碼認可至 Git 存放庫時自動觸發。 

在本教學課程中，您將了解如何使用 YAML 檔案範例來定義多步驟工作，以在您認可原始程式碼時建置及執行一個或多個容器映像，並將其推送至登錄。 若要讓建立的工作只自動化程式碼認可上的單一映像建置，請參閱[教學課程：於認可原始程式碼時在雲端自動執行容器映像建置](container-registry-tutorial-build-task.md)。 如需 ACR 工作的概觀，請參閱[使用 ACR 工作自動化作業系統和架構修補](container-registry-tasks-overview.md)。

本教學課程內容：

> [!div class="checklist"]
> * 使用 YAML 檔案定義多步驟工作
> * 建立工作
> * (選擇性) 將認證新增至工作，以啟用其他登錄的存取權
> * 測試工作
> * 檢視工作狀態
> * 透過程式碼認可觸發工作

本教學課程假設您已完成[上一個教學課程](container-registry-tutorial-quick-task.md)中的步驟。 如果您尚未完成上一個教學課程的[必要條件](container-registry-tutorial-quick-task.md#prerequisites)一節中的步驟，請先加以完成，再繼續操作。

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-a-multi-step-task"></a>建立多步驟工作

現在，您已完成啟用 ACR 工作以讀取認可狀態以及在存放庫中建立 Webhook 所需的步驟，接下來可以建立多步驟工作來觸發容器映像的建置、執行及推送。

### <a name="yaml-file"></a>YAML 檔案

您可以在 [YAML 檔案](container-registry-tasks-reference-yaml.md)中定義多步驟工作的步驟。 本教學課程的第一個多步驟工作範例會定義在 `taskmulti.yaml` 檔案中，該檔案位在您複製的 GitHub 存放庫根目錄中：

```yml
version: v1.1.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

此多步驟工作會執行下列動作：

1. 從工作目錄中的 Dockerfile 執行 `build`步驟來建置映像。 映像會以 `Run.Registry` (執行工作所在的登錄) 為目標，並以唯一的 ACR 工作執行識別碼進行標記。 
1. 執行 `cmd` 步驟在暫存容器中執行映像。 此範例會在背景中啟動長時間執行的容器，並傳回容器識別碼，然後停止容器。 在實際案例中，您可以包含測試執行容器的步驟，以確保容器能正確執行。
1. 在 `push` 步驟中，將已建置的映像推送到執行登錄。

### <a name="task-command"></a>工作命令

首先，請在這些殼層環境變數中填入您的環境適用的值。 此步驟並不是必要動作，但可簡化在本教學課程中執行多行 Azure CLI 命令的作業。 若未填入這些環境變數，則必須手動取代命令範例中出現的每個值。

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

現在，請執行下列 [az acr task create][az-acr-task-create] 命令以建立工作：

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

此工作會指定只要有程式碼認可至 `--context` 所指定存放庫中的「主要」分支，ACR 工作即會從該分支中的程式碼執行多步驟工作。 存放庫根路徑中 `--file` 指定的 YAML 會定義步驟。 

成功執行的 [az acr task create][az-acr-task-create] 命令會產生如下的輸出：

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2020-11-20T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
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
          "branch": "main",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
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

## <a name="test-the-multi-step-workflow"></a>測試多步驟工作流程

若要測試多步驟工作，請執行 [az acr task run][az-acr-task-run] 命令以手動觸發多步驟工作：

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

根據預設，`az acr task run` 命令會在您執行命令時將記錄輸出串流處理至主控台。 輸出會顯示每個工作步驟的執行進度。 以下輸出會扼要地顯示關鍵步驟。

```output
Queued a run with ID: cab
Waiting for an agent...
2020/11/20 00:03:31 Downloading source code...
2020/11/20 00:03:33 Finished downloading source code
2020/11/20 00:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2020/11/20 00:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 00:03:34 Successfully set up Docker network: acb_default_network
2020/11/20 00:03:34 Setting up Docker configuration...
2020/11/20 00:03:34 Successfully set up Docker configuration
2020/11/20 00:03:34 Logging in to registry: myregistry.azurecr.io
2020/11/20 00:03:35 Successfully logged into myregistry.azurecr.io
2020/11/20 00:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:35 Scanning for dependencies...
2020/11/20 00:03:36 Successfully scanned dependencies
2020/11/20 00:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB
[...]
Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:43 Successfully executed container: acb_step_0
2020/11/20 00:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2020/11/20 00:03:44 Successfully executed container: acb_step_1
2020/11/20 00:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1
[...]
2020/11/20 00:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2020/11/20 00:03:46 Populating digests for step ID: acb_step_0...
2020/11/20 00:03:47 Successfully populated digests for step ID: acb_step_0
2020/11/20 00:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2020/11/20 00:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2020/11/20 00:03:47 The following dependencies were found:
2020/11/20 00:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cab was successful after 18s
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
git push origin main
```

在您執行 `git push` 命令時，系統可能會要求您提供 GitHub 認證。 請提供您的 GitHub 使用者名稱，並輸入您先前為密碼建立的個人存取權杖 (PAT)。

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

在您將認可推送至存放庫後，ACR 工作所建立的 Webhook 即會在 Azure Container Registry 中引發並啟動工作。 請顯示目前所執行工作的記錄，以確認並監視建置進度：

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

輸出會類似於下列內容，顯示目前正在執行 (或最後執行) 的工作：

```output
Showing logs of the last created run.
Run ID: cad

[...]

Run ID: cad was successful after 37s
```

## <a name="list-builds"></a>列出建置

若要查看 ACR 工作已為登錄完成的工作流程執行，請執行 [az acr task list-runs][az-acr-task-list-runs] 命令：

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

此命令的輸出應類似於下列內容。 ACR 工作已執行的流程執行會顯示出來，且最新工作的 [觸發程序] 資料行中會出現「Git 認可」：

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cad       example1        linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:35
cac       taskhelloworld  linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:22
cab       example1        linux       Succeeded  Manual     2020-11-20T00:18:36Z  00:00:47
```

## <a name="create-a-multi-registry-multi-step-task"></a>建立多登錄的多步驟工作

根據預設，ACR 工作具有從登錄 (工作執行之處) 推送或提取映像的權限。 您可以執行以一個或多個登錄 (除了執行登錄以外) 為目標的多步驟工作。 例如，您可能需要在一個登錄中建置映像，並將具有不同標記的映像儲存在生產系統可存取的的第二個登錄。 此範例會示範如何建立這類工作，並提供另一個登錄的認證。

如果您還沒有第二個登錄，請為此範例建立一個。 如果您需要登錄，請參閱[上一個教學課程](container-registry-tutorial-quick-task.md)或[快速入門：使用 Azure CLI 建立容器登錄](container-registry-get-started-azure-cli.md)。

若要建立工作，您需要有登錄的登入伺服器名稱，名稱形式為 mycontainerregistrydate.azurecr.io (皆為小寫)。 在此範例中，您可以使用第二個登錄來儲存標記建置日期的映像。

### <a name="yaml-file"></a>YAML 檔案

本教學課程的第二個多步驟工作範例會定義在 `taskmulti-multiregistry.yaml` 檔案中，該檔案位在您複製的 GitHub 存放庫根目錄中：

```yml
version: v1.1.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

此多步驟工作會執行下列動作：

1. 從工作目錄中的 Dockerfile 執行兩個 `build` 步驟來建置映像：
    * 第一個步驟會以 `Run.Registry` (執行工作所在的登錄) 為目標，並以 ACR 工作執行識別碼進行標記。 
    * 第二個步驟會以根據 `regDate` 值識別的登錄為目標，該值會在您建立工作時設定 (或透過將外部 `values.yaml` 檔案傳遞至 `az acr task create` 來提供)。 此映像上會標記執行日期。
1. 執行 `cmd` 步驟来執行其中一個已建置的容器。 此範例會在背景中啟動長時間執行的容器，並傳回容器識別碼，然後停止容器。 在實際案例中，您可以測試執行的容器，以確保容器能正確執行。
1. 在 `push` 步驟中，我們會推送所建置的映像，第一個堆送至執行登錄，第二個推送至以 `regDate` 識別的登錄。

### <a name="task-command"></a>工作命令

藉由執行下列 [az acr task create][az-acr-task-create] 命令，使用先前定義的殼層環境變數建立工作。 將登錄名稱取代為 mycontainerregistrydate。

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>新增工作認證

若要將映像推送至根據 `regDate` 值識別的登錄，請使用 [az acr task credential add][az-acr-task-credential-add] 命令，將該登錄的登入認證新增至工作。

在此範例中，建議您建立 [服務主體](container-registry-auth-service-principal.md)，並使其有權存取 *AcrPush* 角色範圍的登錄，而具備推送映像的權限。 若要建立服務主體，請參閱此 [Azure CLI 指令碼](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)。

在下列 `az acr task credential add` 命令中，傳遞服務主體的應用程式識別碼和密碼。 請務必以您第二個登錄的名稱更新登入伺服器名稱 *mycontainerregistrydate*：

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

CLI 會傳回您所新增的登錄登入伺服器名稱。

### <a name="test-the-multi-step-workflow"></a>測試多步驟工作流程

如同前述範例，若要測試多步驟工作，請執行 [az acr task run][az-acr-task-run] 命令以手動觸發多步驟工作。 若要以傳送至 Git 存放庫的認可來觸發工作，請參閱[透過認可觸發建置](#trigger-a-build-with-a-commit)一節。

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

根據預設，`az acr task run` 命令會在您執行命令時將記錄輸出串流處理至主控台。 跟之前一樣，輸出會顯示每個工作步驟的執行進度。 輸出會扼要地顯示關鍵步驟。

輸出：

```output
Queued a run with ID: cf1g
Waiting for an agent...
2020/11/20 04:33:39 Downloading source code...
2020/11/20 04:33:41 Finished downloading source code
2020/11/20 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2020/11/20 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 04:33:43 Successfully set up Docker network: acb_default_network
2020/11/20 04:33:43 Setting up Docker configuration...
2020/11/20 04:33:44 Successfully set up Docker configuration
2020/11/20 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:47 Scanning for dependencies...
2020/11/20 04:33:47 Successfully scanned dependencies
2020/11/20 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:33:55 Successfully executed container: acb_step_0
2020/11/20 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:55 Scanning for dependencies...
2020/11/20 04:33:56 Successfully scanned dependencies
2020/11/20 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:33:57 Successfully executed container: acb_step_1
2020/11/20 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2020/11/20 04:33:58 Successfully executed container: acb_step_2
2020/11/20 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:58 Launching container with name: acb_step_3
test
2020/11/20 04:34:09 Successfully executed container: acb_step_3
2020/11/20 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]
[...]
2020/11/20 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]
[...]
2020/11/20 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2020/11/20 04:34:19 Populating digests for step ID: acb_step_0...
2020/11/20 04:34:21 Successfully populated digests for step ID: acb_step_0
2020/11/20 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2020/11/20 04:34:21 Populating digests for step ID: acb_step_1...
2020/11/20 04:34:23 Successfully populated digests for step ID: acb_step_1
2020/11/20 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2020/11/20 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2020/11/20 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2020/11/20 04:34:23 The following dependencies were found:
2020/11/20 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立能在將原始程式碼認可至 Git 存放庫時自動觸發的多步驟及多容器式工作。 如需多步驟工作的進階功能，包括平行和相依步驟執行，請參閱 [ACR 工作的 YAML 參考](container-registry-tasks-reference-yaml.md)。 請進入下一個教學課程，以了解如何建立會在容器映像的基底映像更新時觸發建置的工作。

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
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
