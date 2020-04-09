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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879338"
---
# <a name="install-and-run-read-containers-preview"></a>安裝並執行讀取容器(預覽)

容器使您能夠在您自己的環境中運行電腦視覺 API。 容器非常適合特定的安全和數據治理要求。 在本文中,您將學習如何下載、安裝和運行計算機視覺容器。

單個 Docker 容器*Read*可用於電腦視覺。 *「讀取」* 容器允許您從不同表面與背景的各種物件(如收取、海報和名片)的影像中偵測和擷*取 列印的文字*。 此外,*讀取*容器可檢測圖像中的*手寫文本*,並提供 PDF、TIFF 和多頁檔支援。 有關詳細資訊,請參閱[讀取](concept-recognizing-text.md#read-api)API 文檔。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

在使用容器之前,必須滿足以下先決條件:

|必要|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|電腦視覺資源 |若要使用此容器，您必須具備：<br><br>Azure**計算機視覺**資源和關聯的 API 鍵端點 URI。 這兩個值都可以在資源的「概述」和「鍵」頁上使用,並且需要啟動容器。<br><br>**[API_KEY]**:**金鑰**頁上的兩個可用資源鍵之一<br><br>**[ENDPOINT_URI]**:**概述**頁上提供的終結點|

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>進階向量延伸支援

**主機**是運行 Docker 容器的電腦。 主機*必須支援*[進階向量擴展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)(AVX2)。 可以使用以下指令檢查 Linux 主機上的 AVX2 支援:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> 支援 AVX2*需要*主機。 如果沒有 AVX2 支援,容器*將不能*正常工作。

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

可供讀取的容器映射可用。

| 容器 | 容器註冊表 / 儲存函式庫 / 映像名稱 |
|-----------|------------|
| 讀取 | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令下載容器映射。

### <a name="docker-pull-for-the-read-container"></a>讀取容器的 Docker 拉拔

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](computer-vision-resource-container-config.md)可供參考。 
1. [查詢容器的預測終結點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 有關如何取得與`{ENDPOINT_URI}``{API_KEY}`值的詳細資訊,請參考[收集所需的參數](#gathering-required-parameters)。

[命令的範例可用。](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run`

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射運行讀取容器。
* 分配 8 個 CPU 內核和 16 GB 的記憶體。
* 公開 TCP 埠 5000 並為容器分配偽 TTY。
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

可以協同使用`POST /vision/v2.0/read/core/asyncBatchAnalyze`和`GET /vision/v2.0/read/operations/{operationId}`操作異步讀取圖像,類似於計算機視覺服務使用這些相應的 REST 操作的方式。 非同步 POST 方法將`operationId`用作辨識器的 將返回到 HTTP GET 請求。

從搖曳的 UI`asyncBatchAnalyze`中, 選擇 在瀏覽器中展開它。 選擇 **「嘗試」 選** > **單 。** 此範例中,我們將使用以下影像:

![選項卡 vs 空白](media/tabs-vs-spaces.png)

當非同步 POST 成功執行時,它將返回**HTTP 202**狀態代碼。 作為回應的一部分,有一個`operation-location`標頭保存請求的結果終結點。

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

是`operation-location`完全限定的 URL,可通過 HTTP GET 進行訪問。 以下是從前一個映像執行`operation-location`網址的 JSON 回應:

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

可以使用`POST /vision/v2.0/read/core/Analyze`該操作同步讀取圖像。 當圖像完整讀取時,API才會返回 JSON 回應。 唯一的例外是如果發生錯誤。 發生錯誤時,返回以下 JSON:

```json
{
    status: "Failed"
}
```

JSON 回應物件與異步版本具有相同的物件圖。 如果您是 JavaScript 使用者,並且想要類型安全,可以使用以下類型將 JSON`AnalyzeResult`回應轉換為 物件。

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

有關範例用例,請參閱<a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">此<span class="docon docon-navigate-external x-hidden-focus"></span>處的 TypeScript 沙箱</a>,然後選擇 **「運行」** 以可視化其易用性。

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](./computer-vision-resource-container-config.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

認知服務容器使用 Azure 帳戶上的相應資源向 Azure 發送計費資訊。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](./computer-vision-resource-container-config.md)。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，您已了解下載、安裝及執行電腦視覺容器的概念和工作流程。 摘要說明：

* 計算機視覺為 Docker 提供 Linux 容器,封裝讀取。
* 容器映射從 Azure 中的「容器預覽」容器註冊表下載。
* 容器映像是在 Docker 中執行。
* 通過指定容器的主機 URI,可以使用 REST API 或 SDK 在讀取容器中調用操作。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](computer-vision-resource-container-config.md)以了解組態設定
* 檢閱[電腦視覺概觀](Home.md)，以深入了解辨識印刷和手寫的文字
* 參閱[電腦視覺 API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)，以取得容器支援的方法之詳細資訊。
* 參閱[常見問題集 (FAQ)](FAQ.md) 來解決與電腦視覺功能相關的問題。
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
