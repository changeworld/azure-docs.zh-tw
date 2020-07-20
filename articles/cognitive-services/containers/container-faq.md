---
title: 認知服務容器常見問題（FAQ）
titleSuffix: Azure Cognitive Services
description: 常見問題和解答。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bf30fc5e6ccfc0f59c1769245e58177428472156
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701805"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure 認知服務容器的常見問題（FAQ）

## <a name="general-questions"></a>一般問題

**問：什麼是可用的？**

**答：** Azure 認知服務容器可讓開發人員使用 Azure 中可用的相同智慧型 Api，但具有容器化的[優點](../cognitive-services-container-support.md#features-and-benefits)。 有些容器是以閘道預覽的形式提供，可能需要應用程式才能存取。 其他容器則以 ungated 預覽的形式公開提供，或已正式推出。 您可以在 Azure 認知服務一文中的[容器支援](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services)中找到容器及其可用性的完整清單。 

**問：認知服務雲端與容器之間是否有任何差異？**

**答：** 認知服務容器是認知服務雲端的替代方案。 容器提供的功能與對應的雲端服務相同。 客戶可以將容器部署在內部部署或 Azure 中。 在容器和對應的雲端服務之間，核心 AI 技術、定價層、API 金鑰和 API 簽章都相同。 以下是在其雲端服務對等的情況中選擇容器的[功能和優點](../cognitive-services-container-support.md#features-and-benefits)。

**問：容器是否適用于所有的認知服務，以及下一組預期的容器是什麼？**

**答：** 我們想要將更多認知服務提供給容器供應專案。 請洽詢您的本機 Microsoft 帳戶管理員，以取得新容器版本和其他認知服務宣告的更新。

**問：認知服務容器的服務等級協定（SLA）為何？**

**答：** 認知服務容器沒有 SLA。

認知服務資源的容器設定是由客戶所控制，因此 Microsoft 不會提供公開上市（GA）的 SLA。 客戶可以免費部署內部部署容器，因此會定義主機環境。

> [!IMPORTANT]
> 若要深入瞭解認知服務服務等級協定，請[造訪我們的 SLA 頁面](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)。

**問：這些容器是否可在主權雲端中使用？**

**答：** 並非每個人都熟悉「主權 cloud」一詞，因此讓我們從定義開始：

> 「主權雲端」包含[Azure Government](../../azure-government/documentation-government-welcome.md)、 [Azure 德國](../../germany/germany-welcome.md)和[azure 中國世紀](https://docs.microsoft.com/azure/china/overview-operations)雲端。

可惜的是，主權雲端原本*不*支援認知服務容器。 容器可以在這些雲端中執行，但會從公用雲端提取，而且需要將使用方式資料傳送至公用端點。

### <a name="versioning"></a>版本控制

**問：如何將容器更新為最新版本？**

**答：** 客戶可以選擇何時更新已部署的容器。 容器將會以標準[Docker 標記](https://docs.docker.com/engine/reference/commandline/tag/)（例如）標示， `latest` 以指出最新版本。 我們鼓勵客戶在發行時提取最新版本的容器， [Azure Container Registry webhook](../../container-registry/container-registry-webhook.md) ，以取得如何在更新映射時收到通知的詳細資訊。
 
**問：將支援哪些版本？**

**答：** 將支援容器的目前和最後一個主要版本。 不過，我們鼓勵客戶隨時保持最新的技術。
 
**問：更新的版本如何？**

**答：** 主要版本變更表示 API 簽章有中斷性變更。 我們預期這通常會與對應認知服務雲端供應專案的主要版本變更一致。 次要版本變更表示 bug 修正、模型更新，或不會對 API 簽章進行重大變更的新功能。

## <a name="technical-questions"></a>技術問題

**問：如何在 IoT 裝置上執行認知服務容器？**

**答：** 您是否沒有可靠的網際網路連線，或想要節省頻寬成本。 或者，如果具有低延遲需求，或正在處理需要在網站上分析的機密資料，[使用認知服務容器 Azure IoT Edge](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/)可讓您與雲端保持一致。

**問：這些容器是否與 OpenShift 相容？** 

我們不會使用 OpenShift 來測試容器，但一般來說，認知服務容器應該在支援 Docker 映射的任何平臺上執行。 如果您使用的是 OpenShift，建議您將容器當做來執行 `root-user` 。

**問：如何? 提供產品意見反應和功能建議嗎？**

**答：** 我們鼓勵客戶以公開的方式對[他們的顧慮進行語音](https://cognitive.uservoice.com/)處理，並將在潛在問題重迭的其他人投票。 使用者語音工具可用於產品意見反應和功能建議。

**問：誰可以尋求支援？**

**答：** 客戶支援通道與認知服務雲端供應專案相同。 所有認知服務容器都包含記錄功能，可協助我們和「社區」支援客戶。 如需其他支援，請參閱下列選項。

### <a name="customer-support-plan"></a>客戶支援方案

客戶應參考其[Azure 支援方案](https://azure.microsoft.com/support/plans/)，以查看要聯絡尋求支援的人員。

### <a name="azure-knowledge-center"></a>Azure 知識中心

客戶可以免費探索[Azure 知識中心](https://azure.microsoft.com/resources/knowledge-center/)，以回答問題和支援問題。

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow)是專業和愛好者程式設計人員的問答網站。

探索下列標記，尋找符合您需求的潛在問題和答案。

* [Azure 認知服務](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft 認知](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**問：計費如何運作？**

**答：** 客戶會根據耗用量計費，類似于認知服務雲端。 容器必須設定為將計量資料傳送至 Azure，而交易則會據此計費。 跨託管和內部部署服務使用的資源，將會新增至具有分層定價的單一配額，並針對這兩種使用方式進行計算。 如需詳細資訊，請參閱對應供應專案的定價頁面。

* [異常偵測器][ad-containers-billing]
* [電腦視覺][cv-containers-billing]
* [臉部][fa-containers-billing]
* [表單辨識器][fr-containers-billing]
* [語言理解 (LUIS)][lu-containers-billing]
* [語音服務 API][sp-containers-billing]
* [文字分析][ta-containers-billing]

> [!IMPORTANT]
> 認知服務容器在未連線至 Azure 以進行計量的情況下，將無法被授權以執行。 客戶必須啟用容器以持續與計量服務進行帳單資訊的通訊。 認知服務容器不會將客戶資料傳送至 Microsoft。
 
**問：容器目前的支援擔保為何？**

**答：** 預覽不提供任何擔保。 當容器正式發佈為公開上市（GA）時，Microsoft 的企業軟體標準擔保將適用。
 
**問：當網際網路連線中斷時，認知服務容器會發生什麼事？**

**答：** 未連線至 Azure 以進行計量的情況下，認知服務容器*未獲授權*可執行。 客戶必須讓容器能夠隨時與計量服務進行通訊。

**問：容器在未連線至 Azure 的情況下可運作多久？**

**答：** 未連線至 Azure 以進行計量的情況下，認知服務容器*未獲授權*可執行。 客戶必須讓容器能夠隨時與計量服務進行通訊。
 
**問：執行這些容器所需的目前硬體為何？**

**答：** 認知服務容器是以 x64 為基礎的容器，可執行任何支援 x64 Linux Docker 容器的相容 Linux 節點、VM 和 edge 裝置。 它們都需要 CPU 處理器。 以下提供每個容器供應專案的最低和建議設定：

* [異常偵測器][ad-containers-recommendations]
* [電腦視覺][cv-containers-recommendations]
* [臉部][fa-containers-recommendations]
* [表單辨識器][fr-containers-recommendations]
* [語言理解 (LUIS)][lu-containers-recommendations]
* [語音服務 API][sp-containers-recommendations]
* [文字分析][ta-containers-recommendations]
 
**問：這些容器目前是否支援 Windows？**

**答：** 認知服務容器是 Linux 容器，但在 Windows 上有一些 Linux 容器的支援。 如需 Windows 上 Linux 容器的詳細資訊，請參閱[Docker 檔](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)。
 
**問：如何? 探索容器嗎？**

**答：** 認知服務容器可在各種不同的位置使用，例如 Azure 入口網站、Docker hub 和 Azure container registry。 如需最新的容器位置，請參閱[容器存放庫和映射](../cognitive-services-container-support.md#container-repositories-and-images)。

**問：認知服務容器與 AWS 和 Google 供應專案有何不同？**

**答：** Microsoft 是第一個雲端提供者，可使用簡單的每筆交易計費，在容器中移動預先定型的 AI 模型，如同客戶使用雲端服務一樣。 Microsoft 相信混合式雲端讓客戶擁有更多選擇。

**問：容器有哪些合規性認證？**

**答：** 認知服務容器沒有任何合規性認證

**問：哪些區域是可用的認知服務容器？**

**答：** 容器可以在任何區域中的任何位置執行，但它們需要金鑰，並回呼至 Azure 以進行計量。 容器計量呼叫支援雲端服務的所有支援區域。

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
