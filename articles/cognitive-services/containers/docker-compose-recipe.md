---
title: 使用 Docker Compose 來部署多個容器
titleSuffix: Azure Cognitive Services
description: 瞭解如何部署多個認知服務容器。 本文說明如何使用 Docker Compose 來協調多個 Docker 容器映射。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 54a2aac3db47d60f02a45adae9aaa6077d675a43
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716905"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>使用 Docker Compose 來部署多個容器

本文說明如何部署多個 Azure 認知服務容器。 具體而言，您將瞭解如何使用 Docker Compose 來協調多個 Docker 容器映射。

> [Docker Compose](https://docs.docker.com/compose/)是用來定義和執行多容器 Docker 應用程式的工具。 在 [撰寫中] 中，您可以使用 YAML 檔案來設定應用程式的服務。 然後，藉由執行單一命令，從您的設定建立並啟動所有服務。

在單一主機電腦上協調多個容器映射可能會很有用。 在本文中，我們會將「辨識文字」和「表單辨識器」容器一起提取在一起。

## <a name="prerequisites"></a>Prerequisites

此程式需要幾個必須在本機上安裝和執行的工具：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* [Docker 引擎](https://www.docker.com/products/docker-engine)。 確認 Docker CLI 可在主控台視窗中運作。
* 具有正確定價層的 Azure 資源。 只有下列定價層可與此容器搭配使用：
  * 僅**電腦視覺**具有 F0 或標準定價層的資源。
  * 僅使用 F0 或標準定價層的**表單辨識器**資源。
  * 有 S0 定價層的**認知服務**資源。

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

完成並提交[認知服務語音容器要求表單](https://aka.ms/speechcontainerspreview/)。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker Compose 檔案

YAML 檔案會定義要部署的所有服務。 這些服務依賴 `DockerFile` 或現有的容器映射。 在此情況下，我們將使用兩個預覽影像。 複製並貼上下列 YAML 檔案，並將它儲存為*YAML*。 在檔案中提供適當的**apikey**、**帳單**和**EndpointUri**值。

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> 在 [**磁片**區] 節點底下指定的主機電腦上建立目錄。 這是必要的方法，因為在您嘗試使用磁片區系結來掛接映射之前，目錄必須存在。

## <a name="start-the-configured-docker-compose-services"></a>啟動設定的 Docker Compose 服務

Docker Compose 檔案可讓您管理已定義服務生命週期中的所有階段：啟動、停止和重建服務;正在查看服務狀態;和記錄資料流程。 從專案目錄（docker yaml 檔案所在的位置）開啟命令列介面。

> [!NOTE]
> 為避免發生錯誤，請確定主機電腦已正確地與 Docker 引擎共用磁片磁碟機。 例如，如果 E:\publicpreview 是用來作為 yaml 檔案中的目錄，請與 Docker 共用磁片磁碟機 E。

從命令列介面執行下列命令，以啟動（或重新開機） yaml 檔案中定義的所有服務：

```console
docker-compose up
```

Docker 第一次使用此**設定來執行 docker 撰寫**命令時，它會提取在 [**服務**] 節點下設定的映射，然後下載並掛接它們：

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

下載影像之後，映射服務就會啟動：

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>確認服務可用性

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

以下是一些範例輸出：

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>測試辨識文字容器

在主機電腦上開啟瀏覽器，並使用 yaml 檔案中的指定埠（例如 http://localhost:5021/swagger/index.html）移至**localhost** 。 您可以使用 API 中的「試試看」功能來測試辨識文字端點。

![辨識文字容器](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>測試表單辨識器容器

在主機電腦上開啟瀏覽器，並使用 yaml 檔案中的指定埠（例如 http://localhost:5010/swagger/index.html）移至**localhost** 。 您可以使用 API 中的「試試看」功能來測試表單辨識器端點。

![表單辨識器容器](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [認知服務容器](../cognitive-services-container-support.md)
