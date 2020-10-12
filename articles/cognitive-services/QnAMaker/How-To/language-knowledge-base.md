---
title: 語言概念-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 支援多種語言的知識庫內容。 不過，個別的 QnA Maker 服務均應保留給單一語言使用。 第一個建立的知識庫（以特定的 QnA Maker 服務為目標）會設定該服務的語言。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: b0d4250a6659996187923905955a9825a44cea42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132614"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>QnA Maker 知識庫內容的語言支援

當您在資源中建立第一個知識庫時，會選取服務的語言。 資源中的所有其他知識庫都必須採用相同的語言。

語言會決定 QnA Maker 提供來回應使用者查詢的結果相關性。

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>資源中的所有知識庫都有一種語言

QnA Maker 可讓您在建立第一個知識庫時選取 QnA 服務的語言。 QnA Maker 資源中的所有知識庫，全都必須使用相同的語言。 此語言無法變更。

在一個資源中以不同語言建立知識庫，會對使用者查詢所提供之結果 QnA Maker 的相關性造成負面影響。

請參閱支援的 [語言](../overview/language-support.md#languages-supported) 清單，以及語言對比對 [和相關性](#query-matching-and-relevance)的影響。

## <a name="select-language-when-creating-first-knowledge-base"></a>在建立第一個知識庫時選取語言

[語言選擇] 是在資源中建立第一個知識庫之步驟的一部分。

![QnA Maker 入口網站螢幕擷取畫面：選取第一個知識庫的語言](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>查詢比對和相關性
QnA Maker 相依于 [Azure 認知搜尋語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support) 來提供結果。

雖然 Azure 認知搜尋功能與支援的語言相等，但 QnA Maker 具有位於 Azure 搜尋服務結果上方的額外 ranker。 在此 ranker 模型中，我們會以下列語言使用一些特殊的語義和單字型功能。

|具有其他 ranker 的語言|
|--|
|中文|
|捷克文|
|荷蘭文|
|英文|
|法文|
|德文|
|匈牙利文|
|義大利文|
|日文|
|韓文|
|波蘭文|
|葡萄牙文|
|西班牙文|
|瑞典文|

這項額外的排名是 QnA Maker 的 ranker 內部工作。

## <a name="verify-language"></a>驗證語言

您可以從 QnA Maker 中的 [服務設定] 頁面，確認 QnA Maker 資源的語言。

![QnA Maker 服務設定頁面的入口網站螢幕擷取畫面](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [移轉知識庫](../Tutorials/migrate-knowledge-base.md)
