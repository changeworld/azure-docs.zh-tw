---
title: 發佈應用程式-LUIS
titleSuffix: Azure Cognitive Services
description: 當您完成作用中 LUIS 應用程式的建置和測試時，將它發佈至端點以供用戶端應用程式使用。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: c91a3ca73d70dd5fd2848bed0f43f14a817087d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80053443"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>將作用中且經過訓練的應用程式發佈至預備或生產端點

當您完成建立、定型和測試使用中的 LUIS 應用程式時，請將它發佈至端點，讓用戶端應用程式可以使用它。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>發佈

1. 若要發佈至端點，請選取右側面板頂端的 [發佈]****。 

    ![右上流覽列中的 [發佈] 按鈕](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. 選取已發佈預測端點的設定，然後選取 [**發佈**]。

    ![選取 [發行設定]，然後選取 [發佈] 按鈕](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>發行位置

在快顯視窗顯示時選取正確的位置： 

* 執行
* Production 

藉由使用這兩個發佈位置，可讓您在已發行的端點或相同版本的兩個不同端點上，使用應用程式的兩個不同版本。 

### <a name="publishing-regions"></a>發行區域

應用程式會發佈至與 LUIS 入口網站中新增的 LUIS 預測端點資源相關聯的所有區域，從 [**管理** -> **[Azure 資源](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)**] 頁面。 

例如，針對在[www.luis.ai](https://www.luis.ai)上建立的應用程式，如果您在兩個區域（ **westus**和**eastus**）中建立 luis 資源，並將其新增至應用程式做為資源，則會在這兩個區域中發佈應用程式。 如需 LUIS 區域的相關資訊，請參閱[區域](luis-reference-regions.md)。

> [!TIP]
> 有3個撰寫區域。 您必須在想要發佈的區域中撰寫。 如果您需要發行至所有區域，您必須在所有3個撰寫區域中管理您的撰寫流程和產生的定型模型。 


## <a name="configuring-publish-settings"></a>配置發佈設定

選取位置之後，請設定下列各內容的發佈設定：

* 情感分析
* 拼寫更正-僅限 v2 預測端點
* 語音預備 

在您發佈之後，這些設定可從 [**管理**] 區段的 [**發行設定**] 頁面中進行審核。 您可以在每次發行時變更設定。 如果您取消發行，則在發行期間所做的任何變更也會一併取消。 

### <a name="when-your-app-is-published"></a>當您的應用程式發佈時

當您的應用程式成功發佈時，瀏覽器頂端會出現成功通知。 通知也包含端點的連結。 

如果您需要端點 URL，請選取此連結。 您也可以在頂端功能表中選取 [**管理**]，然後選取左側功能表中的 [ **Azure 資源**]，以取得端點 url。 

## <a name="sentiment-analysis"></a>情感分析

<a name="enable-sentiment-analysis"></a>

情感分析可讓 LUIS 與[文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)整合，以提供情感和關鍵片語分析。 

您不需要提供文字分析金鑰，而且不會向您的 Azure 帳戶收取此服務的費用。 

情感資料是一個介於 1 與 0 之間的分數，指出資料的正面 (較接近 1) 或負面 (較接近 0) 情感。 `positive`、`neutral` 及 `negative` 情感標籤是依支援的文化特性 (Culture) 而定。 目前，只有英文支援情感標籤。 

如需使用情感分析之 JSON 端點回應的詳細資訊，請參閱[情感分析](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>拼字校正

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

更正拼寫是在 LUIS 使用者語句預測之前進行。 在回應中，您可以看到原始語句的任何變更（包括拼寫）。

## <a name="speech-priming"></a>語音預備

語音預備是在將文字轉換成語音之前，使用將 LUIS 模型傳送至語音服務的程式。 這可讓語音服務更精確地為您的模型提供語音轉換。 這可讓 bot 語音和 LUIS 要求和回應呼叫一次，方法是進行一次語音通話並取回 LUIS 回應。 它提供的整體延遲較低。

## <a name="next-steps"></a>後續步驟

* 請參閱[管理金鑰](./luis-how-to-azure-subscription.md)，將金鑰新增至 LUIS 的 Azure 訂用帳戶金鑰，以及了解如何設定 Bing 拼字檢查金鑰並且在結果中包含所有意圖。
* 如需如何在測試主控台中測試已發佈應用程式的指示，請參閱[訓練和測試應用程式](luis-interactive-test.md)。

