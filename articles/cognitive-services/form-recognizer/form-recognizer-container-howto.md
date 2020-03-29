---
title: 如何為表單識別器安裝和運行容器
titleSuffix: Azure Cognitive Services
description: 本文將介紹如何使用 Azure 認知服務表單識別器容器來分析表單和表資料。
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: dapine
ms.openlocfilehash: c8ce4b913548429ff83e0b8aa3cb65455fc9b4c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474691"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>安裝並運行表單識別器容器（預覽）

Azure 表單識別器應用機器學習技術來標識和提取表單中的索引碼對和表。 它將值和表條目與鍵值對關聯，然後輸出包含原始檔案中關係的結構化資料。 

為了降低複雜性並輕鬆地將自訂表格單識別器模型集成到工作流自動化過程或其他應用程式中，可以使用簡單的 REST API 呼叫模型。 只需要五個表單文檔（或一個空表單和兩個填寫表單），因此您可以快速、準確地獲得結果，並根據特定內容進行定制。 無需大量手動干預或廣泛的資料科學專業知識。 它不需要資料標記或資料注釋。

> [!IMPORTANT]
> 表單識別器容器當前使用表單識別器 API 的版本 1.0。 您可以使用託管服務來訪問最新版本的 API。

| 函式 | 特性 |
|----------|----------|
| 表單辨識器 | <li>處理 PDF、PNG 和 JPG 檔<li>使用至少五種相同佈局的自訂模型進行訓練 <li>提取鍵值對和表資訊 <li>使用 Azure 認知服務電腦視覺 API 識別文本功能從表單內的圖像中檢測和提取列印的文本<li>不需要注釋或標注 |

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

在使用表單識別器容器之前，必須滿足以下先決條件：

| 必要 | 目的 |
|----------|---------|
| Docker 引擎 | 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> 在 Windows 上，也必須將 Docker 設定為支援 Linux 容器。<br><br> |
| 熟悉 Docker | 您應該對 Docker 概念（如註冊表、存儲庫、容器和容器映射）的基本瞭解以及基本`docker`命令的知識。 |
| Azure CLI | 在主機上安裝[Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) |
| 電腦視覺 API 資源 | 要處理掃描的文檔和圖像，您需要電腦視覺資源。 可以作為 Azure 資源（REST API 或 SDK）或*認知服務-識別文本*[容器](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)訪問"識別文本"功能。 適用一般計費費用。 <br><br>將電腦視覺資源（Azure 雲或認知服務容器）的 API 金鑰和終結點交用。 使用此 API 鍵，終結點為 **[COMPUTER_VISION_API_KEY]** 和 **[COMPUTER_VISION_ENDPOINT_URI]**。<br><br> 如果使用*認知服務識別文本*容器，請確保：<br><br>表單識別器容器的電腦視覺金鑰是`docker run`*認知服務識別文本*容器的電腦視覺命令中指定的金鑰。<br>帳單終結點是容器的終結點（例如。 `http://localhost:5000` 如果在同一主機上同時使用電腦視覺容器和表單識別器容器，則不能同時使用預設埠*5000*啟動它們。 |
| 表單辨識器資源 | 要使用這些容器，您必須具有：<br><br>用於獲取關聯的 API 金鑰和終結點 URI 的 Azure**表單識別器**資源。 這兩個值在 Azure 門戶**表單識別器**概述和金鑰頁上都可用，啟動容器需要這兩個值。<br><br>**[FORM_RECOGNIZER_API_KEY]**： 金鑰頁上的兩個可用資源鍵之一<br><br>**[FORM_RECOGNIZER_ENDPOINT_URI]**： 概述頁上提供的終結點 |

> [!NOTE]
> 電腦視覺資源名稱應為單個單詞，沒有連字號`-`或任何其他特殊字元。 此限制是為了確保表單識別器和識別文本容器的相容性。

## <a name="gathering-required-parameters"></a>收集所需的參數

所有認知服務容器都需要三個主要參數。 使用者授權合約 （EULA） 必須具有 的值。 `accept` 此外，還需要終結點 URL 和 API 金鑰。

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>端點`{COMPUTER_VISION_ENDPOINT_URI}`URI 和`{FORM_RECOGNIZER_ENDPOINT_URI}`

**端點**URI 值在相應認知服務資源的 Azure 門戶*概述*頁上可用。 導航到 *"概述"* 頁，將滑鼠懸停在終結點上`Copy to clipboard`<span class="docon docon-edit-copy x-hidden-focus"></span>，並顯示一個圖示。 根據需要複製和使用。

![收集終結點 uri 供以後使用](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>鑰匙`{COMPUTER_VISION_API_KEY}`和`{FORM_RECOGNIZER_API_KEY}`

此金鑰用於啟動容器，並在相應認知服務資源的 Azure 門戶的"金鑰"頁上可用。 導航到 *"金鑰"* 頁，然後按一下該`Copy to clipboard`<span class="docon docon-edit-copy x-hidden-focus"></span>圖示。

![獲取兩個金鑰之一，供以後使用](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> 這些訂閱金鑰用於訪問您的認知服務 API。 不要共用您的金鑰。 使用 Azure 金鑰保存庫安全地存儲它們。 我們還建議定期重新生成這些金鑰。 進行 API 呼叫只需要一個金鑰。 重新生成第一個鍵時，可以使用第二個鍵繼續訪問服務。

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須首先填寫並提交[認知服務表單識別器容器訪問請求表單](https://aka.ms/FormRecognizerContainerRequestAccess)，以請求訪問容器。 這樣做也會讓您註冊電腦視覺。 您無需單獨註冊電腦視覺請求表單。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表描述了為每個表單識別器容器分配的最小和推薦的 CPU 內核和記憶體：

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 表單辨識器 | 2 核心、4 GB 記憶體 | 4 核心，8 GB 記憶體 |
| 辨識文字 | 1 核，8 GB 記憶體 | 2 個內核，8 GB 記憶體 |

* 每個核心必須至少 2.6 GHz 或更快。
* 核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

> [!Note]
> 最小值和推薦值基於 Docker 限制，*而不是*主機資源。

## <a name="get-the-container-images-with-the-docker-pull-command"></a>使用 Docker 拉取命令獲取容器映射

**表單識別器**和**識別文本**產品提供的容器映射在以下容器註冊表中都有：

| 容器 | 完全限定的圖像名稱 |
|-----------|------------|
| 表單辨識器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| 辨識文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

您需要兩個容器，請注意，**識別器文本**容器在[本文外部是詳細介紹的。](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>表單辨識器容器的 Docker Pull

#### <a name="form-recognizer"></a>表單辨識器

要獲取表單識別器容器，請使用以下命令：

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>辨識文字容器的 Docker Pull

#### <a name="recognize-text"></a>辨識文字

要獲取"識別文本"容器，請使用以下命令：

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機](#the-host-computer)上後，使用以下過程處理容器。

1. 使用所需的計費設定[執行容器](#run-the-container-by-using-the-docker-run-command)。 `docker run` 命令有相關[範例](form-recognizer-container-configuration.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測終結點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-by-using-the-docker-run-command"></a>使用 Docker 運行命令運行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 有關如何獲取`{COMPUTER_VISION_ENDPOINT_URI}``{COMPUTER_VISION_API_KEY}``{FORM_RECOGNIZER_ENDPOINT_URI}`和`{FORM_RECOGNIZER_API_KEY}`值的詳細資訊，請參閱[收集所需參數](#gathering-required-parameters)。

[命令的示例可用。](form-recognizer-container-configuration.md#example-docker-run-commands) `docker run`

### <a name="form-recognizer"></a>表單辨識器

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

此命令：

* 從容器映射運行表單識別器容器。
* 分配 2 個 CPU 內核和 8 GB 記憶體。
* 公開 TCP 埠 5000 並為容器分配偽 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。
* 將 /input 和 /輸出卷安裝到容器中。

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>以個別的 docker 執行命令執行個別的容器

對於在同一主機上本地託管的表單識別器和文本識別器組合，請使用以下兩個示例 Docker CLI 命令：

在連接埠 5000 上執行第一個容器。 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

在連接埠 5001 上執行第二個容器。

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
每個後續容器應該位於不同的連接埠。 

### <a name="run-separate-containers-with-docker-compose"></a>透過 Docker Compose 執行個別的容器

有關在同一主機上本地託管的表單識別器和文本識別器組合，請參閱以下示例 Docker Compose YAML 檔。 `formrecognizer` 和 `ocr` 容器的文字辨識器 `{COMPUTER_VISION_API_KEY}` 必須相同。 `{COMPUTER_VISION_ENDPOINT_URI}`僅在容器中使用`ocr`，因為`formrecognizer`容器使用`ocr`名稱和埠。 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> 必須`Eula`指定`Billing`和`ApiKey``FormRecognizer:ComputerVisionApiKey``FormRecognizer:ComputerVisionEndpointUri`和 ， 才能運行容器，否則，容器將無法啟動。 如需詳細資訊，請參閱[帳單](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

|容器|端點|
|--|--|
|form-recognizer|http://localhost:5000

### <a name="form-recognizer"></a>表單辨識器

該容器提供基於 Websocket 的查詢終結點 API，您可以通過[表單識別器服務 SDK 文檔](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)訪問這些 API。

根據預設，表單辨識器 SDK 會使用線上服務。 若要使用容器，您必須變更初始化方法。 請參閱以下範例。

#### <a name="for-c"></a>針對 C#

從使用此 Azure 雲端初始化呼叫變更：

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
此調用（使用容器終結點）：

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>適用於 Python

從使用此 Azure 雲端初始化呼叫變更：

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

此調用（使用容器終結點）：

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>表單辨識器

容器提供 REST 終結點 API，您可以在[表單識別器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)頁上找到該 API。


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](form-recognizer-container-configuration.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

表單識別器容器通過使用 Azure 帳戶上的_表單識別器_資源向 Azure 發送計費資訊。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](form-recognizer-container-configuration.md)。

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>總結

在本文中，您已了解下載、安裝及執行表單辨識器容器的概念和工作流程。 摘要說明：

* 表單辨識器會針對 Docker 提供一個 Linux 容器。
* 容器映像是從 Azure 中的私人容器登錄下載。
* 容器映像是在 Docker 中執行。
* 可以使用 REST API 或 REST SDK 通過指定容器的主機 URI 來調用表單識別器容器中的操作。
* 具現化容器時必須指定計費資訊。

> [!IMPORTANT]
>  認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 查看[配置容器](form-recognizer-container-configuration.md)的配置設置。
* 使用更多的[認知服務容器](../cognitive-services-container-support.md)。
