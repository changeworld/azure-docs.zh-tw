---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 了解 Docker 容器如何使認知服務更接近您的資料。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7a38ec47d416027e8ea3fa772ae01e4f6264197a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876810"
---
# <a name="container-support-in-azure-cognitive-services"></a>Azure 認知服務中的容器支援

Azure 認知服務中的容器支援允許開發人員使用 Azure 中可用的相同豐富的 API,並可在部署和託管[Docker 容器](https://www.docker.com/what-container)附帶的服務時靈活進行。 容器支援目前可用於 Azure 認知服務的子集,包括以下部分:

> [!div class="checklist"]
> * [異常偵測器][ad-containers]
> * [電腦視覺][cv-containers]
> * [臉部][fa-containers]
> * [表單辨識器][fr-containers]
> * [語言理解 (LUIS)][lu-containers]
> * [語音服務 API][sp-containers]
> * [文字分析][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

容器化是散發軟體的方法，它會將應用程式或服務 (包括其相依性及設定) 一起封裝成容器映像。 在只需要小幅修改或不修改的情況下，便可將容器映像部署在容器主機上。 容器之間彼此隔離，也與基礎作業系統隔離，且磁碟使用量比虛擬機器更小。 容器可以從容器映像具現化以進行短期工作，並於不再需要時移除。

認知服務資源在微軟[Azure](https://azure.microsoft.com)上可用。 請登入 [Azure 入口網站](https://portal.azure.com/)以建立並探索適用於這些服務的 Azure 資源。

## <a name="features-and-benefits"></a>功能與優點

- **不可改變的基礎架構**:使 DevOps 團隊能夠利用一組一致且可靠的已知系統參數,同時能夠適應變化。 容器提供了在可預測的生態系統中旋轉的靈活性,並避免配置漂移。
- **控制數據**:允許客戶選擇這些認知服務處理其數據的位置。 這對於無法將資料傳送到雲端，但需要存取認知服務技術的客戶來說，這是不可或缺的。 支援混合式環境中的一致性，橫跨資料、管理、身分識別及安全性。
- **對模型更新的控制**：為客戶針對部署於其解決方案中的模型，提供版本控制和更新上的彈性。
- **可移植體系結構**:啟用可部署在 Azure、本地和邊緣上的可移植應用程式體系結構。 您可以將容器直接部署至 [Azure Kubernetes Service](../aks/index.yml)、[Azure 容器執行個體](../container-instances/index.yml)，或是已部署至 [Azure Stack](/azure-stack/operator) 的 [Kubernetes](https://kubernetes.io/) 叢集。 如需詳細資訊，請參閱[將 Kubernetes 部署至 Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)。
- **高輸送量/低延遲**:通過使認知服務能夠實際運行到其應用程式邏輯和數據附近,為客戶提供擴展高輸送量和低延遲要求的能力。 容器不會限制每秒交易 (TPS)，而且如果您提供必要的硬體資源，會相應增加和相應放大來處理要求。
- **可擴充性**:隨著容器化和容器編排軟體的日益普及,如Kubernetes;可擴充性是技術進步的前沿。 應用程式開發基於可擴展的群集基礎,可滿足高可用性。

## <a name="containers-in-azure-cognitive-services"></a>Azure 認知服務中的容器

Azure 認知服務容器能提供下列 Docker 容器集合，每個容器都包含 Azure 認知服務中服務之功能的子集：

| 服務 | 支援的定價層 | 容器 | 描述 |
|---------|----------|----------|-------------|
|[例外偵測器][ad-containers] |F0, S0|**例外偵測器** |Anomaly Detector API 可讓您透過機器學習，監視和偵測時間序列資料中的異常狀況。<br>[要求存取](https://aka.ms/adcontainer)|
|[電腦視覺][cv-containers] |F0, S1|**讀取** |從具不同表面和背景之各種物件 (例如收據、海報和名片) 的影像擷取印刷文字。 讀取容器還檢測圖像中的*手寫文本*,並提供 PDF/TIFF/多頁支援。<br/><br/>**重要提示:**"讀取"容器目前僅適用於英語。|
|[臉部][fa-containers] |F0, S0|**臉部** |能偵測影像中的人臉並識別其特性，包括臉部特徵點 (例如鼻子和眼睛)、性別、年齡及其他機器預測的臉部容貌。 除了偵測以外，臉部也可以使用信賴分數檢查相同或不同影像中的兩張臉是否相同，或將臉部向資料庫進行比對，看看是否有樣貌相似或相同的臉部。 它也能夠使用共同視覺特徵，將相似臉部分組。<br>[要求存取](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[表單辨識器][fr-containers] |F0, S0|**表單辨識器** |表單理解應用機器學習技術來識別和提取窗體中的關鍵值對和表。<br>[要求存取](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[路易士][lu-containers] |F0, S0|**LUIS** ([影像](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|將已定型或發佈的 Language Understanding 模型 (也稱為 LUIS 應用程式) 載入 Docker 容器中，並提供從容器的 API 端點存取查詢預測的權限。 您可以從容器收集查詢記錄，並將這些記錄重新上傳至 [LUIS 入口網站](https://www.luis.ai)，以改善應用程式的預測精確度。|
|[語音服務 API][sp-containers-stt] |F0, S0|**語音轉文字** |連續的即時語音謄寫成文字。|
|[語音服務 API][sp-containers-cstt] |F0, S0|**自訂語音到文字** |使用自定義模型將連續即時語音轉錄為文本。|
|[語音服務 API][sp-containers-tts] |F0, S0|**文字轉換語音** |將文字轉換成自然發音語音。|
|[語音服務 API][sp-containers-ctts] |F0, S0|**自訂文字到語音** |使用自定義模型將文本轉換為自然語音。|
|[文字分析][ta-containers-keyphrase] |F0, S|**關鍵片語擷取** ([影像](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |擷取關鍵片語來識別重點。 例如，若輸入文字為 "The food was delicious and there were wonderful staff"，API 即會傳回主要討論要點："food" 和 "wonderful staff"。 |
|[文字分析][ta-containers-language]|F0, S|**語言偵測** ([影像](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |偵測輸入文字是以何種語言撰寫的，並針對要求所提交的每份文件回報單一語言代碼，最多可達 120 種語言。 語言代碼各配有一個分數，表示分數的強度。 |
|[文字分析][ta-containers-sentiment]|F0, S|**情感分析** ([影像](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |分析原始文字以尋找正面或負面情感的線索。 此 API 會為每份文件傳回 0 到 1 之間的情感分數，1 代表最正面的情感。 分析模型是使用大量文字主體和 Microsoft 的自然語言技術預先定型。 針對[選取的語言](./text-analytics/language-support.md)，API 可對您所提供的任何原始文字進行分析及評分，並直接將結果傳回至呼叫端應用程式。 |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

此外,認知服務[**一體一體提供**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)資源密鑰時,還支援某些容器。 您可以建立一體式認知服務一體式資源,並在受支援的服務中使用相同的計費密鑰,以便獲得以下服務:

* 電腦視覺
* 臉部
* LUIS
* 文字分析

## <a name="container-availability-in-azure-cognitive-services"></a>Azure 認知服務中的容器可用性

Azure 認知服務容器可透過您的 Azure 訂用帳戶公開取得，而 Docker 容器映像則可以從 Microsoft Container Registry 或 Docker Hub 提取。 您可以使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) \(英文\) 命令來從適當的登錄下載容器映像。

> [!IMPORTANT]
> 目前,您必須完成註冊過程才能訪問以下容器,在其中填寫並提交調查表,其中包含有關您、您的公司和要實現容器的用例的問題。 授予訪問許可權並提供認證後,可以從 Azure 容器註冊表託管的專用容器註冊錶中提取容器映射。
> * [例外偵測器](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [臉部](Face/face-how-to-install-containers.md)
> * [表單辨識器](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [讀取](computer-vision/computer-vision-how-to-install-containers.md)
> * [語音到文字與文字到語音](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Prerequisites

您必須滿足下列必要條件才能使用 Azure 認知服務容器：

**Docker 引擎**：您必須在本機安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/) \(英文\)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) \(英文\) 和 [Windows](https://docs.docker.com/docker-for-windows/) \(英文\) 上設定 Docker 環境的套件。 在 Windows 上，必須將 Docker 設定為支援 Linux 容器。 您也可以將 Docker 容器直接部署至 [Azure Kubernetes Service](../aks/index.yml) 或 [Azure 容器執行個體](../container-instances/index.yml)。

Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。

**對 Microsoft Container Registry 和 Docker 的熟悉度**：您應具備對 Microsoft Container Registry 和 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。

如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。

個別的容器可能也會有各自的需求，包括伺服器和記憶體配置需求。

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>後續步驟

瞭解可用於認知服務的[容器配方](containers/container-reuse-recipe.md)。

安裝並探索由 Azure 認知服務中的容器所提供的功能：

* [例外偵測器容器][ad-containers]
* [電腦視覺容器][cv-containers]
* [面容器][fa-containers]
* [表單辨識器容器][fr-containers]
* [語言理解 (LUIS) 容器][lu-containers]
* [語音服務 API 容器][sp-containers]
* [文字分析容器][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment