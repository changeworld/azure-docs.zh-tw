---
title: 發佈應用程式-LUIS
titleSuffix: Azure Cognitive Services
description: 當您完成作用中 LUIS 應用程式的建置和測試時，將它發佈至端點以供用戶端應用程式使用。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: b72f1fd64cca0fa77ebc486670a512c5228e1146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541470"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>將作用中且經過訓練的應用程式發佈至預備或生產端點

當您完成建立、定型及測試使用中的 LUIS 應用程式時，請將其發佈至端點，使其可供用戶端應用程式使用。

## <a name="publishing"></a>發佈
1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 若要發佈至端點，請選取右側面板頂端的 [發佈]****。

    ![右上方導覽列中的 [發佈] 按鈕](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. 選取已發佈預測端點的設定，然後選取 [ **發行**]。

    ![選取 [發佈設定]，然後選取 [發佈] 按鈕](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>發佈位置

當快顯視窗顯示時，請選取正確的位置：

* 預備
* 生產

藉由使用兩個發行位置，這可讓您在已發佈的端點或相同版本的兩個不同端點上有兩個不同版本的應用程式可供使用。

### <a name="publishing-regions"></a>發行區域

應用程式會從 [**管理**  ->  **[Azure 資源](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)**] 頁面上，發佈至與 LUIS 入口網站中新增的 LUIS 預測端點資源相關聯的所有區域。

例如，針對在 [www.luis.ai](https://www.luis.ai)上建立的應用程式，如果您在兩個區域中建立 luis 資源（ **westus** 和 **eastus**），並將這些資源新增至應用程式作為資源，則會在這兩個區域中發佈應用程式。 如需 LUIS 區域的相關資訊，請參閱[區域](luis-reference-regions.md)。

> [!TIP]
> 有3個撰寫區域。 您必須在您想要發佈的區域中進行撰寫。 如果您需要發佈到所有區域，您需要在所有3個撰寫區域中管理撰寫程式和產生的定型模型。


## <a name="configuring-publish-settings"></a>配置發佈設定

選取位置之後，請設定的發佈設定：

* 情感分析
* [拼寫更正](luis-tutorial-bing-spellcheck.md) -僅限 v2 預測端點
* 語音預備

發佈之後，您可以從 [ **管理** ] 區段的 [ **發佈設定** ] 頁面查看這些設定。 您可以在每次發行時變更設定。 如果您取消發行，在發佈期間所做的任何變更也會一併取消。

### <a name="when-your-app-is-published"></a>當您的應用程式發佈時

當您的應用程式成功發佈時，瀏覽器頂端會出現成功通知。 通知也包含端點的連結。

如果您需要端點 URL，請選取此連結。 您也可以選取頂端功能表中的 [ **管理** ]，然後選取左側功能表中的 [ **Azure 資源** ]，以移至端點 url。

## <a name="sentiment-analysis"></a>情感分析

<a name="enable-sentiment-analysis"></a>

情感分析可讓 LUIS 與[文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)整合，以提供情感和關鍵片語分析。

您不需要提供文字分析金鑰，而且不會向您的 Azure 帳戶收取此服務的費用。

情感資料是一個介於 1 與 0 之間的分數，指出資料的正面 (較接近 1) 或負面 (較接近 0) 情感。 `positive`、`neutral` 及 `negative` 情感標籤是依支援的文化特性 (Culture) 而定。 目前，只有英文支援情感標籤。

如需使用情感分析之 JSON 端點回應的詳細資訊，請參閱[情感分析](luis-reference-prebuilt-sentiment.md)

## <a name="spelling-correction"></a>拼字校正

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

更正拼寫是在 LUIS 使用者語句預測之前進行。 您可以在回應中看到原始語句的任何變更，包括拼寫。

## <a name="speech-priming"></a>語音預備

語音預備是在將文字轉換成語音之前，使用將 LUIS 模型傳送至語音服務的程式。 這可讓語音服務為您的模型提供更精確的語音轉換。 如此一來，就可以進行一次語音通話並取回 LUIS 回應，藉以在單一呼叫中 LUIS bot 語音和回應。 它可降低整體延遲。

## <a name="next-steps"></a>後續步驟

* 請參閱[管理金鑰](./luis-how-to-azure-subscription.md)，將金鑰新增至 LUIS 的 Azure 訂用帳戶金鑰，以及了解如何設定 Bing 拼字檢查金鑰並且在結果中包含所有意圖。
* 如需如何在測試主控台中測試已發佈應用程式的指示，請參閱[訓練和測試應用程式](luis-interactive-test.md)。

