---
title: 安裝和執行文字分析 API 的 Docker 容器
titleSuffix: Azure Cognitive Services
description: 使用文字分析 API 的 Docker 容器來執行自然語言處理，例如內部部署的情感分析。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: aahi
keywords: 內部部署、Docker、容器、情感分析、自然語言處理
ms.openlocfilehash: 42b22c94f8f2eed0514906422fe9f1d5e7cd12e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461490"
---
# <a name="install-and-run-text-analytics-containers"></a>安裝並執行文字分析容器

> [!NOTE]
> * 情感分析 v3 的容器現已正式推出。 關鍵字組的擷取和語言偵測容器是以不受限的公開預覽形式提供。
> * 實體連結和 NER 目前無法作為容器使用。
> * 存取 health 容器的文字分析需要要求 [表單](https://aka.ms/csgate)。 目前，您不需要支付其使用量的費用。
> * 容器映射位置最近可能已變更。 請閱讀本文以查看此容器的更新位置。

容器可讓您在自己的環境中執行文字分析 API，而且非常適合您的特定安全性和資料控管需求。 文字分析容器提供原始文字的 advanced 自然語言處理，並包含三個主要功能：情感分析、關鍵字組解壓縮和語言偵測。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

> [!IMPORTANT]
> 免費帳戶的限制為每月5000筆交易，而只有**免費**和**標準**<a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">定價層 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>適用于容器。 如需交易要求速率的詳細資訊，請參閱 [資料限制](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits)。

## <a name="prerequisites"></a>必要條件

若要執行任何文字分析容器，您必須擁有主機電腦和容器環境。

## <a name="preparation"></a>準備

使用文字分析容器之前，您必須符合下列必要條件：

|必要|目的|
|--|--|
|Docker 引擎| 您必須在[主機電腦](#the-host-computer)上安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上設定 Docker 環境的套件。 如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。<br><br> Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。 <br><br> **在 Windows 上**，也必須將 Docker 設定為支援 Linux 容器。<br><br>|
|熟悉 Docker | 您應具備對 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。| 
|文字分析資源 |若要使用此容器，您必須具備：<br><br>Azure [文字分析資源](../../cognitive-services-apis-create-account.md) ，可取得相關聯的 API 金鑰和端點 URI。 這兩個值可在 Azure 入口網站的 [文字分析概觀和金鑰] 頁面上取得，需要這些值才能啟動容器。<br><br>**{API_KEY}**： [ **金鑰** ] 頁面上兩個可用資源金鑰的其中一個<br><br>**{ENDPOINT_URI}**： **總覽** 頁面上提供的端點|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主機電腦

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器的需求和建議

下表解說文字分析容器的最低和建議規格。 至少需要 2 gb 的記憶體 (GB) ，而且每個 CPU 核心至少必須是 2.6 (ghz) 或更快的速率。 也會列出每個區段 (TPS) 允許的交易。

|  | 最小主機規格 | 建議的主機規格 | 最小 TP | 最大 TPS|
|---|---------|-------------|--|--|
| **語言偵測，關鍵字組解壓縮**   | 1核心，2 GB 記憶體 | 1核心，4 GB 記憶體 |15 | 30|
| **情感分析 v3**   | 1核心，2 GB 記憶體 | 4核心，8GB 記憶體 |15 | 30|
| **適用于 health 1 檔/要求的文字分析**   |  4核心，10GB 記憶體 | 6核心，12GB 記憶體 |15 | 30|
| **適用于健康情況10檔/要求的文字分析**   |  6核心，16 GB 記憶體 | 8核心，20 gb 記憶體 |15 | 30|

CPU 核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，這些設定會當做命令的一部分使用 `docker run` 。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 取得容器映像

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

您可以在 Microsoft Container Registry 上取得文字分析的容器映射。

# <a name="sentiment-analysis-v3"></a>[情感分析 v3](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[關鍵片語擷取 (preview) ](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[語言偵測 (preview) ](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[適用于 health (preview 的文字分析) ](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>如何使用容器

容器位於[主機電腦](#the-host-computer)上時，請透過下列程序來使用容器。

1. 使用所需的計費設定[執行容器](#run-the-container-with-docker-run)。
1. [查詢容器的預測端點](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>透過 `docker run` 執行容器

使用 [docker 執行](https://docs.docker.com/engine/reference/commandline/run/) 命令來執行容器。 容器將會繼續執行，直到您將它停止為止。

> [!IMPORTANT]
> * 以下幾節的 Docker 命令會使用反斜線 `\` 作為行接續字元。 請根據您主機作業系統的需求加以替換或移除。 
> * 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。
> * 情感分析 v3 容器現已正式推出，其會在回應中傳回 [情感標籤](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) 。 關鍵字組解壓縮和語言偵測容器會使用 API 的 v2，並處於預覽狀態。

# <a name="sentiment-analysis-v3"></a>[情感分析 v3](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[關鍵片語擷取 (preview) ](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[語言偵測 (preview) ](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[適用于 health (preview 的文字分析) ](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查詢容器的預測端點

容器會提供以 REST 為基礎的查詢預測端點 API。

請對容器 API 使用主機 `http://localhost:5000`。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑難排解

如果您在啟用輸出[掛接](../text-analytics-resource-container-config.md#mount-settings)和記錄的情況下執行容器，容器將會產生記錄檔，有助於排解在啟動或執行容器時所發生的問題。

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>計費

文字分析容器會使用您 Azure 帳戶上的_文字分析_資源傳送計費資訊至 Azure。 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

如需這些選項的詳細資訊，請參閱[設定容器](../text-analytics-resource-container-config.md)。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，您已了解下載、安裝及執行文字分析容器的概念和工作流程。 摘要說明：

* 文字分析提供三個適用于 Docker 的 Linux 容器，並封裝各種功能：
   * *情感分析*
   * *關鍵片語擷取 (preview) * 
   * *語言偵測 (preview) *
   * *適用于 Health (preview 的文字分析) *
* 您可以從 Microsoft Container Registry 下載容器映射 (MCR) 或預覽容器存放庫。
* 容器映像是在 Docker 中執行。
* 您可以指定容器的主機 URI，來使用 REST API 或 SDK 呼叫文字分析容器中的作業。
* 將容器具現化時，您必須指定帳單資訊。

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料（例如正在分析的文字）傳送給 Microsoft， (例如) 。

## <a name="next-steps"></a>後續步驟

* 檢閱[設定容器](../text-analytics-resource-container-config.md)以了解組態設定
* 參閱[常見問題集 (FAQ)](../text-analytics-resource-faq.md) 來解決功能相關問題。
