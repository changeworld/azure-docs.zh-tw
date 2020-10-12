---
title: 反復式應用程式設計-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 在模型變更、語句範例、發佈及從端點查詢收集資料的反覆循環中學習時，成效最佳。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 753d214b520affb59722bc29dbabc50c6e5968f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538716"
---
# <a name="iterative-app-design-for-luis"></a>LUIS 的反復式應用程式設計

Language Understanding (LUIS) 應用程式會使用反復專案來學習和執行最有效率的工作。 以下是典型的反復專案迴圈：

* 建立新版本
* 編輯 LUIS 應用程式架構。 這包括：
    * 使用範例語句的意圖
    * 實體
    * 特性
* 定型、測試和發佈
    * 在預測端點上測試主動式學習
* 從端點查詢收集資料

![撰寫循環](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>建立 LUIS 架構

應用程式的架構會定義使用者要求 (_意圖_ 或 _意圖_ ) 以及意圖的哪些部分提供詳細資料 (稱為 _實體_) ，可用來協助判斷答案。

應用程式架構必須專屬於應用程式域，以決定相關的單字和片語，以及決定一般文字順序。

範例語句代表應用程式在執行時間所預期的使用者輸入，例如可辨識的語音或文字。

架構需要意圖，而且 _應該有_ 實體。

### <a name="example-schema-of-intents"></a>意圖的範例架構

最常見的架構是使用意圖組織的意圖架構。 這種類型的架構使用 LUIS 來判斷使用者的意圖。

意圖架構型別可能會有可協助 LUIS 判斷使用者意圖的實體。 例如，出貨實體 (為意圖的 _機器學習功能_) 協助 LUIS 判斷出貨的目的。

### <a name="example-schema-of-entities"></a>實體的範例架構

實體架構會著重于實體，也就是從使用者語句中解壓縮的資料。 例如，如果使用者說：「我想要訂購三個比薩」。 有兩個實體會被解壓縮： _三個_ 和 _比薩_。 這些是用來協助滿足目的，也就是要進行訂單。

對於實體架構而言，語句的意圖對於用戶端應用程式來說比較不重要。

組織實體架構的常見方法是將所有範例語句新增至「 **無** 」意圖。

### <a name="example-of-a-mixed-schema"></a>混合架構的範例

最強大且最成熟的架構是具有完整範圍之實體和功能的意圖架構。 此架構可以作為意圖或實體架構來開始，並隨著用戶端應用程式需要這些資訊，而擴大為包含兩者的概念。

## <a name="add-example-utterances-to-intents"></a>將範例語句新增至意圖

LUIS 需要每個 **意圖**中語句的一些範例。 範例語句需要足夠的單字選擇和文字順序，才能判斷語句的用途。

> [!CAUTION]
> 請勿大量新增範例語句。 從15到30個特定和不同的範例開始。

每個範例語句都需要有任何 **必要的資料，以** 使用 **實體**進行設計和標記。

|Key 元素|目的|
|--|--|
|Intent|將使用者語句**分類**成單一意圖或動作。 例如 `BookFlight` 與 `GetWeather`。|
|實體|將完成意圖所需的語句的資料**解壓縮**。 範例包括出差的日期和時間，以及位置。|

LUIS 應用程式的設計可將語句指派給 **None** 意圖，以忽略與應用程式網域無關的語句。

## <a name="test-and-train-your-app"></a>測試和定型您的應用程式

在每個意圖中有15到30個不同的範例語句，並標示必要實體時，您需要測試並 [定型](luis-how-to-train.md) 您的 LUIS 應用程式。

## <a name="publish-to-a-prediction-endpoint"></a>發佈至預測端點

您必須發佈 LUIS 應用程式，才能在清單 [預測端點區域](luis-reference-regions.md)中使用。

## <a name="test-your-published-app"></a>測試已發佈的應用程式

您可以從 HTTPS 預測端點測試已發佈的 LUIS 應用程式。 從預測端點進行測試可讓 LUIS 選擇任何具有低信賴度的語句來進行 [審查](luis-how-to-review-endpoint-utterances.md)。

## <a name="create-a-new-version-for-each-cycle"></a>為每個週期建立新的版本

每個版本都是 LUIS 應用程式的時間快照。 在變更應用程式之前，請建立新版本。 您可以更輕鬆地回到較舊的版本，而不是嘗試移除意圖，並語句至先前的狀態。

版本識別碼由字元、數字或 '.' 所組成，且長度不可超過 10 個字元。

初始版本 (0.1) 是預設的作用中版本。

### <a name="begin-by-cloning-an-existing-version"></a>開始複製現有的版本

複製現有的版本做為每個新版本的起點。 複製版本之後，新版本會變成作用**中版本。**

### <a name="publishing-slots"></a>發佈位置

您可以發佈至階段和/或生產位置。 每個位置的版本可以不同，也可以相同。 這在發行至生產環境之前（可供 bot 或其他 LUIS 呼叫應用程式使用）之前，可用於驗證變更。

LUIS 應用程式的 [端點](luis-glossary.md#endpoint)不會自動提供已定型的版本。 您必須 [發佈](luis-how-to-publish-app.md) 或重新發佈版本，才能在您的 LUIS 應用程式端點上使用。 您可以發佈至 **預備** 和 **生產環境**，讓您可以在端點上取得兩個可用的應用程式版本。 如果端點需要有更多版本的應用程式，您應該匯出版本並將其重新匯入至新的應用程式。 新應用程式會有不同的應用程式識別碼。

### <a name="import-a-version"></a>匯入版本

版本可以匯 **入** 為新的：
* 應用程式，具有新的應用程式識別碼
* 現有應用程式的版本

該版本會變成作用中的版本，並在應用程式檔的屬性中使用版本識別碼 `versionId` 。

### <a name="export-a-version"></a>匯出版本

您可以在應用層級或版本層級從 LUIS 入口網站 **匯出** 版本：

* 應用層級-在**我的應用程式**] 頁面上選取應用程式，然後選取 [**匯出**]
* 版本層級-選取 **我的應用程式** 頁面上的應用程式連結，選取 [ **管理**]，選取 **版本**

唯一的差別是應用層級的匯出版本是目前使用中的版本，而在版本層級，您可以在 [ **[設定](luis-how-to-manage-versions.md)** ] 頁面上選擇要匯出的任何版本。

匯出的檔案 **不** 包含：

* 機器學習服務資訊，因為應用程式會在匯入後重新定型
* 參與者資訊

若要備份您的 LUIS 應用程式架構，請從 [LUIS 入口網站](https://www.luis.ai/applications)匯出版本。

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>使用版本和參與者管理參與者變更

LUIS 藉由提供 Azure 資源層級許可權，來使用應用程式的參與者概念。 結合此概念與版本設定，以提供目標共同作業。

使用下列技術來管理應用程式的參與者變更。

### <a name="manage-multiple-versions-inside-the-same-app"></a>管理相同應用程式內的多個版本

首先，從每個作者的基底版本 [複製](luis-how-to-manage-versions.md#clone-a-version) 。

每位作者都會對自己的應用程式版本進行變更。 當作者對模型感到滿意後，請將新版本匯出至 JSON 檔案。

您 `.json` 可以比較匯出的應用程式或檔案 `.lu` 來進行變更。 合併檔案以建立新版本的單一檔案。 變更 `versionId` 屬性以表示新的合併版本。 將該版本匯入至原始應用程式。

此方法可讓您有一個作用中版本、一個預備版本，以及一個已發佈版本。 您可以在 [ [互動式測試] 窗格](luis-interactive-test.md)中，將作用中版本的結果與已發佈的 (階段或生產) 的版本進行比較。

### <a name="manage-multiple-versions-as-apps"></a>以應用程式方式管理多個檔案

[匯出](luis-how-to-manage-versions.md#export-version)基底版本。 每個作者都匯入版本。 匯入應用程式的人即為該版本的擁有者。 當他們修改完應用程式時，請匯出版本。

匯出的應用程式會是 JSON 格式的檔案，可用來與基底匯出版本比較變更。 結合檔案以建立新版本的單一 JSON 檔案。 變更 JSON 中的 **versionId** 屬性，以表示新的合併版本。 將該版本匯入至原始應用程式。

深入瞭解如何 [撰寫來自共同作業者的貢獻](luis-how-to-collaborate.md)。

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>查看端點語句以開始新的反復迴圈

當您完成反覆運算迴圈時，您可以重複此程式。 首先，請先審核標示低信賴度的 [預測端點語句](luis-how-to-review-endpoint-utterances.md) LUIS。 請檢查這些語句是否有正確的預測意圖和正確且完整的實體已解壓縮。 在您檢查並接受變更之後，審核清單應該是空的。

## <a name="next-steps"></a>後續步驟

了解[共同作業](luis-how-to-azure-subscription.md)的相關概念。
