---
title: 使用 Docker Compose 來部署多個容器
titleSuffix: Azure Cognitive Services
description: 瞭解如何部署多個認知服務容器。 本文介紹如何使用 Docker 合成來協調多個 Docker 容器映射。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 458cda927a6a123fcd9962efc6ab705e13f43286
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878777"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>使用 Docker Compose 來部署多個容器

本文介紹如何部署多個 Azure 認知服務容器。 具體來說,您將學習如何使用 Docker Compose 來協調多個 Docker 容器映射。

> [Docker Compose](https://docs.docker.com/compose/)是一種用於定義和運行多容器 Docker 應用程式的工具。 在「撰寫」中,您可以使用 YAML 檔來設定應用程式的服務。 然後,通過運行單個命令從配置創建和啟動所有服務。

在一台主機上協調多個容器映射非常有用。 在本文中,我們將組合讀取和表單識別器容器。

## <a name="prerequisites"></a>Prerequisites

此過程需要必須在本地端安裝和執行的幾個工具:

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* [多克引擎](https://www.docker.com/products/docker-engine)。 確認 Docker CLI 在主控台視窗中工作。
* 具有正確定價層的 Azure 資源。 只有以下定價層才能使用此容器:
  * 僅具有 F0 或標準定價層**的計算機視覺**資源。
  * 僅具有 F0 或標準定價層**的窗體識別器**資源。
  * 有 S0 定價層的**認知服務**資源。

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

填寫並提交[認知服務語音容器申請表](https://aka.ms/speechcontainerspreview/)。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker 撰寫檔案

YAML 檔定義要部署的所有服務。 這些服務依賴於或`DockerFile`現有的容器映射。 在這種情況下,我們將使用兩個預覽圖像。 複製並貼上以下 YAML 檔,並將其另存為*docker-compose.yaml*。 在檔案中提供適當的**apikey、****計費**和**終結點Uri**值。

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
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> 在**卷**節點下指定的主機上創建目錄。 此方法是必需的,因為在嘗試使用捲綁定裝載映射之前,目錄必須存在。

## <a name="start-the-configured-docker-compose-services"></a>啟動設定的 Docker 群組服務

Docker Compose 檔案支援管理已定義服務生命週期中的所有階段:啟動、停止和重建服務;查看服務狀態;和日誌流。 從專案目錄(docker-compose.yaml 檔案所在的位置)打開命令列介面。

> [!NOTE]
> 為避免錯誤,請確保主機與 Docker 引擎正確共用驅動器。 例如,如果*E:\公共預覽*在*docker-compose.yaml*檔中用作目錄,則與 Docker 共享驅動器**E。**

從命令列介面中,執行以下指令以啟動(或重新啟動 *)docker-compose.yaml*檔中定義的所有服務:

```console
docker-compose up
```

當 Docker 首次使用此設定執行**Docker-compose 命令**時,它會提取**服務**節點下設定的影像,然後下載並載入它們:

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
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
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

下載影像後,將啟動影像服務:

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

## <a name="verify-the-service-availability"></a>驗證服務可用性

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

以下是一些輸出範例︰

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>測試容器

在主機上開啟瀏覽器,並使用*docker-compose.yaml*檔中的指定連接埠http://localhost:5021/swagger/index.html轉到**本地主機**,例如 。 例如,可以使用 API 中的 **「嘗試它」** 功能來測試表單識別器終結點。 兩個容器的擺動頁面都應該可用和可測試。

![表單辨識器容器](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [認知服務容器](../cognitive-services-container-support.md)
