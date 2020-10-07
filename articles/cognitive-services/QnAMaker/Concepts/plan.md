---
title: 規劃您的應用程式-QnA Maker
description: 瞭解如何規劃您的 QnA Maker 應用程式。 瞭解 QnA Maker 如何運作，並與其他 Azure 服務和一些知識庫概念互動。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: 84e4d6907c9036503f43cd607b54577fd3d97444
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776930"
---
# <a name="plan-your-qna-maker-app"></a>規劃您的 QnA Maker 應用程式

若要規劃您的 QnA Maker 應用程式，您需要瞭解 QnA Maker 的運作方式，並與其他 Azure 服務互動。 您也應該瞭解知識庫的概念。

## <a name="azure-resources"></a>Azure 資源

使用 QnA Maker 建立的每個 [Azure 資源](azure-resources.md#resource-purposes) 都有特定用途。 每個資源都有自己的用途、限制和 [定價層](azure-resources.md#pricing-tier-considerations)。 請務必瞭解這些資源的功能，讓您可以在規劃程式中使用該知識。

| 資源 | 目的 |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource) 資源 | 撰寫和查詢預測 |
| [認知搜尋](azure-resources.md#cognitive-search-resource) 資源 | 資料儲存和搜尋 |
| [App Service 資源和 App 方案服務](azure-resources.md#app-service-and-app-service-plan) 資源 | 查詢預測端點 |
| [Application Insights](azure-resources.md#application-insights) 資源 | 查詢預測遙測 |

### <a name="resource-planning"></a>資源規劃

每個資源的免費層都 `F0` 可以運作，並可同時提供撰寫和查詢預測體驗。 您可以使用這一層來學習撰寫和查詢預測。 當您移至生產或即時案例時，請重新評估您的資源選取專案。

#### <a name="qna-maker-resource"></a>QnA Maker 資源

單一 QnA Maker 資源可以裝載一個以上的知識庫。 知識庫的數目是由認知搜尋定價層的支援索引數量所決定。 深入瞭解 [索引與知識庫的關聯](azure-resources.md#index-usage)性。

#### <a name="knowledge-base-size-and-throughput"></a>知識庫大小和輸送量

當您建立實際的應用程式時，請針對知識庫的大小和預期的查詢預測要求，規劃足夠的資源。

知識庫大小是由下列各項所控制：
* [認知搜尋資源](../../../search/search-limits-quotas-capacity.md) 定價層限制
* [QnA Maker 限制](../limits.md)

知識庫查詢預測要求是由 web 應用程式方案和 web 應用程式所控制。 請參閱 [建議的設定](azure-resources.md#recommended-settings) 來規劃您的定價層。

### <a name="resource-sharing"></a>資源共用

如果您已經使用這些資源，您可以考慮共用資源。 查看 [可共用](azure-resources.md#share-services-with-qna-maker) 哪些資源，以瞭解資源分享是 advanced 案例。

在相同 QnA Maker 資源中建立的所有知識庫都會共用相同的 **測試** 查詢預測端點。

### <a name="understand-the-impact-of-resource-selection"></a>瞭解資源選擇的影響

適當的資源選擇表示您的知識庫已成功回應查詢預測。

如果您的知識庫無法正常運作，則通常是不當的資源管理問題。

不當的資源選取需要調查，以判斷 [需要變更的資源](azure-resources.md#when-to-change-a-pricing-tier)。

## <a name="knowledge-bases"></a>知識庫

知識庫會將其 QnA Maker 資源直接系結。 它會保留問題和解答 (用來回答查詢預測要求的 QnA) 組。

### <a name="language-considerations"></a>語言考量因素

在 QnA Maker 資源上建立的第一個知識庫會設定資源的語言。 QnA Maker 資源只能有一種語言。

您可以依語言來結構 QnA Maker 資源，也可以使用 [翻譯工具](../../translator/translator-info-overview.md) 將查詢從另一種語言變更為知識庫的語言，再將查詢傳送至查詢預測端點。

### <a name="ingest-data-sources"></a>內嵌資料來源

您可以使用下列其中一種內嵌 [資料來源](knowledge-base.md) 來建立知識庫：

* 公用 URL
* 私用 SharePoint URL
* 檔案

內嵌進程會將 [支援的內容類型](content-types.md) 轉換為 markdown。 所有進一步的 *答案* 編輯都是使用 markdown 完成。 建立知識庫之後，您可以在 QnA Maker 入口網站中編輯 [QnA](question-answer-set.md) 組，並提供 [豐富的文字撰寫](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)。

### <a name="data-format-considerations"></a>資料格式考慮

因為 QnA 組的最終格式是 markdown，所以請務必瞭解 [markdown 支援](../reference-markdown-format.md)。

連結的影像必須可從公用 URL 取得，才能在 QnA Maker 入口網站或用戶端應用程式的 [測試] 窗格中顯示。 QnA Maker 不會提供內容的驗證，包括影像。

### <a name="bot-personality"></a>Bot 個人化

使用 [閒聊聊天](../how-to/chit-chat-knowledge-base.md)將 bot 個人化新增至您的知識庫。 這項特性是透過特定對話語氣（例如 *professional* 和 *易記*）提供的解答。 這項閒聊的交談是以對話設定的方式提供，您可以在其中加入、編輯和移除所有的控制權。

如果您的 bot 連接到您的知識庫，建議使用 bot 特性。 即使您也連接到其他服務，您也可以選擇在您的知識庫中使用閒聊聊天，但您應該檢查 bot 服務如何互動，以瞭解這是否為您使用的正確架構設計。

### <a name="conversation-flow-with-a-knowledge-base"></a>具有知識庫的對話流程

對話流程的開頭通常是使用者的稱呼，例如 `Hi` 或 `Hello` 。 您的知識庫可以回答一般答案（例如 `Hi, how can I help you` ），也可以提供後續追蹤提示以繼續交談。

您應考慮使用迴圈來設計對話流程，讓使用者知道如何使用您的 bot，而不會由對話中的 bot 放棄。 [後續提示會](../how-to/multiturn-conversation.md) 提供 QnA 組之間的連結，以允許對話流程。

### <a name="authoring-with-collaborators"></a>使用共同作業者撰寫

共同作業者可能是共用知識庫應用程式之完整開發堆疊的其他開發人員，或可能僅限於撰寫知識庫。

知識庫撰寫支援您在 Azure 入口網站中套用的數個 [角色型存取權限](../how-to/collaborate-knowledge-base.md) ，以限制共同作業者的能力。

## <a name="integration-with-client-applications"></a>與用戶端應用程式整合

[用戶端應用程式](integration-with-other-applications.md)的整合是透過將查詢傳送至預測執行時間端點來完成。 查詢會使用 SDK 或以 REST 為基礎的要求傳送至您 QnA Maker 的 web 應用程式端點，以傳送至您的特定知識庫。

若要正確驗證用戶端要求，用戶端應用程式必須傳送正確的認證和知識庫識別碼。 如果您使用 Azure Bot Service，請在 Azure 入口網站中，將這些設定設定為 Bot 設定的一部分。

### <a name="conversation-flow-in-a-client-application"></a>用戶端應用程式中的對話流程

[用戶端應用程式](integration-with-other-applications.md)（例如 Azure bot）中的對話流程，可能需要與知識庫互動之前和之後的功能。

您的用戶端應用程式是否支援交談流程，方法是提供替代的方法來處理後續提示或包含閒聊閒聊？ 若是如此，請及早設計這些服務，並確定用戶端應用程式查詢是由另一項服務或傳送至您的知識庫時正確處理。

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>在 QnA Maker 和 Language Understanding (LUIS 之間分派) 

用戶端應用程式可能會提供數個功能，但只有其中一個是由知識庫回答。 其他功能仍然需要瞭解交談文字，並從它中取出意義。

常見的用戶端應用程式架構是同時使用 QnA Maker 和 [Language Understanding (LUIS) ](../../LUIS/what-is-luis.md) 。 LUIS 會針對任何查詢（包括其他服務）提供文字分類和解壓縮。 QnA Maker 從您的知識庫提供解答。

在這種 [共用架構](../choose-natural-language-processing-service.md) 案例中，兩個服務之間的分派是由 Bot Framework 的 [分派](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) 工具來完成。

### <a name="active-learning-from-a-client-application"></a>來自用戶端應用程式的主動式學習

QnA Maker 使用 _主動式學習_ 來改善您的知識庫，方法是向答案建議替代問題。 用戶端應用程式負責此 [主動式學習](active-learning-suggestions.md)的一部分。 透過對話式提示，用戶端應用程式可以判斷知識庫是否傳回對使用者而言不實用的答案，而且可以判斷更好的答案。 用戶端應用程式必須將 [該資訊傳回知識庫](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) ，以改善預測品質。

### <a name="providing-a-default-answer"></a>提供預設答案

如果您的知識庫找不到答案，則會傳回 _預設答案_。 此答案可在 QnA Maker 入口網站或[api](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body)中的 [設定] 頁面上**設定**。

此預設答案不同于 Azure bot 的預設答案。 您可以在 Azure 入口網站中設定 Azure bot 的預設答案作為設定的一部分。 當分數閾值不符合時，就會傳回此值。

## <a name="prediction"></a>預測

預測是來自您知識庫的回應，其中包含的資訊不只是答案。 若要取得查詢預測回應，請使用 [GENERATEANSWER API](query-knowledge-base.md)。

### <a name="prediction-score-fluctuations"></a>預測分數波動

分數可能會根據數個因素而變更：

* 使用屬性回應[GenerateAnswer](query-knowledge-base.md)所要求的答案數目 `top`
* 各種可用的替代問題
* 篩選中繼資料
* 傳送至 `test` 或 `production` 知識庫的查詢

有 [兩個階段的答案排名](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)：
- 認知搜尋優先排名。 將 _回應_ 的數目設得夠高，認知搜尋會傳回最佳的答案，然後傳遞至 QnA Maker ranker。
- QnA Maker 秒排名。 套用特徵化和機器學習服務，以判斷最佳答案。

### <a name="service-updates"></a>服務更新

套用 [最新的執行時間更新](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) ，以自動管理服務更新。

### <a name="scaling-throughput-and-resiliency"></a>調整規模、輸送量和復原能力

調整規模、輸送量和復原能力取決於 [Azure 資源](../how-to/set-up-qnamaker-service-azure.md)、其定價層和任何周圍的架構，例如 [流量管理員](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager)。

### <a name="analytics-with-application-insights"></a>使用 Application Insights 分析

您知識庫的所有查詢都會儲存在 Application Insights 中。 使用我們的 [最佳查詢](../how-to/get-analytics-knowledge-base.md) 來瞭解您的計量。

## <a name="development-lifecycle"></a>開發生命週期

知識庫的 [開發生命週期](development-lifecycle-knowledge-base.md) 正在進行中：編輯、測試和發佈您的知識庫。

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker 配對的知識庫開發

您應根據用戶端應用程式的使用方式，來設計和開發您的 [QnA 配對](question-answer-set.md) 。

每一組可以包含：
* 在查詢時可篩選中繼資料可篩選，可讓您使用資料的來源、內容、格式和用途的其他相關資訊來標記 QnA 組。
* 後續提示-可協助您判斷整個知識庫的路徑，讓使用者抵達正確的答案。
* 替代問題-務必讓搜尋符合您的答案，而不是來自不同表單的問題。 [主動式學習建議](active-learning-suggestions.md) 會變成替代問題。

### <a name="devops-development"></a>DevOps 開發

開發要插入至 DevOps 管線的知識庫時，必須在 [批次測試](../quickstarts/batch-testing.md)期間隔離知識庫。

知識庫會與 QnA Maker 資源上的所有其他知識庫共用認知搜尋索引。 雖然知識庫是依資料分割隔離，但相較于已發佈的知識庫，共用索引可能會導致分數的差異。

若要在和知識庫上擁有 _相同的分數_ `test` `production` ，請將 QnA Maker 資源隔離到單一知識庫。 在此架構中，只要隔離的批次測試，資源就只需要存留。

## <a name="next-steps"></a>後續步驟

* [Azure 資源](../how-to/set-up-qnamaker-service-azure.md)
* [問與答組](question-answer-set.md)