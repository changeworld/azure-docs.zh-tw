---
title: 安裝語音容器-語音服務
titleSuffix: Azure Cognitive Services
description: 安裝並執行語音容器。 語音轉文字會即時地將音訊串流轉譯成文字，以便您的應用程式、工具或裝置使用或顯示。 文字轉語音會將輸入文字轉換為仿真人的合成語音。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: aahi
ms.openlocfilehash: b76690cfbe0eb4851bdd1e4316235a7a9092c86e
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84781203"
---
# <a name="install-and-run-speech-service-containers-preview"></a>安裝和執行語音服務容器（預覽）

容器可讓您在自己的環境中執行某些語音服務 Api。 容器非常適合用於特定的安全性和資料控管需求。 在本文中，您將瞭解如何下載、安裝及執行語音容器。

語音容器可讓客戶建立已針對健全的雲端功能和邊緣位置優化的語音應用程式架構。 有四個不同的容器可供使用。 這兩個標準容器是**語音轉換文字**和**文字轉換語音**。 這兩個自訂容器是**自訂的語音轉換文字**和**自訂文字轉換語音**。 語音容器與雲端式 Azure 語音服務具有相同的[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

> [!IMPORTANT]
> 所有語音容器目前都是[公開「閘道」預覽](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services)的一部分。 當語音容器進行公開上市（GA）時，將會發出公告。

| 函式 | 功能 | Latest |
|--|--|--|
| 語音轉文字 | 使用中繼結果分析情感和可將連續即時語音或批次音訊錄製。  | 2.2.0 |
| 自訂語音轉換文字 | 使用自[定義語音入口網站](https://speech.microsoft.com/customspeech)中的自訂模型，將連續即時語音或批次錄音可將為具有中繼結果的文字。 | 2.2.0 |
| 文字轉換語音 | 使用純文字輸入或語音合成標記語言（SSML），將文字轉換成自然發音的語音。 | 1.4.0 |
| 自訂文字轉換語音 | 使用自[定義語音入口網站](https://aka.ms/custom-voice-portal)中的自訂模型，以純文字輸入或語音合成標記語言（SSML）將文字轉換成自然發音的語音。 | 1.4.0 |

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

使用語音容器之前的必要條件如下：

| 必要 | 目的 |
|--|--|
| Docker 引擎 | 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br> |
| 熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。 |
| 語音資源 | 若要使用這些容器，您必須具備：<br><br>Azure_語音_資源，用來取得相關聯的 API 金鑰和端點 URI。 這兩個值都可在 Azure 入口網站的 [**語音**總覽] 和 [金鑰] 頁面上取得。 這兩者都是啟動容器的必要項。<br><br>**{API_KEY}**： [**金鑰**] 頁面上有兩個可用的資源金鑰之一<br><br>**{ENDPOINT_URI}**： [**總覽**] 頁面上所提供的端點 |

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

填寫並提交[要求表單](https://aka.ms/cognitivegate)，以要求容器的存取權。 


[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Advanced Vector Extension 支援

**主機**是執行 Docker 容器的電腦。 主機*必須支援*「[先進向量延伸](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)」（AVX2）。 您可以使用下列命令來檢查 Linux 主機上的 AVX2 支援：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> *必須*要有主機電腦，才能支援 AVX2。 如果沒有 AVX2 支援，容器*將無法*正確運作。

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明每個語音容器的資源的最低和建議配置。

# <a name="speech-to-text"></a>[語音轉文字](#tab/stt)

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 語音轉文字 | 2核心，2 GB 記憶體 | 4核心，4 GB 記憶體 |

# <a name="custom-speech-to-text"></a>[自訂語音轉換文字](#tab/cstt)

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 自訂語音轉換文字 | 2核心，2 GB 記憶體 | 4核心，4 GB 記憶體 |

# <a name="text-to-speech"></a>[文字轉換語音](#tab/tts)

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 文字轉換語音 | 1核心，2 GB 記憶體 | 2核心，3 GB 記憶體 |

# <a name="custom-text-to-speech"></a>[自訂文字轉換語音](#tab/ctts)

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 自訂文字轉換語音 | 1核心，2 GB 記憶體 | 2核心，3 GB 記憶體 |

***

* 每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

> [!NOTE]
> 最低和建議是根據 Docker 限制，而*不*是主機電腦資源。 例如，適用于大型語言模型的語音轉換文字容器記憶體對應部分，*建議您*將整個檔案納入記憶體中，這是額外的 4-6 GB。 此外，第一次執行任一容器可能需要較長的時間，因為模型會分頁到記憶體中。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

適用于語音的容器映射可在下列 Container Registry 中使用。

# <a name="speech-to-text"></a>[語音轉文字](#tab/stt)

| 容器 | Repository |
|-----------|------------|
| 語音轉文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[自訂語音轉換文字](#tab/cstt)

| 容器 | Repository |
|-----------|------------|
| 自訂語音轉換文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[文字轉換語音](#tab/tts)

| 容器 | Repository |
|-----------|------------|
| 文字轉換語音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[自訂文字轉換語音](#tab/ctts)

| 容器 | Repository |
|-----------|------------|
| 自訂文字轉換語音 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>適用于語音容器的 Docker pull

# <a name="speech-to-text"></a>[語音轉文字](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>適用于語音轉換文字容器的 Docker pull

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令從容器預覽登錄下載容器映射。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> `latest`標記會提取 `en-US` 地區設定。 如需其他地區設定，請參閱[語音轉換文字地區](#speech-to-text-locales)設定。

#### <a name="speech-to-text-locales"></a>語音轉換文字地區設定

除了以外的所有標記 `latest` 都採用下列格式，而且會區分大小寫：

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

下列標記是格式的範例：

```
2.2.0-amd64-en-us-preview
```

如需**語音轉換文字**容器的所有支援地區設定，請參閱[語音轉換文字影像標記](../containers/container-image-tags.md#speech-to-text)。

# <a name="custom-speech-to-text"></a>[自訂語音轉換文字](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>自訂語音轉換文字容器的 Docker pull

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令從容器預覽登錄下載容器映射。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> `locale` `voice` 自訂語音容器的和是由容器所內嵌的自訂模型所決定。

# <a name="text-to-speech"></a>[文字轉換語音](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>文字轉換語音容器的 Docker pull

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令從容器預覽登錄下載容器映射。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> `latest`標記會提取 `en-US` 地區設定和 `jessarus` 語音。 如需其他地區設定，請參閱[文字轉換語音的地區](#text-to-speech-locales)設定。

#### <a name="text-to-speech-locales"></a>文字轉換語音的地區設定

除了以外的所有標記 `latest` 都採用下列格式，而且會區分大小寫：

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

下列標記是格式的範例：

```
1.3.0-amd64-en-us-jessarus-preview
```

如需所有支援的地區設定和**文字轉換語音**容器的對應語音，請參閱[文字轉換語音影像標記](../containers/container-image-tags.md#text-to-speech)。

> [!IMPORTANT]
> 當您建立*標準的文字轉換語音*HTTP POST 時，[語音合成標記語言（SSML）](speech-synthesis-markup.md)訊息需要 `voice` 具有屬性的元素 `name` 。 值是對應的容器地區設定和語音，也稱為「[簡短名稱](language-support.md#standard-voices)」。 例如，標記的 `latest` 語音名稱會是 `en-US-JessaRUS` 。

# <a name="custom-text-to-speech"></a>[自訂文字轉換語音](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>自訂文字轉換語音容器的 Docker pull

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令從容器預覽登錄下載容器映射。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> `locale` `voice` 自訂語音容器的和是由容器所內嵌的自訂模型所決定。

***

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](speech-container-configuration.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 如需如何取得和值的詳細資訊，請參閱[收集必要的參數](#gathering-required-parameters) `{Endpoint_URI}` `{API_Key}` 。 [examples](speech-container-configuration.md#example-docker-run-commands) `docker run` 也可以使用命令的其他範例。

# <a name="speech-to-text"></a>[語音轉文字](#tab/stt)

若要執行*語音轉換文字*容器，請執行下列 `docker run` 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行*語音轉換文字*容器。
* 配置4個 CPU 核心和 4 gb 的記憶體。
* 公開 TCP 埠5000，並為容器配置虛擬 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>在語音轉換文字輸出上分析情感 

從語音轉換文字容器的 v 2.2.0 開始，您可以在輸出上呼叫[情感分析 V3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) 。 若要呼叫情感分析，您將需要文字分析 API 資源端點。 例如： 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

如果您要存取雲端中的文字分析端點，您將需要金鑰。 如果您是在本機執行文字分析，可能就不需要提供這種方式。

金鑰和端點會當做引數傳遞至語音容器，如下列範例所示。

```bash
docker run -it --rm -p 5000:5000 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

此命令：

* 執行與上述命令相同的步驟。
* 儲存文字分析 API 端點和金鑰，以用於傳送情感分析要求。 


# <a name="custom-speech-to-text"></a>[自訂語音轉換文字](#tab/cstt)

*自訂語音轉換文字*容器會依賴自訂語音模型。 自訂模型必須已經使用[自訂語音入口網站](https://speech.microsoft.com/customspeech)進行[定型](how-to-custom-speech-train-model.md)。

> [!IMPORTANT]
> 自訂語音模型必須從下列其中一個模型版本進行定型：
> * **20181201（3.3 版整合）**
> * **20190520（v 4.14 整合）**
> * **20190701（v 4.17 整合）**<br>
> ![自訂語音訓練容器模型](media/custom-speech/custom-speech-train-model-container-scoped.png)

需要有自訂語音**模型識別碼**，才能執行容器。 您可以在自訂語音入口網站的 [**訓練**] 頁面上找到此資訊。 從自訂語音入口網站，流覽至 [**定型**] 頁面，然後選取模型。
<br>

![自訂語音訓練頁面](media/custom-speech/custom-speech-model-training.png)

取得**模型識別碼**，做為 `ModelId` 命令之參數的引數使用 `docker run` 。
<br>

![自訂語音模型詳細資料](media/custom-speech/custom-speech-model-details.png)

下表代表各種 `docker run` 參數和其對應的描述：

| 參數 | 描述 |
|---------|---------|
| `{VOLUME_MOUNT}` | Docker 用來保存自訂模型的主機電腦[磁片區掛接](https://docs.docker.com/storage/volumes/)。 例如， *C:\CustomSpeech* ，其中*C 磁片磁碟機*位於主機電腦上。 |
| `{MODEL_ID}` | 自訂語音入口網站的 [**訓練**] 頁面中的自訂語音**模型識別碼**。 |
| `{ENDPOINT_URI}` | 需要端點才能進行計量和計費。 如需詳細資訊，請參閱[收集必要的參數](#gathering-required-parameters)。 |
| `{API_KEY}` | 需要 API 金鑰。 如需詳細資訊，請參閱[收集必要的參數](#gathering-required-parameters)。 |

若要執行*自訂的語音轉換文字*容器，請執行下列 `docker run` 命令：

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行*自訂的語音轉換文字*容器。
* 配置4個 CPU 核心和 4 gb 的記憶體。
* 從磁片區輸入裝載載入*自訂語音轉換文字*模型，例如*C:\CustomSpeech*。
* 公開 TCP 埠5000，並為容器配置虛擬 TTY。
* 下載指定的模型 `ModelId` （如果在磁片區掛接上找不到的話）。
* 如果先前已下載自訂模型，則 `ModelId` 會忽略。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

# <a name="text-to-speech"></a>[文字轉換語音](#tab/tts)

若要執行*文字轉換語音*容器，請執行下列 `docker run` 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行*文字轉換語音*容器。
* 配置2個 CPU 核心和 1 gb 的記憶體。
* 公開 TCP 埠5000，並為容器配置虛擬 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

# <a name="custom-text-to-speech"></a>[自訂文字轉換語音](#tab/ctts)

*自訂文字轉換語音*容器會依賴自訂語音模型。 自訂模型必須已經使用[自訂語音入口網站](https://aka.ms/custom-voice-portal)進行[定型](how-to-custom-voice-create-voice.md)。 需要自訂語音**模型識別碼**才能執行容器。 您可以在自訂語音入口網站的 [**訓練**] 頁面上找到此資訊。 從自訂語音入口網站，流覽至 [**定型**] 頁面，然後選取模型。
<br>

![自訂語音訓練頁面](media/custom-voice/custom-voice-model-training.png)

取得**模型識別碼**，做為 `ModelId` docker run 命令之參數的引數使用。
<br>

![自訂語音模型詳細資料](media/custom-voice/custom-voice-model-details.png)

下表代表各種 `docker run` 參數和其對應的描述：

| 參數 | 描述 |
|---------|---------|
| `{VOLUME_MOUNT}` | Docker 用來保存自訂模型的主機電腦[磁片區掛接](https://docs.docker.com/storage/volumes/)。 例如， *C:\CustomSpeech* ，其中*C 磁片磁碟機*位於主機電腦上。 |
| `{MODEL_ID}` | 自訂語音入口網站的 [**訓練**] 頁面中的自訂語音**模型識別碼**。 |
| `{ENDPOINT_URI}` | 需要端點才能進行計量和計費。 如需詳細資訊，請參閱[收集必要的參數](#gathering-required-parameters)。 |
| `{API_KEY}` | 需要 API 金鑰。 如需詳細資訊，請參閱[收集必要的參數](#gathering-required-parameters)。 |

若要執行*自訂文字轉換語音*容器，請執行下列 `docker run` 命令：

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行*自訂的文字轉換語音*容器。
* 配置2個 CPU 核心和 1 gb 的記憶體。
* 從磁片區輸入裝載載入*自訂文字轉換語音*模型，例如*C:\CustomVoice*。
* 公開 TCP 埠5000，並為容器配置虛擬 TTY。
* 下載指定的模型 `ModelId` （如果在磁片區掛接上找不到的話）。
* 如果先前已下載自訂模型，則 `ModelId` 會忽略。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

***

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

> [!NOTE]
> 如果您正在執行多個容器，請使用唯一的埠號碼。

| 容器 | SDK 主機 URL | 通訊協定 |
|--|--|--|
| 語音轉換文字和自訂語音轉換文字 | `ws://localhost:5000` | WS |
| 文字轉換語音和自訂文字轉換語音 | `http://localhost:5000` | HTTP |

如需有關使用 WSS 和 HTTPS 通訊協定的詳細資訊，請參閱[容器安全性](../cognitive-services-container-support.md#azure-cognitive-services-container-security)。

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>分析情感

如果您將文字分析 API 認證提供[給容器](#analyze-sentiment-on-the-speech-to-text-output)，您可以使用語音 SDK 來傳送具有情感分析的語音辨識要求。 您可以將 API 回應設定為使用*簡單*或*詳細*格式。

# <a name="simple-format"></a>[簡單格式](#tab/simple-format)

若要將語音用戶端設定為使用簡單的格式，請新增 `"Sentiment"` 做為的值 `Simple.Extensions` 。 如果您想要選擇特定的文字分析模型版本，請 `'latest'` 將屬性設定中的取代為 `speechcontext-phraseDetection.sentimentAnalysis.modelversion` 。

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions`會傳迴響應的根層中的情感結果。

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[詳細格式](#tab/detailed-format)

若要將語音用戶端設定為使用詳細的格式，請新增 `"Sentiment"` 做為 `Detailed.Extensions` 、 `Detailed.Options` 或兩者的值。 如果您想要選擇特定的文字分析模型版本，請 `'latest'` 將屬性設定中的取代為 `speechcontext-phraseDetection.sentimentAnalysis.modelversion` 。

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions`在回應的根層中提供情感結果。 `Detailed.Options`提供回應層級的結果 `NBest` 。 它們可以單獨使用或一起使用。

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

如果您想要完全停用情感分析，請將 `false` 值新增至 `sentimentanalysis.enabled` 。

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-or-custom-text-to-speech"></a>文字轉換語音或自訂文字轉換語音

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>在相同主機上執行多個容器

如果您打算使用公開的連接埠執行多個容器，請務必使用不同的公開連接埠來執行每個容器。 例如，在連接埠 5000 上執行第一個容器，以及在連接埠 5001 上執行第二個容器。

您可以讓此容器和不同的 Azure 認知服務容器在主機上一起執行。 您也可以針對相同的認知服務容器執行多個容器。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

啟動或執行容器時，您可能會遇到問題。 使用輸出[裝載](speech-container-configuration.md#mount-settings)並啟用記錄。 這麼做可讓容器產生記錄檔，這在針對問題進行疑難排解時很有説明。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

語音容器會使用您 Azure 帳戶上的*語音*資源，將帳單資訊傳送至 azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](speech-container-configuration.md)。

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，您已瞭解下載、安裝及執行語音容器的概念和工作流程。 摘要說明：

* 語音提供四個適用于 Docker 的 Linux 容器，封裝各種功能：
  * *語音轉文字*
  * *自訂語音轉換文字*
  * *文字轉換語音*
  * *自訂文字轉換語音*
* 容器映射是從 Azure 中的 container registry 下載。
* 容器映像是在 Docker 中執行。
* 不論是使用 REST API （僅限文字轉換語音）或是 SDK （語音轉換文字或文字轉換語音），您都要指定容器的主機 URI。 
* 當具現化容器時，您必須提供帳單資訊。

> [!IMPORTANT]
>  認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 審查[設定](speech-container-configuration.md)設定的容器
* 瞭解如何搭配[Kubernetes 和 Helm 使用語音服務容器](speech-container-howto-on-premises.md)
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
