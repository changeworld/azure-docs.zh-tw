---
title: 認知服務和 Machine Learning
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "75531474"
---
# <a name="cognitive-services-and-machine-learning"></a>認知服務和機器學習

認知服務提供機器學習功能來解決一般問題，例如分析文字的情緒情感，或分析影像以辨識物件或臉部。 您不需要特殊的機器學習或資料科學知識，就可以使用這些服務。 

[認知服務](welcome.md)是一組服務，各自支援不同、通用的預測功能。 這些服務會分成不同的類別，協助您找到正確的服務。 

|服務類別|目的|
|--|--|
|[決策](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|建置應用程式，以顯示有助於做出明智與高效決策的建議。|
|[語言](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|允許您的應用程式使用預先建立的指令碼處理自然語言、評估情感，以及了解如何辨識使用者想要的內容。|
|[搜尋](https://azure.microsoft.com/services/cognitive-services/directory/search/)|將 Bing 搜尋 API 新增至您的應用程式，並充分利用以單一 API 呼叫合併數十億個網頁、影像、影片和新聞的能力。|
|[語音](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|將語音轉換成文字，並將文字轉換成自然發音語音。 從一種語言翻譯成另一種語言，並啟用說話者驗證和辨識。|
|[視覺](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|辨識、識別、仲裁您的圖片、影片和數位筆跡內容，並為其製作標題及編製索引。|
||||

當您執行下列動作時，請使用認知服務：

* 可以使用一般化解決方案。
* 從程式設計 REST API 或 SDK 存取解決方案。 

當您執行下列動作時，請使用另一種機器學習解決方案：

* 需要選擇演算法，並需要針對非常特定的資料進行定型。

## <a name="what-is-machine-learning"></a>什麼是機器學習？

機器學習服務是一種概念，可讓您將資料和演算法結合在一起，以解決特定的需求。 一旦資料和演算法定型之後，輸出就會是您可以使用不同資料再次使用的模型。 定型的模型會根據新的資料提供見解。 

建立機器學習系統的程式需要對機器學習或資料科學有一些知識。

機器學習服務是使用[Azure Machine Learning （AML）產品與服務](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)來提供的。

## <a name="what-is-a-cognitive-service"></a>什麼是認知服務？

認知服務會在機器學習解決方案中提供部分或所有元件：資料、演算法和定型模型。 這些服務的目的是要要求資料的一般知識，而不需要使用機器學習或資料科學的經驗。 這些服務同時提供 REST API 和以語言為基礎的 Sdk。 因此，您必須具備程式設計語言知識，才能使用服務。

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>認知服務和 Azure Machine Learning （AML）是否類似？

這兩者的最終目標都是套用人工智慧（AI）來增強商務營運，但是各自的供應專案各自提供不同的功能。 

一般而言，這些物件是不同的：

* 認知服務適用于沒有機器學習服務經驗的開發人員。
* Azure Machine Learning 是針對資料科學家量身打造的。 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>認知服務與機器學習有何不同？

認知服務會為您提供已定型的模型。 這會將資料和演算法結合在一起，可從 REST API 或 SDK 取得。 視您的案例而定，您可以在幾分鐘內執行此服務。  認知服務會提供一般問題的解答，例如文字中的關鍵字組或影像中的專案識別。 

機器學習服務是一種程式，通常需要較長的時間才能順利執行。 這段時間是花在資料收集、清理、轉換、演算法選擇、模型定型和部署上，以取得認知服務所提供的相同功能層級。 有了機器學習服務，就可以針對高度特製化和/或特定問題提供解答。 機器學習服務問題需要熟悉特定的主題和問題的相關資料，以及資料科學的專業知識。

## <a name="what-kind-of-data-do-you-have"></a>您有何種資料？

認知服務，身為一組服務，可以針對定型的模型要求無、部分或所有自訂資料。 

### <a name="no-additional-training-data-required"></a>不需要其他定型資料

提供完整定型模型的服務可以視為_黑色_方塊。 您不需要知道它們的工作方式，或使用哪些資料來進行定型。 您可以將資料帶入經過完整訓練的模型，以取得預測。 

### <a name="some-or-all-training-data-required"></a>需要部分或所有定型資料

某些服務可讓您帶入自己的資料，然後將模型定型。 這可讓您使用服務的資料和演算法，搭配您自己的資料來擴充模型。 輸出會符合您的需求。 當您帶入自己的資料時，可能需要以服務特定的方式標記資料。 例如，如果您要定型模型來識別花卉，您可以提供花卉影像的目錄，以及每個影像中的花卉位置來定型模型。 

服務可_讓_您提供資料，以增強其本身的資料。 服務可能會_要求_您提供資料。 

### <a name="real-time-or-near-real-time-data-required"></a>需要即時或近乎即時的資料

服務可能需要即時或近乎即時的資料，才能建立有效的模型。 這些服務會處理大量的模型資料。 

## <a name="service-requirements-for-the-data-model"></a>資料模型的服務需求

下列資料會依其允許或需要的資料類型來分類每個服務。

|認知服務|不需要定型資料|您提供部分或所有定型資料|即時或近乎即時的資料收集|
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
|[語音文字轉換語音（TTS）](speech-service/text-to-speech.md)|x|x||
|[語音轉換文字（STT）](speech-service/speech-to-text.md)|x|x||
|[語音翻譯](speech-service/speech-translation.md)|x|||
|[文字分析](./text-analytics/overview.md)|x|||
|[翻譯文字](./translator/translator-info-overview.md)|x|||
|[翻譯工具文字-自訂翻譯工具](./translator/custom-translator/overview.md)||x||

* 個人化工具只需要服務所收集的訓練資料（因為它會即時運作），以評估您的原則和資料。 個人化工具不需要用來進行前置或批次定型的大型歷程資料集。 

## <a name="where-can-you-use-cognitive-services"></a>您可以在哪裡使用認知服務？
 
這些服務會用於任何可進行 REST API 或 SDK 呼叫的應用程式中。 應用程式的範例包括網站、bot、虛擬或混合現實、桌上型電腦和行動應用程式。 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure 認知搜尋與認知服務有何關聯？

[Azure 認知搜尋](../search/search-what-is-azure-search.md)是一種獨立的雲端搜尋服務，可選擇性地使用認知服務來新增影像和自然語言處理，以編制工作負載的索引。 藉由包裝個別 Api 的[內建技能](../search/cognitive-search-predefined-skills.md)，在 Azure 認知搜尋中公開認知服務。 您可以使用免費的資源進行逐步解說，但計畫針對較大的磁片區建立及附加可[計費資源](../search/cognitive-search-attach-cognitive-services.md)。

## <a name="how-can-you-use-cognitive-services"></a>您可以如何使用認知服務？

每個服務都會提供您資料的相關資訊。 您可以將服務結合在一起，以連鎖解決方案，例如將語音（音訊）轉換成文字、將文字翻譯成多種語言，然後使用翻譯的語言從知識庫取得答案。 雖然認知服務可用來自行建立智慧型解決方案，但也可以與傳統機器學習專案結合，以補充模型或加速開發流程。 

為其他機器學習工具提供匯出模型的認知服務：

|認知服務|模型資訊|
|--|--|
|[自訂視覺](./custom-vision-service/home.md)|[Export](./Custom-Vision-Service/export-model-python.md) for Tensorflow for Android，CoreML for IOS11，ONNX FOR Windows ML|

## <a name="learn-more"></a>深入了解

* [架構指南-Microsoft 有哪些機器學習服務產品？](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [機器學習服務-深度學習與機器學習的簡介](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>後續步驟

* 在[Azure 入口網站](cognitive-services-apis-create-account.md)中或透過[Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)建立認知服務帳戶。
* 瞭解如何向認知服務[進行驗證](authentication.md)。
* 使用[診斷記錄](diagnostic-logging.md)來進行問題識別和偵錯工具。 
* 在 Docker[容器](cognitive-services-container-support.md)中部署認知服務。
* 隨時掌握最新的[服務更新](https://azure.microsoft.com/updates/?product=cognitive-services)。
