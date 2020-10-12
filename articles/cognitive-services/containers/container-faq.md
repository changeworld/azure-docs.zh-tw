---
title: '認知服務容器的常見問題 (常見問題) '
titleSuffix: Azure Cognitive Services
description: 常見問題和解答。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 3d35a1f6913d0b657956489d0e57836a05f9eb1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900054"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure 認知服務容器的常見問題 (常見問題) 

## <a name="general-questions"></a>一般問題

**問：什麼是可用的？**

**答：** Azure 認知服務容器可讓開發人員使用 Azure 中可用的相同智慧型 Api，但有容器化的 [優點](../cognitive-services-container-support.md#features-and-benefits) 。 某些容器是以閘道預覽形式提供，可能需要應用程式才能存取。 其他容器可公開作為 ungated 預覽，或已正式推出。 您可以在 [Azure 認知服務中的容器支援](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services) 文章中找到完整的容器清單及其可用性。 您也可以查看 [Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services)中的容器。

**問：認知服務雲端與容器之間是否有任何差異？**

**答：** 認知服務容器是認知服務雲端的替代方案。 容器提供的功能與對應的雲端服務相同。 客戶可以在內部部署或在 Azure 中部署容器。 核心 AI 技術、定價層、API 金鑰和 API 簽章在容器和對應的雲端服務之間是相同的。 以下是在其雲端服務對等專案上選擇容器的 [功能和優點](../cognitive-services-container-support.md#features-and-benefits) 。

**問：如何? 存取並使用閘道預覽容器嗎？**

**答：** 先前，閘道預覽容器裝載于存放 `containerpreview.azurecr.io` 庫。 自2020年9月22日起，這些容器會裝載在 Microsoft Container Registry 上，因此不需要您使用 docker login 命令就能下載這些容器。 如果您的 Azure 資源是使用已核准的 Azure 訂用帳戶識別碼建立的，您將能夠執行閘道預覽容器。 如果您的 Azure 訂用帳戶在完成 [申請表單](https://aka.ms/csgate)之後尚未核准，您將無法執行容器。


**問：容器是否適用于所有認知服務，以及我們應預期的下一組容器為何？**

**答：** 我們想要讓更多認知服務可作為容器供應專案。 請洽詢您的本機 Microsoft 帳戶管理員，以取得新的容器版本和其他認知服務公告的更新。

**問：認知服務容器 Service-Level 合約 (SLA) 為何？**

**答：** 認知服務容器沒有 SLA。

資源的認知服務容器設定由客戶控制，因此 Microsoft 不會提供正式運作 (GA) 的 SLA。 客戶可自由地在內部部署容器，因此可定義主機環境。

> [!IMPORTANT]
> 若要深入瞭解認知服務 Service-Level 協定，請 [造訪我們的 SLA 頁面](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)。

**問：這些容器是否可在主權雲端中使用？**

**答：** 並非每個人都熟悉「主權雲端」一詞，因此讓我們從定義開始：

> 「主權雲端」包含 [Azure Government](../../azure-government/documentation-government-welcome.md)、 [Azure 德國](../../germany/germany-welcome.md)和 [Azure 中國的世紀](https://docs.microsoft.com/azure/china/overview-operations) 雲端。

可惜的是，主權雲端並 *不* 支援認知服務容器。 這些容器可以在這些雲端中執行，但它們將會從公用雲端提取，且需要將使用量資料傳送至公用端點。

### <a name="versioning"></a>版本控制

**問：容器如何更新為最新版本？**

**答：** 客戶可以選擇何時更新其已部署的容器。 容器將會以標準的 [Docker 標記](https://docs.docker.com/engine/reference/commandline/tag/) 標示，例如 `latest` ，表示最新版本。 我們鼓勵客戶在發行時提取最新版本的容器、簽出 [Azure Container Registry webhook](../../container-registry/container-registry-webhook.md) ，以取得如何在影像更新時收到通知的詳細資料。
 
**問：將支援哪些版本？**

**答：** 將支援容器的目前和最後一個主要版本。 不過，我們鼓勵客戶保持最新的技術，以取得最新技術。
 
**問：更新的版本為何？**

**答：** 主要版本變更指出 API 簽章有重大變更。 我們預期這通常會與對應認知服務雲端供應專案的主要版本變更一致。 次要版本變更指出錯誤修正、模型更新或未對 API 簽章進行重大變更的新功能。

## <a name="technical-questions"></a>技術問題

**問：如何在 IoT 裝置上執行認知服務容器？**

**答：** 您是否沒有可靠的網際網路連線，或想要節省頻寬成本。 或者，如果有低延遲需求，或正在處理需要在網站上分析的機密資料， [則與認知服務容器 Azure IoT Edge](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) ，可讓您與雲端保持一致。

**問：這些容器是否與 OpenShift 相容？** 

我們不會使用 OpenShift 來測試容器，但一般而言，認知服務容器應該在任何支援 Docker 映射的平臺上執行。 如果您使用的是 OpenShift，建議您以執行容器 `root-user` 。

**問：如何? 提供產品意見反應和功能建議？**

**答：** 我們鼓勵客戶對外聽取 [他們的疑慮](https://cognitive.uservoice.com/) ，並向上投票出可能發生問題的其他人。 「使用者語音」工具可用於產品意見反應和功能建議。

**問：認知服務容器會傳回哪些狀態訊息和錯誤？**

**答：** 請參閱下表中的狀態訊息和錯誤清單。

|狀態  | 描述  |
|---------|---------|
| `Valid` | 您的 API 金鑰有效，不需要採取任何動作。 |
| `Invalid` |   您的 API 金鑰無效。 您必須提供有效的 API 金鑰，才能執行容器。 在 Azure 入口網站中的 Azure 認知服務資源的 [ **金鑰和端點** ] 區段中，尋找您的 API 金鑰和服務區域。 |
| `Mismatch` | 您已針對不同種類的認知服務資源，提供 API 金鑰或端點。 在 Azure 認知服務資源的 [ **金鑰和端點** ] 區段中，尋找您的 API 金鑰和服務區域。 |
| `CouldNotConnect` | 容器無法連接至帳單端點。 檢查 `Retry-After` 值，並等候此期間結束後再提出其他要求。 |
| `OutOfQuota` | API 金鑰超出配額。 您可以升級定價層，或等候其他配額可供使用。 在 Azure 認知服務資源的 [ **定價層** ] 區段中，于 Azure 入口網站中尋找您的階層。 |
| `BillingEndpointBusy` | 帳單端點目前忙碌中。 檢查 `Retry-After` 值，並等候此期間結束後再提出其他要求。 |
| `ContainerUseUnauthorized` | 提供的 API 金鑰未獲授權，無法與此容器搭配使用。 您可能會使用閘道容器，因此請提交 [線上要求](https://aka.ms/csgate)以確定您的 Azure 訂用帳戶識別碼已獲得核准。 |
| `Unknown` | 伺服器目前無法處理計費要求。 |


**問：誰可以聯絡支援人員？**

**答：** 客戶支援通道與認知服務雲端供應專案相同。 所有認知服務容器都包含記錄功能，可協助我們和社區支援客戶。 如需其他支援，請參閱下列選項。

### <a name="customer-support-plan"></a>客戶支援方案

客戶應參考其 [Azure 支援計畫](https://azure.microsoft.com/support/plans/) ，以瞭解誰需要聯繫以尋求支援。

### <a name="azure-knowledge-center"></a>Azure 知識中心

客戶可免費探索 [Azure 知識中心](https://azure.microsoft.com/resources/knowledge-center/) ，以回答問題和支援問題。

### <a name="stack-overflow"></a>堆疊溢位

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) 是適用于專業人員和愛好者程式設計師的問題和解答網站。

探索下列標記，找出符合您需求的潛在問題和解答。

* [Azure 認知服務](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft 認知](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**問：帳單的運作方式為何？**

**答：** 客戶會根據耗用量收費，類似于認知服務雲端。 容器必須設定為將計量資料傳送到 Azure，並據以計費交易。 跨託管和內部部署服務使用的資源會新增至具有階層式定價的單一配額，並針對這兩種使用方式進行計算。 如需詳細資訊，請參閱對應供應專案的定價頁面。

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

**答：** 預覽版沒有任何保證。 當容器正式宣佈正式上市 (GA) 時，適用于企業軟體的 Microsoft 標準擔保。
 
**問：當網際網路連線中斷時，認知服務容器會發生什麼事？**

**答：** 認知服務容器不會在未連線至 Azure 進行計量的情況下執行 *授權* 。 客戶必須讓容器隨時都能與計量服務進行通訊。

**問：容器在未連線至 Azure 的情況下可以運作多久？**

**答：** 認知服務容器不會在未連線至 Azure 進行計量的情況下執行 *授權* 。 客戶必須讓容器隨時都能與計量服務進行通訊。
 
**問：執行這些容器需要什麼目前的硬體？**

**答：** 認知服務容器是以 x64 為基礎的容器，可執行任何相容的 Linux 節點、VM，以及支援 x64 Linux Docker 容器的邊緣裝置。 它們都需要 CPU 處理器。 以下提供每個容器供應專案的最低和建議設定：

* [異常偵測器][ad-containers-recommendations]
* [電腦視覺][cv-containers-recommendations]
* [臉部][fa-containers-recommendations]
* [表單辨識器][fr-containers-recommendations]
* [語言理解 (LUIS)][lu-containers-recommendations]
* [語音服務 API][sp-containers-recommendations]
* [文字分析][ta-containers-recommendations]
 
**問：這些容器目前是否支援 Windows？**

**答：** 認知服務容器是 Linux 容器，不過對於 Windows 上的 Linux 容器有一些支援。 如需 Windows 上的 Linux 容器的詳細資訊，請參閱 [Docker 檔](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)集。
 
**問：如何? 探索容器嗎？**

**答：** 認知服務容器可在各種不同的位置中使用，例如 Azure 入口網站、Docker hub 和 Azure container registry。 如需最新的容器位置，請參閱 [容器存放庫和映射](../cognitive-services-container-support.md#container-repositories-and-images)。

**問：認知服務容器與 AWS 和 Google 供應專案有何不同？**

**答：** Microsoft 是第一個雲端提供者，可在容器中移動其預先定型的 AI 模型，只要是客戶使用雲端服務，就能以簡單的方式計費每筆交易。 Microsoft 認為混合式雲端提供客戶更多選擇。

**問：容器擁有哪些合規性認證？**

**答：** 認知服務容器沒有任何合規性認證

**問：認知服務容器有哪些區域可供使用？**

**答：** 容器可以在任何區域中的任何位置執行，不過它們需要金鑰並回呼至 Azure 進行計量。 容器計量呼叫支援雲端服務的所有支援區域。

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
