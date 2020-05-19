---
title: 反復應用程式設計-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 在模型變更、語句範例、發佈及從端點查詢收集資料的反覆循環中學習時，成效最佳。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: cc994875e39410ecf01417c4d08248d5bc7d034a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589817"
---
# <a name="iterative-app-design-for-luis"></a>LUIS 的反復應用程式設計

Language Understanding （LUIS）應用程式會透過反復專案來學習和執行最有效率的方式。 以下是典型的反復專案迴圈：

* 建立新版本
* 編輯 LUIS 應用程式架構。 這包括：
    * 範例語句的意圖
    * 實體
    * 功能
* 定型、測試和發佈
    * 在主動式學習的預測端點上測試
* 從端點查詢收集資料

![撰寫循環](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>建立 LUIS 架構

應用程式的架構會定義使用者要求的內容（_意圖_或_意圖_），而意圖的哪些部分會提供用來協助判斷答案的詳細資料（稱為「_實體_」）。

應用程式架構必須是應用程式域特有的，才能判斷相關的單字和片語，以及判斷一般的文字順序。

範例語句代表應用程式在執行時間所預期的使用者輸入，例如辨識的語音或文字。

架構需要意圖，而且_應該有_實體。

### <a name="example-schema-of-intents"></a>意圖的範例架構

最常見的架構是使用意圖進行組織的意圖架構。 這種類型的架構會使用 LUIS 來判斷使用者的意圖。

如果意圖架構類型有助於 LUIS 判斷使用者的意圖，則可能會有實體。 例如，出貨實體（做為意圖的_機器學習功能_）可協助 LUIS 判斷出貨的意圖。

### <a name="example-schema-of-entities"></a>實體的範例架構

實體架構著重于實體，也就是從使用者語句中提取的資料。 例如，如果使用者說：「我想要訂購三個比薩」。 有兩個要解壓縮的實體：_三個_和_比薩_。 這些是用來協助滿足目的，也就是建立訂單。

針對實體架構，語句的目的對於用戶端應用程式而言較不重要。

組織實體架構的常見方法是將所有範例語句新增至**None**意圖。

### <a name="example-of-a-mixed-schema"></a>混合架構的範例

最強大且成熟的架構是具有完整範圍的實體和功能的意圖架構。 此架構可以做為意圖或實體架構，並隨著用戶端應用程式需要這些資訊而成長以包含兩者的概念。

## <a name="add-example-utterances-to-intents"></a>將範例語句新增至意圖

LUIS 需要每個**意圖**中的幾個範例語句。 範例語句需要有足夠的單字選擇和文字順序變化，才能判斷語句的用途。

> [!CAUTION]
> 請勿在 bulk 中新增範例語句。 從15到30個特定和不同的範例開始。

每個範例語句都必須有任何**必要的資料，才能使用實體來進行解壓縮**設計和標記。 **entities**

|Key 元素|目的|
|--|--|
|Intent|將使用者語句**分類**為單一意圖或動作。 例如 `BookFlight` 與 `GetWeather`。|
|單位|從需要的語句**解壓縮**資料，以完成意圖。 範例包括旅遊的日期和時間，以及位置。|

LUIS 應用程式可以藉由將語句指派給**None**意圖，來忽略與應用程式網域無關的語句。

## <a name="test-and-train-your-app"></a>測試和定型您的應用程式

在每個意圖中有15到30個不同的範例語句，並將所需的實體標示為的情況下，您需要測試並[訓練](luis-how-to-train.md)LUIS 應用程式。

## <a name="publish-to-a-prediction-endpoint"></a>發行至預測端點

LUIS 應用程式必須發佈，才能在清單[預測端點區域](luis-reference-regions.md)中使用。

## <a name="test-your-published-app"></a>測試已發佈的應用程式

您可以從 HTTPS 預測端點測試已發佈的 LUIS 應用程式。 從預測端點進行測試，可讓 LUIS 選擇任何具有低信賴度[審查](luis-how-to-review-endpoint-utterances.md)的語句。

## <a name="create-a-new-version-for-each-cycle"></a>為每個週期建立新的版本

每個版本都是 LUIS 應用程式時間的快照集。 在變更應用程式之前，請建立新版本。 您可以更輕鬆地回到較舊的版本，而不是嘗試移除意圖，並語句到先前的狀態。

版本識別碼由字元、數字或 '.' 所組成，且長度不可超過 10 個字元。

初始版本 (0.1) 是預設的作用中版本。

### <a name="begin-by-cloning-an-existing-version"></a>從複製現有版本開始

複製現有的版本，做為每個新版本的起點。 複製版本之後，新版本就會變成作用中**版本。**

### <a name="publishing-slots"></a>發行位置

您可以發行至階段和（或）生產位置。 每個位置的版本可以不同，也可以相同。 這適用于在發行至生產環境之前驗證變更，這適用于 bot 或其他 LUIS 呼叫應用程式。

定型版本不會自動在您的 LUIS 應用程式的[端點](luis-glossary.md#endpoint)上提供。 您必須[發佈](luis-how-to-publish-app.md)或重新發佈版本，才能在您的 LUIS 應用程式端點上使用。 您可以發行至**預備**和**生產環境**，讓您可以在端點上使用兩個版本的應用程式。 如果端點需要有更多版本的應用程式，您應該匯出版本，並將其重新匯入至新的應用程式。 新應用程式會有不同的應用程式識別碼。

### <a name="import-and-export-a-version"></a>匯入及匯出版本

您可以在應用層級匯入版本。 該版本會變成作用中版本，並在應用程式檔的屬性中使用版本識別碼 `versionId` 。 您也可以在版本層級匯入到現有的應用程式。 新版本會變成作用中版本。

您也可以在應用程式或版本層級匯出版本。 唯一的差別在於，在應用程式層級匯出的版本是目前作用中的版本，而在版本層級，您則可以在 [[Settings](luis-how-to-manage-versions.md)****] \(設定\) 頁面上選擇任何要匯出的版本。

匯出的檔案**不**包含：

* 機器學習的資訊，因為應用程式會在匯入後重新訓練
* 參與者資訊

若要備份 LUIS 應用程式架構，請從[LUIS 入口網站](https://www.luis.ai/applications)匯出版本。

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>使用版本和參與者管理參與者變更

LUIS 會藉由提供 Azure 資源層級許可權，來使用應用程式的參與者概念。 將此概念與版本控制結合，以提供目標共同作業。

使用下列技術來管理應用程式的參與者變更。

### <a name="manage-multiple-versions-inside-the-same-app"></a>管理相同應用程式內的多個版本

一開始會從每位作者的基底版本[複製](luis-how-to-manage-versions.md#clone-a-version)。

每位作者都會對自己的應用程式版本進行變更。 當作者對模型感到滿意時，請將新版本匯出至 JSON 檔案。

已匯出的應用程式（json 或 lu 檔案）可以針對變更進行比較。 結合檔案以建立新版本的單一檔案。 變更 `versionId` 屬性以表示新的合併版本。 將該版本匯入至原始應用程式。

此方法可讓您有一個作用中版本、一個預備版本，以及一個已發佈版本。 您可以在 [[互動式測試] 窗格](luis-interactive-test.md)中，將作用中版本的結果與已發行的版本（階段或生產）做比較。

### <a name="manage-multiple-versions-as-apps"></a>以應用程式方式管理多個檔案

[匯出](luis-how-to-manage-versions.md#export-version)基底版本。 每個作者都匯入版本。 匯入應用程式的人即為該版本的擁有者。 當他們修改完應用程式時，請匯出版本。

匯出的應用程式會是 JSON 格式的檔案，可用來與基底匯出版本比較變更。 結合檔案以建立新版本的單一 JSON 檔案。 變更 JSON 中的 **versionId** 屬性，以表示新的合併版本。 將該版本匯入至原始應用程式。

深入瞭解如何[撰寫來自共同作業者的投稿](luis-how-to-collaborate.md)。

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>審查端點語句以開始新的反復迴圈

當您完成反復專案迴圈時，就可以重複此程式。 一開始請先複習以低信賴度標記的[預測端點語句](luis-how-to-review-endpoint-utterances.md)LUIS。 請檢查這些語句是否有正確的預測意圖，以及是否已解壓縮正確且完整的實體。 在您審查並接受變更之後，審核清單應該是空的。

## <a name="next-steps"></a>後續步驟

了解[共同作業](luis-concept-keys.md)的相關概念。
