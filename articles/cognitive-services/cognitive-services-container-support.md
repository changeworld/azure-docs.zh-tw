---
title: 使用內部部署的 Azure 認知服務容器
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用 Docker 容器來使用內部部署的認知服務。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 12/16/2020
ms.author: aahi
keywords: 內部部署、Docker、容器、Kubernetes
ms.openlocfilehash: f5caac0a0da3595075dddd5a273a6883a0cca468
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861820"
---
# <a name="azure-cognitive-services-containers"></a>Azure 認知服務容器

Azure 認知服務提供數個 [Docker 容器](https://www.docker.com/what-container) ，可讓您使用 Azure 中可用的相同 api （內部部署）。 使用這些容器可讓您彈性地將認知服務帶到更接近您的資料，以符合法規、安全性或其他操作原因。 容器支援目前適用于 Azure 認知服務的子集。

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

容器化是散發軟體的方法，它會將應用程式或服務 (包括其相依性及設定) 一起封裝成容器映像。 在只需要小幅修改或不修改的情況下，便可將容器映像部署在容器主機上。 容器之間彼此隔離，也與基礎作業系統隔離，且磁碟使用量比虛擬機器更小。 容器可以從容器映像具現化以進行短期工作，並於不再需要時移除。

## <a name="features-and-benefits"></a>功能與優點

- **不可變的基礎結構**：讓 DevOps 團隊能夠利用一致且可靠的已知系統參數集，同時能夠適應變更。 容器可讓您彈性地在可預測的生態系統內進行資料透視，並避免設定漂移。
- **對資料的控制**：選擇認知服務處理資料的位置。 如果您無法將資料傳送至雲端，但需要存取認知服務 API，這可能是不可或缺的。 支援混合式環境中的一致性，橫跨資料、管理、身分識別及安全性。
- **對模型更新的控制**：在解決方案中部署的模型版本控制和更新的彈性。
- **可移植的架構**：可讓您建立可在 Azure、內部部署和邊緣上部署的可移植應用程式架構。 您可以將容器直接部署至 [Azure Kubernetes Service](../aks/index.yml)、[Azure 容器執行個體](../container-instances/index.yml)，或是已部署至 [Azure Stack](/azure-stack/operator) 的 [Kubernetes](https://kubernetes.io/) 叢集。 如需詳細資訊，請參閱[將 Kubernetes 部署至 Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)。
- **高輸送量/低延遲**：讓認知服務在實體上以接近其應用程式邏輯和資料的方式執行，讓客戶能夠針對高輸送量和低延遲需求進行調整。 容器不會限制每秒交易 (TPS)，而且如果您提供必要的硬體資源，會相應增加和相應放大來處理要求。
- 擴充 **性**：隨著容器化和容器協調流程軟體的普及，例如 Kubernetes;擴充性是技術進步的 forefront。 以可調整的叢集基礎為基礎，應用程式開發已經考慮至高可用性。

## <a name="containers-in-azure-cognitive-services"></a>Azure 認知服務中的容器

Azure 認知服務容器提供下列一組 Docker 容器，每個容器都包含 Azure 認知服務中服務的功能子集。 您可以在下表中找到指示和影像位置。 也可以使用 [容器映射](containers/container-image-tags.md) 的清單。

### <a name="decision-containers"></a>決策容器

| 服務 |  容器 | 描述 | 可用性 |
|--|--|--|--|
| [異常偵測器][ad-containers] | **異常** 偵測器 ([映射](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))   | Anomaly Detector API 可讓您透過機器學習，監視和偵測時間序列資料中的異常狀況。 | 正式推出 |

### <a name="language-containers"></a>語言容器

| 服務 |  容器 | 描述 | 可用性 |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([影像](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | 將已定型或發佈的 Language Understanding 模型 (也稱為 LUIS 應用程式) 載入 Docker 容器中，並提供從容器的 API 端點存取查詢預測的權限。 您可以從容器收集查詢記錄，並將這些記錄重新上傳至 [LUIS 入口網站](https://www.luis.ai)，以改善應用程式的預測精確度。 | 正式推出 |
| [文字分析][ta-containers-keyphrase] | **關鍵片語擷取** ([影像](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | 擷取關鍵片語來識別重點。 例如，若輸入文字為 "The food was delicious and there were wonderful staff"，API 即會傳回主要討論要點："food" 和 "wonderful staff"。 | 預覽 |
| [文字分析][ta-containers-language] |  **文字語言偵測** ([影像](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409))  | 偵測輸入文字是以何種語言撰寫的，並針對要求所提交的每份文件回報單一語言代碼，最多可達 120 種語言。 語言代碼各配有一個分數，表示分數的強度。 | 預覽 |
| [文字分析][ta-containers-sentiment] | **情感分析 v3** ([映射](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409))  | 分析原始文字以尋找正面或負面情感的線索。 此版本的情感分析會傳回情感標籤 (例如，在其中每個檔和句子的 *正面* 或 *負面*) 。 |  正式推出 |
| [文字分析][ta-containers-health] |  **健康情況的文字分析** | 從非結構化臨床文字中將醫療資訊解壓縮並加上標籤。 | 閘道預覽。 [要求存取權][request-access]。 |

### <a name="speech-containers"></a>語音容器

> [!NOTE]
> 若要使用語音容器，您將需要完成 [線上要求表單](https://aka.ms/csgate)。

| 服務 |  容器 | 描述 | 可用性 |
|--|--|--|
| [語音服務 API][sp-containers-stt] |  **語音轉換文字** ([映射](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text))  | 連續的即時語音謄寫成文字。 | 正式推出 |
| [語音服務 API][sp-containers-cstt] | **自訂語音轉換文字** ([映射](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text))  | 使用自訂模型，將即時語音轉譯為文字。 | 正式推出 |
| [語音服務 API][sp-containers-tts] | **文字轉換語音** ([映射](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech))  | 將文字轉換成自然發音語音。 | 正式推出 |
| [語音服務 API][sp-containers-ctts] | **自訂文字轉換語音** ([映射](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech))  | 使用自訂模型將文字轉換成自然發音語音。 | 閘道預覽 |
| [語音服務 API][sp-containers-ntts] | **神經文字轉換語音** ([影像](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech))  | 使用深度類神經網路技術將文字轉換成自然發音語音，以允許更自然合成的語音。 | 正式推出 |
| [語音服務 API][sp-containers-lid] | **語音語言偵測** ([映射](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection))  | 判斷說話音訊的語言。 | 閘道預覽 |

### <a name="vision-containers"></a>視覺容器

> [!WARNING]
> Microsoft 於 2020 年 6 月 11 日宣佈，除非已制訂立基於人權的健全監管法規，否則不會將臉部辨識技術銷銷售給美國的警察部門。 因此，如果客戶隸屬於美國警察部門，或允許美國警察部門使用此類服務，則客戶可能無法使用臉部識別功能或 Azure 服務中納入的功能 (例如「臉部索引器」或「影片索引器」)。

| 服務 |  容器 | 描述 | 可用性 |
|--|--|--|--|
| [電腦視覺][cv-containers] | **讀取 OCR** ([映射](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read))  | 「讀取 OCR」容器可讓您從影像和檔中取出列印和手寫的文字，並支援 JPEG、PNG、BMP、PDF 和 TIFF 檔案格式。 如需詳細資訊，請參閱 [讀取 API 檔](./computer-vision/concept-recognizing-text.md)。 | 閘道預覽。 [要求存取權][request-access]。 |
| [空間分析][spa-containers] | **空間分析** ([映射](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis))  | 分析即時串流影片，以瞭解人員之間的空間關聯性、其移動和實體環境中物件的互動。 | 閘道預覽。 [要求存取權][request-access]。 |
| [臉部][fa-containers] | **臉部** | 能偵測影像中的人臉並識別其特性，包括臉部特徵點 (例如鼻子和眼睛)、性別、年齡及其他機器預測的臉部容貌。 除了偵測以外，臉部也可以使用信賴分數檢查相同或不同影像中的兩張臉是否相同，或將臉部向資料庫進行比對，看看是否有樣貌相似或相同的臉部。 它也能夠使用共同視覺特徵，將相似臉部分組。 | 無法使用 |
| [表單辨識器][fr-containers] | **表單辨識器** | 表單理解會套用機器學習技術來識別和解壓縮表單中的索引鍵/值組和資料表。 | 無法使用 | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

此外，認知服務 [多服務資源](cognitive-services-apis-create-account.md) 供應專案中支援某些容器。 您可以建立單一的「認知服務」全功能資源，並在支援的服務之間使用相同的計費金鑰來執行下列服務：

* 電腦視覺
* 臉部
* LUIS
* 文字分析

## <a name="prerequisites"></a>必要條件

您必須滿足下列必要條件才能使用 Azure 認知服務容器：

**Docker 引擎**：您必須在本機安裝 Docker 引擎。 Docker 提供可在 [macOS](https://docs.docker.com/docker-for-mac/) \(英文\)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) \(英文\) 和 [Windows](https://docs.docker.com/docker-for-windows/) \(英文\) 上設定 Docker 環境的套件。 在 Windows 上，必須將 Docker 設定為支援 Linux 容器。 您也可以將 Docker 容器直接部署至 [Azure Kubernetes Service](../aks/index.yml) 或 [Azure 容器執行個體](../container-instances/index.yml)。

Docker 必須設定為允許容器與 Azure 連線，以及傳送帳單資料至 Azure。

**對 Microsoft Container Registry 和 Docker 的熟悉度**：您應具備對 Microsoft Container Registry 和 Docker 概念 (例如登錄、存放庫、容器和容器映像等) 的基本了解，以及基本 `docker` 命令的知識。

如需 Docker 和容器基本概念的入門，請參閱 [Docker 概觀](https://docs.docker.com/engine/docker-overview/) \(英文\)。

個別的容器可能也會有各自的需求，包括伺服器和記憶體配置需求。

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>開發人員範例

開發人員範例可從我們的 [GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-containers-samples)取得。

## <a name="next-steps"></a>後續步驟

深入瞭解您可以搭配認知服務使用的 [容器配方](containers/container-reuse-recipe.md) 。

安裝並探索由 Azure 認知服務中的容器所提供的功能：

* [異常偵測器容器][ad-containers]
* [電腦視覺容器][cv-containers]
* [臉部容器][fa-containers]
* [表單辨識器容器][fr-containers]
* [Language Understanding (LUIS) 容器][lu-containers]
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
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u