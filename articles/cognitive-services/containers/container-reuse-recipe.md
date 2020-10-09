---
title: 適用于 Docker 容器的配方
titleSuffix: Azure Cognitive Services
description: 瞭解如何建立、測試和儲存容器，以及部署和重複使用的部分或所有設定。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7380ff58d033a68565de7e419ff318f7bdec121d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80875073"
---
# <a name="create-containers-for-reuse"></a>建立要重複使用的容器

使用這些容器配方來建立可重複使用的認知服務容器。 您可以使用部分或所有的設定來建立容器，以便在容器啟動時 _不_ 需要這些設定。

當您將這個新的容器層 (設定) 並在本機進行測試之後，就可以將容器儲存在容器登錄中。 當容器啟動時，只會需要目前未儲存在容器中的設定。 私人登錄容器會提供設定空間，讓您在中傳遞這些設定。

## <a name="docker-run-syntax"></a>Docker 執行語法

`docker run`本檔中的任何範例都採用具有 `^` 行接續字元的 Windows 主控台。 請考慮下列各項以供您使用：

* 若非十分熟悉 Docker 容器，請勿變更引數的順序。
* 如果您使用 Windows 以外的作業系統或 Windows 主控台以外的主控台，請使用正確的主控台/終端機、適用于裝載的資料夾語法，以及主控台和系統的行接續字元。  因為認知服務容器是 Linux 作業系統，所以目標裝載會使用 Linux 樣式的資料夾語法。
* `docker run` 範例會使用 `c:` 磁片磁碟機上的目錄，以避免在 Windows 上發生任何許可權衝突。 如果您需要使用特定目錄作為輸入目錄，您可能需要授與 Docker 服務權限。

## <a name="store-no-configuration-settings-in-image"></a>不在映射中儲存任何設定

`docker run`每個服務的範例命令都不會將任何設定設定儲存在容器中。 當您從主控台或登錄服務啟動容器時，必須傳入這些設定。 私人登錄容器會提供設定空間，讓您在中傳遞這些設定。

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>重複使用配方：將所有設定設定為容器

為了儲存所有的設定，請 `Dockerfile` 使用這些設定來建立。

此方法的問題：

* 新容器具有來自原始容器的不同名稱和標記。
* 為了變更這些設定，您必須變更 Dockerfile 的值、重建映射，然後重新發佈至您的登錄。
* 如果有人取得容器登錄或本機主機的存取權，他們就可以執行容器並使用認知服務端點。
* 如果您的認知服務不需要輸入裝載，請勿將這 `COPY` 幾行新增至您的 Dockerfile。

建立 Dockerfile，從您想要使用的現有認知服務容器中提取，然後使用 Dockerfile 中的 docker 命令來設定或提取容器所需的資訊。

此範例：

* `{BILLING_ENDPOINT}`使用來自主機環境金鑰的設定計費端點 `ENV` 。
* `{ENDPOINT_KEY}`從主機的環境金鑰（使用 ' ENV）設定計費 API 金鑰。

### <a name="reuse-recipe-store-billing-settings-with-container"></a>重複使用配方：使用容器儲存帳單設定

此範例顯示如何從 Dockerfile 建立文字分析的情感容器。

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

視需要在 [本機](#how-to-use-container-on-your-local-host) 或從 [私人登錄容器](#how-to-add-container-to-private-registry) 建立和執行容器。

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>重複使用配方：使用容器儲存帳單和掛接設定

此範例示範如何使用 Language Understanding，從 Dockerfile 儲存帳單和模型。

* 使用從主機的檔案系統複製 Language Understanding (LUIS) 模型檔 `COPY` 。
* LUIS 容器支援一個以上的模型。 如果所有模型都儲存在相同的資料夾中，您就需要一個 `COPY` 語句。
* 從模型輸入目錄的相對父系執行 docker 檔案。 針對下列範例，請 `docker build` `docker run` 從的相對父系執行和命令 `/input` 。 命令的第一個 `/input` `COPY` 是主機電腦的目錄。 第二個 `/input` 是容器的目錄。

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

視需要在 [本機](#how-to-use-container-on-your-local-host) 或從 [私人登錄容器](#how-to-add-container-to-private-registry) 建立和執行容器。

## <a name="how-to-use-container-on-your-local-host"></a>如何在您的本機主機上使用容器

若要建立 Docker 檔案，請以 `<your-image-name>` 映射的新名稱取代，然後使用：

```console
docker build -t <your-image-name> .
```

若要執行映射，並在容器停止 (時將其移除 `--rm`) ：

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>如何將容器新增至私人登錄

遵循下列步驟以使用 Dockerfile，並將新的映射放在您的私人容器登錄中。  

1. `Dockerfile`使用重複使用配方的文字建立。 `Dockerfile`沒有延伸模組。

1. 將角括弧中的任何值取代為您自己的值。

1. 使用下列命令，在命令列或終端機的映射中建立檔案。 將角括弧中的值取代為 `<>` 您自己的容器名稱和標記。  

    標記選項 `-t` 是一種方式，可新增您已針對容器變更之內容的相關資訊。 例如，的容器名稱 `modified-LUIS` 表示原始容器已分層。 的標記名稱 `with-billing-and-model` 表示已修改 Language Understanding (LUIS) 容器的方式。

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. 從主控台登入 Azure CLI。 此命令會開啟瀏覽器並需要驗證。 經過驗證後，您就可以關閉瀏覽器，然後繼續在主控台中工作。

    ```azurecli
    az login
    ```

1. 使用主控台的 Azure CLI 來登入您的私人登錄。

    將角括弧中的值取代為 `<my-registry>` 您自己的登錄名稱。  

    ```azurecli
    az acr login --name <my-registry>
    ```

    如果您已指派服務主體，您也可以使用 docker 登入登入。

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. 標記具有私人登錄位置的容器。 將角括弧中的值取代為 `<my-registry>` 您自己的登錄名稱。 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    如果您未使用標記名稱， `latest` 則會隱含。

1. 將新的映射推送至您的私人容器登錄。 當您查看私用容器登錄時，在下列 CLI 命令中使用的容器名稱將會是存放庫的名稱。

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立和使用 Azure 容器實例](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->