---
title: 語言概念 - QnA 製造商
titleSuffix: Azure Cognitive Services
description: QnA Maker 支援多種語言的知識庫內容。 不過，個別的 QnA Maker 服務均應保留給單一語言使用。 創建的第一個知識庫針對特定的 QnA Maker 服務，設置該服務的語言。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221445"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>QnA Maker 知識庫內容的語言支援

在創建資源中的第一個知識庫時，將選擇服務的語言。 資源中的所有附加知識庫必須使用同一語言。

語言確定 QnA Maker 為回應使用者查詢而提供的結果的相關性。

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>資源中所有知識庫的一種語言

QnA Maker 允許您為 QnA 服務選擇語言，同時創建第一個知識庫。 對於 QnA Maker 資源中的所有知識庫，它們都必須使用同一語言。 無法更改此語言。

在一個資源中以不同語言創建知識庫會對 QnA Maker 回應使用者查詢的結果的相關性產生負面影響。

查看[支援的語言](../overview/language-support.md#languages-supported)清單以及語言如何影響[匹配和相關性](#query-matching-and-relevance)。

## <a name="select-language-when-creating-first-knowledge-base"></a>創建第一個知識庫時選擇語言

語言選擇是創建資源中第一個知識庫的步驟的一部分。

![為第一知識庫選擇語言的 QnA Maker 門戶螢幕截圖](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>查詢比對和相關性
QnA Maker 依賴于[Azure 認知搜索語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)來提供結果。

雖然 Azure 認知搜索功能與支援的語言不相上下，但 QnA Maker 具有位於 Azure 搜尋結果上方的附加排名。 在此排名模型中，我們在以下語言中使用一些特殊的語義和基於單詞的功能。

|具有附加排名語言的語言|
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

此附加排名是 QnA 製造商的記號的內部工作。

## <a name="verify-language"></a>驗證語言

您可以在 QnA Maker 中的服務設置頁面驗證 QnA Maker 資源的語言。

![服務設置頁面的 QnA Maker 門戶螢幕截圖](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移轉知識庫](../Tutorials/migrate-knowledge-base.md)
