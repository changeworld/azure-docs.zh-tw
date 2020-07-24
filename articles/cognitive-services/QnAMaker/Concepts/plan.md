---
title: 規劃您的應用程式-QnA Maker
description: 瞭解如何規劃您的 QnA Maker 應用程式。 瞭解 QnA Maker 的運作方式，並與其他 Azure 服務互動，以及一些知識庫概念。
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: 19499aceed96155fa42c78865b1d673a3830f5cc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054197"
---
# <a name="plan-your-qna-maker-app"></a>規劃您的 QnA Maker 應用程式

若要規劃您的 QnA Maker 應用程式，您必須瞭解 QnA Maker 的運作方式，並與其他 Azure 服務互動。 您也應該具備知識庫概念的堅實知識。

## <a name="azure-resources"></a>Azure 資源

使用 QnA Maker 建立的每個[Azure 資源](azure-resources.md#resource-purposes)都有特定的用途。 每個資源都有自己的用途、限制和[定價層](azure-resources.md#pricing-tier-considerations)。 請務必瞭解這些資源的功能，讓您可以將該知識用於您的規劃程式。

| 資源 | 目的 |
|--|--|
| [QnA Maker](azure-resources.md#qna-maker-resource)資源 | 撰寫和查詢預測 |
| [認知搜尋](azure-resources.md#cognitive-search-resource)資源 | 資料儲存和搜尋 |
| [App Service 資源和應用程式方案服務](azure-resources.md#app-service-and-app-service-plan)資源 | 查詢預測端點 |
| [Application Insights](azure-resources.md#application-insights)資源 | 查詢預測遙測 |

### <a name="resource-planning"></a>資源規劃

每個資源的免費層都能 `F0` 運作，而且可以同時提供撰寫和查詢預測體驗。 您可以使用此層來學習撰寫和查詢預測。 當您移至生產或即時案例時，請重新評估您的資源選取範圍。

#### <a name="qna-maker-resource"></a>QnA Maker 資源

單一 QnA Maker 資源可以裝載一個以上的知識庫。 知識庫的數目取決於認知搜尋定價層的支援索引數量。 深入瞭解[索引與知識庫之間的關聯](azure-resources.md#index-usage)性。

#### <a name="knowledge-base-size-and-throughput"></a>知識庫大小和輸送量

當您建立實際的應用程式時，請為您的知識庫和預期的查詢預測要求，規劃足夠的資源。

知識庫大小由控制：
* [認知搜尋資源](../../../search/search-limits-quotas-capacity.md)定價層限制
* [QnA Maker 限制](../limits.md)

知識庫查詢預測要求是由 web 應用程式方案和 web 應用程式所控制。 請參閱[建議的設定](azure-resources.md#recommended-settings)，以規劃您的定價層。

### <a name="resource-sharing"></a>資源共用

如果您已經在使用這些資源，您可以考慮共用資源。 查看哪些資源[可以共用](azure-resources.md#share-services-with-qna-maker)，瞭解資源分享是一個先進的案例。

在相同 QnA Maker 資源中建立的所有知識庫會共用相同的**測試**查詢預測端點。

### <a name="understand-the-impact-of-resource-selection"></a>瞭解資源選取範圍的影響

適當的資源選擇表示您的知識庫已成功回答查詢預測。

如果您的知識庫無法正常運作，通常是不正確的資源管理問題。

不正確的資源選取需要進行調查，以判斷[需要變更的資源](azure-resources.md#when-to-change-a-pricing-tier)。

## <a name="knowledge-bases"></a>知識庫

知識庫會直接系結其 QnA Maker 資源。 它包含用來回答查詢預測要求的問答（QnA）配對。

### <a name="language-considerations"></a>語言考量因素

在您的 QnA Maker 資源上建立的第一個知識庫會設定資源的語言。 QnA Maker 資源只能有一種語言。

您可以依據語言來建立 QnA Maker 資源的結構，也可以使用[Translator](../../translator/translator-info-overview.md) ，將查詢從另一種語言變更為知識庫的語言，再將查詢傳送至查詢預測端點。

### <a name="ingest-data-sources"></a>內嵌資料來源

您可以使用下列其中一個內嵌[資料來源](knowledge-base.md)來建立知識庫：

* 公用 URL
* 私人 SharePoint URL
* 檔案

內嵌程式會將[支援的內容類型](content-types.md)轉換成 markdown。 所有進一步的*答案*編輯動作都是透過 markdown 來完成。 建立知識庫之後，您可以在 QnA Maker 入口網站中，使用[豐富的文字撰寫](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)來編輯[QnA 配對](question-answer-set.md)。

### <a name="data-format-considerations"></a>資料格式考慮

由於 QnA 組的最終格式是 markdown，因此請務必瞭解[markdown 支援](../reference-markdown-format.md)。

連結的影像必須可從公用 URL 取得，才能顯示在 QnA Maker 入口網站的 [測試] 窗格中，或在用戶端應用程式中。 QnA Maker 不會提供內容的驗證，包括影像。

### <a name="bot-personality"></a>Bot 個人化

使用[閒聊](../how-to/chit-chat-knowledge-base.md)，將 bot 個人化新增至您的知識庫。 這項特性是透過特定交談音調提供的答案，例如*professional*和*易記*。 這個閒聊是以交談的方式提供，您可以在其中新增、編輯和移除所有的控制項。

如果您的 bot 連接到您的知識庫，則建議使用 bot 個人化。 即使您也連接到其他服務，也可以選擇在您的知識庫中使用閒聊，但您應該查看 bot 服務如何互動，以知道這是否為您使用的正確架構設計。

### <a name="conversation-flow-with-a-knowledge-base"></a>含知識庫的交談流程

對話流程通常會以使用者的稱呼開頭，例如 `Hi` 或 `Hello` 。 您的知識庫可以回答一般的答案（例如 `Hi, how can I help you` ），也可以提供後續的追蹤提示來繼續交談。

您應該使用迴圈來設計您的交談流程，讓使用者知道如何使用您的 bot，而不是由對話中的 bot 放棄。 [後續提示會](../how-to/multiturn-conversation.md)提供 QnA 組之間的連結，以允許交談流程。

### <a name="authoring-with-collaborators"></a>使用共同作業者撰寫

共同作業者可能是其他共用知識庫應用程式之完整開發堆疊的開發人員，或僅限於撰寫知識庫。

知識庫撰寫支援數個以[角色為基礎的存取權限](../how-to/collaborate-knowledge-base.md)，您可以在 Azure 入口網站中套用這些許可權，以限制共同作業者的能力。

## <a name="integration-with-client-applications"></a>與用戶端應用程式整合

將查詢傳送至預測執行時間端點，即可完成與[用戶端應用程式](integration-with-other-applications.md)的整合。 查詢會使用 SDK 或 REST 型要求傳送至您的 QnA Maker web 應用程式端點的特定知識庫。

若要正確驗證用戶端要求，用戶端應用程式必須傳送正確的認證和知識庫識別碼。 如果您使用的是 Azure Bot 服務，請在 Azure 入口網站中，將這些設定設為 Bot 設定的一部分。

### <a name="conversation-flow-in-a-client-application"></a>用戶端應用程式中的交談流程

[用戶端應用程式](integration-with-other-applications.md)（例如 Azure bot）中的交談流程可能需要在與知識庫互動之前和之後的功能。

您的用戶端應用程式是否支援交談流程，方法是提供替代的方法來處理後續提示或包含閒聊-閒聊？ 若是如此，請及早設計這些資訊，並確定用戶端應用程式查詢已由另一個服務正確處理，或傳送至您的知識庫。

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>QnA Maker 和 Language Understanding 之間分派（LUIS）

用戶端應用程式可能會提供數個功能，其中只有一個是由知識庫所回答。 其他功能仍然需要瞭解交談文字，並從中摘錄意義。

常見的用戶端應用程式架構是同時使用 QnA Maker 和[Language Understanding （LUIS）](../../LUIS/what-is-luis.md) 。 LUIS 提供任何查詢的文字分類和解壓縮，包括其他服務。 QnA Maker 從您的知識庫提供解答。

在這種[共用架構](../choose-natural-language-processing-service.md)案例中，兩個服務之間的分派作業是由 Bot Framework 的[分派](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)工具來完成。

### <a name="active-learning-from-a-client-application"></a>用戶端應用程式的主動式學習

QnA Maker 使用_主動式學習_來改善您的知識庫，方法是向答案建議替代問題。 用戶端應用程式負責此[主動式學習](active-learning-suggestions.md)的一部分。 透過對話式提示，用戶端應用程式可以判斷知識庫是否傳回對使用者來說不實用的答案，而且可以判斷更好的答案。 用戶端應用程式必須將[該資訊傳送回知識庫](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api)，以改善預測品質。

### <a name="providing-a-default-answer"></a>提供預設答案

如果您的知識庫找不到答案，則會傳回_預設答案_。 此答案可在 QnA Maker 入口網站的 [設定] 頁面上或在[api](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body)中**設定**。

此預設答案與 Azure bot 的預設答案不同。 您會在 Azure 入口網站中設定 Azure bot 的預設答案，做為設定的一部分。 當分數臨界值不符合時，就會傳回它。

## <a name="prediction"></a>預測

預測是來自您知識庫的回應，其中包含的資訊不只是解答。 若要取得查詢預測回應，請使用[GENERATEANSWER API](query-knowledge-base.md)。

### <a name="prediction-score-fluctuations"></a>預測分數波動

分數可以根據數個因素而變更：

* 回應[GenerateAnswer](query-knowledge-base.md) with 屬性所要求的解答數 `top`
* 各種可用的替代問題
* 篩選中繼資料
* 傳送至 `test` 或 `production` 知識庫的查詢

有[兩階段答案的排名](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer)：
- 認知搜尋優先的排名。 設定所允許的_答案_數目，讓認知搜尋傳回最佳答案，然後將其傳入 QnA Maker ranker。
- QnA Maker-第二個順位。 套用特徵化和機器學習服務，以判斷最佳答案。

### <a name="service-updates"></a>服務更新

套用[最新的執行時間更新](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)，以自動管理服務更新。

### <a name="scaling-throughput-and-resiliency"></a>調整、輸送量和復原

調整、輸送量和復原取決於[Azure 資源](../how-to/set-up-qnamaker-service-azure.md)、其定價層，以及任何周圍的架構，例如[流量管理員](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager)。

### <a name="analytics-with-application-insights"></a>使用 Application Insights 進行分析

您知識庫的所有查詢都會儲存在 Application Insights 中。 使用我們的[熱門查詢](../how-to/get-analytics-knowledge-base.md)來瞭解您的計量。

## <a name="development-lifecycle"></a>開發生命週期

知識庫的[開發生命週期](development-lifecycle-knowledge-base.md)正在進行中：編輯、測試和發佈您的知識庫。

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker 組的知識庫開發

您的[QnA 配對](question-answer-set.md)應根據用戶端應用程式的使用方式來設計和開發。

每個配對可以包含：
* 可在查詢時篩選中繼資料，讓您以資料的來源、內容、格式和用途的其他相關資訊來標記 QnA 組。
* 後續提示-協助您判斷整個知識庫的路徑，讓使用者抵達正確的答案。
* 替代問題-請務必讓搜尋符合不同形式的問題的答案。 [主動式學習建議](active-learning-suggestions.md)轉變成替代的問題。

### <a name="devops-development"></a>DevOps 開發

開發知識庫以插入 DevOps 管線時，必須在[批次測試](../quickstarts/batch-testing.md)期間隔離知識庫。

知識庫會與 QnA Maker 資源上的所有其他知識庫共用認知搜尋索引。 雖然知識庫是依資料分割來隔離，但相較于已發行的知識庫，共用索引可能會導致分數的差異。

若要在和知識庫上具有_相同的分數_ `test` `production` ，請將 QnA Maker 資源隔離至單一知識庫。 在此架構中，只要隔離的批次測試，資源只需要存留。

## <a name="next-steps"></a>後續步驟

* [Azure 資源](../how-to/set-up-qnamaker-service-azure.md)
* [問與答組](question-answer-set.md)