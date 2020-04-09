---
title: Docker 容器的配方
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用部分或全部配置設置構建、測試和存儲容器,以便進行部署和重用。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7380ff58d033a68565de7e419ff318f7bdec121d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875073"
---
# <a name="create-containers-for-reuse"></a>建立要重複使用的容器

使用這些容器配方創建可重複使用的認知服務容器。 容器可以使用部分或全部配置設置生成,以便在啟動容器時_不需要_這些設置。

擁有此新容器層(帶有設置)並在本地測試后,可以將容器存儲在容器註冊表中。 當容器啟動時,它只需要當前未存儲在容器中的設置。 專用註冊表容器提供配置空間,以便您將這些設置傳遞到。

## <a name="docker-run-syntax"></a>Docker 執行文法

本文`docker run`檔中的任何示例都假定 Windows`^`主控台具有行延續字元。 請考慮以下事項供您自己使用:

* 若非十分熟悉 Docker 容器，請勿變更引數的順序。
* 如果您使用的是 Windows 以外的作業系統或 Windows 主控台以外的主控台,請使用正確的控制台/終端、載載的資料夾語法以及控制台和系統的行延續字元。  由於認知服務容器是 Linux 作業系統,因此目標裝載使用 Linux 風格的資料夾語法。
* `docker run`示例使用`c:`驅動器上的目錄來避免 Windows 上的任何許可權衝突。 如果您需要使用特定目錄作為輸入目錄，您可能需要授與 Docker 服務權限。

## <a name="store-no-configuration-settings-in-image"></a>儲存映像中儲存未設定設定

每個服務`docker run`的範例命令不儲存在容器中的任何設定設置中。 當您從主控台或註冊表服務啟動容器時,這些配置設置需要傳遞。 專用註冊表容器提供配置空間,以便您將這些設置傳遞到。

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>重用配方:使用容器儲存所有設定設定

為了儲存所有設定設定,請使用這些設定建立`Dockerfile`。

此方法的問題:

* 新容器的名稱和標記與原始容器不同。
* 要更改這些設定,您必須更改 Dockerfile 的值、重建映射並重新發布到註冊表。
* 如果有人可以訪問您的容器註冊錶或本地主機,他們可以運行該容器並使用認知服務終結點。
* 如果您的認知服務不需要輸入載入,請不要將`COPY`行添加到 Dockerfile。

創建 Dockerfile,從要使用的現有認知服務容器中提取,然後使用 Dockerfile 中的 Docker 命令來設置或拉取容器所需的資訊。

此範例：

* `{BILLING_ENDPOINT}`使用從主機的環境密鑰設置`ENV`計費終結點。
* 使用"ENV"`{ENDPOINT_KEY}`從主機的環境金鑰設置計費 API 金鑰。

### <a name="reuse-recipe-store-billing-settings-with-container"></a>重用配方:使用容器儲存計費設定

此示例演示如何從 Dockerfile 構建文本分析的情緒容器。

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

根據需要[在本地](#how-to-use-container-on-your-local-host)或從[專用註冊表容器](#how-to-add-container-to-private-registry)生成和運行該容器。

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>重用配方:使用容器儲存計費和裝載設定

此示例演示如何使用語言理解,從 Dockerfile 保存計費和模型。

* 使用`COPY`從主機的檔案系統複製語言理解 (LUIS) 模型檔。
* LUIS 容器支援多個模型。 如果所有模型都存儲在同一檔夾中,則都需要一`COPY`個語句。
* 從模型輸入目錄的相對父級運行 docker 檔。 對於以下示例,從的`docker build`相對`docker run`父級運行`/input`和命令。 命令上`/input`的第`COPY`一個是主機的目錄。 第二`/input`個是容器的目錄。

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

根據需要[在本地](#how-to-use-container-on-your-local-host)或從[專用註冊表容器](#how-to-add-container-to-private-registry)生成和運行該容器。

## <a name="how-to-use-container-on-your-local-host"></a>如何在本地端主機上使用容器

要產生 Docker`<your-image-name>`檔, 請取代為映像的新名稱,然後使用:

```console
docker build -t <your-image-name> .
```

要執行映像,並在容器停止時將刪除`--rm`(

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>如何向專用註冊表添加容器

按照以下步驟使用 Dockerfile 並將新映射放在專用容器註冊表中。  

1. 使用重用`Dockerfile`的項目建立 。 `Dockerfile`沒有副檔名。

1. 將角括弧中的任何值替換為您自己的值。

1. 使用以下命令將檔案構建到命令列或終端的圖像中。 將角度括弧`<>`中的值替換為您自己的容器名稱和標記。  

    標記選項`-t`是添加有關容器已更改內容的資訊的一種方式。 例如,的`modified-LUIS`容器名稱表示原始容器已分層。 的`with-billing-and-model`標記名稱指示如何修改語言理解 (LUIS) 容器。

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. 從控制台登錄到 Azure CLI。 此命令打開瀏覽器,需要身份驗證。 經過身份驗證後,您可以關閉瀏覽器並繼續在控制台中工作。

    ```azurecli
    az login
    ```

1. 使用主控台使用 Azure CLI 登錄到專用註冊表。

    將角度括弧`<my-registry>`中的值替換為您自己的註冊表名稱。  

    ```azurecli
    az acr login --name <my-registry>
    ```

    如果為服務主體分配了 Docker 登錄,也可以登錄。

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. 使用專用註冊表位置標記容器。 將角度括弧`<my-registry>`中的值替換為您自己的註冊表名稱。 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    如果不使用標記名稱,`latest`則表示為暗示。

1. 將新映射推送到專用容器註冊表。 當您查看專用容器註冊表時,以下 CLI 命令中使用的容器名稱將是儲存庫的名稱。

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立與使用 Azure 容器實體](azure-container-instance-recipe.md)

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