---
title: 如何安裝及執行容器,用於使用異常檢測器 API
titleSuffix: Azure Cognitive Services
description: 使用 Anomaly Detector API 的進階的演算法來識別時間序列資料中的異常狀況。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fa25d27e99a9516d461a84dde184e2a6412baa0b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875028"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>安裝並執行異常偵測器容器(預覽版)

例外偵測器有以下容器功能:

| 函式 | 特性 |
|--|--|
| 例外偵測器 | <li> 實時檢測異常。 <li> 檢測整個數據集的異常情況,作為批處理。 <li> 推斷數據的預期正常範圍。 <li> 支持異常檢測靈敏度調整,以更好地適合您的數據。 |

有關 API 的詳細資訊,請參閱:
* [瞭解有關例外偵測器 API 服務的更多資訊](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

在使用異常檢測器容器之前,必須滿足以下先決條件:

|必要|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|例外偵測器資源 |若要使用這些容器，您必須具備：<br><br>Azure_異常檢測器_資源,用於獲取關聯的 API 金鑰和終結點 URI。 這兩個值都可以在 Azure 門戶的**異常檢測器**概述和密鑰頁上使用,並且需要啟動容器。<br><br>**[API_KEY]**:**金鑰**頁上的兩個可用資源鍵之一<br><br>**[ENDPOINT_URI]**:**概述**頁上提供的終結點|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須首先填寫並提交[異常檢測器容器請求表單](https://aka.ms/adcontainer),以請求對容器的訪問。

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表描述了為異常檢測器容器分配的最小和推薦的 CPU 內核和記憶體。

| QPS(每秒查詢) | 最小值 | 建議 |
|-----------|---------|-------------|
| 10 QPS | 4 核,1GB記憶體 | 8 核 2-GB 記憶體 |
| 20 QPS | 8 核,2 GB 記憶體 | 16 核 4 GB 記憶體 |

每個核心必須至少 2.6 GHz 或更快。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令下載容器映射。

| 容器 | Repository |
|-----------|------------|
| 認知服務-異常檢測器 | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>例外檢測器容器的塢取拉

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。 `docker run` 命令有相關[範例](anomaly-detector-container-configuration.md#example-docker-run-commands)可供參考。
1. [查詢容器的預測終結點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

將 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令執行容器。 有關如何取得與`{ENDPOINT_URI}``{API_KEY}`值的詳細資訊,請參考[收集所需的參數](#gathering-required-parameters)。

[命令的範例可用。](anomaly-detector-container-configuration.md#example-docker-run-commands) `docker run`

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 從容器映像執行異常偵測器容器
* 配置一個 CPU 核心和 4 GB 的記憶體
* 公開 TCP 連接埠 5000，並為容器配置虛擬 TTY
* 在容器結束之後自動將其移除。 容器映像仍可在主機電腦上使用。 

> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

### <a name="running-multiple-containers-on-the-same-host"></a>在同一主機上執行多個容器

如果您打算使用公開的連接埠執行多個容器，請務必使用不同的連接埠執行每個容器。 例如，在連接埠 5000 上執行第一個容器，以及在連接埠 5001 上執行第二個容器。

以您使用的容器值，取代 `<container-registry>` 和 `<container-name>`。 這些容器值不必是相同的容器。 您可以讓異常檢測器容器和 LUIS 容器一起在 HOST 上運行,也可以運行多個異常檢測器容器。 

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

針對容器 API 請使用主機 http://localhost:5000。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](anomaly-detector-container-configuration.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

異常檢測器容器使用 Azure 帳戶上的_異常檢測器_資源向 Azure 發送計費資訊。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](anomaly-detector-container-configuration.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中,您學習了下載、安裝和運行異常檢測器容器的概念和工作流。 摘要說明：

* 異常檢測器為 Docker 提供了一個 Linux 容器,通過批處理與流式處理、預期範圍推理和靈敏度調優封裝異常檢測。
* 容器映射從專用於容器預覽的專用 Azure 容器註冊表下載。
* 容器映像是在 Docker 中執行。
* 通過指定容器的主機 URI,可以使用 REST API 或 SDK 在異常檢測器容器中調用操作。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會向 Microsoft 發送客戶數據(例如,正在分析的時間序列數據)。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](anomaly-detector-container-configuration.md)以了解組態設定
* [將例外偵測器容器部署到 Azure 容器實體](how-to/deploy-anomaly-detection-on-container-instances.md)
* [瞭解有關例外偵測器 API 服務的更多資訊](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
