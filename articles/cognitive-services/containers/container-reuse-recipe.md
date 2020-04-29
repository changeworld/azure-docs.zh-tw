---
title: Docker 容器的配方
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用您的部分或所有設定設定來建立、測試及儲存容器，以進行部署和重複使用。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7380ff58d033a68565de7e419ff318f7bdec121d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80875073"
---
# <a name="create-containers-for-reuse"></a>建立要重複使用的容器

使用這些容器配方來建立可重複使用的認知服務容器。 您可以使用部分或所有設定設定來建立容器，以便在容器啟動時_不_需要它們。

一旦擁有這個新的容器層（含設定），而且您已在本機進行測試，您就可以將容器儲存在容器登錄中。 當容器啟動時，它只會需要目前未儲存在容器中的設定。 私用登錄容器會提供設定空間，讓您在中傳遞這些設定。

## <a name="docker-run-syntax"></a>Docker 執行語法

本`docker run`檔中的任何範例都假設 Windows 主控台包含`^`行接續字元。 請考慮下列各項以供您自己使用：

* 若非十分熟悉 Docker 容器，請勿變更引數的順序。
* 如果您使用的作業系統不是 Windows，或是 Windows 主控台以外的主控台，請使用正確的主控台/終端機、掛接的資料夾語法，以及主控台和系統的行接續字元。  由於認知服務容器是 Linux 作業系統，因此目標掛接會使用 Linux 樣式的資料夾語法。
* `docker run`範例會使用`c:`磁片磁碟機上的目錄，以避免在 Windows 上發生任何許可權衝突。 如果您需要使用特定目錄作為輸入目錄，您可能需要授與 Docker 服務權限。

## <a name="store-no-configuration-settings-in-image"></a>在映射中不儲存任何設定

每個`docker run`服務的範例命令不會在容器中儲存任何設定。 當您從主控台或登錄服務啟動容器時，這些設定值必須傳入。 私用登錄容器會提供設定空間，讓您在中傳遞這些設定。

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>重複使用配方：儲存所有具有容器的設定

若要儲存所有的設定，請`Dockerfile`使用這些設定來建立。

這種方法的問題：

* 新容器的名稱和標記會與原始容器不同。
* 若要變更這些設定，您必須變更 Dockerfile 的值、重建映射，然後重新發佈至您的登錄。
* 如果有人取得您容器登錄或本機主機的存取權，他們就可以執行容器，並使用認知服務端點。
* 如果您的認知服務不需要輸入裝載，請不要`COPY`將這些行新增至您的 Dockerfile。

建立 Dockerfile，從您想要使用的現有認知服務容器中提取，然後在 Dockerfile 中使用 docker 命令來設定或提取容器所需的資訊。

此範例：

* 使用`{BILLING_ENDPOINT}` `ENV`，從主機的環境金鑰設定計費端點。
* 使用 ' ENV '， `{ENDPOINT_KEY}`從主機的環境金鑰設定計費 API 金鑰。

### <a name="reuse-recipe-store-billing-settings-with-container"></a>重複使用配方：使用容器儲存帳單設定

這個範例示範如何從 Dockerfile 建立文字分析 ' 情感容器。

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

視需要在本機或從您的私用[登錄](#how-to-use-container-on-your-local-host)[容器](#how-to-add-container-to-private-registry)中，建立並執行容器。

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>重複使用配方：使用容器儲存帳單和掛接設定

這個範例示範如何使用 Language Understanding，從 Dockerfile 儲存計費和模型。

* 使用`COPY`，從主機的檔案系統複製 LANGUAGE UNDERSTANDING （LUIS）模型檔案。
* LUIS 容器支援多個模型。 如果所有模型都儲存在相同的資料夾中，您就需要`COPY`一個語句。
* 從模型輸入目錄的相對父系執行 docker 檔案。 在下列範例中，請從`docker build`的`docker run`相對父系執行和命令`/input`。 `COPY`命令的第一個`/input`是主機電腦的目錄。 第二`/input`個是容器的目錄。

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

視需要在本機或從您的私用[登錄](#how-to-use-container-on-your-local-host)[容器](#how-to-add-container-to-private-registry)中，建立並執行容器。

## <a name="how-to-use-container-on-your-local-host"></a>如何使用本機主機上的容器

若要建立 Docker 檔案，請`<your-image-name>`將取代為映射的新名稱，然後使用：

```console
docker build -t <your-image-name> .
```

若要執行映射，並在容器停止時將它移除`--rm`（）：

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>如何將容器新增至私用登錄

請遵循下列步驟來使用 Dockerfile，並將新的映射放在您的私人容器登錄中。  

1. `Dockerfile`使用 [重複使用] 配方中的文字建立。 A `Dockerfile`沒有延伸模組。

1. 以您自己的值取代角括弧中的任何值。

1. 使用下列命令，將檔案建立在命令列或終端機的映射中。 將角括弧`<>`中的值取代為您自己的容器名稱和標記。  

    標記選項`-t`是一種方式，可新增您已變更之容器的相關資訊。 例如，容器名稱`modified-LUIS`表示原始容器已分層。 的標記名稱`with-billing-and-model`表示 LANGUAGE UNDERSTANDING （LUIS）容器的修改方式。

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. 從主控台登入 Azure CLI。 此命令會開啟瀏覽器並要求驗證。 驗證之後，您可以關閉瀏覽器並繼續在主控台中工作。

    ```azurecli
    az login
    ```

1. 使用主控台的 Azure CLI 登入您的私用登錄。

    將角括弧`<my-registry>`中的值取代為您自己的登錄名稱。  

    ```azurecli
    az acr login --name <my-registry>
    ```

    如果您已指派服務主體，您也可以使用 docker 登入來登入。

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. 以私人登錄位置標記容器。 將角括弧`<my-registry>`中的值取代為您自己的登錄名稱。 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    如果您不使用標記名稱， `latest`則會隱含。

1. 將新的映射推送至您的私人容器登錄。 當您查看私人容器登錄時，下列 CLI 命令中使用的容器名稱會是存放庫的名稱。

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立並使用 Azure 容器實例](azure-container-instance-recipe.md)

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