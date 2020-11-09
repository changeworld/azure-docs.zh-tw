---
title: 語言支援 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 針對您的知識庫支援的文化特性、自然語言清單。 請勿在相同知識庫中混用語言。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: dc910c01c401468a3dae392a6318344bee25efb7
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375632"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>QnA Maker 資源和知識庫的語言支援

本文說明 QnA Maker 資源和知識庫的語言支援選項。 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (穩定版本) ](#tab/v1)

當您在資源中建立第一個知識庫時，會選取服務的語言。 資源中的所有其他知識庫都必須採用相同的語言。 

語言會決定 QnA Maker 提供來回應使用者查詢的結果相關性。 QnA Maker 資源和該資源內的所有知識庫都支援單一語言。 您必須使用單一語言來提供查詢的最佳解答結果。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 受控 (預覽版本) ](#tab/v2)

在 QnA Maker 管理的中，您可以選擇在個別的知識庫層級進行語言設定。 只有在服務的知識庫中，才能啟用此設定。 設定之後，就無法變更服務的語言設定。 

如果您選取 [語言設定] 作為 [知識庫特定]，就可以在服務本身中建立不同語言的知識庫。 

---

## <a name="single-language-per-resource"></a>每個資源的單一語言

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (穩定版本) ](#tab/v1)

請考慮下列事項：

* QnA Maker 服務及其所有知識庫都只支援一種語言。
* 建立服務的第一個知識庫時，會明確地設定語言。
* 語言取決於建立知識庫時新增的檔案和 Url。
* 無法變更服務中任何其他知識庫的語言。
* 認知搜尋服務 (ranker #1) 和 QnA Maker 服務 (ranker #2) ，以產生最佳的查詢答案。

# <a name="qnamaker-managed-preview"></a>[QnAMaker 受控 (預覽) ](#tab/v2)
![QnA Maker 管理的語言設定](../media/language-support/language-setting-managed.png)

如果您 **未選取核取方塊以啟用每個知識庫的語言設定** ，請考慮下列各項： 
* QnA Maker 服務及其所有知識庫都只支援一種語言。
* 建立服務的第一個知識庫時，會明確設定語言
* 語言取決於建立知識庫時新增的檔案和 Url。
* 無法變更服務中任何其他知識庫的語言
* 認知搜尋服務 (ranker #1) 和 QnA Maker 服務 (ranker #2) ，以產生查詢的最佳答案，以使用語言

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>在一個 QnA Maker 資源中支援多種語言

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (穩定版本) ](#tab/v1)
目前正式推出的 (GA) 穩定版本中不支援這項功能。 簽出 QnA Maker 管理] 以測試此功能。 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 受控 (預覽版本) ](#tab/v2)
* 當您在服務中建立第一個知識庫時，您可以選擇啟用每個知識庫的語言設定。 選取此核取方塊，以在單一服務內建立屬於不同語言的知識庫。
* 建立第一個知識庫之後，就無法修改服務的 [語言] 設定選項。
* 如果您啟用每個知識庫專屬的語言設定，則不會有服務的一個測試索引，而是每個知識庫都有一個測試索引。 

![QnA Maker 管理的語言設定](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>在一個知識庫中支援多種語言

如果您需要支援包含數種語言的知識庫系統，您可以：

* 使用 [Translator 服務](../../translator/translator-info-overview.md) 將問題轉譯成單一語言，然後再將問題傳送至您的知識庫。 這可讓您專注于單一語言的品質，以及替代問題和解答的品質。
* 針對每個語言建立 QnA Maker 資源，以及該資源內的知識庫。 這可讓您管理個別的替代問題，以及針對每種語言更差別細微的解答文字。 這可提供您更大的彈性，但在所有語言的問題或答案變更時，需要更高的維護成本。


## <a name="languages-supported"></a>支援的語言

下列清單包含 QnA Maker 資源支援的語言。 

|語言|
|--|
|阿拉伯文|
|亞美尼亞文|
|孟加拉文|
|巴斯克文|
|保加利亞文|
|卡達隆尼亞文|
|簡體中文|
|繁體中文|
|克羅埃西亞文|
|捷克文|
|丹麥文|
|荷蘭文|
|英文|
|愛沙尼亞文|
|芬蘭文|
|法文|
|加利西亞文|
|德文|
|希臘文|
|古吉拉特文|
|Hebrew|
|Hindi|
|匈牙利文|
|冰島文|
|印尼文|
|愛爾蘭文|
|義大利文|
|日文|
|坎那達文|
|韓文|
|拉脫維亞文|
|立陶宛文|
|馬來亞拉姆文|
|馬來文|
|挪威文|
|波蘭文|
|葡萄牙文|
|旁遮普文|
|羅馬尼亞文|
|俄文|
|塞爾維亞文_斯拉夫|
|塞爾維亞文_拉丁|
|斯洛伐克文|
|斯洛維尼亞文|
|西班牙文|
|瑞典文|
|坦米爾文|
|泰盧固文|
|泰文|
|土耳其文|
|烏克蘭文|
|烏都文|
|越南文|

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

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [語言選擇](../how-to/language-knowledge-base.md)
