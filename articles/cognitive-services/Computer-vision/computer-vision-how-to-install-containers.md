---
title: 如何安裝及執行容器 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 本逐步解說教學課程的內容包含如何下載、安裝及執行適用於電腦視覺的容器。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 5f36c429041a8182551d1f077f0a1229f520e8c1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80879338"
---
# <a name="install-and-run-read-containers-preview"></a>安裝和執行讀取容器（預覽）

容器可讓您在自己的環境中執行電腦視覺 Api。 容器非常適合用於特定的安全性和資料控管需求。 在本文中，您將瞭解如何下載、安裝及執行電腦視覺容器。

單一 Docker 容器 [*讀取*] 可供電腦視覺。 *讀取*容器可讓您從具有不同表面和背景的各種物件影像中偵測及解壓縮*印刷文字*，例如收據、海報和名片。 此外，*讀取*容器會偵測影像中的*手寫文字*，並提供 PDF、TIFF 和多分頁檔案支援。 如需詳細資訊，請參閱[讀取](concept-recognizing-text.md#read-api)API 檔。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

使用容器之前，您必須符合下列必要條件：

|必要|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|電腦視覺資源 |若要使用此容器，您必須具備：<br><br>Azure**電腦視覺**資源和相關聯的 API 金鑰端點 URI。 這兩個值都可在資源的 [總覽] 和 [金鑰] 頁面上取得，而且必須要有才能啟動容器。<br><br>**{API_KEY}**： [**金鑰**] 頁面上有兩個可用的資源金鑰之一<br><br>**{ENDPOINT_URI}**： [**總覽**] 頁面上所提供的端點|

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Advanced Vector Extension 支援

**主**電腦是執行 docker 容器的電腦。 主機*必須支援*「[先進向量延伸](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)」（AVX2）。 您可以使用下列命令來檢查 Linux 主機上的 AVX2 支援：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> *必須*要有主機電腦，才能支援 AVX2。 如果沒有 AVX2 支援，容器*將無法*正確運作。

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

有可供讀取的容器映射。

| 容器 | Container Registry/存放庫/映射名稱 |
|-----------|------------|
| 讀取 | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令下載容器映射。

### <a name="docker-pull-for-the-read-container"></a>讀取容器的 Docker pull

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](computer-vision-resource-container-config.md)可供參考。 
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 如需如何取得`{ENDPOINT_URI}`和`{API_KEY}`值的詳細資訊，請參閱[收集必要的參數](#gathering-required-parameters)。

命令的範例可供使用。 [Examples](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run`

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行讀取容器。
* 配置8個 CPU 核心和 16 gb 的記憶體。
* 公開 TCP 埠5000，並為容器配置虛擬 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

`docker run` 命令有相關[範例](./computer-vision-resource-container-config.md#example-docker-run-commands)可供參考。 

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 

針對容器 API 請使用主機 `http://localhost:5000`。

### <a name="asynchronous-read"></a>非同步讀取

您可以使用`POST /vision/v2.0/read/core/asyncBatchAnalyze`和`GET /vision/v2.0/read/operations/{operationId}`作業，以非同步方式讀取影像，類似于電腦視覺服務使用這些對應 REST 作業的方式。 非同步 POST 方法會傳回`operationId` ，做為 HTTP GET 要求的識別碼使用。

從 swagger UI 中，選取`asyncBatchAnalyze`以在瀏覽器中展開。 然後選取 [**試試** > 看]**[選擇**檔案]。 在此範例中，我們將使用下列影像：

![索引標籤與空格](media/tabs-vs-spaces.png)

當非同步 POST 順利執行時，它會傳回**HTTP 202**狀態碼。 作為回應的一部分，有一個`operation-location`標頭會保存要求的結果端點。

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`是完整的 URL，並可透過 HTTP GET 來存取。 以下是從上一個影像執行`operation-location` URL 的 JSON 回應：

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>同步讀取

您可以使用`POST /vision/v2.0/read/core/Analyze`作業來同步讀取影像。 當影像完整讀取時，只有，API 才會傳回 JSON 回應。 唯一的例外狀況是如果發生錯誤。 當發生錯誤時，會傳回下列 JSON：

```json
{
    status: "Failed"
}
```

JSON 回應物件與非同步版本具有相同的物件圖形。 如果您是 JavaScript 使用者，而且想要型別安全，可以使用下列類型，將 JSON 回應轉換為`AnalyzeResult`物件。

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

如需範例使用案例，請參閱<a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">這裡<span class="docon docon-navigate-external x-hidden-focus"></span>的 TypeScript 沙箱</a>，然後選取 [**執行**] 以視覺化其便於使用。

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](./computer-vision-resource-container-config.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

認知服務容器會使用您 Azure 帳戶上的對應資源，將帳單資訊傳送至 Azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](./computer-vision-resource-container-config.md)。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>[摘要]

在本文中，您已了解下載、安裝及執行電腦視覺容器的概念和工作流程。 摘要說明：

* 電腦視覺提供適用于 Docker 的 Linux 容器，封裝讀取。
* 容器映射是從 Azure 中的「容器預覽」容器登錄下載。
* 容器映像是在 Docker 中執行。
* 您可以藉由指定容器的主機 URI，使用 REST API 或 SDK 來呼叫讀取容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](computer-vision-resource-container-config.md)以了解組態設定
* 檢閱[電腦視覺概觀](Home.md)，以深入了解辨識印刷和手寫的文字
* 參閱[電腦視覺 API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)，以取得容器支援的方法之詳細資訊。
* 參閱[常見問題集 (FAQ)](FAQ.md) 來解決與電腦視覺功能相關的問題。
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
