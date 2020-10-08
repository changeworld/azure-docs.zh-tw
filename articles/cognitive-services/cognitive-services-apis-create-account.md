---
title: 在 Azure 入口網站中建立認知服務資源
titleSuffix: Azure Cognitive Services
description: 藉由建立及訂閱 Azure 入口網站中的資源，開始使用 Azure 認知服務。
services: cognitive-services
author: aahill
manager: nitinme
keywords: 認知服務, 認知智慧, 認知解決方案, ai 服務
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: cad20f589bb5a6d3e73481081d9fe532381ab647
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827395"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立認知服務資源

使用本快速入門開始使用 Azure 認知服務。 在 Azure 入口網站中建立認知服務資源之後，您將會取得用來驗證應用程式的端點和金鑰。

Azure 認知服務是具有 REST Api 的雲端式服務，也提供用戶端程式庫 Sdk，可協助開發人員在不需要人工智慧 (AI) 或資料科學技能或知識的情況下，在應用程式中建立認知智慧。 Azure 認知服務可讓開發人員使用認知解決方案，輕鬆地將認知功能新增至其應用程式，以查看、聆聽、說出、了解，甚至是開始的原因。


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>必要條件

* 有效的 Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="create-a-new-azure-cognitive-services-resource"></a>建立新的 Azure 認知服務資源

1. 建立資源。

    #### <a name="multi-service-resource"></a>[多服務資源](#tab/multiservice)

    在入口網站中，多服務資源的名稱為「 **認知服務** 」。 [建立認知服務資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)。

    目前，多服務資源可讓您存取下列認知服務：

    - 電腦視覺
    - 內容仲裁
    - 臉部
    - 語言理解 (LUIS)
    - 文字分析
    - 轉譯程式
    - Bing 搜尋 v7 <br> (Web、影像、新聞、影片、Visual) 
    - Bing 自訂搜尋
    - Bing 實體搜尋
    - Bing 自動建議
    - Bing 拼字檢查

    #### <a name="single-service-resource"></a>[單一服務資源](#tab/singleservice)

    使用下列連結來建立可用認知服務的資源：

    | 視覺                      | 語音                  | 語言                          | 決策             | 搜尋                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [電腦視覺](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [語音服務](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [沉浸式讀者](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [異常偵測器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing 搜尋 API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [自訂視覺服務](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [說話者辨識](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [語言理解 (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [內容仲裁](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Bing 自訂搜尋](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [臉部](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [個人化工具](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing 實體搜尋](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [筆跡辨識器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [文字分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    | [Bing 拼字檢查](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [翻譯工具](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing 自動建議](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |

    ***

3. 在 [建立]**** 頁面上，提供下列資訊：

    #### <a name="multi-service-resource"></a>[多服務資源](#tab/multiservice)

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 例如， *MyCognitiveServicesResource*。 |
    | **訂用帳戶** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源群組** | Azure 資源群組，將包含您的認知服務資源。 您可以建立新的群組，或將群組新增到既有的群組。 |

    ![多服務資源資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    按一下 [建立]。

    #### <a name="single-service-resource"></a>[單一服務資源](#tab/singleservice)

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 例如， *TextAnalyticsResource*。 |
    | **訂用帳戶** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源群組** | Azure 資源群組，將包含您的認知服務資源。 您可以建立新的群組，或將群組新增到既有的群組。 |

    ![單一服務資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen.png)

    按一下 [建立]。

    ***

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>取得資源的金鑰

1. 成功部署您的資源之後，請按一下 **[前往下一個步驟]** 下的 [**移至資源**]。

    ![搜尋認知服務](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. 從開啟的 [快速入門] 窗格中，您可以存取您的金鑰和端點。

    ![取得金鑰和端點](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除與群組中的任何其他資源。

1. 在 Azure 入口網站中，展開左側功能表以開啟服務的功能表，然後選擇 [資源群組] 以顯示資源群組的清單。
2. 找出要刪除的資源位於哪一個資源群組
3. 以滑鼠右鍵按一下資源群組清單。 選取 [刪除資源群組] 並且確認。

## <a name="see-also"></a>請參閱

* [驗證 Azure 認知服務要求](authentication.md)
* [什麼是 Azure 認知服務？](Welcome.md)
* [使用 Azure 管理用戶端程式庫建立新的資源](.\cognitive-services-apis-create-account-client-library.md)
* [自然語言支援](language-support.md)
* [Docker 容器支援](cognitive-services-container-support.md)
