---
title: 如何安裝及執行表單辨識器的容器
titleSuffix: Azure Cognitive Services
description: 本文將說明如何使用 Azure 認知服務表單辨識器容器來剖析表單和資料表資料。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3860b2cedbdf8154658449349439f918775cb25
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701881"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>安裝和執行表單辨識器容器（預覽）

Azure 表單辨識器會套用機器學習技術，以識別並從表單中解壓縮索引鍵/值組和資料表。 它會將值和資料表專案與索引鍵/值配對產生關聯，然後輸出包含原始檔案中之關聯性的結構化資料。 

若要降低複雜性並輕鬆地將自訂表單辨識器模型整合到您的工作流程自動化程式或其他應用程式中，您可以使用簡單的 REST API 來呼叫模型。 只需要五個表單檔（或一個空白表單和兩個填滿表單），因此您可以快速、精確地取得結果，並針對您的特定內容量身打造。 不需要大量的手動介入或廣泛的資料科學專業知識。 而且它不需要資料標記或資料批註。

> [!IMPORTANT]
> 表單辨識器容器目前使用1.0 版的表單辨識器 API。 您可以改為使用受控服務來存取 API 的最新版本。

| 函式 | 特性 |
|----------|----------|
| 表單辨識器 | <li>處理 PDF、PNG 和 JPG 檔案<li>以相同版面配置的最少五種形式，訓練自訂模型 <li>解壓縮索引鍵/值組和資料表資訊 <li>使用 Azure 認知服務電腦視覺 API 辨識文字功能，從表單內的影像偵測及解壓縮印刷文字<li>不需要注釋或標籤 |

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

使用表單辨識器容器之前，您必須符合下列必要條件：

| 必要 | 目的 |
|----------|---------|
| Docker 引擎 | 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> 在 Windows 上，也必須將 Docker 設定為支援 Linux 容器。<br><br> |
| 熟悉 Docker | 您應該對 Docker 概念有基本的瞭解，例如登錄、存放庫、容器和容器映射，以及基本命令的知識 `docker` 。 |
| Azure CLI | 在您的主機上安裝[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 。 |
| 電腦視覺 API 資源 | 若要處理掃描的檔和影像，您需要電腦視覺資源。 您可以將「辨識文字」功能當作 Azure 資源（REST API 或 SDK）或*認知服務辨識文字*[容器](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)來存取。 適用一般計費費用。 <br><br>同時傳入電腦視覺資源（Azure 雲端或認知服務容器）的 API 金鑰和端點。 使用此 API 金鑰和端點作為 **{COMPUTER_VISION_API_KEY}** 和 **{COMPUTER_VISION_ENDPOINT_URI}**。<br><br> 如果您使用*認知服務-辨識文字*容器，請確定：<br><br>您的表單辨識器容器的電腦視覺金鑰是 `docker run` *認知服務*的電腦視覺命令中所指定的金鑰。<br>您的帳單端點是容器的端點（例如 `http://localhost:5000` ）。 如果您同時在相同的主機上同時使用電腦視覺容器和表單辨識器容器，則無法使用預設通訊埠*5000*來啟動它們。 |
| 表單辨識器資源 | 若要使用這些容器，您必須具有：<br><br>Azure**表單辨識器**資源，用來取得相關聯的 API 金鑰和端點 URI。 這兩個值都可在 Azure 入口網站**表單辨識器**總覽和 [金鑰] 頁面上取得，而這兩個值都是啟動容器的必要條件。<br><br>**{FORM_RECOGNIZER_API_KEY}**： [金鑰] 頁面上有兩個可用的資源金鑰之一<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**： [總覽] 頁面上所提供的端點 |

> [!NOTE]
> 電腦視覺資源名稱應為單一單字，而不含連字號 `-` 或任何其他特殊字元。 這項限制已準備好，以確保表單辨識器和辨識文字容器相容性。

## <a name="gathering-required-parameters"></a>正在搜集必要的參數

所有必要的認知服務容器都有三個主要參數。 使用者授權合約（EULA）必須存在，且值為 `accept` 。 此外，也需要端點 URL 和 API 金鑰。

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>端點 URI `{COMPUTER_VISION_ENDPOINT_URI}` 和`{FORM_RECOGNIZER_ENDPOINT_URI}`

[**端點**URI] 值可在對應認知服務資源的 [Azure 入口網站*總覽*] 頁面上取得。 流覽至 [*總覽*] 頁面，將滑鼠停留在端點上， `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 圖示就會出現。 視需要複製並使用。

![收集端點 uri 以供稍後使用](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>金鑰 `{COMPUTER_VISION_API_KEY}` 和`{FORM_RECOGNIZER_API_KEY}`

此金鑰用來啟動容器，並可在對應認知服務資源的 Azure 入口網站的 [金鑰] 頁面上取得。 流覽至 [*金鑰*] 頁面，然後按一下 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 圖示。

![取得兩個金鑰的其中一個供稍後使用](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> 這些訂用帳戶金鑰可用來存取您的認知服務 API。 請勿共用您的金鑰。 以安全的方式儲存它們，例如使用 Azure Key Vault。 我們也建議您定期重新產生這些金鑰。 進行 API 呼叫時，只需要一個金鑰。 重新產生第一個金鑰時，您可以使用第二個金鑰繼續存取服務。

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

填寫並提交[要求表單](https://aka.ms/FormRecognizerContainerRequestAccess)，以要求容器的存取權。

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明每個表單辨識器容器所要配置的最低和建議的 CPU 核心和記憶體：

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 表單辨識器 | 2 核心、4 GB 記憶體 | 4 核心，8 GB 記憶體 |
| 辨識文字 | 1個核心，8 GB 記憶體 | 2個核心，8 GB 記憶體 |

* 每個核心必須至少 2.6 GHz 或更快。
* 核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

> [!Note]
> 最低和建議值是根據 Docker 限制，而*不*是主機電腦資源。

## <a name="get-the-container-images-with-the-docker-pull-command"></a>使用 docker pull 命令取得容器映射

下列 container registry 提供**表單辨識器**和**辨識文字**供應專案的容器映射：

| 容器 | 完整的映射名稱 |
|-----------|------------|
| 表單辨識器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| 辨識文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

您需要這兩個容器，請注意，在本文外會[詳細](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)說明辨識**器文字**容器。

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>表單辨識器容器的 Docker Pull

#### <a name="form-recognizer"></a>表單辨識器

若要取得表單辨識器容器，請使用下列命令：

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>辨識文字容器的 Docker Pull

#### <a name="recognize-text"></a>辨識文字

若要取得辨識文字容器，請使用下列命令：

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

在容器位於[主機電腦](#the-host-computer)上之後，請使用下列程式來處理容器。

1. 使用所需的計費設定[執行容器](#run-the-container-by-using-the-docker-run-command)。 `docker run` 命令有相關[範例](form-recognizer-container-configuration.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-by-using-the-docker-run-command"></a>使用 docker run 命令執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 如[需](#gathering-required-parameters)如何取得 `{COMPUTER_VISION_ENDPOINT_URI}` 、 `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_ENDPOINT_URI}` 和值的詳細資訊，請參閱收集必要的參數 `{FORM_RECOGNIZER_API_KEY}` 。

[Examples](form-recognizer-container-configuration.md#example-docker-run-commands)命令的範例 `docker run` 可供使用。

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

* 從容器映射執行表單辨識器容器。
* 配置2個 CPU 核心和 8 gb 的記憶體。
* 公開 TCP 埠5000，並為容器配置虛擬 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。
* 將/input 和/output 磁片區掛接至容器。

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>以個別的 docker 執行命令執行個別的容器

針對裝載在相同主機本機上的表單辨識器和文字辨識器組合，請使用下列兩個範例 Docker CLI 命令：

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

針對裝載在相同主機本機上的表單辨識器和文字辨識器組合，請參閱下列範例 Docker Compose YAML 檔案。 `formrecognizer` 和 `ocr` 容器的文字辨識器 `{COMPUTER_VISION_API_KEY}` 必須相同。 `{COMPUTER_VISION_ENDPOINT_URI}`只能在容器中使用 `ocr` ，因為容器會 `formrecognizer` 使用 `ocr` 名稱和埠。 

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
> `Eula` `Billing` 您必須指定、、和以及 `ApiKey` `FormRecognizer:ComputerVisionApiKey` 和 `FormRecognizer:ComputerVisionEndpointUri` 選項，才能執行容器，否則容器將不會啟動。 如需詳細資訊，請參閱[帳單](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

|容器|端點|
|--|--|
|form-recognizer|http://localhost:5000

### <a name="form-recognizer"></a>表單辨識器

容器提供以 websocket 為基礎的查詢端點 Api，您可以透過[表單辨識器服務 SDK 檔](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)來存取。

根據預設，表單辨識器 SDK 會使用線上服務。 若要使用容器，您必須變更初始化方法。 請參閱以下範例。

#### <a name="for-c"></a>針對 C#

從使用此 Azure 雲端初始化呼叫變更：

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
此呼叫會使用容器端點：

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

此呼叫會使用容器端點：

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>表單辨識器

容器會提供 REST 端點 Api，您可以在[表單辨識器 api](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)頁面上找到它。


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](form-recognizer-container-configuration.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

表單辨識器容器會使用您 Azure 帳戶上的_表單辨識器_資源，將帳單資訊傳送至 azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](form-recognizer-container-configuration.md)。

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，您已了解下載、安裝及執行表單辨識器容器的概念和工作流程。 摘要說明：

* 表單辨識器會針對 Docker 提供一個 Linux 容器。
* 容器映像是從 Azure 中的私人容器登錄下載。
* 容器映像是在 Docker 中執行。
* 您可以藉由指定容器的主機 URI，使用 REST API 或 REST SDK 來呼叫表單辨識器容器中的作業。
* 當您具現化容器時，您必須指定帳單資訊。

> [!IMPORTANT]
>  認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 請參閱[設定容器](form-recognizer-container-configuration.md)以進行配置設定。
* 使用更多[認知服務容器](../cognitive-services-container-support.md)。
