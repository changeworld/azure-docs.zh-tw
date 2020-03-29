---
title: 認知服務容器常見問題 （FAQ）
titleSuffix: Azure Cognitive Services
description: 常見問題和答案。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: 33b99d50db4384c7de818a7dd0bb8492c86bef97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73961885"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure 認知服務容器常見問題 （FAQ）

## <a name="general-questions"></a>一般問題

**問：有哪些可用內容？**

**答：Azure** [認知服務中的容器支援](../cognitive-services-container-support.md)允許開發人員使用 Azure 中可用的智慧 API，但具有容器化[的優點](../cognitive-services-container-support.md#features-and-benefits)。 當前，Azure 認知服務的子集的預覽版提供容器支援，包括以下部分：

> [!div class="checklist"]
> * [異常偵測器][ad-containers]
> * [電腦視覺][cv-containers]
> * [臉部][fa-containers]
> * [表單辨識器][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [語音服務 API][sp-containers]
> * [文本分析][ta-containers]

**問：認知服務雲和容器之間有什麼區別嗎？**

**答：** 認知服務容器是認知服務雲的替代方法。 容器提供的功能與相應的雲服務相同。 客戶可以在本地或 Azure 中部署容器。 核心 AI 技術、定價層、API 金鑰和 API 簽名在容器和相應的雲服務之間是相同的。 以下是選擇容器而不是雲服務等效項的功能[和好處](../cognitive-services-container-support.md#features-and-benefits)。

**問：容器能否適用于所有認知服務，以及下一組容器應該期待什麼？**

**答：** 我們希望提供更多的認知服務作為容器產品提供。 聯繫您當地的 Microsoft 客戶經理，獲取有關新容器版本和其他認知服務公告的更新。

**問：認知服務容器的服務等級協定 （SLA） 是什麼？**

**答：** 認知服務容器沒有 SLA。

資源認知服務容器配置由客戶控制，因此 Microsoft 不會提供 SLA 以提供一般可用性 （GA）。 客戶可以自由地在本地部署容器，因此他們定義了主機環境。

> [!IMPORTANT]
> 要瞭解有關認知服務服務等級協定的更多[內容，請訪問我們的 SLA 頁面](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)。

**問：這些容器在主權雲中可用嗎？**

**答：** 不是每個人都熟悉"主權雲"一詞，因此讓我們從定義開始：

> "主權雲"由 Azure[政府](../../azure-government/documentation-government-welcome.md)[、Azure 德國](../../germany/germany-welcome.md)和[Azure 中國 21Vianet](https://docs.microsoft.com/azure/china/overview-operations)雲組成。

遺憾的是，在主權雲中*不支援*認知服務容器。 容器可以在這些雲中運行，但它們將從公共雲中拉出，並且需要將使用方式資料發送到公共終結點。

### <a name="versioning"></a>版本控制

**問：如何將容器更新到最新版本？**

**答：** 客戶可以選擇何時更新已部署的容器。 容器將標有標準[Docker 標記](https://docs.docker.com/engine/reference/commandline/tag/)，例如`latest`指示最新版本。 我們鼓勵客戶在發佈時提取最新版本的容器，結帳[Azure 容器註冊表 Webhook，](../../container-registry/container-registry-webhook.md)以獲取有關如何在更新映射時收到通知的詳細資訊。
 
**問：將支援哪些版本？**

**答：** 容器的當前和最後一個主要版本將受支援。 然而，我們鼓勵客戶保持最新狀態，以獲得最新的技術。
 
**問：如何對更新進行版本控制？**

**答：** 主要版本更改表示 API 簽名發生了重大更改。 我們預計，這通常與相應認知服務雲服務產品的主要版本更改一致。 次要版本更改表示錯誤修復、模型更新或新功能，這些功能不會對 API 簽名進行重大更改。

## <a name="technical-questions"></a>技術問題

**問：如何在 IoT 設備上運行認知服務容器？**

無論您沒有可靠的互聯網連接，還是想要節省頻寬成本。 或者，如果具有低延遲要求，或者正在處理需要現場分析的敏感性資料，[則使用認知服務容器的 Azure IoT Edge](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/)可使您與雲保持一致。

**問：如何提供產品回饋和功能建議？**

**答：** 鼓勵客戶公開[表達他們的憂慮](https://cognitive.uservoice.com/)，並在潛在問題重疊時對這樣做的其他人進行投票。 使用者語音工具可用於產品回饋和功能建議。

**問：我應聯繫誰尋求支援？**

**答：** 客戶支援管道與認知服務雲產品相同。 所有認知服務容器都包含日誌記錄功能，可説明我們和社區支援客戶。 有關其他支援，請參閱以下選項。

### <a name="customer-support-plan"></a>客戶支援計畫

客戶應參閱其[Azure 支援計畫](https://azure.microsoft.com/support/plans/)，瞭解與誰聯繫以尋求支援。

### <a name="azure-knowledge-center"></a>Azure 知識中心

客戶可以自由流覽 Azure[知識中心](https://azure.microsoft.com/resources/knowledge-center/)來回答問題和支援問題。

### <a name="stack-overflow"></a>堆疊溢位

> [堆疊溢位](https://en.wikipedia.org/wiki/Stack_Overflow)是專業和愛好者程式師的問答網站。

流覽以下標籤，瞭解符合您需求的潛在問題和答案。

* [Azure 認知服務](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [微軟認知](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**問：計費如何工作？**

**答：** 客戶根據消費付費，類似于認知服務雲。 需要將容器配置為向 Azure 發送計量資料，並將相應地對事務進行計費。 跨託管和本機服務使用的資源將添加到具有分層定價的單配額中，並計入這兩個用法。 有關詳細資訊，請參閱相應產品的定價頁面。

* [異常偵測器][ad-containers-billing]
* [電腦視覺][cv-containers-billing]
* [臉部][fa-containers-billing]
* [表單辨識器][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [語音服務 API][sp-containers-billing]
* [文本分析][ta-containers-billing]

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料傳送至 Microsoft。
 
**問：當前集裝箱支援保修是什麼？**

**答：** 預覽沒有保修。 當容器正式宣佈為通用 （GA） 時，微軟的企業軟體標準保修將適用。
 
**問：當互聯網連接丟失時，認知服務容器會發生什麼情況？**

**答：** 認知服務容器未連接到 Azure 進行計量時 *，未獲得運行許可*。 客戶需要使容器能夠隨時與計量服務通信。

**問：容器在不連接到 Azure 的情況下可以運行多長時間？**

**答：** 認知服務容器未連接到 Azure 進行計量時 *，未獲得運行許可*。 客戶需要使容器能夠隨時與計量服務通信。
 
**問：運行這些容器需要哪些當前硬體？**

**答：** 認知服務容器是基於 x64 的容器，可以運行任何支援 x64 Linux Docker 容器的相容 Linux 節點、VM 和邊緣設備。 它們都需要 CPU 處理器。 以下各容器產品的最低配置和推薦配置如下：

* [異常偵測器][ad-containers-recommendations]
* [電腦視覺][cv-containers-recommendations]
* [臉部][fa-containers-recommendations]
* [表單辨識器][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [語音服務 API][sp-containers-recommendations]
* [文本分析][ta-containers-recommendations]
 
**問：這些容器當前在 Windows 上是否受支援？**

**答：** 認知服務容器是 Linux 容器，但是 Windows 上有一些對 Linux 容器的支援。 有關 Windows 上的 Linux 容器的詳細資訊，請參閱[Docker 文檔](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)。
 
**問：如何發現容器？**

**答：** 認知服務容器可在不同位置使用，例如 Azure 門戶、Docker 集線器和 Azure 容器註冊表。 有關最新的容器位置，請參閱[容器存儲庫和圖像](../cognitive-services-container-support.md#container-repositories-and-images)。

**問：認知服務容器與 AWS 和 Google 產品相比如何？**

**答：** Microsoft 是第一個將預先訓練的 AI 模型移動到容器中的雲供應商，每次交易只需簡單計費，就像客戶使用雲服務一樣。 微軟認為混合雲為客戶提供了更多的選擇。

**問：容器有哪些合規性認證？**

**答：** 認知服務容器沒有任何合規性認證

**問：認知服務容器在哪些區域可用？**

**答：** 容器可以在任何區域的任意位置運行，但它們需要金鑰，並回檔到 Azure 進行計量。 對於容器計量調用，雲服務的所有支援區域都受支援。

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
