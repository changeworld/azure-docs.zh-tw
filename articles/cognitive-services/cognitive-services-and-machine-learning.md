---
title: 認知服務和機器學習
titleSuffix: Azure Cognitive Services
description: 了解 Microsoft 認知服務如何與其他機器學習相關的 Azure 供應項目搭配使用。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531474"
---
# <a name="cognitive-services-and-machine-learning"></a>認知服務和機器學習

認知服務提供機器學習功能，以解決一般問題，例如分析情緒中的文本或分析圖像以識別物件或面孔。 您不需要特殊的機器學習或資料科學知識來使用這些服務。 

[認知服務](welcome.md)是一組服務，每個服務都支援不同的廣義預測功能。 這些服務分為不同的類別，以説明您找到合適的服務。 

|服務類別|目的|
|--|--|
|[決策](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|建置應用程式，以顯示有助於做出明智與高效決策的建議。|
|[Language](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|允許您的應用程式使用預先建立的指令碼處理自然語言、評估情感，以及了解如何辨識使用者想要的內容。|
|[搜尋](https://azure.microsoft.com/services/cognitive-services/directory/search/)|將 Bing 搜尋 API 新增至您的應用程式，並充分利用以單一 API 呼叫合併數十億個網頁、影像、影片和新聞的能力。|
|[語音](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|將語音轉換成文字，並將文字轉換成自然發音語音。 從一種語言翻譯成另一種語言，並啟用說話者驗證和辨識。|
|[視覺](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|辨識、識別、仲裁您的圖片、影片和數位筆跡內容，並為其製作標題及編製索引。|
||||

在您：

* 可以使用通用解決方案。
* 從程式設計 REST API 或 SDK 訪問解決方案。 

在您：

* 需要選擇演算法，並需要對非常具體的資料進行培訓。

## <a name="what-is-machine-learning"></a>什麼是機器學習？

機器學習是一個概念，將資料和演算法結合在一起，以解決特定需求。 訓練資料和演算法後，輸出是一個模型，您可以對不同的資料再次使用。 經過培訓的模型根據新資料提供見解。 

構建機器學習系統的過程需要一些機器學習或資料科學知識。

機器學習是使用[Azure 機器學習 （AML） 產品和服務](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)提供的。

## <a name="what-is-a-cognitive-service"></a>什麼是認知服務？

認知服務提供機器學習解決方案中的全部或部分元件：資料、演算法和經過訓練的模型。 這些服務旨在要求瞭解您的資料，而無需機器學習或資料科學經驗。 這些服務同時提供 REST API 和基於語言的 SDK。 因此，您需要具備程式設計語言知識才能使用服務。

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>認知服務和 Azure 機器學習 （AML） 如何相似？

兩者都有應用人工智慧 （AI） 來增強業務運營的最終目標是，儘管每種產品在相應產品中提供此功能的方式是不同的。 

通常，受眾不同：

* 認知服務適用于沒有機器學習經驗的開發人員。
* Azure 機器學習專為數據科學家量身定制。 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>認知服務與機器學習有什麼不同？

認知服務為您提供經過培訓的模型。 這將資料和演算法彙集在一起，可從 REST API 或 SDK 獲得。 您可以在幾分鐘內實現此服務，具體取決於您的方案。  認知服務提供一般問題的答案，如文本中的關鍵短語或圖像中的專案標識。 

機器學習是一個通常需要較長時間才能成功實現的過程。 這一時間用於資料收集、清理、轉換、演算法選擇、模型培訓和部署，以達到認知服務提供的相同級別的功能。 通過機器學習，可以提供高度專業化和/或特定問題的答案。 機器學習問題需要熟悉所考慮問題的特定主題和資料，以及資料科學的專業知識。

## <a name="what-kind-of-data-do-you-have"></a>你有什麼資料？

認知服務作為一組服務，可能需要訓練模型的一些、某些或所有自訂資料。 

### <a name="no-additional-training-data-required"></a>無需額外的培訓資料

提供經過全面培訓的模型的服務可以被視為_黑匣子_。 您不需要知道他們是如何工作的，也不需要知道哪些資料用於訓練它們。 您將資料帶到經過全面訓練的模型中，以獲得預測。 

### <a name="some-or-all-training-data-required"></a>所需的部分或全部培訓資料

某些服務允許您攜帶自己的資料，然後訓練模型。 這允許您使用服務的資料和演算法將模型與您自己的資料擴展。 輸出符合您的需求。 當您自帶資料時，您可能需要以特定于服務的方式標記資料。 例如，如果要訓練模型以標識花，則可以提供花圖像目錄以及每個圖像中花的位置來訓練模型。 

服務可能_允許您_提供資料以增強其自己的資料。 服務_可能需要您_提供資料。 

### <a name="real-time-or-near-real-time-data-required"></a>需要即時或接近即時資料

服務可能需要即時或接近即時資料來構建有效的模型。 這些服務處理大量模型資料。 

## <a name="service-requirements-for-the-data-model"></a>資料模型的服務要求

以下資料按允許或需要的資料類型對每個服務進行分類。

|認知服務|無需培訓資料|您提供部分或全部培訓資料|即時或近即時資料收集|
|--|--|--|--|
|[異常偵測器](./Anomaly-Detector/overview.md)|x|x|x|
|Bing 搜尋 |x|||
|[電腦視覺](./Computer-vision/Home.md)|x|||
|[內容檢閱者](./Content-Moderator/overview.md)|x||x|
|[自訂願景](./Custom-Vision-Service/home.md)||x||
|[臉部](./Face/Overview.md)|x|x||
|[表單辨識器](./form-recognizer/overview.md)||x||
|[沈浸式閱讀程式](./immersive-reader/overview.md)|x|||
|[筆跡辨識器](./Ink-recognizer/overview.md)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[個人化器](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA 製造商](./QnAMaker/Overview/overview.md)||x||
|[揚聲器識別器](./speaker-recognition/home.md)||x||
|[語音文本到語音轉換 （TTS）](speech-service/text-to-speech.md)|x|x||
|[語音到文本 （STT）](speech-service/speech-to-text.md)|x|x||
|[語音翻譯](speech-service/speech-translation.md)|x|||
|[文本分析](./text-analytics/overview.md)|x|||
|[翻譯文本](./translator/translator-info-overview.md)|x|||
|[翻譯文本 - 自訂翻譯器](./translator/custom-translator/overview.md)||x||

*個人化服務只需要由服務收集的培訓資料（因為它即時運行）來評估您的政策和資料。 個人化程式不需要大型歷史資料集進行前期或批次處理培訓。 

## <a name="where-can-you-use-cognitive-services"></a>在哪裡可以使用認知服務？
 
這些服務用於任何可以進行 REST API 或 SDK 調用的應用程式。 應用程式的示例包括網站、機器人、虛擬或混合現實、桌面和移動應用程式。 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure 認知搜索與認知服務的關係如何？

[Azure 認知搜索](../search/search-what-is-azure-search.md)是一個單獨的雲搜索服務，可以選擇使用認知服務將圖像和自然語言處理添加到索引工作負荷。 認知服務通過包裝單個 API[的內置技能](../search/cognitive-search-predefined-skills.md)在 Azure 認知搜索中公開。 您可以將免費資源用於演練，但計畫為較大的卷創建和附加[計費資源](../search/cognitive-search-attach-cognitive-services.md)。

## <a name="how-can-you-use-cognitive-services"></a>如何使用認知服務？

每個服務都提供有關您的資料的資訊。 您可以將服務組合到鏈式解決方案中，例如將語音（音訊）轉換為文本、將文本轉換為多種語言，然後使用翻譯的語言從知識庫中獲取答案。 雖然認知服務可以自行創建智慧解決方案，但它們也可以與傳統機器學習專案結合使用，以補充模型或加快開發過程。 

為其他機器學習工具提供匯出模型的認知服務：

|認知服務|模型資訊|
|--|--|
|[自訂願景](./custom-vision-service/home.md)|[匯出](./Custom-Vision-Service/export-model-python.md)用於 Android 的 Tensorflow，iOS11 的 CoreML，用於 Windows ML 的 ONNX|

## <a name="learn-more"></a>深入了解

* [架構指南 - 微軟的機器學習產品是什麼？](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [機器學習 - 深度學習與機器學習簡介](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>後續步驟

* 在[Azure 門戶](cognitive-services-apis-create-account.md)或使用[Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)創建認知服務帳戶。
* 瞭解如何對認知服務[進行身份驗證](authentication.md)。
* 使用[診斷日誌記錄](diagnostic-logging.md)進行問題識別和調試。 
* 在 Docker[容器](cognitive-services-container-support.md)中部署認知服務。
* 隨時瞭解[最新的服務更新](https://azure.microsoft.com/updates/?product=cognitive-services)。
