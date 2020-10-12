---
title: 認知服務和 Machine Learning
titleSuffix: Azure Cognitive Services
description: 了解 Microsoft 認知服務如何與其他機器學習相關的 Azure 供應項目搭配使用。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: 0b034d0135c66852b07e0a2ed2fd69e8c3fdc4a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132835"
---
# <a name="cognitive-services-and-machine-learning"></a>認知服務和機器學習

認知服務提供機器學習功能來解決一般問題，例如分析情緒情感的文字，或分析影像以辨識物件或臉部。 您不需要具備機器學習或資料科學的專門知識，即可使用這些服務。 

[認知服務](welcome.md) 是一組服務，每個服務都支援不同的一般化預測功能。 這些服務分為不同的類別，可協助您找出正確的服務。 

|服務類別|目的|
|--|--|
|[決策](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|建置應用程式，以顯示有助於做出明智與高效決策的建議。|
|[語言](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|允許您的應用程式使用預先建立的指令碼處理自然語言、評估情感，以及了解如何辨識使用者想要的內容。|
|[搜尋](https://azure.microsoft.com/services/cognitive-services/directory/search/)|將 Bing 搜尋 API 新增至您的應用程式，並充分利用以單一 API 呼叫合併數十億個網頁、影像、影片和新聞的能力。|
|[語音](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|將語音轉換成文字，並將文字轉換成自然發音語音。 從一種語言翻譯成另一種語言，並啟用說話者驗證和辨識。|
|[視覺](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|辨識、識別、仲裁您的圖片、影片和數位筆跡內容，並為其製作標題及編製索引。|
||||

當您執行下列動作時，請使用認知服務：

* 可以使用一般化的解決方案。
* 從程式設計 REST API 或 SDK 存取解決方案。 

當您執行下列動作時，請使用另一種機器學習解決方案：

* 需要選擇演算法，且需要針對非常特定的資料進行定型。

## <a name="what-is-machine-learning"></a>什麼是機器學習服務？

機器學習是一種概念，可讓您整合資料和演算法來解決特定需求。 一旦資料和演算法經過定型，輸出就會是您可以使用不同資料再次使用的模型。 定型的模型會根據新的資料提供見解。 

建立機器學習系統的程式需要瞭解機器學習或資料科學。

您可以使用 [Azure Machine Learning (AML) 產品與服務](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)來提供機器學習服務。

## <a name="what-is-a-cognitive-service"></a>什麼是認知服務？

認知服務會在機器學習解決方案中提供部分或所有元件：資料、演算法和定型的模型。 這些服務的目的是要要求您資料的一般知識，而不需要使用機器學習或資料科學的經驗。 這些服務提供 REST API () 和以語言為基礎的 Sdk。 因此，您需要有程式設計語言知識才能使用服務。

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>認知服務和 Azure Machine Learning (AML) 類似嗎？

兩者都有將人工智慧 (AI) 以增強商務營運的最終目標，不過每個專案在各自的供應專案中提供的方式都不同。 

一般而言，這些物件不同：

* 認知服務適用于沒有機器學習經驗的開發人員。
* Azure Machine Learning 是專為數據科學家量身打造。 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>認知服務與機器學習服務有何不同？

認知服務會為您提供已定型的模型。 這會將資料和演算法結合在一起，可從 REST API 的 (s) 或 SDK 取得。 視您的案例而定，您可以在幾分鐘內執行此服務。  認知服務會提供一般問題的解答，例如文字中的關鍵字組或影像中的專案識別。 

機器學習服務通常需要較長的時間才能成功執行。 這段時間花費在資料收集、清除、轉換、演算法選取、模型定型及部署，以取得認知服務所提供的相同層級功能。 利用機器學習，您可以提供高特殊和/或特定問題的解答。 機器學習問題需要熟悉所考慮之問題的特定主題和資料，以及資料科學的專業知識。

## <a name="what-kind-of-data-do-you-have"></a>您有何種資料？

認知服務是一組服務，可針對定型的模型要求無、部分或所有自訂資料。 

### <a name="no-additional-training-data-required"></a>不需要其他定型資料

提供完整定型模型的服務可以視為不 _透明_的方塊。 您不需要知道它們的運作方式，或是用來定型它們的資料。 您可以將資料帶入經過完整定型的模型，以取得預測。 

### <a name="some-or-all-training-data-required"></a>需要部分或全部定型資料

某些服務可讓您使用自己的資料，然後將模型定型。 這可讓您使用服務的資料和演算法，搭配您自己的資料來擴充模型。 輸出符合您的需求。 當您攜帶自己的資料時，可能需要以服務特定的方式來標記資料。 例如，如果您要訓練模型來識別花卉，您可以提供花卉影像的目錄以及每個影像中的花位置來定型模型。 

服務可 _讓_ 您提供資料來增強本身的資料。 服務可能會 _要求_ 您提供資料。 

### <a name="real-time-or-near-real-time-data-required"></a>需要即時或近乎即時的資料

服務可能需要即時或近乎即時的資料，才能建立有效的模型。 這些服務處理大量的模型資料。 

## <a name="service-requirements-for-the-data-model"></a>資料模型的服務需求

下列資料會將每個服務分類為允許或需要的資料類型。

|認知服務|不需要定型資料|您可以提供部分或所有定型資料|即時或近乎即時的資料收集|
|--|--|--|--|
|[異常偵測器](./Anomaly-Detector/overview.md)|x|x|x|
|Bing 搜尋 |x|||
|[電腦視覺](./Computer-vision/Home.md)|x|||
|[內容仲裁](./Content-Moderator/overview.md)|x||x|
|[自訂視覺](./Custom-Vision-Service/home.md)||x||
|[臉部](./Face/Overview.md)|x|x||
|[表單辨識器](./form-recognizer/overview.md)||x||
|[沈浸式閱讀程式](./immersive-reader/overview.md)|x|||
|[筆跡辨識器](./Ink-recognizer/overview.md)|x|x||
|[語言理解 (LUIS)](./LUIS/what-is-luis.md)||x||
|[個人化工具](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[說話者辨識器](./speaker-recognition/home.md)||x||
|[語音文字轉換語音 (TTS) ](speech-service/text-to-speech.md)|x|x||
|[語音轉換文字 (STT) ](speech-service/speech-to-text.md)|x|x||
|[語音翻譯](speech-service/speech-translation.md)|x|||
|[文字分析](./text-analytics/overview.md)|x|||
|[翻譯工具](./translator/translator-info-overview.md)|x|||
|[Translator-自訂翻譯工具](./translator/custom-translator/overview.md)||x||

* 個人化工具只需要服務 (所收集的定型資料，因為它會即時) 來評估您的原則和資料。 個人化工具不需要大型歷程記錄資料集，就能進行預先或批次訓練。 

## <a name="where-can-you-use-cognitive-services"></a>您可以在哪裡使用認知服務？
 
這些服務會用於任何可讓 REST API () 或 SDK 呼叫的應用程式。 應用程式範例包括網站、bot、虛擬或混合現實、桌面和行動應用程式。 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure 認知搜尋與認知服務有何關聯？

[Azure 認知搜尋](../search/search-what-is-azure-search.md) 是個別的雲端搜尋服務，可選擇性地使用認知服務來新增影像和自然語言處理來編制工作負載的索引。 認知服務會透過包裝個別 Api 的 [內建技能](../search/cognitive-search-predefined-skills.md) ，在 Azure 認知搜尋中公開。 您可以使用免費資源進行逐步解說，但請規劃為較大的磁片區建立和附加 [計費資源](../search/cognitive-search-attach-cognitive-services.md) 。

## <a name="how-can-you-use-cognitive-services"></a>您可以如何使用認知服務？

每個服務都會提供您資料的相關資訊。 您可以將服務結合在一起，以將語音 (音訊) 轉換成文字、將文字翻譯成多種語言，然後使用翻譯的語言從知識庫取得答案。 雖然認知服務可以用來自行建立智慧型解決方案，但也可以結合傳統的機器學習服務專案來補充模型或加速開發流程。 

針對其他機器學習工具提供匯出模型的認知服務：

|認知服務|模型資訊|
|--|--|
|[自訂視覺](./custom-vision-service/home.md)|針對 Android Tensorflow[匯出](./Custom-Vision-Service/export-model-python.md)，CoreML for IOS11，ONNX FOR Windows ML|

## <a name="learn-more"></a>深入了解

* [架構指南-Microsoft 的機器學習服務產品是什麼？](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [機器學習服務-深度學習和機器學習的簡介](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>接下來的步驟

* 在 [Azure 入口網站](cognitive-services-apis-create-account.md) 中或使用 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)建立您的認知服務帳戶。
* 瞭解如何對認知服務 [進行驗證](authentication.md) 。
* 使用 [診斷記錄](diagnostic-logging.md) 來進行問題的識別和偵測。 
* 在 Docker [容器](cognitive-services-container-support.md)中部署認知服務。
* 隨時掌握最新的 [服務更新](https://azure.microsoft.com/updates/?product=cognitive-services)。
