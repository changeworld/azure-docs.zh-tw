---
title: 何謂 Language Understanding Intelligent Service (LUIS)？
description: Language Understanding (LUIS) - 雲端式 API 服務，使用機器學習來進行對話式自然語言以預測意義並擷取資訊。
keywords: Azure, 人工智慧, ai, 自然語言處理, nlp, 自然語言理解, nlu, ai 對話, 對話式 ai, ai 聊天機器人, 聊天機器人製作者, LUIS, nlp ai, luis ai, azure luis, 理解自然語言
ms.topic: overview
ms.date: 09/02/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: ff9aa2652fe8a1e503b5e2c93ca149112770b081
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400937"
---
# <a name="what-is-language-understanding-luis"></a>何謂 Language Understanding Intelligent Service (LUIS)？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding Intelligent Service (LUIS) 是一種 API 雲端式服務，可將自訂機器學習智慧套用至使用者的對話、自然語言文字中，以預測整體意義，並找出相關的詳細資訊。

LUIS 的用戶端應用程式是任何對話應用程式，可與使用者透過自然語言溝通以完成工作。 用戶端應用程式的例子包括社群媒體應用程式、聊天機器人，以及具備語音功能的桌面應用程式。

![3 個使用認知服務 Language Understanding (LUIS) 的用戶端應用程式概念影像](./media/luis-overview/luis-entry-point.png "3 個使用認知服務 Language Understanding (LUIS) 的用戶端應用程式概念影像")

## <a name="use-luis-in-a-chat-bot"></a>在聊天機器人中使用 LUIS

<a name="Accessing-LUIS"></a>

LUIS 應用程式發佈後，用戶端應用程式會將語句 (文字) 傳送至 LUIS 自然語言處理端點 [API][endpoint-apis]，並以 JSON 回應的形式接收結果。 聊天機器人是 LUIS 的常見用戶端應用程式之一。


![LUIS 使用聊天機器人來預測具有自然語言理解 (NLP) 的使用者文字概念影像](./media/luis-overview/LUIS-chat-bot-request-response.svg "LUIS 使用聊天機器人來預測具有自然語言理解 (NLP) 的使用者文字概念影像")

|步驟|動作|
|:--|:--|
|1|用戶端應用程式將使用者_語句_ (其個人風格的文字) "I want to call my HR rep." 傳送至 LUIS 端點作為 HTTP 要求。|
|2|LUIS 可讓您製作自訂語言模型，讓應用程式更加智慧化。 機器學習的語言模型會採用使用者的非結構化輸入文字，並傳回 JSON 格式的回應，其具有最高的意圖 `HRContact`。 JSON 最基本的端點回應包含查詢語句和評分最高的意圖。 它也可以擷取「連絡人類型」實體之類的資料。|
|3|用戶端應用程式使用 JSON 回應來決定如何達成使用者要求。 這些決策可包含 Bot Framework 程式碼中的決策樹和對其他服務的呼叫。 |

LUIS 應用程式會提供智慧，讓用戶端應用程式得以做出聰明的選擇。 LUIS 不會提供這些選擇。

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>自然語言理解 (NLU)

[LUIS 以 NLU 的形式提供人工智慧 (AI)](artificial-intelligence.md "LUIS 提供人工智慧 (AI)")，這是自然語言處理 (NLP) 的子集。

您的 LUIS 應用程式包含領域特定自然語言模型。 您可以從預先建立的領域模型開始使用 LUIS 應用程式、自行建置模型，或以自己的自訂資訊融合預先建立的領域片段。

* **預先建立的模型** LUIS 有許多預先建立的領域模型，包括意圖、語句和預先建立的實體。 您可以使用預先建立的實體，而不需要使用預先建立模型的意圖和語句。 [預先建立的領域模型](luis-how-to-use-prebuilt-domains.md "預建領域模型")包含您所需的整體設計，是快速著手使用 LUIS 的好方法。

* **自訂模型** LUIS 提供您幾種方式來識別您自己的自訂模型，包括意圖和實體。 實體包括機器學習實體、特定或常值實體，以及機器學習和常值的組合。

深入了解 [NLP](artificial-intelligence.md "NLP")，以及 NLU 的 LUIS 特定區域。

## <a name="step-1-design-and-build-your-model"></a>步驟 1:設計和建置您的模型

使用稱為 **[意圖](luis-concept-intent.md "意圖")** 的使用者意圖類別來設計您的模型。 每個意圖都需要使用者 **[語句](luis-concept-utterance.md "語句")** 的範例。 每個語句都可以提供需要使用[機器學習實體](luis-concept-entity-types.md#effective-machine-learned-entities "機器學習實體")來擷取的資料。

|範例使用者語句|Intent|擷取的資料|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|開啟|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|下午 1 點、Bob|

使用[撰寫](https://go.microsoft.com/fwlink/?linkid=2092087 "編寫") API 和 (或) 使用 **[LUIS 入口網站](https://www.luis.ai "LUIS 入口網站")** 建置模型。 深入了解如何使用[入口網站](get-started-portal-build-app.md "入口網站")和 [SDK 用戶端程式庫](azure-sdk-quickstart.md "SDK 用戶端程式庫")來建置。

## <a name="step-2-get-the-query-prediction"></a>步驟 2:取得查詢預測

在已定型應用程式的模型並發佈至端點後，用戶端應用程式 (例如聊天機器人) 會將語句傳送至預測[端點](https://go.microsoft.com/fwlink/?linkid=2092356 "端點") API。 API 會將模型套用至語句進行分析，並以 JSON 格式回應預測結果。

JSON 最基本的端點回應包含查詢語句和評分最高的意圖。 它也可以擷取下列**連絡人類型**實體和整體情感之類的資料。

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>步驟 3：改善模型預測

在 LUIS 應用程式發佈並接收實際使用者語句之後，LUIS 會提供端點語句的[主動式學習](luis-concept-review-endpoint-utterances.md "主動式學習")，以改善預測準確度。 請在您開發生命週期中的定期維護工作進行過程中檢閱這些建議。

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>開發生命週期和工具
LUIS 會提供工具、版本控制和與其他 LUIS 作者的共同作業，以便整合到完整的[開發生命週期](luis-concept-app-iteration.md "開發生命週期")。

Language Understanding (LUIS) 在作為 REST API 時，可以與任何具有 HTTP 要求的產品、服務或架構搭配使用。 LUIS 也提供數個熱門程式設計語言的用戶端程式庫 (SDK)。 深入了解所提供的[開發人員資源](developer-reference-resource.md "開發人員資源")。

可供快速且輕鬆地搭配使用 LUIS 與聊天機器人的工具：
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "LUIS CLI") NPM 套件可透過獨立命令列工具或匯入形式來提供撰寫和預測功能。
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen") LUISGen 工具可從匯出的 LUIS 模型中產生強型別的 C# 和 typescript 原始程式碼。
* [分派](https://aka.ms/dispatch-tool "分派")可讓您透過發送器模型從父應用程式使用數個 LUIS 和 QnA Maker 應用程式。
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown") LUDown 是一種命令列工具，可協助您管理聊天機器人的語言模型。

## <a name="integrate-with-a-bot"></a>與聊天機器人整合

使用 [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0 "Azure Bot 服務") 搭配 [Microsoft Bot Framework](https://dev.botframework.com/ "Microsoft Bot Framework") 來建置和部署聊天機器人。 使用圖形化介面工具 [Composer](https://docs.microsoft.com/composer/ "編輯器") 來進行設計和開發，或使用針對最常見聊天機器人案例所設計的[有效聊天機器人範例](https://github.com/microsoft/BotBuilder-Samples "Bot 使用範例")來進行。

## <a name="integrate-with-other-cognitive-services"></a>與其他認知服務整合

其他可與 LUIS 搭配使用的認知服務包括：
* [QnA Maker](../QnAMaker/overview/overview.md "QnA Maker") 可讓數種文字類型合併為問題和答案知識庫。
* [語音服務](../Speech-Service/overview.md "語音服務")可將口說語言要求轉換成文字。

LUIS 會提供文字分析中的功能作為現有 LUIS 資源的一部分。 這項功能包括[情感分析](luis-how-to-publish-app.md#configuring-publish-settings "情感分析")，以及使用預先建置的 keyPhrase 實體來進行的[關鍵片語擷取](luis-reference-prebuilt-keyphrase.md "關鍵片語擷取")。

## <a name="learn-with-the-quickstarts"></a>使用快速入門來了解

使用[入口網站](get-started-portal-build-app.md "入口網站")和 [SDK 用戶端程式庫](azure-sdk-quickstart.md "SDK 用戶端程式庫")進行實際操作快速入門來了解 LUIS。


## <a name="next-steps"></a>後續步驟

* 服務和文件的[最新動向](whats-new.md "新功能")
* 使用[意圖](luis-concept-intent.md "意圖")和[實體](luis-concept-entity-types.md "實體")來[規劃您的應用程式](luis-how-plan-your-app.md "規劃您的應用程式")。
* [查詢預測端點](luis-get-started-get-intent-from-browser.md "查詢預測端點")。
* LUIS 的[開發人員資源](developer-reference-resource.md "開發人員資源")。

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
