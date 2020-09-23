---
title: 如何安裝及執行使用異常偵測器 API 的容器
titleSuffix: Azure Cognitive Services
description: 瞭解如何安裝和使用容器，以使用異常偵測器 API 的 advanced 演算法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 2a4ff7da16524e0706601e43dff39325952990ff
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903556"
---
# <a name="install-and-run-anomaly-detector-containers"></a>安裝和執行異常偵測器容器 

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

異常偵測器具有下列容器特徵功能：

| 功能 | 功能 |
|--|--|
| 異常偵測器 | <li> 偵測即時發生的異常狀況。 <li> 以批次方式偵測整個資料集的異常狀況。 <li> 以批次方式偵測資料集中的趨勢變更點。<li> 推斷資料的預期標準範圍。 <li> 支援異常偵測敏感度調整，使其更符合您的資料。 |

如需 Api 的詳細資訊，請參閱：
* [深入瞭解異常偵測器 API 服務](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>必要條件

使用異常偵測器容器之前，您必須符合下列必要條件：

|必要|用途|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。|
|異常偵測器資源 |若要使用這些容器，您必須具備：<br><br>Azure _異常_ 偵測器資源，用來取得相關聯的 API 金鑰和端點 URI。 這兩個值都可在 Azure 入口網站的 **異常** 偵測器總覽和金鑰頁面上取得，而且必須要有這些值才能啟動容器。<br><br>**{API_KEY}**： [ **金鑰** ] 頁面上兩個可用資源金鑰的其中一個<br><br>**{ENDPOINT_URI}**： **總覽** 頁面上提供的端點|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表說明針對異常偵測器容器配置的最小和建議的 CPU 核心和記憶體。

| QPS (每秒查詢數)  | 最小值 | 建議 |
|-----------|---------|-------------|
| 10 QPS | 4核心，1 GB 記憶體 | 8核心 2 GB 記憶體 |
| 20 QPS | 8核心，2 GB 記憶體 | 16核心 4 GB 記憶體 |

每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下載容器映射。

| 容器 | Repository |
|-----------|------------|
| 認知服務-異常偵測器 | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>適用于異常偵測器容器的 Docker pull

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](anomaly-detector-container-configuration.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 如需如何取得和值的詳細資訊，請參閱 [收集必要參數](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` 。

[Examples](anomaly-detector-container-configuration.md#example-docker-run-commands)命令的範例 `docker run` 可供使用。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映射執行異常偵測器容器
* 配置一個 CPU 核心和 4 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

### <a name="running-multiple-containers-on-the-same-host"></a>在相同的主機上執行多個容器

如果您打算使用公開的連接埠執行多個容器，請務必使用不同的連接埠執行每個容器。 例如，在連接埠 5000 上執行第一個容器，以及在連接埠 5001 上執行第二個容器。

以您使用的容器值，取代 `<container-registry>` 和 `<container-name>`。 這些容器值不必是相同的容器。 您可以讓異常偵測器容器和在主機上執行的 LUIS 容器一起執行，或者您可以有多個異常偵測器容器正在執行。

在連接埠 5000 上執行第一個容器。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

在連接埠 5001 上執行第二個容器。


```bash
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

每個後續容器應該位於不同的連接埠。

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。

請對容器 API 使用主機 http://localhost:5000。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](anomaly-detector-container-configuration.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

異常偵測器容器會使用您 Azure 帳戶上的 _異常_ 偵測器資源，將計費資訊傳送至 azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](anomaly-detector-container-configuration.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，您已瞭解下載、安裝及執行異常偵測器容器的概念和工作流程。 摘要說明：

* 異常偵測器提供一個適用于 Docker 的 Linux 容器、使用 batch 與串流封裝異常偵測、預期的範圍推斷，以及敏感度調整。
* 容器映射是從專用於容器的專用 Azure Container Registry 下載。
* 容器映像是在 Docker 中執行。
* 您可以使用 REST API 或 SDK，藉由指定容器的主機 URI 來呼叫異常偵測器容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料 (例如，正在分析) 的時間序列資料傳送給 Microsoft。

## <a name="next-steps"></a>下一步

* 檢閱[設定容器](anomaly-detector-container-configuration.md)以了解組態設定
* [將異常偵測器容器部署至 Azure 容器實例](how-to/deploy-anomaly-detection-on-container-instances.md)
* [深入瞭解異常偵測器 API 服務](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
