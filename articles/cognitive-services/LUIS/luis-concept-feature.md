---
title: 使用 LUIS 的機器學習功能
description: 將特性新增至語言模型，以針對如何辨識您想要標示或分類的輸入，提供相關提示。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 08ab71375171d4bb4167c725bc7118bec2e1ebfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371986"
---
# <a name="machine-learning-features"></a>機器學習功能

在機器學習中， *功能*   是指您的系統觀察和學習的資料特性或屬性。

機器學習功能提供 LUIS 重要的提示，讓您找出可分辨概念的專案。 它們是 LUIS 可使用的提示，但它們不是困難的規則。 LUIS 會使用這些提示搭配標籤來尋找資料。

功能可以描述為函數，例如 f (x) = y。 在範例語句中，此功能會告訴您要在哪裡尋找區別特性。 使用此資訊來協助建立您的架構。

## <a name="types-of-features"></a>功能類型

功能是您架構設計的必要部分。 LUIS 支援片語清單和模型作為功能：

* 片語清單功能
* 模型 (意圖或實體) 作為功能

## <a name="find-features-in-your-example-utterances"></a>在範例語句中尋找功能

由於 LUIS 是以語言為基礎的應用程式，因此功能是以文字為基礎。 選擇文字，指出您想要區分的特性。 針對 LUIS，最小單位是 *權杖*。 針對英文語言，權杖是不含空格或標點符號的連續字母和數位範圍。

由於空格和標點符號不是 token，因此請將焦點放在您可以用來做為功能的文字線索。 請記得包含單字的變化，例如：

* 複數形式
* 動詞時態
* 縮寫
* 拼寫與拼寫錯誤

判斷文字是否可區別特性，且必須：

* 符合確切的單字或片語：考慮將正則運算式實體或清單實體做為功能加入至實體或意圖。
* 符合已知的概念，例如日期、時間或人員的名稱：使用預建實體做為實體或意圖的功能。
* 學習一段時間的新範例：使用概念範例的片語清單做為實體或意圖的功能。

## <a name="create-a-phrase-list-for-a-concept"></a>建立概念的片語清單

片語清單是描述概念的單字或片語清單。 在權杖層級上，會將片語清單套用為不區分大小寫的相符項。

新增片語清單時，您可以將此功能設定為 **[全域](#global-features)**。 全域功能適用于整個應用程式。

### <a name="when-to-use-a-phrase-list"></a>使用片語清單的時機

當您需要 LUIS 應用程式來一般化並識別概念的新專案時，請使用片語清單。 片語清單就像網域特定詞彙一樣。 它們可增強對意圖和實體的理解品質。

### <a name="how-to-use-a-phrase-list"></a>如何使用片語清單

使用片語清單，LUIS 會考慮內容和一般化，以識別類似的專案，但不是完全相符的文字。 遵循下列步驟以使用片語清單：

1. 從機器學習實體開始：
    1. 新增範例語句。
    1. 具有機器學習服務實體的標籤。
1. 新增片語清單：
    1. 新增具有類似意義的單字。 請勿新增每個可能的單字或片語。 相反地，請一次新增一些單字或片語。 然後重新訓練併發布。
    1. 檢查並新增建議的字組。

### <a name="a-typical-scenario-for-a-phrase-list"></a>片語清單的一般案例

片語清單的一般案例是提高與特定想法相關的單字。

醫療詞彙是很好的單字範例，可能需要片語清單來提高其重要性。 這些詞彙可以有特定的實體、化學、ekko-wave 治療或抽象意義。 LUIS 不知道在沒有片語清單的情況下，您的主旨網域中的條款是很重要的。

若要將醫療詞彙解壓縮：

1. 在這些語句內建立語句的範例，並為醫療詞彙加上標籤。
2. 使用主旨網域內的詞彙範例建立片語清單。 此片語清單應包含您標示的實際字詞，以及描述相同概念的其他詞彙。
3. 將片語清單新增至實體或列，以解壓縮片語清單中使用的概念。 最常見的案例是機器學習服務實體 (子) 元件。 如果片語清單應套用到所有意圖或實體，請將片語清單標記為全域片語清單。 **EnabledForAllModels**旗標會控制此模型在 API 中的範圍。

### <a name="token-matches-for-a-phrase-list"></a>片語清單的權杖相符專案

片語清單一律會在權杖層級套用。 下表顯示具有 word **王王** 的片語清單如何套用至該順序中相同字元的變化。


| **王小姐**的 Token 變化 | 找到權杖時的片語清單相符 |
|--------------------------|---------------------------------------|
| **安**<br>**安**<br>           | 是-token 為 **王小姐**                  |
| **王小姐**                    | 是-token 為 **王小姐**                  |
| **Anne**                     | No-token 為 **Anne**                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>模型即功能可協助另一個模型

您可以將模型 (意圖或實體) 為另一個模型 (意圖或實體) 的功能。 藉由新增現有的意圖或實體做為功能，您將加入定義完善的概念，其中已加上標籤的範例。

將模型新增為功能時，您可以將此功能設定為：
* **[必要](#required-features)**。 必須找到必要的功能，才能從預測端點傳回模型。
* **[Global](#global-features)**。 全域功能適用于整個應用程式。

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>使用實體做為意圖功能的時機

當實體的偵測對意圖而言很重要時，請將實體新增為意圖的功能。

例如，如果意圖是預訂航班（例如 **BookFlight**），而實體是票證資訊 (例如基座數目、來源和目的地) ，則尋找 [票證資訊] 實體應該會將大量加權新增至 **BookFlight** 意圖的預測。

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>何時使用實體做為另一個實體的功能

當實體 () 的預測很重要時，) 的實體 (應新增為另一個實體的功能 (B) 。

例如，如果「寄件位址」實體包含在街道位址列中，則尋找街道位址列會將大量加權新增至「運送位址」實體的預測。

* 運送位址 (機器學習實體) ：

    * 列) 的街道號碼 (
    * 街道位址 (列) 
    * 城市 (列) 
    * 州或省 (列) 
    * 國家/地區 (列) 
    * 郵遞區號 (列) 

## <a name="nested-subentities-with-features"></a>具有功能的嵌套子實體

機器學習清單示概念存在於父實體中。 父系可以是其他列或最上層實體。 列的值會作為其父系的功能。

列可同時有片語清單和模型 (另一個實體) 為功能。

當列有片語清單時，它會提高概念的詞彙，但不會將任何資訊新增至預測的 JSON 回應。

當列具有另一個實體的功能時，JSON 回應會包含該其他實體的已解壓縮資料。


## <a name="required-features"></a>必要的功能

必須找到必要的功能，才能從預測端點傳回模型。 當您知道傳入的資料必須符合此功能時，請使用必要的功能。

如果語句文字不符合所需的功能，則不會將它解壓縮。

必要功能使用非機器學習實體：

* 規則運算式實體
* 清單實體
* 預建實體

如果您確信會在資料中找到您的模型，請視需要設定此功能。 如果找不到所需的功能，則不會傳回任何功能。

繼續進行交貨位址的範例：

 (機器學習實體) 交貨位址

 * 列) 的街道號碼 (
 * 街道位址 (列) 
 * 街道名稱 (列) 
 * 城市 (列) 
 * 州或省 (列) 
 * 國家/地區 (列) 
 * 郵遞區號 (列) 

### <a name="required-feature-using-prebuilt-entities"></a>使用預建實體所需的功能

City、state 和 country/region 通常是一組封閉的清單，這表示它們不會隨著時間而變更。 這些實體可能會有相關的建議功能，而這些功能可能會標示為必要。 這表示，如果找不到具有所需功能的實體，就不會傳回整個交貨位址。

如果城市、州或國家/地區是在語句中，但它們位於某個位置或俚語 LUIS 未預期的情況下，該怎麼辦？ 如果您想要提供一些 post 處理來協助解決實體，因為 LUIS 的低信賴分數，請不要將此功能標示為必要。

送貨位址的另一個必要功能範例是讓街道號碼成為必要的 [預建](luis-reference-prebuilt-entities.md) 編號。 這可讓使用者輸入「1個 Microsoft 的方式」或「一種 Microsoft 的方式」。 兩者都會解析為列的數位 "1"。

### <a name="required-feature-using-list-entities"></a>使用清單實體的必要功能

[清單實體](reference-entity-list.md)會用來做為正式名稱及其同義字的清單。 作為必要功能，如果語句不包含正式名稱或同義字，則不會在預測端點中傳回該實體。

假設您的公司只寄給一組有限的國家/地區。 您可以建立包含數種方式的清單實體，讓客戶參考國家/地區。 如果 LUIS 在語句的文字內找不到完全相符的實體，則會在預測中傳回具有清單實體所需功能的實體 () 。

|正式名稱|同義字|
|--|--|
|美國|美式英文<br>美國<br>美國<br>USA<br>0|

用戶端應用程式（例如聊天機器人）可以提出待處理的問題來提供協助。 這可協助客戶瞭解國家/地區選擇受限且 *必要*。

### <a name="required-feature-using-regular-expression-entities"></a>使用正則運算式實體的必要功能

作為必要功能使用的 [正則運算式實體](reference-entity-regular-expression.md) 會提供豐富的文字比對功能。

在交貨位址範例中，您可以建立可捕獲國家/地區郵遞區號之語法規則的正則運算式。

## <a name="global-features"></a>全域功能

雖然最常見的用法是將功能套用至特定模型，但您可以將此功能設定為 **全域功能** ，以將其套用至整個應用程式。

全域功能最常見的用途是將額外的詞彙新增至應用程式。 例如，如果您的客戶使用主要語言，但預期能夠使用相同語句中的另一種語言，您可以新增包含次要語言文字的功能。

因為使用者預期會在任何意圖或實體上使用次要語言，所以請從次要語言將文字新增至片語清單。 將片語清單設定為通用功能。

## <a name="combine-features-for-added-benefit"></a>結合功能以增加優點

您可以使用多項功能來描述特性或概念。 常見的配對是使用：

* 片語清單功能：您可以使用多個片語清單作為相同模型的特徵。
* 模型即功能： [預建的實體](luis-reference-prebuilt-entities.md)、 [正則運算式實體](reference-entity-regular-expression.md)、 [清單實體](reference-entity-list.md)。 

### <a name="example-ticket-booking-entity-features-for-a-travel-app"></a>範例：旅遊應用程式的票證預約實體功能  

在基本範例中，請考慮使用航班預訂 _意圖_ 和票證預約 _實體_預約航班的應用程式。 票證預訂實體會捕捉資訊，以在保留系統中預訂飛機票證。 

適用于票證書籍的機器學習服務實體有兩個子實體，可供您用來捕捉來源和目的地。 這些功能必須加入至每個列，而不是最上層實體。

:::image type="content" source="media/luis-concept-features/ticket-booking-entity.png" alt-text="Ticketbooking 實體架構":::

「票證預約」實體是一種機器學習實體，具有子實體，包括 _來源_ 和 _目的地_。 這些子實體都會指出地理位置。 為了協助您將位置解壓縮，並區分 _來源_ 和 _目的地_，每個列都應該有功能。

|類型|原始列 |目的地列|
|--|--|--|
|模型即功能|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) 預建實體|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) 預建實體|
|片語清單|**原始字**組： `start at` 、 `begin from` 、 `leave`|**目的地單字**： `to` 、 `arrive` 、 `land at` 、 `go` 、 `going` 、 `stay` 、 `heading`|
|片語清單|機場代碼-適用于來源和目的地的相同清單|機場代碼-適用于來源和目的地的相同清單|
|片語清單|機場名稱-適用于來源和目的地的相同清單|機場代碼-適用于來源和目的地的相同清單|

如果您預期使用者使用機場代碼和機場名稱，則 LUIS 應該會有使用這兩種片語類型的片語清單。 機場代碼可能會比在聊天機器人中輸入的文字更常見，而機場名稱可能較常見，例如語音啟用的聊天機器人。

這些功能的相符詳細資料只會針對模型傳回，而不會針對片語清單傳回，因為只有在預測 JSON 中才會傳回模型。

#### <a name="ticket-booking-labeling-in-the-intent"></a>意圖中的票證預約標記

建立機器學習服務實體之後，您必須將範例語句新增至意圖，並為父實體和所有子實體加上標籤。

針對票證預約範例，請在意圖中標示範例語句，以及 `TicketBooking` 文字中的實體和任何子實體。

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity.png" alt-text="Ticketbooking 實體架構":::

### <a name="example-pizza-ordering-app"></a>範例：比薩訂購應用程式

針對第二個範例，請考慮使用適用于比薩餐廳的應用程式來接收比薩訂單，包括某人所訂購的比薩類型詳細資料。 每個比薩餅的詳細資料應該盡可能解壓縮（如果可能的話），才能完成訂單處理。

此範例中的機器學習實體會更複雜，且具有嵌套子實體、片語清單、預先建立的實體和自訂實體。

:::image type="content" source="media/luis-concept-features/pizza-order-entity.png" alt-text="Ticketbooking 實體架構":::

此範例會使用列層級的功能和列層級的子系。 哪一種層級可讓片語清單或模型做為實體設計的重要部分。

雖然子實體可以有許多片語清單，做為有助於偵測實體的功能，但每個列只有一個模型做為特徵。 在此 [比薩應用程式](https://github.com/Azure/pizza_luis_bot/blob/master/CognitiveModels/MicrosoftPizza.json)中，這些模型主要是清單。

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity-pizza.png" alt-text="Ticketbooking 實體架構":::

正確加上標籤的範例語句會以顯示實體如何嵌套的方式顯示。 


## <a name="best-practices"></a>最佳作法

了解[最佳做法](luis-concept-best-practices.md)。

## <a name="next-steps"></a>後續步驟

* 在預測執行時間[擴充](schema-change-prediction-runtime.md)您的應用程式模型。
* 請參閱 [新增功能](luis-how-to-add-features.md) ，以深入瞭解如何將功能新增至您的 LUIS 應用程式。
