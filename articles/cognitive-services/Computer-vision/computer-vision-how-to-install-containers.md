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
ms.date: 09/03/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 2c21c872649e3b171f2658ef6bdb0476552f0e59
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397179"
---
# <a name="install-and-run-read-containers-preview"></a>安裝和執行讀取容器 (預覽) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

容器可讓您在自己的環境中執行電腦視覺 API。 容器非常適合用於特定的安全性和資料控管需求。 在本文中，您將了解如何下載、安裝及執行電腦視覺容器。

「 *讀取* 」容器可讓您從具有不同表面和背景的各種物件影像（例如，收據、海報和名片）偵測和解壓縮 *列印的文字* 。 此外， *讀取* 容器會偵測影像中的 *手寫文字* ，並提供 PDF、TIFF 和多重頁面檔案支援。 如需詳細資訊，請參閱 [讀取 API 檔](concept-recognizing-text.md#read-api)。

有兩個版本的3.x 容器可供預覽。 這兩個版本都能在先前的容器上提供額外的精確度和功能。

讀取 3.0-預覽容器提供：
* 增強精確度的新模型。
* 在相同檔中支援多種語言
* 支援：荷蘭文、英文、法文、德文、義大利文、葡萄牙文和西班牙文。
* 適用于檔和影像的單一作業。
* 支援較大的檔和影像。
* 從0到1的信賴分數。
* 支援具有列印和手寫文字的檔

「讀取 3.1-預覽」容器提供與「v3.0-預覽」相同的優點，以及其他功能：

* 支援簡體中文和日文。
* 列印和手寫文字的信賴分數和標籤。 
* 可以從檔中的) 只將選取的頁面中的文字解壓縮 (s。

當考慮要使用哪一個容器版本時，請注意 3.1-preview 處於先前的預覽狀態。 如果您目前使用讀取2.0 容器，請參閱 [遷移指南](read-container-migration-guide.md) ，以瞭解新版本中的變更。

## <a name="prerequisites"></a>必要條件

使用容器之前，您必須符合下列必要條件：

|必要|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|電腦視覺資源 |若要使用此容器，您必須具備：<br><br>Azure **電腦視覺** 資源和相關聯的 API 金鑰（端點 URI）。 這兩個值都可在資源的 [總覽] 和 [金鑰] 頁面上取得，而且必須要有這些值才能啟動容器。<br><br>**{API_KEY}**： [ **金鑰** ] 頁面上兩個可用資源金鑰的其中一個<br><br>**{ENDPOINT_URI}**： **總覽** 頁面上提供的端點|

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="request-approval-to-run-the-container"></a>要求核准以執行容器

填寫並提交 [要求表單](https://aka.ms/csgate) ，以要求核准以執行容器。 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>先進的向量擴充功能支援

**主**電腦是執行 docker 容器的電腦。 主機 *必須支援* (AVX2) 的 [Advanced 向量擴充](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) 功能。 您可以使用下列命令，在 Linux 主機上檢查 AVX2 支援：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> *需要*主機電腦才能支援 AVX2。 如果沒有 AVX2 支援，容器 *將無法* 正確運作。

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

可使用讀取的容器映射。

| 容器 | Container Registry/存放庫/映射名稱 |
|-----------|------------|
| 閱讀 2.0-預覽 | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| 閱讀 3.0-預覽 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| 閱讀 3.1-預覽 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下載容器映射。

### <a name="docker-pull-for-the-read-container"></a>讀取容器的 Docker pull

# <a name="version-31-preview"></a>[版本 3.1-預覽](#tab/version-3-1)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview
```

# <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview
```

# <a name="version-20-preview"></a>[版本 2.0-預覽](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](computer-vision-resource-container-config.md)可供參考。 
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 如需如何取得和值的詳細資訊，請參閱 [收集必要參數](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` 。

[Examples](computer-vision-resource-container-config.md#example-docker-run-commands)命令的範例 `docker run` 可供使用。

# <a name="version-31-preview"></a>[版本 3.1-預覽](#tab/version-3-1)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行讀取容器。
* 配置8個 CPU 核心和 18 gb (GB) 的記憶體。
* 公開 TCP 通訊埠5000，並為容器配置虛擬 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

# <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

此命令：

* 從容器映射執行讀取容器。
* 配置8個 CPU 核心和 18 gb (GB) 的記憶體。
* 公開 TCP 通訊埠5000，並為容器配置虛擬 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

# <a name="version-20-preview"></a>[版本 2.0-預覽](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行讀取容器。
* 配置8個 CPU 核心和 16 gb (GB) 的記憶體。
* 公開 TCP 通訊埠5000，並為容器配置虛擬 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

---


`docker run` 命令有相關[範例](./computer-vision-resource-container-config.md#example-docker-run-commands)可供參考。 

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

如果您需要更高的輸送量 (例如，在處理多分頁檔案) 時，請考慮使用[Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-account-create)和[Azure 佇列](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction)，[在 Kubernetes 叢集上](deploy-computer-vision-on-premises.md)部署多個 v3.0 或3.1 版的容器。

如果您要使用 Azure 儲存體儲存影像以進行處理，您可以建立呼叫容器時所要使用的 [連接字串](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string) 。

若要尋找您的連接字串：

1. 流覽至 Azure 入口網站上的 **儲存體帳戶** ，並尋找您的帳戶。
2. 按一下左側導覽清單中的 [ **存取金鑰** ]。
3. 您的連接字串位於**連接字串**下方

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 

# <a name="version-31-preview"></a>[版本 3.1-預覽](#tab/version-3-1)

請對容器 API 使用主機 `http://localhost:5000`。 您可以在下列位置查看 Swagger 路徑： `http://localhost:5000/swagger/vision-v3.1-preview-read/swagger.json` 。

# <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

請對容器 API 使用主機 `http://localhost:5000`。 您可以在下列位置查看 Swagger 路徑： `http://localhost:5000/swagger/vision-v3.0-preview-read/swagger.json` 。

# <a name="version-20-preview"></a>[版本 2.0-預覽](#tab/version-2)

請對容器 API 使用主機 `http://localhost:5000`。 您可以在下列位置查看 Swagger 路徑： `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` 。

---

### <a name="asynchronous-read"></a>非同步讀取


# <a name="version-31-preview"></a>[版本 3.1-預覽](#tab/version-3-1)

您可以搭配使用 `POST /vision/v3.1/read/analyze` 和 `GET /vision/v3.1/read/operations/{operationId}` 作業來以非同步方式讀取影像，類似于電腦視覺服務如何使用這些對應的 REST 作業。 非同步 POST 方法會傳回，做為 `operationId` HTTP GET 要求的識別碼。


從 swagger UI 中選取， `asyncBatchAnalyze` 以在瀏覽器中加以展開。 然後選取 [立即**試用]，**  >  **選擇 [** 檔案]。 在此範例中，我們將使用下圖：

![索引標籤與空格](media/tabs-vs-spaces.png)

當非同步 POST 成功執行時，它會傳回 **HTTP 202** 狀態碼。 作為回應的一部分，有一個 `operation-location` 標頭會保存要求的結果端點。

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.1/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`是完整的 URL，可透過 HTTP GET 存取。 以下是從 `operation-location` 上一個影像執行 URL 的 JSON 回應：

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:30:14Z",
  "lastUpdatedDateTime": "2020-09-02T10:30:15Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.999
            },
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "appearance": {
              "style": "print",
              "styleConfidence": 0.603
            },
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

您可以搭配使用 `POST /vision/v3.0/read/analyze` 和 `GET /vision/v3.0/read/operations/{operationId}` 作業來以非同步方式讀取影像，類似于電腦視覺服務如何使用這些對應的 REST 作業。 非同步 POST 方法會傳回，做為 `operationId` HTTP GET 要求的識別碼。

從 swagger UI 中選取， `asyncBatchAnalyze` 以在瀏覽器中加以展開。 然後選取 [立即**試用]，**  >  **選擇 [** 檔案]。 在此範例中，我們將使用下圖：

![索引標籤與空格](media/tabs-vs-spaces.png)

當非同步 POST 成功執行時，它會傳回 **HTTP 202** 狀態碼。 作為回應的一部分，有一個 `operation-location` 標頭會保存要求的結果端點。

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`是完整的 URL，可透過 HTTP GET 存取。 以下是從 `operation-location` 上一個影像執行 URL 的 JSON 回應：

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:24:49Z",
  "lastUpdatedDateTime": "2020-09-02T10:24:50Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[版本 2.0-預覽](#tab/version-2)

您可以搭配使用 `POST /vision/v2.0/read/core/asyncBatchAnalyze` 和 `GET /vision/v2.0/read/operations/{operationId}` 作業來以非同步方式讀取影像，類似于電腦視覺服務如何使用這些對應的 REST 作業。 非同步 POST 方法會傳回，做為 `operationId` HTTP GET 要求的識別碼。

從 swagger UI 中選取， `asyncBatchAnalyze` 以在瀏覽器中加以展開。 然後選取 [立即**試用]，**  >  **選擇 [** 檔案]。 在此範例中，我們將使用下圖：

![索引標籤與空格](media/tabs-vs-spaces.png)

當非同步 POST 成功執行時，它會傳回 **HTTP 202** 狀態碼。 作為回應的一部分，有一個 `operation-location` 標頭會保存要求的結果端點。

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`是完整的 URL，可透過 HTTP GET 存取。 以下是從 `operation-location` 上一個影像執行 URL 的 JSON 回應：

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

---

> [!IMPORTANT]
> 如果您在負載平衡器後方部署多個讀取容器，例如 Docker Compose 或 Kubernetes 下，您必須有外部快取。 因為處理容器和 GET 要求容器可能不相同，所以外部快取會儲存結果，並在容器之間共用這些結果。 如需快取設定的詳細資訊，請參閱 [設定電腦視覺 Docker 容器](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config)。

### <a name="synchronous-read"></a>同步讀取

您可以使用下列作業，以同步方式讀取映射。 

# <a name="version-31-preview"></a>[版本 3.1-預覽](#tab/version-3-1)

`POST /vision/v3.1/read/syncAnalyze` 

# <a name="version-30-preview"></a>[3.0-preview 版](#tab/version-3)

`POST /vision/v3.0/read/syncAnalyze`

# <a name="version-20-preview"></a>[版本 2.0-預覽](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

當影像完整讀取時，則 API 會傳回 JSON 回應。 唯一的例外狀況是發生錯誤。 當發生錯誤時，會傳回下列 JSON：

```json
{
    "status": "Failed"
}
```

JSON 回應物件與非同步版本具有相同的物件圖形。 如果您是 JavaScript 使用者且需要型別安全，請考慮使用 TypeScript 來轉換 JSON 回應。

如需範例使用案例，請參閱<a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">此處 <span class="docon docon-navigate-external x-hidden-focus"></span> 的 TypeScript 沙箱</a>，然後選取 [**執行**] 以視覺化其容易使用。

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](./computer-vision-resource-container-config.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

認知服務容器會使用您 Azure 帳戶上的對應資源，將計費資訊傳送至 Azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](./computer-vision-resource-container-config.md)。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，您已了解下載、安裝及執行電腦視覺容器的概念和工作流程。 摘要說明：

* 電腦視覺提供適用于 Docker 的 Linux 容器，並封裝讀取。
* 容器映射是從 Azure 中的 "Container Preview" 容器登錄下載。
* 容器映像是在 Docker 中執行。
* 您可以藉由指定容器的主機 URI，使用 REST API 或 SDK 來呼叫讀取容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](computer-vision-resource-container-config.md)以了解組態設定
* 檢閱[電腦視覺概觀](overview.md)，以深入了解辨識印刷和手寫的文字
* 參閱[電腦視覺 API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)，以取得容器支援的方法之詳細資訊。
* 參閱[常見問題集 (FAQ)](FAQ.md) 來解決與電腦視覺功能相關的問題。
* 使用更多[認知服務容器](../cognitive-services-container-support.md)
