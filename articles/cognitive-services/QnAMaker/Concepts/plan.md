---
title: 規劃您的應用程式-QnA Maker
description: 規劃您的 QnA Maker 應用程式需要瞭解 QnA Maker 的運作方式，並與其他 Azure 服務互動，以及一些知識庫概念。
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875703"
---
# <a name="plan-your-qna-maker-app"></a>規劃您的 QnA Maker 應用程式

規劃您的 QnA Maker 應用程式需要瞭解 QnA Maker 的運作方式，並與其他 Azure 服務互動，以及一些知識庫概念。

## <a name="azure-resources"></a>Azure 資源

使用 QnA Maker 建立的每個[Azure 資源](azure-resources.md#resource-purposes)都有特定的用途。 因為每個資源都有自己的用途、限制和[定價層](azure-resources.md#pricing-tier-considerations)，所以請務必瞭解這些資源在您規劃過程中的作用。

|資源|目的|
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource)資源|撰寫和查詢預測|
| [認知搜尋](azure-resources.md#cognitive-search-resource)資源|資料儲存和搜尋|
| [App Service 資源和應用程式方案服務](azure-resources.md#app-service-and-app-service-plan)資源|查詢預測端點|
| [Application Insights](azure-resources.md#application-insights)資源|查詢預測遙測|

### <a name="resource-planning"></a>資源規劃

當您使用免費層來學習撰寫和查詢預測時， `F0` 每個資源都可運作，並提供撰寫和查詢預測體驗。 當您移至生產環境、即時案例時，請重新評估您的資源選取範圍。

#### <a name="qna-maker-resource"></a>QnA Maker 資源

單一 QnA Maker 資源可以裝載一個以上的知識庫。 知識庫的數目取決於認知搜尋定價層的支援索引數量。 深入瞭解[索引與知識庫之間的關聯](azure-resources.md#index-usage)性。

#### <a name="knowledge-base-size-and-throughput"></a>知識庫大小和輸送量

當您打算建立實際的應用程式時，請規劃您的知識庫大小的資源，以及您預期的查詢預測要求。

知識庫大小由控制：
* [認知搜尋資源](../../../search/search-limits-quotas-capacity.md)定價層限制
* [QnA Maker 限制](../limits.md)

知識庫查詢預測要求是由 Web 應用程式方案和 web 應用程式所控制。 請參閱[建議的設定](azure-resources.md#recommended-settings)，以規劃您的定價層。

### <a name="resource-sharing"></a>資源分享

如果您已經在使用這些資源，您可以考慮共用資源。 雖然有些資源[可以共用](azure-resources.md#share-services-with-qna-maker)，但這是一個先進的案例。

在相同 QnA Maker 資源中建立的所有知識庫會共用相同的**測試**查詢預測端點。

### <a name="understanding-impact-of-resource-selection"></a>瞭解資源選取範圍的影響

適當的資源選擇表示您的知識庫已成功回答查詢預測。

如果您的知識庫無法正常運作，通常問題是不正確的資源管理。

不正確的資源選取需要進行調查，以判斷[需要變更的資源](azure-resources.md#when-to-change-a-pricing-tier)。

## <a name="knowledge-bases"></a>知識庫

知識庫會直接系結其 QnA Maker 資源，並保有用來回答查詢預測要求的問答（QnA）配對。

### <a name="language-considerations"></a>語言考量因素

在您的 QnA Maker 資源上建立的第一個知識庫會設定資源的語言。 QnA Maker 資源只能有一種語言。

根據語言來建立 QnA Maker 資源的結構，或使用[Translator](../../translator/translator-info-overview.md)將查詢從另一種語言變更為知識庫的語言，再將查詢傳送至查詢預測端點。

### <a name="ingesting-data-sources"></a>內嵌資料來源

內嵌[資料來源](knowledge-base.md)（用來建立知識庫）可以是下列其中一項：

* 公用 URL
* 私人 SharePoint URL
* 檔案

內嵌程式會將[支援的內容類型](content-types.md)轉換成 markdown。 所有進一步的*答案*編輯動作都是透過 markdown 來完成。 建立您的知識庫之後，您可以在 QnA Maker 入口網站中，使用[豐富的文字撰寫](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)來編輯[QnA 配對](question-answer-set.md)。

### <a name="data-format-considerations"></a>資料格式考慮

因為 QnA 組的最終格式是 markdown，所以瞭解[markdown 支援](../reference-markdown-format.md)很重要。

連結的影像必須可從公用 URL 取得，才能顯示在 QnA Maker 入口網站的 [測試] 窗格以及任何用戶端應用程式中，因為 QnA Maker 不會提供內容的驗證，包括影像。

### <a name="bot-personality"></a>Bot 個人化

使用[閒聊](../how-to/chit-chat-knowledge-base.md)，將 bot 個人化新增至您的知識庫。 這項特性是透過特定交談音調提供的答案，例如*professional*和*易記*。 這個閒聊是以交談的方式提供，您可以在其中新增、編輯和移除所有的控制項。

如果您的 bot 連接到您的知識庫，則建議使用 bot 個人化。 如果您要連接到數個服務，其中一個是您的知識庫，您可以選擇繼續在知識庫中使用閒聊，但您應該查看 bot 服務如何互動，以知道這是否為您使用的正確架構設計。

### <a name="conversation-flow-with-a-knowledge-base"></a>含知識庫的交談流程

對話流程通常會以使用者的稱呼開頭，例如 `Hi` 或 `Hello` 。 您的知識庫可以回答一般的答案（例如 `Hi, how can I help you` ），也可以提供後續的追蹤提示，以繼續交談。

您應該使用迴圈來設計您的交談流程，讓使用者知道如何使用您的 bot，而不是由對話中的 bot 放棄。 [後續提示會](../how-to/multiturn-conversation.md)提供 QnA 組之間的連結，以允許交談流程。

### <a name="authoring-with-collaborators"></a>使用共同作業者撰寫

共同作業者可能是其他共用知識庫應用程式之完整開發堆疊的開發人員，或僅限於撰寫知識庫。

知識庫撰寫支援數個以[角色為基礎的存取權限](../how-to/collaborate-knowledge-base.md)，您可以在 Azure 入口網站中套用這些許可權，以限制共同作業者的能力。

## <a name="integration-with-client-applications"></a>與用戶端應用程式整合

與[用戶端應用程式](integration-with-other-applications.md)的整合表示傳送查詢至預測執行時間端點。 查詢會使用 SDK 或 REST 型要求傳送至您的 QnA Maker web 應用程式端點的特定知識庫。

用戶端應用程式必須傳送正確的認證和知識庫識別碼，才能正確驗證用戶端要求。 如果您使用 Azure Bot 服務，請在 Azure 入口網站中，將設定設為 Bot 設定的一部分。

### <a name="conversation-flow-in-a-client-application"></a>用戶端應用程式中的交談流程

[用戶端應用程式](integration-with-other-applications.md)（例如 Azure bot）中的交談流程可能需要在與知識庫互動之前和之後的功能。

如果您的用戶端應用程式支援交談流程，可提供替代的方法來處理後續提示或閒聊閒聊，請及早進行設計，並確保在用戶端應用程式中使用的查詢會由另一個服務正確處理，或傳送至您的知識庫。

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>QnA Maker 和 Language Understanding 之間分派（LUIS）

用戶端應用程式可能會提供數個功能，其中只有一個是由知識庫所回答。 其他功能仍然需要瞭解交談文字，並從中摘錄意義。

常見的用戶端應用程式架構是同時使用 QnA Maker 和[Language Understanding （LUIS）](../../LUIS/what-is-luis.md) 。 LUIS 提供任何查詢的文字分類和解壓縮，包括其他服務，QnA Maker 則提供您知識庫的答案。

在[共用架構](../choose-natural-language-processing-service.md)中，這兩項服務之間的分派作業是使用 Bot Framework 的[分派](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)工具來完成。

### <a name="active-learning-from-a-client-application"></a>用戶端應用程式的主動式學習

QnA Maker 使用_主動式學習_來改善您的知識庫，方法是向答案建議替代問題。 用戶端應用程式負責此[主動式學習](active-learning-suggestions.md)的一部分。 用戶端應用程式透過對話式提示，可以判斷從知識庫傳回的答案不是使用者所要尋找的答案，而是決定更好的答案。 用戶端應用程式必須將[該資訊傳送回知識庫](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api)，以改善預測品質。

### <a name="providing-a-default-answer"></a>提供預設答案

如果您的知識庫找不到答案，則會傳回_預設答案_。 此答案可從 QnA Maker 入口網站、[**設定**] 頁面或[api](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body)來設定。

此預設答案與 Azure bot 的預設答案不同。 Azure bot 預設回答是在 bot 的 Azure 入口網站中設定，做為設定的一部分，並會在不符合分數臨界值時傳回。

## <a name="prediction"></a>預測

預測是來自您知識庫的回應，其中包含的資訊不只是解答。 若要取得查詢預測回應，請使用[GENEATEANSWER API](query-knowledge-base.md)。

### <a name="prediction-score-fluctuations"></a>預測分數波動

分數可以根據數個因素而變更：

* 回應[GenerateAnswer](query-knowledge-base.md) with 屬性所要求的解答數 `top`
* 各種可用的替代問題
* 篩選中繼資料
* 傳送至 `test` 或 `production` 知識庫的查詢

有[兩階段答案的排名](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)：
* 認知搜尋優先的排名-若要從認知搜尋傳回答案，_允許的解答_數必須夠高，才能讓認知搜尋傳回最佳答案，讓他們可以傳入 QnA Maker ranker
* QnA Maker-第二個排名-套用特徵化和機器學習服務，以判斷最佳答案。

### <a name="service-updates"></a>服務更新

服務更新會透過套用[最新的執行時間更新](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)來自動管理。

### <a name="scaling-throughput-and-resiliency"></a>調整、輸送量和復原

調整、輸送量和復原取決於[Azure 資源](../how-to/set-up-qnamaker-service-azure.md)、其定價層，以及任何周圍的架構，例如[流量管理員](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager)。

### <a name="analytics-with-application-insights"></a>使用 Application Insights 進行分析

您知識庫的所有查詢都會儲存在 Application Insights 中。 使用我們的[熱門查詢](../how-to/get-analytics-knowledge-base.md)來瞭解您的計量。

## <a name="development-lifecycle"></a>開發生命週期

知識庫的[開發生命週期](development-lifecycle-knowledge-base.md)正在進行中：編輯、測試和發佈您的知識庫。

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker 組的知識庫開發

您的[QnA 配對](question-answer-set.md)應根據用戶端應用程式的使用方式來設計和開發。

每個配對可以包含：
* 中繼資料-查詢時可篩選。 這可讓您以資料的來源、內容、格式和用途的其他相關資訊，為您的 QnA 組加上標記。
* 後續提示-判斷您的知識庫路徑，讓使用者抵達正確的答案。
* 替代問題-若要讓搜尋符合各種形式之問題的答案，請務必使用替代問題。 [主動式學習建議](active-learning-suggestions.md)轉變成替代的問題。

### <a name="devops-development"></a>DevOps 開發

開發知識庫以插入 DevOps 管線時，必須在[批次測試](../quickstarts/batch-testing.md)期間隔離知識庫。

知識庫會與 QnA Maker 資源上的所有其他知識庫共用認知搜尋索引。 雖然知識庫是依資料分割來隔離，但相較于已發行的知識庫，共用索引可能會導致分數的差異。

若要在和知識庫上具有_相同_的 `test` 分數 `production` ，請將 QnA Maker 資源隔離至單一知識庫。 在此架構中，資源只需要存留到隔離批次測試的長度。

## <a name="next-step"></a>下一步

* [Azure 資源](../how-to/set-up-qnamaker-service-azure.md)
* [問與答組](question-answer-set.md)