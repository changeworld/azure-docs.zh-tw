---
title: 反覆運算應用設計 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 在模型變更、語句範例、發佈及從端點查詢收集資料的反覆循環中學習時，成效最佳。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74422597"
---
# <a name="iterative-app-design-for-luis"></a>LUIS 的反覆運算應用設計

語言理解 （LUIS） 應用通過反覆運算最有效地學習並執行。 下面是一個典型的反覆運算週期：

* 創建新版本
* 編輯 LUIS 應用架構。 這包括：
    * 具有示例陳述的意圖
    * 實體
    * 特性
* 訓練、測試和發佈
    * 在預測終結點測試活動學習
* 從終結點查詢收集資料

![撰寫循環](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>構建 LUIS 架構

應用的架構定義使用者要求的內容（_意圖_或_意圖_），以及意圖中哪些部分提供用於説明確定答案的詳細資訊（稱為_實體_）。 

應用架構必須特定于應用域，以確定相關的單詞和短語，以及確定典型的單詞排序。 

示例陳述表示應用在運行時期望的使用者輸入（如可識別的語音或文本）。 

架構需要意圖，_並且應該有_實體。 

### <a name="example-schema-of-intents"></a>意圖模式示例

最常見的架構是按意圖組織的意圖架構。 這種類型的架構使用 LUIS 來確定使用者的意圖。 

如果意圖架構類型説明 LUIS 確定使用者的意圖，則該模式類型可能具有實體。 例如，裝運實體（作為意向的描述項）可説明 LUIS 確定裝運意圖。 

### <a name="example-schema-of-entities"></a>實體模式示例

實體架構側重于實體，即從使用者話語中提取的資料。 例如，如果使用者說"我想訂購三個比薩餅"。 有兩個實體將被提取：_三_個和_比薩餅_。 這些用於説明實現意圖，即下訂單。 

對於實體架構，話語的意圖對用戶端應用程式不太重要。 

組織實體架構的一種常見方法是將所有示例陳述添加到**None**意圖。 

### <a name="example-of-a-mixed-schema"></a>混合架構的示例

最強大和成熟的架構是具有各種實體和功能的意圖架構。 此架構可以開始作為意圖或實體架構，並增長到包括兩者的概念，因為用戶端應用程式需要這些資訊段。 

## <a name="add-example-utterances-to-intents"></a>將示例陳述添加到意圖

LUIS 在每個**意圖**中都需要一些示例陳述。 示例陳述需要足夠的單詞選擇和單詞順序的變體，以便能夠確定話語的用意是目的。 

> [!CAUTION]
> 不要大量新增示例陳述。 從 15 到 30 個特定且不同的示例開始。 

每個示例陳述都需要具有任何**所需的資料來提取****設計和標記**實體的資料。 

|Key 元素|目的|
|--|--|
|Intent|**將**使用者話語分類為單個意圖或操作。 例如 `BookFlight` 與 `GetWeather`。|
|單位|從完成意圖所需的陳述**中提取**資料。 示例包括旅行的日期和時間以及位置。|

LUIS 應用可以通過將陳述分配給 **"無**"意圖來忽略與應用域無關的表述。

## <a name="test-and-train-your-app"></a>測試和訓練應用

在每個意圖中包含 15 到 30 個不同的示例陳述，並標記了所需的實體後，需要測試和[訓練](luis-how-to-train.md)LUIS 應用。 

## <a name="publish-to-a-prediction-endpoint"></a>發佈到預測終結點

必須發佈 LUIS 應用，以便在清單[預測終結點區域](luis-reference-regions.md)中可供您使用。

## <a name="test-your-published-app"></a>測試已發佈的應用程式

可以從 HTTPS 預測終結點測試已發佈的 LUIS 應用。 從預測終結點進行測試允許 LUIS 選擇任何低可信度的表述進行[審核](luis-how-to-review-endpoint-utterances.md)。  

## <a name="create-a-new-version-for-each-cycle"></a>為每個週期創建新版本

每個版本都是 LUIS 應用的時間快照。 在變更應用程式之前，請建立新版本。 回到舊版本比嘗試刪除以前狀態的意圖和陳述更容易。

版本識別碼由字元、數字或 '.' 所組成，且長度不可超過 10 個字元。

初始版本 (0.1) 是預設的作用中版本。 

### <a name="begin-by-cloning-an-existing-version"></a>從克隆現有版本開始

克隆現有版本，用作每個新版本的起點。 克隆版本後，新版本將成為**活動**版本。 

### <a name="publishing-slots"></a>發佈槽

您可以發佈到舞臺和/或生產插槽。 每個位置的版本可以不同，也可以相同。 這對於在發佈到生產之前驗證更改非常有用，該更改可用於機器人或其他 LUIS 調用應用。 

已訓練的版本在 LUIS 應用的[終結點](luis-glossary.md#endpoint)上不會自動提供。 您必須[發佈](luis-how-to-publish-app.md)或重新發佈版本，以便在 LUIS 應用終結點上提供版本。 您可以發佈到**暫存****和生產，** 為您提供兩個版本的應用在終結點上可用。 如果需要在終結點上提供應用的更多版本，則應匯出該版本並將其重新導入到新應用。 新應用程式會有不同的應用程式識別碼。

### <a name="import-and-export-a-version"></a>匯入及匯出版本

可以在應用級別導入版本。 該版本將成為活動版本，並在應用檔`versionId`的屬性中使用版本 ID。 您還可以在版本級別導入現有應用。 新版本會變成作用中版本。 

也可以在應用或版本級別匯出版本。 唯一的差別在於，在應用程式層級匯出的版本是目前作用中的版本，而在版本層級，您則可以在 [[Settings](luis-how-to-manage-versions.md)****] \(設定\) 頁面上選擇任何要匯出的版本。 

匯出的檔**不包含**：

* 機器學習資訊，因為應用在導入後會重新訓練
* 參與者資訊

為了備份 LUIS 應用架構，請從[LUIS 門戶](https://www.luis.ai/applications)匯出版本。

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>使用版本和參與者管理參與者更改

LUIS 通過提供 Azure 資源級許可權，使用應用參與者的概念。 將這個概念與版本控制相結合，提供有針對性的協作。 

使用以下技術管理對應用的參與者更改。

### <a name="manage-multiple-versions-inside-the-same-app"></a>管理相同應用程式內的多個版本

首先從每個作者的基版本[進行克隆](luis-how-to-manage-versions.md#clone-a-version)。 

每個作者都會更改自己的應用版本。 當作者對模型滿意時，將新版本匯出到 JSON 檔。  

匯出的應用程式 .json 或 .lu 檔可以比較更改。 合併檔以創建新版本的單個檔。 更改屬性`versionId`以表示新的合併版本。 將該版本匯入至原始應用程式。 

此方法可讓您有一個作用中版本、一個預備版本，以及一個已發佈版本。 您可以在[互動式測試窗格](luis-interactive-test.md)中將活動版本的結果與已發佈版本（階段或生產）進行比較。

### <a name="manage-multiple-versions-as-apps"></a>以應用程式方式管理多個檔案

[匯出](luis-how-to-manage-versions.md#export-version)基底版本。 每個作者都匯入版本。 匯入應用程式的人即為該版本的擁有者。 當他們修改完應用程式時，請匯出版本。 

匯出的應用程式會是 JSON 格式的檔案，可用來與基底匯出版本比較變更。 結合檔案以建立新版本的單一 JSON 檔案。 變更 JSON 中的 **versionId** 屬性，以表示新的合併版本。 將該版本匯入至原始應用程式。

詳細瞭解如何創作[來自協作者](luis-how-to-collaborate.md)的貢獻。

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>查看終結點陳述以開始新的反覆運算週期

完成反覆運算週期後，可以重複此過程。 首先查看以低置度標記[的 LUIS 預測終結點話語](luis-how-to-review-endpoint-utterances.md)。 檢查這些陳述，瞭解正確的預測意圖以及提取的正確和完整的實體。 查看並接受更改後，審核清單應為空。  

## <a name="next-steps"></a>後續步驟

了解[共同作業](luis-concept-keys.md)的相關概念。
