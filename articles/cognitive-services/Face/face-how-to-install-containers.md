---
title: 安裝和執行臉部 API 的 Docker 容器
titleSuffix: Azure Cognitive Services
description: 使用臉部 API 的 Docker 容器來偵測及識別影像中的人臉。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
keywords: 內部部署、Docker、容器、識別
ms.openlocfilehash: 0f6807f771510f85c5a20cfb2a160cfe1e8726a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461507"
---
# <a name="install-and-run-face-containers-preview"></a>安裝並執行臉部容器 (預覽) 

> [!IMPORTANT]
> 已達到臉部容器使用者的限制。 我們目前不接受臉部容器的新應用程式。

Azure 認知服務臉部 API 提供 Linux Docker 容器，可偵測並分析影像中的人臉。 它也會識別屬性，包括臉部地標，例如鼻子和眼睛、性別、年齡及其他機器預測的臉部特徵。 除了偵測之外，臉部也可以使用信賴分數來檢查相同影像或不同影像中的兩個臉部是否相同。 臉部也可以比較臉部與資料庫，以查看是否已有類似外觀或相同的臉部。 也可以使用共用視覺特性，將類似的臉部組織成群組。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>必要條件

使用臉部服務容器之前，您必須符合下列必要條件。

|必要|目的|
|--|--|
|Docker 引擎| 您必須在 [主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> 在 Windows 上，您也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您需要對 Docker 概念（例如登錄、存放庫、容器和容器映射）有基本的瞭解。 您也需要基本命令的知識 `docker` 。| 
|臉部資源 |若要使用容器，您必須具備：<br><br>Azure **臉部** 資源和相關聯的 API 金鑰和端點 URI。 這兩個值都可在資源的 [ **總覽** ] 和 [ **金鑰** ] 頁面上取得。 您必須要有它們才能啟動容器。<br><br>**{API_KEY}**： [ **金鑰** ] 頁面上兩個可用資源金鑰的其中一個<br><br>**{ENDPOINT_URI}**： **總覽** 頁面上提供的端點

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明針對每個臉部服務容器配置的最小和建議的 CPU 核心和記憶體。

| 容器 | 最小值 | 建議 | 每秒交易<br> (最小值、最大值) |
|-----------|---------|-------------|--|
|臉部 | 1核心，2 GB 記憶體 | 1核心，4 GB 記憶體 |10, 20|

* 每個核心都必須至少有 2.6 GHz 或更快。
* 每秒交易數 (TPS) 。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 docker pull 取得容器映射

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

在容器位於 [主機電腦](#the-host-computer)之後，請使用下列程式來處理容器。

1. 使用必要的帳單設定來[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](./face-resource-container-config.md#example-docker-run-commands)可供參考。 
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>使用 docker run 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 如需如何取得和值的詳細資訊，請參閱 [收集必要參數](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` 。

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
* 公開 TCP 通訊埠5000，並為容器配置虛擬 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。 

`docker run` 命令有相關[範例](./face-resource-container-config.md#example-docker-run-commands)可供參考。 

> [!IMPORTANT]
> 您 `Eula` `Billing` `ApiKey` 必須指定、和選項才能執行容器，否則容器將不會啟動。 如需詳細資訊，請參閱[帳單](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 

請對容器 API 使用主機 `http://localhost:5000`。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您執行具有輸出 [掛接](./face-resource-container-config.md#mount-settings) 的容器，並啟用記錄功能，則容器會產生記錄檔，這些記錄檔有助於疑難排解您啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

臉部服務容器會使用您 Azure 帳戶上的臉部資源，將計費資訊傳送至 Azure。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](./face-resource-container-config.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，您已瞭解如何下載、安裝及執行臉部服務容器的概念和工作流程。 摘要說明：

* 從 Azure Container Registry 下載容器映射。
* 容器映像是在 Docker 中執行。
* 您可以藉由指定容器的主機 URI，使用 REST API 或 SDK 來呼叫臉部服務容器中的作業。
* 當您將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器未獲得授權，無法在未連線至 Azure 的情況下執行以進行計量。 客戶必須讓容器隨時都能與計量服務通訊帳單資訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 如需設定的設定，請參閱 [設定容器](face-resource-container-config.md)。
* 若要深入瞭解如何偵測和識別臉部，請參閱 [臉部總覽](Overview.md)。
* 如需容器所支援之方法的詳細資訊，請參閱 [臉部 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。
* 若要使用更多認知服務容器，請參閱 [認知服務容器](../cognitive-services-container-support.md)。
