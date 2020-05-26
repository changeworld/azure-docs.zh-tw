---
title: 安裝和執行容器-臉部
titleSuffix: Azure Cognitive Services
description: 本文說明如何在本逐步解說教學課程中下載、安裝及執行臉部的容器。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 611ab503dfea44e8287e95cf607ce6af3b447d1f
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815786"
---
# <a name="install-and-run-face-containers-preview"></a>安裝和執行臉部容器（預覽）

Azure 認知服務臉部提供 Docker 的標準化 Linux 容器，以偵測影像中的人臉。 它也會識別屬性，包括臉部地標（例如鼻子和眼睛）、性別、年齡及其他機器預測的臉部特徵。 除了偵測以外，臉部也可以使用信賴分數來檢查相同影像或不同影像中的兩張臉部是否相同。 臉部也可以比較臉部與資料庫，以查看是否已存在外觀相似或相同的臉部。 它也可以使用共用視覺特性，將類似的臉部組織成群組。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

使用臉部服務容器之前，您必須符合下列必要條件。

|必要|目的|
|--|--|
|Docker 引擎| Docker 引擎必須安裝在[主機電腦](#the-host-computer)上。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> 在 Windows 上，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您需要對 Docker 概念（例如登錄、存放庫、容器和容器映射）的基本瞭解。 您也需要基本命令的知識 `docker` 。| 
|臉部資源 |若要使用容器，您必須具有：<br><br>Azure**臉部**資源和相關聯的 API 金鑰和端點 URI。 這兩個值都可在資源的 [**總覽**] 和 [**金鑰**] 頁面上取得。 您必須啟動容器。<br><br>**{API_KEY}**： [**金鑰**] 頁面上有兩個可用的資源金鑰之一<br><br>**{ENDPOINT_URI}**： [**總覽**] 頁面上所提供的端點

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

填寫並提交[要求表單](https://aka.ms/cognitivegate)，以要求容器的存取權。 

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明要為每個臉部服務容器配置的最低和建議 CPU 核心和記憶體。

| 容器 | 最小值 | 建議 | 每秒交易<br>（最小值，最大值）|
|-----------|---------|-------------|--|
|臉部 | 1核心，2 GB 記憶體 | 1核心，4 GB 記憶體 |10, 20|

* 每個核心必須至少有 2.6 GHz 或更快的速度。
* 每秒交易數（TP）。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>取得具有 docker pull 的容器映射

臉部服務的容器映射可供使用。 

| 容器 | Repository |
|-----------|------------|
| 臉部 | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>臉部容器的 docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>使用容器

在容器位於[主機電腦](#the-host-computer)上之後，請使用下列程式來處理容器。

1. 使用必要的帳單設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](./face-resource-container-config.md#example-docker-run-commands)可供參考。 
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>使用 docker run 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 如需如何取得和值的詳細資訊，請參閱[收集必要的參數](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` 。

[Examples](face-resource-container-config.md#example-docker-run-commands)命令的範例 `docker run` 可供使用。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行臉部容器。
* 配置一個 CPU 核心和 4 GB 的記憶體。
* 公開 TCP 埠5000，並為容器配置虛擬 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。 

`docker run` 命令有相關[範例](./face-resource-container-config.md#example-docker-run-commands)可供參考。 

> [!IMPORTANT]
> 您 `Eula` `Billing` `ApiKey` 必須指定、和選項來執行容器，否則容器將不會啟動。 如需詳細資訊，請參閱[帳單](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 

針對容器 API 請使用主機 `http://localhost:5000`。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您執行具有輸出[裝載](./face-resource-container-config.md#mount-settings)的容器，而且已啟用記錄，則容器會產生記錄檔，有助於疑難排解您啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

臉部服務容器會使用您 Azure 帳戶上的臉部資源，將帳單資訊傳送至 Azure。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](./face-resource-container-config.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>總結

在本文中，您已瞭解如何下載、安裝及執行臉部服務容器的概念和工作流程。 摘要說明：

* 容器映射會從 Azure Container Registry 下載。
* 容器映像是在 Docker 中執行。
* 您可以藉由指定容器的主機 URI，使用 REST API 或 SDK 來呼叫臉部服務容器中的作業。
* 當您具現化容器時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器不會在未連線至 Azure 以進行計量的情況下執行授權。 客戶必須讓容器隨時與計量服務溝通帳單資訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 如需設定，請參閱[設定容器](face-resource-container-config.md)。
* 若要深入瞭解如何偵測和識別臉部，請參閱[臉部總覽](Overview.md)。
* 如需容器所支援之方法的詳細資訊，請參閱[臉部 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。
* 若要使用更認知服務的容器，請參閱[認知服務容器](../cognitive-services-container-support.md)。
