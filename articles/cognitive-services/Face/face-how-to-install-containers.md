---
title: 安裝和運行容器 - 面
titleSuffix: Azure Cognitive Services
description: 本文在本演練教程中演示如何下載、安裝和運行 Face 容器。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: e467b195ab1e2124286bfef74d7d1b71a4d99dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165985"
---
# <a name="install-and-run-face-containers-preview"></a>安裝和運行面容器（預覽）

Azure 認知服務面為 Docker 提供了一個標準化的 Linux 容器，用於檢測圖像中的人臉。 它還識別屬性，包括面部地標，如鼻子和眼睛、性別、年齡和其他機器預測的面部特徵。 除了檢測之外，Face 還可以使用置信度分數檢查同一圖像中的兩個面或不同的圖像是否相同。 面還可以將面與資料庫進行比較，以查看是否存在相似外觀或相同面。 它還可以使用共用的視覺特徵將相似的面孔組織成組。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

在使用 Face 服務容器之前，必須滿足以下先決條件。

|必要|目的|
|--|--|
|Docker 引擎| Docker 引擎必須安裝在[主機](#the-host-computer)上。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> 在 Windows 上，還必須將 Docker 配置為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您需要對 Docker 概念（如註冊表、存儲庫、容器和容器映射）的基本瞭解。 您還需要基本`docker`命令的知識。| 
|人臉資源 |要使用容器，您必須具有：<br><br>Azure**面**資源以及關聯的 API 金鑰和終結點 URI。 這兩個值都可以在資源的 **"概述**"和 **"鍵"** 頁上使用。 他們需要啟動容器。<br><br>**[API_KEY]**：**金鑰**頁上的兩個可用資源鍵之一<br><br>**[ENDPOINT_URI]**：**概述**頁上提供的終結點

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表描述了為每個 Face 服務容器分配的最小和推薦的 CPU 內核和記憶體。

| 容器 | 最小值 | 建議 | 每秒交易<br>（最小，最大）|
|-----------|---------|-------------|--|
|臉部 | 1 個內核，2 GB 記憶體 | 1 核，4 GB 記憶體 |10, 20|

* 每個內核必須至少為 2.6 GHz 或更快。
* 每秒事務 （TPS）。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用碼頭拉取容器映射

面服務的容器映射可用。 

| 容器 | Repository |
|-----------|------------|
| 臉部 | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>臉部容器的 docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>使用容器

容器位於[主機](#the-host-computer)上後，使用以下過程處理容器。

1. 使用所需的計費設置[運行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](./face-resource-container-config.md#example-docker-run-commands)可供參考。 
1. [查詢容器的預測終結點](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>使用 Docker 運行運行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 有關如何獲取 和`{ENDPOINT_URI}``{API_KEY}`值的詳細資訊，請參閱[收集所需的參數](#gathering-required-parameters)。

[命令的示例可用。](face-resource-container-config.md#example-docker-run-commands) `docker run`

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射運行面容器。
* 分配一個 CPU 內核和 4 GB 記憶體。
* 公開 TCP 埠 5000 並為容器分配偽 TTY。
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。 

`docker run` 命令有相關[範例](./face-resource-container-config.md#example-docker-run-commands)可供參考。 

> [!IMPORTANT]
> 必須`Eula`指定`Billing`和`ApiKey`選項才能運行容器，否則容器將無法啟動。 如需詳細資訊，請參閱[帳單](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。 

針對容器 API 請使用主機 `http://localhost:5000`。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果啟用了輸出[裝載](./face-resource-container-config.md#mount-settings)和日誌記錄運行容器，則容器將生成日誌檔，這些檔有助於解決啟動或運行容器時出現的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

面服務容器通過使用 Azure 帳戶上的面資源向 Azure 發送計費資訊。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](./face-resource-container-config.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>總結

在本文中，您學習了如何下載、安裝和運行 Face 服務容器的概念和工作流。 摘要說明：

* 容器映射從 Azure 容器註冊表下載。
* 容器映像是在 Docker 中執行。
* 可以使用 REST API 或 SDK 通過指定容器的主機 URI 來調用 Face 服務容器中的操作。
* 具現化容器時必須指定計費資訊。

> [!IMPORTANT]
> 認知服務容器未獲得運行許可，無需連接到 Azure 進行計量。 客戶必須使容器能夠隨時與計量服務通信計費資訊。 認知服務容器不會將客戶資料 (例如正在分析的影像或文字) 傳送至 Microsoft。

## <a name="next-steps"></a>後續步驟

* 有關配置設置，請參閱[配置容器](face-resource-container-config.md)。
* 要瞭解有關如何檢測和識別人臉的詳細資訊，請參閱[人臉概述](Overview.md)。
* 有關容器支援的方法的資訊，請參閱[面 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。
* 要使用更多認知服務容器，請參閱[認知服務容器](../cognitive-services-container-support.md)。
