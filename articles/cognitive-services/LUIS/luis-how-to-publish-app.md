---
title: 發佈應用 - LUIS
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053443"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>將作用中且經過訓練的應用程式發佈至預備或生產端點

完成活動 LUIS 應用的構建、培訓和測試後，通過將應用發佈到終結點，使其可供用戶端應用程式使用。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>發佈

1. 若要發佈至端點，請選取右側面板頂端的 [發佈]****。 

    ![在頂部、右側巡覽列中發佈按鈕](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. 選擇已發佈預測終結點的設置，然後選擇 **"發佈**"。

    ![選擇發佈設置，然後選擇"發佈"按鈕](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>發佈槽

快顯視窗顯示時選擇正確的插槽： 

* 預備
* Production 

通過使用這兩個發佈槽，這允許您在已發佈的終結點上提供兩個不同版本的應用，或者在兩個不同的終結點上提供同一版本。 

### <a name="publishing-regions"></a>發行區域

應用將發佈到與從 **"管理** -> **[Azure 資源](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)**"頁添加到 LUIS 門戶中的 LUIS 預測終結點資源關聯的所有區域。 

例如，對於在[www.luis.ai](https://www.luis.ai)上創建的應用，如果在兩個區域（Westus 和**Eastus）** 中創建 LUIS 資源，並將這些資源作為資源添加到應用，則應用將同時發佈在這兩個區域。 **eastus** 如需 LUIS 區域的相關資訊，請參閱[區域](luis-reference-regions.md)。

> [!TIP]
> 有 3 個創作區域。 您必須在要發佈到的區域創作。 如果需要發佈到所有區域，則需要管理所有 3 個創作區域中的創作過程和生成的訓練模型。 


## <a name="configuring-publish-settings"></a>配置發佈設定

選擇插槽後，為以下設置配置發佈設置：

* 情感分析
* 拼寫校正 - 僅 v2 預測終結點
* 語音預備 

發佈後，這些設置可從 **"管理**"部分的 **"發佈設置"** 頁查看。 您可以更改每次發佈中的設置。 如果取消發佈，則在發佈期間所做的任何更改也會被取消。 

### <a name="when-your-app-is-published"></a>發佈應用時

成功發佈應用後，瀏覽器頂部將顯示一條成功通知。 通知還包括指向終結點的連結。 

如果您需要端點 URL，請選取此連結。 您還可以通過在頂部功能表中選擇 **"管理**"，然後在左側功能表中選擇**Azure 資源**來獲取終結點 URL。 

## <a name="sentiment-analysis"></a>情感分析

<a name="enable-sentiment-analysis"></a>

情感分析可讓 LUIS 與[文字分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)整合，以提供情感和關鍵片語分析。 

您不需要提供文字分析金鑰，而且不會向您的 Azure 帳戶收取此服務的費用。 

情感資料是一個介於 1 與 0 之間的分數，指出資料的正面 (較接近 1) 或負面 (較接近 0) 情感。 `positive`、`neutral` 及 `negative` 情感標籤是依支援的文化特性 (Culture) 而定。 目前，只有英文支援情感標籤。 

如需使用情感分析之 JSON 端點回應的詳細資訊，請參閱[情感分析](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>拼字校正

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

在 LUIS 使用者陳述預測之前對拼寫進行了更正。 您可以在回應中看到對原始陳述（包括拼寫）的任何更改。

## <a name="speech-priming"></a>語音預備

語音準備是在將文本轉換為語音之前將 LUIS 模型發送到語音服務的過程。 這允許語音服務為您的模型更準確地提供語音轉換。 這允許機器人語音和 LUIS 請求和回應在一次調用通過進行一個語音調用和返回 LUIS 回應。 它總體提供更少的延遲。

## <a name="next-steps"></a>後續步驟

* 請參閱[管理金鑰](./luis-how-to-azure-subscription.md)，將金鑰新增至 LUIS 的 Azure 訂用帳戶金鑰，以及了解如何設定 Bing 拼字檢查金鑰並且在結果中包含所有意圖。
* 如需如何在測試主控台中測試已發佈應用程式的指示，請參閱[訓練和測試應用程式](luis-interactive-test.md)。

