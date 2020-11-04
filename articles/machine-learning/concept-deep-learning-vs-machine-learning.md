---
title: 深度學習與機器學習服務
titleSuffix: Azure Machine Learning
description: 瞭解深度學習與機器學習和 AI 之間的關聯。 在 Azure Machine Learning 中，請使用深度學習模型來偵測詐騙、偵測物件，以及執行其他功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 09/22/2020
ms.custom: contperfq1
ms.openlocfilehash: 1a6708214eadb2f5d0d66d72f5db5cb4a195d206
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324865"
---
# <a name="deep-learning-vs-machine-learning-in-azure-machine-learning"></a>Azure Machine Learning 中的深度學習與機器學習

本文說明深度學習與機器學習，以及它們如何融入更廣泛的人工智慧類別中。 瞭解您可以在 Azure Machine Learning 上建立的深度學習解決方案，例如詐騙偵測、語音和臉部辨識、情感分析，以及時間序列預測。

如需有關為您的解決方案選擇演算法的指引，請參閱 Machine Learning 演算法功能提要（適用于 [工作表](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)）。

## <a name="deep-learning-machine-learning-and-ai"></a>深度學習、機器學習和 AI

![關聯性圖表： AI 與機器學習與深度學習的比較](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

請考慮下列定義以瞭解深度學習與機器學習與 AI 的比較：

- **深度學習** 是以人工類神經網路為基礎的機器學習的子集。 _學習_ 程式很 _深_ ，因為人工類神經網路的結構包含多個輸入、輸出和隱藏層。 每一層都包含轉換單位，可將輸入資料轉換成資訊，以供下一層用於特定預測工作。 由於有此結構，機器可以透過自己的資料處理學習。

- **機器學習** 是人工智慧的子集，其使用 (的技術，例如深度學習) ，可讓電腦使用體驗來改善工作。 _學習_ 程式是以下列步驟為基礎：

   1. 將資料送入演算法。  (在此步驟中，您可以將其他資訊提供給模型，例如，藉由執行功能解壓縮。 ) 
   1. 使用此資料來定型模型。
   1. 測試及部署模型。
   1. 使用已部署的模型來進行自動化的預測工作。  (換句話說，請呼叫並使用已部署的模型，以接收模型傳回的預測。 ) 

- **人工智慧 (AI)** 是一種可讓電腦模仿人類智慧的技術。 它包含機器學習服務。 
 
藉由使用機器學習和深度學習技術，您可以建立電腦系統和應用程式，以進行通常與人類智慧相關聯的工作。 這些工作包括影像辨識、語音辨識和語言轉譯。

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>深度學習與機器學習服務的技術 

現在您已經大致瞭解機器學習與深度學習，讓我們來比較這兩種技巧。 在機器學習中，需要告知演算法如何藉由取用詳細資訊 (例如，藉由執行功能解壓縮) 來做出精確的預測。 在深度學習中，演算法可以瞭解如何透過自己的資料處理來進行精確的預測，而不需要人工類神經網路結構。

下表將更詳細地比較這兩種技術：

| |所有機器學習服務 |僅深度學習|
|---|---|---|
|  **資料點數目** | 可以使用少量資料來進行預測。 | 需要使用大量定型資料來進行預測。 |
|  **硬體相依性** | 可以在低終端機上運作。 它不需要大量的計算能力。 | 相依于高階電腦。 它原本就會執行大量的矩陣乘法運算。 GPU 可以有效率地將這些作業優化。 |
|  **特徵化流程** | 需要由使用者正確地識別並建立功能。 | 從資料學習高階功能，並自行建立新功能。 |
|  **學習方法** | 將學習過程分成較小的步驟。 然後，它會將每個步驟的結果合併成一個輸出。 | 藉由以端對端方式解決問題，來逐步完成學習流程。 |
|  **執行時間** | 花一點時間來定型，範圍從幾秒鐘到幾個小時。 | 定型通常需要較長的時間，因為深度學習演算法牽涉到許多圖層。 |
|  **輸出** | 輸出通常是數值，例如分數或分類。 | 輸出可以有多種格式，例如文字、分數或音效。 |

## <a name="deep-learning-use-cases"></a>深度學習使用案例

因為人工類神經網路結構，所以深度學習擅長在非結構化資料（例如影像、音效、影片和文字）中的識別模式。 基於這個理由，深度學習會快速轉換許多產業，包括醫療保健、能源、財務和運輸。 這些產業現在重新思考傳統的商務流程。 

以下段落將說明一些最常見的深度學習應用程式。 在 Azure Machine Learning 中，您可以使用從開放原始碼架構建立的模型，或使用所提供的工具來建立模型。

### <a name="named-entity-recognition"></a>命名實體辨識

命名實體辨識是一個深度學習方法，它會採用一段文字做為輸入，並將其轉換為預先指定的類別。 這種新資訊可能是郵遞區號、日期、產品識別碼。 然後，您可以將此資訊儲存在結構化架構中，以建立地址清單或作為身分識別驗證引擎的基準測試。

### <a name="object-detection"></a>物件偵測

深度學習已套用在許多物件偵測使用案例中。 物件偵測包含兩個部分：影像分類和影像當地語系化。 影像 _分類_ 會識別影像的物件，例如車輛或人員。 影像 _當地語系化_ 會提供這些物件的特定位置。 

物件偵測已用於遊戲、零售、觀光和自我駕駛汽車等產業中。

瞭解如何在 Azure Machine Learning 中使用開放原始碼架構的影像分類模型： [使用 Pytorch 模型分類影像](./how-to-train-pytorch.md?WT.mc_id=docs-article-lazzeri)

### <a name="image-caption-generation"></a>產生影像標題

就像影像辨識一樣，在影像字幕中，針對指定的影像，系統必須產生描述影像內容的標題。 當您可以偵測及標記相片中的物件時，下一步就是將這些標籤轉換成描述性句子。 

影像字幕通常會使用卷積類神經網路來識別影像中的物件，然後使用週期性神經網路將標籤轉換成一致的句子。

### <a name="machine-translation"></a>機器翻譯

機器翻譯會接受一種語言的單字或句子，並自動將它們轉譯成另一種語言。 機器翻譯有很長一段時間，但深度學習在兩個特定區域中可獲得令人印象深刻的結果：自動轉譯文字 (以及將語音轉換成文字) 和自動轉譯影像。

使用適當的資料轉換，類神經網路可以瞭解文字、音訊和視覺信號。 機器翻譯可以用來識別大型音訊檔案中的音效程式碼片段，並以文字形式轉譯說出的單字或影像。

### <a name="text-analytics"></a>文字分析

以深度學習方法為基礎的文字分析牽涉到分析大量的文字資料 (例如，醫療檔或支出收據) 、辨識模式，以及建立組織內和精簡的資訊。

公司會使用深度學習來執行文字分析，以偵測內部交易與政府法規的合規性。 另一個常見的例子是保險詐騙：文字分析通常用來分析大量檔，以辨識保險索賠詐騙的機會。 

瞭解如何在 Azure Machine Learning 中使用 TensorFlow 模型： [使用 TensorFlow 模型將手寫數位分類](./how-to-train-tensorflow.md?WT.mc_id=docs-article-lazzeri)

## <a name="artificial-neural-networks"></a>人工類神經網路

人工類神經網路是由已連線節點的圖層所組成。 深度學習模型會使用具有大量圖層的神經網路。 

下列各節將探索最受歡迎的人工類神經網路 typologies。

### <a name="feedforward-neural-network"></a>Feedforward 類神經網路

Feedforward 類神經網路是最簡單的人工類神經網路類型。 在 feedforward 網路中，資訊只會以一個方向從輸入層移至輸出層。 Feedforward 類神經網路會藉由將輸入放入一系列隱藏層來轉換輸入。 每一層都是由一組神經所組成，而且每個圖層都會完全連接到圖層中的所有神經。 最後一個完全連接層 (輸出層) 表示產生的預測。

### <a name="recurrent-neural-network"></a>週期性神經網路

週期性神經網路是一種廣泛使用的人工類神經網路。 這些網路會儲存圖層的輸出，並將其饋送回輸入層，以協助預測圖層的結果。 週期性神經網路有絕佳的學習能力。 它們廣泛用於複雜的工作，例如時間序列預測、學習手寫和辨識語言。

### <a name="convolutional-neural-networks"></a>卷積類神經網路

卷積類神經網路是特別有效的人工類神經網路，並提供獨特的架構。 圖層是以三個維度組成：寬度、高度和深度。 某一層中的神經不會連接到下一層中的所有神經，而只會連線到圖層神經的小型區域。 最後的輸出會縮減為一種機率分數的向量，並沿著深度維度整理。 

卷積類神經網路已用於影片辨識、影像辨識和推薦系統等區域。

## <a name="next-steps"></a>後續步驟

下列文章說明在 [Azure Machine Learning](./index.yml?WT.mc_id=docs-article-lazzeri)中使用開放原始碼深度學習模型的更多選項：

- [使用 TensorFlow 估算器和 Keras 分類手寫數位](./how-to-train-keras.md?WT.mc_id=docs-article-lazzeri)

- [使用 Chainer 模型將手寫數位分類](./how-to-set-up-training-targets.md)