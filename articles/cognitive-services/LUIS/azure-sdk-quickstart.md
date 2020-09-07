---
title: 快速入門：Language Understanding (LUIS) SDK 用戶端程式庫
description: 搭配此快速入門使用 C#、Python 或 JavaScript，建立並查詢具有 LUIS SDK 用戶端程式庫的 LUIS 應用程式。
ms.topic: quickstart
ms.date: 09/01/2020
keywords: Azure, 人工智慧, ai, 自然語言處理, nlp, 自然語言理解, nlu, ai 對話, 對話式 ai, ai 聊天機器人, 聊天機器人製作者, LUIS, nlp ai, luis ai, azure luis, 理解自然語言
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: 6bcdca85125d44475fadfd195c1dfda88f761f88
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323064"
---
# <a name="quickstart-language-understanding-luis-sdk-client-libraries-to-create-and-query-your-luis-app"></a>快速入門：用於建立及查詢您 LUIS 應用程式的 Language Understanding (LUIS) SDK 用戶端程式庫

搭配此快速入門使用 C#、Python 或 JavaScript，建立並查詢具有 LUIS SDK 用戶端程式庫的 LUIS 應用程式。

Language Understanding (LUIS) 可讓您將自訂機器學習智慧套用至使用者的對話、自然語言文字中，以預測整體意義，並找出相關的詳細資訊。

* **撰寫 SDK** 用戶端程式庫可讓您建立、編輯、定型及發佈 LUIS 應用程式。 * **預測執行階段 SDK** 用戶端程式庫可讓您查詢已發佈的應用程式。

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>清除資源

您可以從 [LUIS 入口網站](https://www.luis.ai)刪除應用程式，並從 [Azure 入口網站](https://portal.azure.com/)刪除 Azure 資源。

## <a name="troubleshooting"></a>疑難排解

* 向用戶端程式庫進行驗證 - 驗證錯誤通常表示使用了錯誤的金鑰及端點。 為方便起見，本快速入門會針對預測執行階段使用撰寫金鑰和端點，但只有在您尚未使用每月配額時才適用。 如果您無法使用撰寫金鑰和端點，則必須在存取預測執行階段 SDK 用戶端程式庫時使用預測執行階段金鑰和端點。
* 建立實體 - 如果您在建立本教學課程中使用的巢狀機器學習實體時發生錯誤，請確定您已複製程式碼，但未改變程式碼來建立不同的實體。
* 建立範例語句 - 如果您在建立本教學課程中使用的已標記範例語句時發生錯誤，請確定您已複製程式碼，但未改變程式碼來建立不同的已標記範例。
* 定型 - 如果您收到定型錯誤，這通常表示應用程式是空的 (沒有包含範例語句的意圖)，或應用程式的意圖或實體格式不正確。
* 雜項錯誤 - 由於程式碼會以文字和 JSON 物件呼叫用戶端程式庫，因此請確定您沒有變更程式碼。

其他錯誤 - 如果您收到上述清單中未涵蓋的錯誤，請在此頁面底部提供意見反應來讓我們知道。 包含您所安裝用戶端程式庫的程式設計語言和版本。 

## <a name="next-steps"></a>後續步驟

* [什麼是 Language Understanding (LUIS) API？](what-is-luis.md)
* [新功能](whats-new.md)
* [意圖](luis-concept-intent.md)、[實體](luis-concept-entity-types.md)、[範例語句](luis-concept-utterance.md)和[預建實體](luis-reference-prebuilt-entities.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code) 上找到。
