---
title: 詞彙-LUIS
description: 此詞彙說明使用 LUIS API Service 時可能遇到的字詞。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 7c65c8272172cab9f5361d16141bf7b229037480
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786939"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>常用詞彙和概念的語言理解詞彙
Language Understanding (LUIS) 詞彙說明使用 LUIS 服務時可能遇到的詞彙。

## <a name="active-version"></a>作用中版本

使用中的版本是您使用 LUIS 入口網站對模型進行變更時，所更新的應用程式 [版本](luis-how-to-manage-versions.md) 。 在 LUIS 入口網站中，如果您想要對非使用中版本的版本進行變更，您必須先將該版本設定為作用中。

## <a name="active-learning"></a>主動學習

主動式學習是機器學習服務的一種技術，使用機器學習模型來識別提供新範例的標籤。 在 LUIS 中，主動式學習是指從端點流量新增語句，其目前的預測不清楚地改善您的模型。 按一下 [審核端點語句]，即可查看語句的標籤。

另請參閱：
* [概念資訊](luis-concept-review-endpoint-utterances.md)
* [檢查端點語句的教學課程](luis-tutorial-review-endpoint-utterances.md)
* 如何藉由[檢查端點語句](luis-how-to-review-endpoint-utterances.md)來改善 LUIS 應用程式

## <a name="application-app"></a>應用程式 (應用程式) 

在 LUIS 中，您的應用程式（或應用程式）是機器學習模型的集合，建置於相同的資料集上，可一起運作來預測特定案例的意圖和實體。 每個應用程式都有個別的預測端點。

如果您要建立 HR bot，您可能會有一組意圖，例如「排程休假時間」、「查詢權益」和「更新個人資訊」，以及您分組到單一應用程式中的每一個意圖的實體。

## <a name="authoring"></a>編寫

撰寫是使用 LUIS 入口網站或撰寫 Api 來建立、管理及部署 LUIS 應用程式的能力。

### <a name="authoring-key"></a>撰寫金鑰

[撰寫金鑰](luis-how-to-azure-subscription.md)是用來撰寫應用程式。 不用於生產環境層級的端點查詢。 如需詳細資訊，請參閱[金鑰限制](luis-limits.md#key-limits)。

### <a name="authoring-resource"></a>撰寫資源

您的 LUIS [撰寫資源](luis-how-to-azure-subscription.md#azure-resources-for-luis) 是透過 Azure 提供的可管理專案。 資源可讓您存取 Azure 服務的相關撰寫、訓練和發佈功能。 此資源包含存取相關聯的 Azure 服務所需的驗證、授權和安全性資訊。

撰寫資源具有的 Azure 「種類」 `LUIS-Authoring` 。

## <a name="batch-test"></a>批次測試

批次測試是能夠使用一致且已知的使用者語句測試集來驗證目前 LUIS 應用程式的模型。 批次測試是在 [JSON 格式](./luis-how-to-batch-test.md#batch-test-file)的檔案中定義。


另請參閱：
* [概念](./luis-how-to-batch-test.md)
* [如何](luis-how-to-batch-test.md) 執行批次測試
* [教學](./luis-how-to-batch-test.md) 課程-建立和執行批次測試

### <a name="f-measure"></a>F 量值

在批次測試中，是指測試精確度的量值。

### <a name="false-negative-fn"></a>誤否定 (FN) 

在批次測試中，資料點代表您的應用程式不正確預測了不存在目標意圖/實體的語句。

### <a name="false-positive-fp"></a>False 正數 (FP) 

在批次測試中，資料點代表您的應用程式不正確預測了存在目標意圖/實體的語句。

### <a name="precision"></a>準確率
在批次測試中，精確度 (也稱為陽性預測值) 是相關語句在所擷取語句之間的比例。

動物批次測試的範例是預測的為伍數目除以動物 (為伍和非為伍等) 的總次數。

### <a name="recall"></a>召回

在批次測試中，記憶 (也稱為敏感度) 是 LUIS 一般化的功能。

動物批次測試的範例是所預測的為伍數目除以可用為伍總數。

### <a name="true-negative-tn"></a>真否定 (TN)

如果您的應用程式正確預測無相符項，則為真負的。 在批次測試中，當您的應用程式針對尚未以該意圖或實體標示的範例，預測意圖或實體時，就會發生真正的負面情形。

### <a name="true-positive-tp"></a>真肯定 (TP)

真肯定 (TP) 真肯定是您的應用程式正確預測相符的情況。 在批次測試中，當您的應用程式針對已使用該意圖或實體標記的範例預測意圖或實體時，就會發生真肯定。

## <a name="classifier"></a>分類器

分類器是一種機器學習模型，可預測輸入適合的分類或類別。

[意圖](#intent)是分類器的範例。

## <a name="collaborator"></a>共同作業者

共同作業者的概念與 [參與者](#contributor)的概念相同。 當擁有者將共同作業者的電子郵件地址新增至未受 Azure 角色型存取控制 (Azure RBAC) 控制的應用程式時，會授與共同作業者的存取權。 如果您仍在使用共同作業者，則應該遷移您的 LUIS 帳戶，並使用 LUIS 撰寫資源來管理 Azure RBAC 的參與者。

## <a name="contributor"></a>參與者

參與者不是應用程式的 [擁有](#owner) 者，但具有新增、編輯和刪除意圖、實體、語句的相同許可權。 參與者可將 Azure 角色型存取控制 (Azure RBAC) 提供給 LUIS 應用程式。

另請參閱：
* [如何](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) 新增參與者

## <a name="descriptor"></a>描述項

描述項是先前用於機器學習 [功能](#features)的詞彙。

## <a name="domain"></a>網域

在 LUIS 的脈絡中，領域是一個知識的範圍。 您的網域是您的案例所特有的。 不同的網域會使用在網域內容中具有意義的特定語言和術語。 例如，如果您要建立應用程式來播放音樂，則您的應用程式會有音樂專屬的條款和語言–例如「歌曲、曲目、專輯、歌詞、b 邊、演出者」。 如需網域的範例，請參閱 [預建網域](#prebuilt-domain)。

## <a name="endpoint"></a>端點

### <a name="authoring-endpoint"></a>撰寫端點

LUIS 撰寫端點 URL 是您撰寫、定型及發佈應用程式的位置。 端點 URL 包含已發佈應用程式的區域或自訂子域，以及應用程式識別碼。

深入瞭解如何從[開發人員參考](developer-reference-resource.md#rest-endpoints)以程式設計方式撰寫您的應用程式

### <a name="prediction-endpoint"></a>預測端點

LUIS 預測端點 URL 是您在撰寫併發布 [LUIS 應用程式](#application-app) 之後，提交 LUIS 查詢的位置。 端點 URL 包含已發佈應用程式的區域或自訂子域，以及應用程式識別碼。 您可以在應用程式的 [ **[Azure 資源](luis-how-to-azure-subscription.md)** ] 頁面上找到端點，也可以從 [Get 應用程式資訊](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API 取得端點 URL。

您對預測端點的存取權會獲得 LUIS 預測金鑰的授權。

## <a name="entity"></a>單位

[實體](luis-concept-entity-types.md) 是語句中的單字，可描述用來滿足或識別意圖的資訊。 如果您的實體很複雜，而且您想要模型識別特定部分，您可以將模型分成子實體。 例如，您可能會想要讓模型預測位址，以及子實體街道、city、state 和 zipcode。 實體也可以當做模型的功能使用。 您從 LUIS 應用程式的回應將會包含預測的意圖和所有實體。

### <a name="entity-extractor"></a>實體解壓縮

實體解壓縮程式有時也稱為「解壓縮程式」，這是 LUIS 用來預測實體的機器學習模型類型。

### <a name="entity-schema"></a>實體架構

實體架構是您使用子實體為機器學習實體定義的結構。 預測端點會傳回架構中定義的所有已解壓縮實體和子實體。

### <a name="entitys-subentity"></a>實體的列

列是機器學習實體的子實體。

### <a name="non-machine-learning-entity"></a>非機器學習實體

使用文字元合來將資料解壓縮的實體：
* 清單實體
* 規則運算式實體

### <a name="list-entity"></a>清單實體

[清單實體](reference-entity-list.md)代表一組固定且封閉的相關單字及其同義字。 清單實體是完全相符的專案，與已加工的學習實體不同。

如果清單實體中的單字包含在清單中，則會預測實體。 例如，如果您有一個名為「大小」的清單實體，而且清單中有「小、中、大」的文字，則會針對所有語句預測大小實體，無論內容為何，都會使用 "small"、"medium" 或 "大型" 等字。

### <a name="regular-expression"></a>規則運算式

[正則運算式實體](reference-entity-regular-expression.md)代表正則運算式。 正則運算式實體是完全相符的專案，與已加工的學習實體不同。
### <a name="prebuilt-entity"></a>預建實體

查看預建[實體](#prebuilt-entity)的預建模型專案

## <a name="features"></a>功能

在機器學習中，功能是可協助模型辨識特定概念的特性。 這是 LUIS 可以使用的提示，但不能使用硬規則。

這個詞彙也稱為 **[機器學習功能](luis-concept-feature.md)**。

這些提示會搭配標籤使用，以瞭解如何預測新資料。 LUIS 同時支援片語清單和使用其他模型作為特徵。

### <a name="required-feature"></a>必要功能

必要的功能是一種限制 LUIS 模型輸出的方式。 當實體的功能標示為必要時，此功能必須存在於要預測的實體範例中，不論機器學習的模型預測為何。

假設您有一個範例，其中的預建編號功能已在功能表排序 bot 的 quantity 實體上標示為必要。 當您的 bot 看到時 `I want a bajillion large pizzas?` ，不論其出現的內容為何，都不會將 bajillion 預測為數量。 Bajillion 不是有效的數位，且不會由預先建立的實體數目預測。

## <a name="intent"></a>Intent

[意圖](luis-concept-intent.md)代表使用者想要執行的工作或動作。 這是以使用者的輸入表示的目的或目標，例如預訂航班或支付帳單。 在 LUIS 中，整體語句會分類為意圖，但語句的元件會被解壓縮為實體

## <a name="labeling-examples"></a>標籤範例

標記或標記是將正面或負面範例與模型相關聯的程式。

### <a name="labeling-for-intents"></a>意圖的標籤
在 LUIS 中，應用程式內的意圖是互斥的。 這表示當您將語句新增至意圖時，會將其視為該意圖的 _正面_ 範例，以及所有其他意圖的 _負_ 範例。 負的範例不應該與「無」意圖混淆，這代表在應用程式範圍外的語句。

### <a name="labeling-for-entities"></a>實體的標籤
在 LUIS 中，您會在意圖的範例語句中 [標示](label-entity-example-utterance.md) 單字或片語，並以實體作為 _正面_ 範例。 標籤會顯示應針對該語句預測的意圖。 加上標籤的語句會用來定型意圖。

## <a name="luis-app"></a>LUIS 應用程式

請參閱 [應用程式 (應用程式) ](#application-app)的定義。

## <a name="model"></a>型號

學習) 模型的 (機器是對輸入資料進行預測的函式。 在 LUIS 中，我們會將意圖分類器和實體擷取器統稱為「模型」，而我們會將一組以「應用程式」方式一起定型、發佈及查詢的模型。

## <a name="normalized-value"></a>標準化值

您可以將值新增至 [清單](#list-entity) 實體。 每一個值都可以有一或多個同義字的清單。 只有正規化值會在回應中傳回。

## <a name="overfitting"></a>過度學習

在特定範例上注視模型時，就會發生過度學習，而且無法充分一般化。

## <a name="owner"></a>擁有者

每個應用程式都有一個擁有者，這是建立應用程式的人員。 擁有者會在 Azure 入口網站中管理應用程式的許可權。

## <a name="phrase-list"></a>片語清單

[片語清單](luis-concept-feature.md)是一種特定類型的機器學習服務功能，其中包含一組值 (單字或片語) 屬於相同的類別，而且必須以類似的方式處理 (例如，城市或產品的名稱) 。

## <a name="prebuilt-model"></a>預建模型

[預建模型](luis-concept-prebuilt-model.md)是一種意圖、實體或兩者的集合，以及加上標籤的範例。 這些常見的預建模型可以新增至您的應用程式，以減少應用程式所需的模型開發工作。

### <a name="prebuilt-domain"></a>預建網域

預先建置的領域是針對特定領域設定的 LUIS 應用程式，例如家庭自動化 (HomeAutomation) 或餐廳預約 (RestaurantReservation)。 意圖、語句和實體會針對此領域設定。

### <a name="prebuilt-entity"></a>預建實體

預先建置的實體是 LUIS 針對一般的資訊類型 (例如數字、URL 和電子郵件) 提供的實體。 這些會根據公用資料建立。 您可以選擇將預先建立的實體新增為獨立實體，或新增為實體的功能

### <a name="prebuilt-intent"></a>預先建立的意圖

預先建立的意圖是意圖 LUIS 提供給常見的資訊類型，並附有自己加上標籤的範例語句。

## <a name="prediction"></a>預測

預測是 Azure LUIS 預測服務的 REST 要求，會將新資料 (使用者語句) ，並將已定型和已發佈的應用程式套用至該資料，以判斷找到哪些意圖和實體。

### <a name="prediction-key"></a>預測金鑰

 (先前稱為訂用帳戶金鑰的 [預測金鑰](luis-how-to-azure-subscription.md)) 是與您在 Azure 中建立的 LUIS 服務相關聯的金鑰，以授權您使用預測端點。

這個金鑰不是編寫金鑰。 如果您有預測端點金鑰，則應該將它用於任何端點要求，而不是撰寫金鑰。 您可以在 LUIS 網站中 Azure 資源頁面底部的端點 URL 中看到目前的預測金鑰。 這是訂用帳戶金鑰名稱/值組的值。

### <a name="prediction-resource"></a>預測資源

您的 LUIS 預測資源是透過 Azure 提供的可管理專案。 資源可讓您存取 Azure 服務的相關預測。 資源包含預測。

預測資源具有的 Azure 「種類」 `LUIS` 。

### <a name="prediction-score"></a>預測分數

[分數](luis-concept-prediction-score.md)是從0和1開始的數位，這是測量系統如何讓特定輸入語句符合特定意圖的量值。 接近1的分數表示系統對於其輸出很有信心，而接近0的分數則表示系統確信輸入不符合特定的輸出。 中間的分數表示系統不確定如何進行決策。

例如，取得用來識別某些客戶文字是否包含食物訂單的模型。 這可能會讓「我想要訂購一咖啡」 (系統很確信這是訂單) ，而「我的團隊在上一夜就贏得遊戲」的分數為0， (系統很確信這不是訂單) 。 而「讓我們有一些茶」的分數可能是0.5， (不確定這是不是) 。

## <a name="programmatic-key"></a>程式設計金鑰

已重新命名為[編寫金鑰](#authoring-key)。

## <a name="publish"></a>發佈

[發行](luis-how-to-publish-app.md) 表示讓 LUIS 使用中的版本可在預備環境或生產 [端點](#endpoint)上使用。

## <a name="quota"></a>Quota

LUIS 配額是 Azure 訂用帳戶層的限制。 LUIS 配額會受到每秒要求數 (HTTP 狀態 429) 和一個月的要求總數 (HTTP 狀態 403) 兩者的限制。

## <a name="schema"></a>結構描述

您的架構包含您的意圖和實體，以及子實體。 架構一開始是計畫在經過一段時間後再進行反覆運算。 架構不包含應用程式設定、功能或範例語句。

## <a name="sentiment-analysis"></a>情感分析
情感分析提供由[文字分析](../text-analytics/overview.md)提供的語句的正值或負值。

## <a name="speech-priming"></a>語音預備

語音預備可透過 [語音服務](../speech-service/overview.md)改善您案例中常用的語音文字與片語辨識。 針對啟用語音預備的應用程式，會使用所有 LUIS 標記的範例，藉由為此特定應用程式建立自訂語音模型，來改善語音辨識的精確度。 例如，您想要確定當使用者說的是「移動 knight」時，不會將它解釋為「移動夜間」。 LUIS 應用程式應該包含將 "knight" 標示為實體的範例。

## <a name="starter-key"></a>入門金鑰

第一次開始使用 LUIS 時要使用的免費金鑰。

## <a name="synonyms"></a>同義字

在 LUIS [清單實體](reference-entity-list.md)中，您可以建立標準化值，每個值都可以有同義字清單。 例如，如果您建立的大小實體具有 small、medium、大型和超大型的標準化值。 您可以為每個值建立同義字，如下所示：

|Nomalized 值| 同義字|
|--|--|
|小型| 小一，8盎司|
|中| 標準、12盎司|
|大| 大型、16盎司|
|Xtra 大型| 最大一，24盎司|

當輸入中出現任何同義字時，此模型會傳回實體的標準化值。

## <a name="test"></a>測試

[測試](luis-concept-test.md) LUIS 應用程式表示要查看模型預測。

## <a name="timezone-offset"></a>時區位移

端點包含 [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 這是您想要加入 datetimeV2 預先建置的實體或從中移除的分鐘數。 例如，如果語句是「現在是幾點？」，傳回的 datetimeV2 是用戶端要求的目前時間。 如果您的用戶端要求是來自 Bot 或與您 Bot 使用者不同的其他應用程式，則您應該傳入 Bot 與使用者之間的時差。

請參閱[變更預先建置 datetimeV2 實體的時區](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity)。

## <a name="token"></a>權杖
[Token](luis-language-support.md#tokenization)是 LUIS 可辨識的最小文字單位。 這在語言方面稍有不同。

若為 **英文**，權杖是連續範圍 (不會有任何空格或標點符號) 字母和數位。 空間不是標記。

|片語|權杖計數|說明|
|--|--|--|
|`Dog`|1|不含標點符號或空格的單一單字。|
|`RMT33W`|1|記錄定位器編號。 它可能有數位和字母，但沒有任何標點符號。|
|`425-555-5555`|5|電話號碼。 每個標點符號都是單一權杖，因此會  `425-555-5555` 是5個權杖：<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Train

[訓練](luis-how-to-train.md) 是在上一次定型之後，對使用中版本的任何變更進行教學 LUIS 的流程。

### <a name="training-data"></a>訓練資料

定型資料是訓練模型所需的一組資訊。 這包括架構、標示為語句、功能和應用程式設定。

### <a name="training-errors"></a>定型錯誤

定型錯誤是您定型資料上不符合其標籤的預測。

## <a name="utterance"></a>表達

「使用者輸入」（ [語句](luis-concept-utterance.md) ）是指對話中句子的簡短文字代表。 它是一種自然語言片語，例如「第2個票證到西雅圖的下週二」。 新增範例語句來定型模型，而模型會在執行時間于新語句上預測

## <a name="version"></a>版本

LUIS [版本](luis-how-to-manage-versions.md) 是與 LUIS 應用程式識別碼和已發佈端點相關聯之 LUIS 應用程式的特定實例。 每個 LUIS 應用程式至少會有一個版本。
