---
title: 安裝語音容器 - 語音服務
titleSuffix: Azure Cognitive Services
description: 安裝並運行語音容器。 語音轉文字會即時地將音訊串流轉譯成文字，以便您的應用程式、工具或裝置使用或顯示。 文字轉語音會將輸入文字轉換為仿真人的合成語音。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2caae4fecdf13a1833f23cf9423cf3ded67f6f72
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879002"
---
# <a name="install-and-run-speech-service-containers-preview"></a>安裝並執行語音服務容器(預覽)

容器使您能夠在您自己的環境中運行某些語音服務 API。 容器非常適合特定的安全和數據治理要求。 在本文中,您將學習如何下載、安裝和運行語音容器。

語音容器使客戶能夠構建針對強大的雲端功能和邊緣區域優化的語音應用程式體系結構。 有四個不同的容器可用。 這兩個標準容器是**語音到文字**與**文字到語音**。 這兩個自訂容器是**自訂語音到文字**與**自訂文字到語音**。

> [!IMPORTANT]
> 所有語音容器目前都是[作為公共"封閉"預覽](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio)的一部分提供的。 當語音容器前進到通用 (GA) 時,將發佈公告。

| 函式 | 特性 | 最新 |
|--|--|--|
| 語音轉文字 | 將連續即時語音或批量音訊錄製轉錄為文本,並帶有中間結果。 | 2.1.1 |
| 自訂語音到文字 | 使用[自定義語音門戶](https://speech.microsoft.com/customspeech)中的自定義模型,將連續即時語音或批處理音訊錄製轉錄到文本中,並帶有中間結果。 | 2.1.1 |
| 文字轉換語音 | 使用純文字輸入或語音合成標記語言 (SSML) 將文字轉換為自然語音。 | 1.3.0 |
| 自訂文字到語音 | 使用[自訂語音門戶](https://aka.ms/custom-voice-portal)中的自定義模型,將文本轉換為具有純文本輸入或語音合成標記語言 (SSML) 的自然語音。 | 1.3.0 |

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

使用語音容器之前,以下先決條件:

| 必要 | 目的 |
|--|--|
| Docker 引擎 | 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br> |
| 熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。 |
| 語音資源 | 若要使用這些容器，您必須具備：<br><br>用於獲取關聯的 API 金鑰和終結點 URI 的 Azure_語音轉換_資源。 這兩個值都可以在 Azure 門戶的**語音**概述和密鑰頁上使用。 它們都需要啟動容器。<br><br>**[API_KEY]**:**金鑰**頁上的兩個可用資源鍵之一<br><br>**[ENDPOINT_URI]**:**概述**頁上提供的終結點 |

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

填寫並提交[認知服務語音容器請求表單](https://aka.ms/speechcontainerspreview/)以請求訪問容器。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>進階向量延伸支援

**主機**是執行 Docker 容器的電腦。 主機*必須支援*[進階向量擴展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)(AVX2)。 可以使用以下指令檢查 Linux 主機上的 AVX2 支援:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> 支援 AVX2*需要*主機。 如果沒有 AVX2 支援,容器*將不能*正常工作。

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表描述了每個語音容器的最小和建議的資源分配。

# <a name="speech-to-text"></a>[語音轉文字](#tab/stt)

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 語音轉文字 | 2 核,2 GB 記憶體 | 4 核,4 GB 記憶體 |

# <a name="custom-speech-to-text"></a>[自訂語音到文字](#tab/cstt)

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 自訂語音到文字 | 2 核,2 GB 記憶體 | 4 核,4 GB 記憶體 |

# <a name="text-to-speech"></a>[文字轉換語音](#tab/tts)

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 文字轉換語音 | 1 個內核,2 GB 記憶體 | 2 核,3 GB 記憶體 |

# <a name="custom-text-to-speech"></a>[自訂文字到語音](#tab/ctts)

| 容器 | 最小值 | 建議 |
|-----------|---------|-------------|
| 自訂文字到語音 | 1 個內核,2 GB 記憶體 | 2 核,3 GB 記憶體 |

***

* 每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

> [!NOTE]
> 最小值和建議的基於 Docker 限制,*而不是*主機資源。 例如,語音到文字容器儲存大型語言模型的記憶體映射部分,*建議*整個檔適合記憶體,即額外的 4-6 GB。 此外,由於模型被分頁到記憶體中,因此任一容器的第一次運行可能需要更長的時間。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

語音的容器映射可在以下容器註冊表中提供。

# <a name="speech-to-text"></a>[語音轉文字](#tab/stt)

| 容器 | Repository |
|-----------|------------|
| 語音轉文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[自訂語音到文字](#tab/cstt)

| 容器 | Repository |
|-----------|------------|
| 自訂語音到文字 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[文字轉換語音](#tab/tts)

| 容器 | Repository |
|-----------|------------|
| 文字轉換語音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[自訂文字到語音](#tab/ctts)

| 容器 | Repository |
|-----------|------------|
| 自訂文字到語音 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>語音容器的 Docker 拉

# <a name="speech-to-text"></a>[語音轉文字](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>語音到文字容器的 Docker 拉拔

使用[Docker 提取](https://docs.docker.com/engine/reference/commandline/pull/)命令從容器預覽註冊表下載容器映射。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> 標記`latest`將拉`en-US`取 區域設置。 有關其他區域設定,請參閱[語音到文字區域設定](#speech-to-text-locales)。

#### <a name="speech-to-text-locales"></a>語音到文字區域設定

除以下`latest`所有標記外,所有標記均採用以下格式,並且區分大小寫:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

以下標記是格式的範例:

```
2.1.1-amd64-en-us-preview
```

關於**語音到文字**容器的所有受支援區域設定,請參閱[語音到文字影像標記](../containers/container-image-tags.md#speech-to-text)。

# <a name="custom-speech-to-text"></a>[自訂語音到文字](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>自訂語音到文字容器的 Docker 拉拔

使用[Docker 提取](https://docs.docker.com/engine/reference/commandline/pull/)命令從容器預覽註冊表下載容器映射。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> 自定義`locale``voice`語音 容器和 的容器由容器引入的自定義模型確定。

# <a name="text-to-speech"></a>[文字轉換語音](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>文字到語音容器的 Docker 拉拔

使用[Docker 提取](https://docs.docker.com/engine/reference/commandline/pull/)命令從容器預覽註冊表下載容器映射。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> 標記`latest`將拉`en-US`出 區域`jessarus`設置和 語音。 有關其他區域設定,請參閱[文字到語音區域設定](#text-to-speech-locales)。

#### <a name="text-to-speech-locales"></a>文字到語音區域設定

除以下`latest`所有標記外,所有標記均採用以下格式,並且區分大小寫:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

以下標記是格式的範例:

```
1.3.0-amd64-en-us-jessarus-preview
```

關於**文字到語音**容器的所有受支援區域設定和相應語音,請參考[文字到語音影像標記](../containers/container-image-tags.md#text-to-speech)。

> [!IMPORTANT]
> 建構*標準文字到語音轉換*的 HTTP POST 時,[語音合成標記語言 (SSML)](speech-synthesis-markup.md)訊息`voice`需要具有`name`屬性的元素。 該值是相應的容器區域設置和語音,也稱為[「短名稱」。。](language-support.md#standard-voices) 例如,`latest`這個標記的語音名`en-US-JessaRUS`稱為 。

# <a name="custom-text-to-speech"></a>[自訂文字到語音](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>自訂文字到語音容器的 Docker 拉拔

使用[Docker 提取](https://docs.docker.com/engine/reference/commandline/pull/)命令從容器預覽註冊表下載容器映射。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> 自定義`locale``voice`語音 容器和 的容器由容器引入的自定義模型確定。

***

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](speech-container-configuration.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測終結點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 有關如何取得與`{Endpoint_URI}``{API_Key}`值的詳細資訊,請參考[收集所需的參數](#gathering-required-parameters)。 命令的其他[示例](speech-container-configuration.md#example-docker-run-commands)`docker run`也可用。

# <a name="speech-to-text"></a>[語音轉文字](#tab/stt)

要執行 *「語音到文字」* 容器,請`docker run`執行以下 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映像運行*語音到文字*容器。
* 分配 4 個 CPU 內核和 4 GB 記憶體。
* 公開 TCP 埠 5000 並為容器分配偽 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

# <a name="custom-speech-to-text"></a>[自訂語音到文字](#tab/cstt)

*自定義語音到文本*容器依賴於自定義語音模型。 自訂模型必須已使用[自訂語音門戶](https://speech.microsoft.com/customspeech)[進行訓練](how-to-custom-speech-train-model.md)。

> [!IMPORTANT]
> 自訂語音模型需要從以下模型版本之一進行訓練:
> * **20181201 (v3.3 統一)**
> * **20190520 (v4.14 統一)**
> * **20190701 (v4.17 統一)**<br>
> ![自訂語音訓練容器模型](media/custom-speech/custom-speech-train-model-container-scoped.png)

運行容器需要自定義語音**模型 ID。** 可在自定義語音門戶的 **「訓練」** 頁面上找到。 從自定義語音門戶導航到 **「訓練」** 頁面並選擇模型。
<br>

![自訂語音訓練頁面](media/custom-speech/custom-speech-model-training.png)

獲取**模型ID**以用作命令`ModelId`參數`docker run`的參數。
<br>

![自訂語音模型詳細資訊](media/custom-speech/custom-speech-model-details.png)

下表表示各種`docker run`參數及其相應的說明:

| 參數 | 描述 |
|---------|---------|
| `{VOLUME_MOUNT}` | 主機[卷裝載](https://docs.docker.com/storage/volumes/),docker 用於持久化自定義模型。 例如 *,C:\自定義語音*,其中*C 驅動器*位於主機上。 |
| `{MODEL_ID}` | 自定義語音**模型 ID**來自自訂語音門戶的 **「訓練**」 頁。 |
| `{ENDPOINT_URI}` | 計量和計費需要終結點。 有關詳細資訊,請參閱[收集所需的參數](#gathering-required-parameters)。 |
| `{API_KEY}` | API 金鑰是必需的。 有關詳細資訊,請參閱[收集所需的參數](#gathering-required-parameters)。 |

要執行*自訂語音到文字*容器,請執行`docker run`以下 指令:

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

* 從容器映射運行*自訂語音到文字*容器。
* 分配 4 個 CPU 內核和 4 GB 記憶體。
* 從捲輸入載入*自訂語音到文字*模型,例如*C:\自訂語音*。
* 公開 TCP 埠 5000 並為容器分配偽 TTY。
* 下載給定的模型`ModelId`(如果在卷裝載上找不到)。
* 如果以前下載了自訂模型,則忽略`ModelId`。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

# <a name="text-to-speech"></a>[文字轉換語音](#tab/tts)

要執行*文字到語音轉換*容器,請`docker run`執行以下 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射運行*文字到語音轉換*容器。
* 分配 2 個 CPU 內核和 1 GB 記憶體。
* 公開 TCP 埠 5000 並為容器分配偽 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

# <a name="custom-text-to-speech"></a>[自訂文字到語音](#tab/ctts)

*自訂文本到語音轉換*容器依賴於自定義語音模型。 自訂模型必須已使用[自訂語音門戶](https://aka.ms/custom-voice-portal)[進行訓練](how-to-custom-voice-create-voice.md)。 運行容器需要自定義語音**模型 ID。** 可在自定義語音門戶的 **「訓練」** 頁面上找到。 從自定義語音門戶導航到 **「訓練」** 頁面並選擇模型。
<br>

![自訂語音訓練頁面](media/custom-voice/custom-voice-model-training.png)

取得**模型識別**碼以用作 docker run`ModelId`命令參數的參數。
<br>

![自訂語音模型詳細資訊](media/custom-voice/custom-voice-model-details.png)

下表表示各種`docker run`參數及其相應的說明:

| 參數 | 描述 |
|---------|---------|
| `{VOLUME_MOUNT}` | 主機[卷裝載](https://docs.docker.com/storage/volumes/),docker 用於持久化自定義模型。 例如 *,C:\自定義語音*,其中*C 驅動器*位於主機上。 |
| `{MODEL_ID}` | 自定義語音**模型 ID**來自自訂語音門戶的 **「訓練**」 頁。 |
| `{ENDPOINT_URI}` | 計量和計費需要終結點。 有關詳細資訊,請參閱[收集所需的參數](#gathering-required-parameters)。 |
| `{API_KEY}` | API 金鑰是必需的。 有關詳細資訊,請參閱[收集所需的參數](#gathering-required-parameters)。 |

要執行*自訂文字到語音轉換*容器,請`docker run`執行以下 指令:

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

* 從容器映射運行*自訂文本到語音轉換*容器。
* 分配 2 個 CPU 內核和 1 GB 記憶體。
* 從捲輸入載入*自訂文字到語音轉換*模型,例如*C:\自訂語音*。
* 公開 TCP 埠 5000 並為容器分配偽 TTY。
* 下載給定的模型`ModelId`(如果在卷裝載上找不到)。
* 如果以前下載了自訂模型,則忽略`ModelId`。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

***

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

| 容器 | SDK 主機網址 | 通訊協定 |
|--|--|--|
| 語音到文字與自訂語音文字 | `ws://localhost:5000` | WS |
| 文字到語音轉換與自訂文字到語音 | `http://localhost:5000` | HTTP |

有關使用 WSS 與 HTTPS 協定的詳細資訊,請參閱[容器安全性](../cognitive-services-container-support.md#azure-cognitive-services-container-security)。

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>文字到語音轉換或自訂文字到語音

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>在相同主機上執行多個容器

如果您打算使用公開的連接埠執行多個容器，請務必使用不同的公開連接埠來執行每個容器。 例如，在連接埠 5000 上執行第一個容器，以及在連接埠 5001 上執行第二個容器。

您可以讓此容器和不同的 Azure 認知服務容器在主機上一起執行。 您也可以針對相同的認知服務容器執行多個容器。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

啟動或運行容器時,可能會遇到問題。 使用輸出[裝載](speech-container-configuration.md#mount-settings)並啟用日誌記錄。 這樣做將允許容器生成在排除故障時有用的日誌檔。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

"語音"容器使用 Azure 帳戶上的*語音*資源向 Azure 發送計費資訊。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](speech-container-configuration.md)。

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中,您學習了用於下載、安裝和運行語音容器的概念和工作流。 摘要說明：

* 語音為 Docker 提供了四個 Linux 容器,封裝了各種功能:
  * *語音轉文字*
  * *自訂語音到文字*
  * *文字轉換語音*
  * *自訂文字到語音*
* 容器映射從 Azure 中的容器註冊表下載。
* 容器映像是在 Docker 中執行。
* 無論是使用 REST API(僅限文字到語音轉換)還是 SDK(語音到文字或文字到語音),您都指定容器的主機 URI。 
* 實例化容器時,您需要提供計費資訊。

> [!IMPORTANT]
>  認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢視[設定容器](speech-container-configuration.md)的設定設定
* 瞭解如何[使用庫伯奈斯和赫爾姆語音服務容器](speech-container-howto-on-premises.md)
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
