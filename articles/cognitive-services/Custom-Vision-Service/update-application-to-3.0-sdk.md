---
title: 如何將您的專案更新為 3.0 API
titleSuffix: Azure Cognitive Services
description: 瞭解如何將舊版 API 的自訂視覺專案更新為 3.0 API。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73647509"
---
# <a name="update-to-the-30-api"></a>更新至 3.0 API

自訂視覺現在已正式運作，並已經歷了 API 更新。
此更新包含幾個新功能，重要的是一些重大變更：

* 根據專案類型，預測 API 現在會分割成兩個。
* 視覺 AI 開發人員套件（VAIDK）匯出選項需要以特定方式建立專案。
* 已移除預設反復專案，以改用已命名的反復專案發行/解除發佈。

本指南將說明如何更新您的專案，以使用新的 API 版本。 如需完整的變更清單，請參閱[版本](release-notes.md)資訊。

## <a name="use-the-updated-prediction-api"></a>使用更新的預測 API

2.x Api 針對影像分類器和物件偵測器專案使用相同的預測呼叫。 **PredictImage**和**PredictImageUrl**呼叫都可以接受這兩種專案類型。 從3.0 開始，我們已分割此 API，因此您必須將專案類型與呼叫進行比對：

* 使用**[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** 和**[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** 來取得影像分類專案的預測。
* 使用**[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** 和**[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** 取得物件偵測專案的預測。

## <a name="use-the-new-iteration-publishing-workflow"></a>使用新的反復專案發行工作流程

2.x Api 使用預設反復專案或指定的反復專案 ID 來選擇要用於預測的反復專案。 從3.0 開始，我們採用了發佈流程，讓您先從訓練 API 以指定名稱來發佈反復專案。 然後將名稱傳遞至預測方法，以指定要使用的反復專案。

> [!IMPORTANT]
> 3.0 Api 不會使用預設的反復專案功能。 在我們取代較舊的 Api 之前，您可以繼續使用 2.x Api 將反復專案切換為預設值。 這些 Api 將會保留一段時間，您可以呼叫**[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** 方法，將反復專案標示為預設值。

### <a name="publish-an-iteration"></a>發行反復專案

一旦反復專案定型之後，您就可以使用**[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** 方法，讓它可用於預測。 若要發行反復專案，您需要有可在 CustomVision 網站的 [設定] 頁面上取得的預測資源識別碼。

![自訂視覺網站設定] 頁面，其中概述了預測資源識別碼。](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> 您也可以前往 [自訂視覺預測資源]，然後選取 [**屬性**]，從[Azure 入口網站](https://portal.azure.com)取得這項資訊。

一旦您的反復專案發佈之後，應用程式就可以在其預測 API 呼叫中指定名稱，以用於預測。 若要讓反復專案無法用於預測呼叫，請使用**[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API。

## <a name="next-steps"></a>後續步驟

* [訓練 API 參考檔（REST）](https://go.microsoft.com/fwlink/?linkid=865446)
* [預測 API 參考檔（REST）](https://go.microsoft.com/fwlink/?linkid=865445)