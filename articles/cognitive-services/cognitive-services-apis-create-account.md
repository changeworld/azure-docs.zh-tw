---
title: 在 Azure 門戶中創建認知服務資源
titleSuffix: Azure Cognitive Services
description: 通過在 Azure 門戶中創建和訂閱資源，開始使用 Azure 認知服務。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221235"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>使用 Azure 門戶創建認知服務資源

使用此快速入門開始使用 Azure 認知服務。 在 Azure 門戶中創建認知服務資源後，您將獲得用於驗證應用程式的終結點和金鑰。


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Prerequisites

* 有效的 Azure 訂閱 -[免費創建一個](https://azure.microsoft.com/free/)。

## <a name="create-a-new-azure-cognitive-services-resource"></a>創建新的 Azure 認知服務資源

1. 建立資源。

    #### <a name="multi-service-resource"></a>[多服務資源](#tab/multiservice)
    
    多服務資源在門戶中命名為**認知服務**。 [創建認知服務資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)。
    
    此時，多服務資源允許訪問以下認知服務：
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | 電腦視覺  | 內容仲裁                                    | 臉部               | 語言理解 (LUIS) | 文字分析   |
    | 翻譯文字  | 必應搜索 v7 <br>（網路， 圖片， 新聞， 視頻， 視覺） | Bing 自訂搜尋 | Bing 實體搜尋            | Bing 自動建議 |
    | Bing 拼字檢查 |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[單一服務資源](#tab/singleservice)

    使用以下連結為可用的認知服務創建資源：

    | 視覺                      | 語音                  | 語言                          | 決策             | 搜尋                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [電腦視覺](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [語音服務](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [沉浸式閱讀器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [異常偵測器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [必應搜索 API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [定制視覺服務](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [說話者辨識](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [內容檢閱者](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [必應自訂搜索](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [臉部](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA 製造商](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [個人化器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [必應實體搜索](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [筆跡辨識器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [文本分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Bing 拼字檢查](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [翻譯文本](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing 自動建議](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. 在 [建立]**** 頁面上，提供下列資訊：

    #### <a name="multi-service-resource"></a>[多服務資源](#tab/multiservice)

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 例如，*我的認知服務資源*。 |
    | **訂閱** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源組** | Azure 資源群組，將包含您的認知服務資源。 您可以建立新的群組，或將群組新增到既有的群組。 |

    ![資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    按一下 **[建立]**。

    #### <a name="single-service-resource"></a>[單一服務資源](#tab/singleservice)

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 例如，*文本分析資源*。 |
    | **訂閱** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源組** | Azure 資源群組，將包含您的認知服務資源。 您可以建立新的群組，或將群組新增到既有的群組。 |

    ![資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen.png)

    按一下 **[建立]**。

    ***


## <a name="get-the-keys-for-your-resource"></a>獲取資源金鑰

1. 成功部署資源後，按一下"**轉到"下一步**"下一步下的**資源**。

    ![搜尋認知服務](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. 從打開的快速入門窗格中，可以訪問金鑰和終結點。

    ![獲取金鑰和終結點](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源組還會刪除該組中包含的任何其他資源。

1. 在 Azure 入口網站中，展開左側功能表以開啟服務的功能表，然後選擇 [資源群組]**** 以顯示資源群組的清單。
2. 查找包含要刪除的資源的資源組
3. 按右鍵資源組清單。 選取 [刪除資源群組]**** 並且確認。

## <a name="see-also"></a>另請參閱

* [驗證 Azure 認知服務要求](authentication.md)
* [什麼是 Azure 認知服務？](Welcome.md)
* [自然語言支援](language-support.md)
* [Docker 容器支援](cognitive-services-container-support.md)
