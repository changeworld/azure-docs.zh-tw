---
title: 將語言分析器新增至字串欄位
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋中非英文查詢和索引的多重語言詞法分析。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 555709776c88dd3003e400bbcefe2ec1cfa0f4af
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934164"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>將語言分析器新增至 Azure 認知搜尋索引中的字串欄位

「語言分析器」是特定類型的[文字分析器](search-analyzers.md)，能使用目標語言的語言規則來執行語彙分析。 每個可搜尋的欄位都會有 **analyzer** 屬性。 如果您的內容包含翻譯的字串（例如英文和中文文字的個別欄位），您可以在每個欄位上指定語言分析器，以存取這些分析器的豐富語言功能。

## <a name="when-to-use-a-language-analyzer"></a>使用語言分析器的時機

當 word 或句子結構的認知將值新增至文字剖析時，您應該考慮使用語言分析器。 常見的範例是將不規則動詞形式的關聯 ( 「攜帶」和「將) 或複數名詞 ( 「老鼠」和「滑鼠」 ) 。 在沒有語言感知的情況下，這些字串只會在實體特性上進行剖析，而無法攔截連接。 因為較大的文字區塊可能具有此內容，所以包含描述、評論或摘要的欄位是語言分析器的絕佳候選項目。

當內容包含非西歐語言字串時，您也應該考慮語言分析器。 雖然 [預設分析器](search-analyzers.md#default-analyzer) 與語言無關，但使用空格和特殊字元的概念 (連字號和斜線) 分隔字串，通常比非西方語言更適用西方語言。 

例如，在中文、日文、韓文 (CJK) 和其他亞洲語言中，空格不一定是單字分隔符號。 請考慮下列日文字串。 因為它沒有空格，所以與語言無關的分析器可能會將整個字串分析為一個標記，事實上字串實際上是一個片語。

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

在上述範例中，成功的查詢必須包含完整權杖或使用後置萬用字元的部分權杖，以產生非自然和限制搜尋體驗。

更好的體驗是搜尋個別的單字：明るい (鮮) 、私たちの () 、銀河系 (Galaxy) 。 使用認知搜尋中提供的其中一個日文分析器比較有可能解除鎖定這種行為，因為這些分析器可以更妥善地將文字區塊分割成目的語言中有意義的文字。

## <a name="comparing-lucene-and-microsoft-analyzers"></a>比較 Lucene 和 Microsoft 分析器

Azure 認知搜尋支援 Lucene 所支援的35語言分析器，以及由 Office 和 Bing 使用的專屬 Microsoft 自然語言處理技術所支援的50語言分析器。

一些開發人員可能偏好使用更熟悉、簡單且開放原始碼的 Lucene 解決方案。 Lucene 語言分析器速度較快，但 Microsoft 分析器具備進階功能，例如詞形歸併還原、複合字詞拆分 (適用於德文、丹麥文、荷蘭文、瑞典文、挪威文、愛沙尼亞文、芬蘭文、匈牙利文、斯洛伐克文等語言) 和實體辨識 (URL、電子郵件、日期、數字)。 如果可以，您應該進行 Microsoft 和 Lucene 分析器的比較，以決定哪一個比較適合。 

使用 Microsoft 分析器編製索引，平均速度約比其 Lucene 對等項目慢兩到三倍，視語言而定。 平均大小的查詢應不至大幅影響搜尋效能。 

### <a name="english-analyzers"></a>英文分析器

預設分析器是標準 Lucene，其可用於英文，但效果較不如 Lucene 的英文分析器或 Microsoft 的英文分析器。 
 
+ Lucene 的英文分析器能擴充標準分析器。 它會從字詞中移除所有格 (結尾的 's)、為每個 Porter 詞幹演算法套用詞幹，然後移除英文停用字詞。  

+ Microsoft 的英文分析器會執行詞形的歸併還原，而不是詞幹分析。 這表示它可以更妥善地處理屈折變化和不規則的文字表單，以產生更相關的搜尋結果 

## <a name="configuring-analyzers"></a>設定分析器

語言分析器是以現況使用。 針對索引定義中的每一個欄位，您可以將 **analyzer** 屬性設為能指定語言和語言堆疊 (Microsoft 或 Lucene) 的分析器名稱。 搜尋與編制該欄位索引時會套用相同的分析器。 例如，您可以有個別適用於英文、法文及西班牙文旅館說明的欄位，這些欄位會在相同的索引中並列存在。

> [!NOTE]
> 在編制索引時，不能使用不同的語言分析器，而是在欄位的查詢時使用。 這項功能已保留給 [自訂分析器](index-add-custom-analyzers.md)。 基於這個理由，如果您嘗試將 **>searchanalyzer** 或 **>indexanalyzer** 屬性設定為語言分析器的名稱，REST API 將會傳回錯誤回應。 您必須改為使用 **分析器** 屬性。

使用 **searchFields** 查詢參數來指定針對查詢所要搜尋的語言特定欄位。 您可以在 [搜尋檔](/rest/api/searchservice/search-documents)中查看包含分析器屬性的查詢範例。 

如需索引屬性的詳細資訊，請參閱 [建立索引 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/create-index)。 如需 Azure 認知搜尋中分析的詳細資訊，請參閱 [Azure 認知搜尋中的分析器](./search-analyzers.md)。

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>語言分析器清單 
 以下是支援的語言清單與 Lucene 和 Microsoft 的分析器名稱。  

| 語言 | Microsoft 分析器名稱 | Lucene 分析器名稱 |
|--|--|--|
| 阿拉伯文 | ar.microsoft | ar.lucene |
| 亞美尼亞文 |  | hy.lucene |  |
| 孟加拉文 | bn.microsoft |  |  |
| 巴斯克文 |  | eu.lucene |  |
| 保加利亞文 | bg.microsoft | bg.lucene |  |
| 卡達隆尼亞文 | ca.microsoft | ca.lucene |  |
| 簡體中文 | zh-Hans.microsoft | zh-Hans.lucene |  |
| 繁體中文 | zh-Hant.microsoft | zh-Hant.lucene |  |
| 克羅埃西亞文 | hr.microsoft |  |  |
| 捷克文 | cs.microsoft | cs.lucene |  |
| 丹麥文 | da.microsoft | da.lucene |  |
| 荷蘭文 | nl.microsoft | nl.lucene |  |
| 英文 | en.microsoft | en.lucene |  |
| 愛沙尼亞文 | et.microsoft |  |  |
| 芬蘭文 | fi.microsoft | fi.lucene |  |
| 法文 | fr.microsoft | fr.lucene |  |
| 加里斯亞文 |  | gl.lucene |  |
| 德文 | de.microsoft | de.lucene |  |
| 希臘文 | el.microsoft | el.lucene |  |
| 古吉拉特文 | gu.microsoft |  |  |
| Hebrew | he.microsoft |  |  |
| Hindi | hi.microsoft | hi.lucene |  |
| 匈牙利文 | hu.microsoft | hu.lucene |  |
| 冰島文 | is.microsoft |  |  |
| 印尼文 (Bahasa) | id.microsoft | id.lucene |  |
| 愛爾蘭文 |  | ga.lucene |  |
| 義大利文 | it.microsoft | it.lucene |  |
| 日文 | ja.microsoft | ja.lucene |  |
| 坎那達文 | kn。 microsoft |  |  |
| 韓文 | ko.microsoft | ko.lucene |  |
| 拉脫維亞文 | lv.microsoft | lv.lucene |  |
| 立陶宛文 | lt.microsoft |  |  |
| 馬來亞拉姆文 | ml.microsoft |  |  |
| 馬來文 (拉丁) | ms.microsoft |  |  |
| 馬拉地文 | mr.microsoft |  |  |
| 挪威文 | nb.microsoft | no.lucene |  |
| 波斯文 |  | fa.lucene |  |
| 波蘭文 | pl.microsoft | pl.lucene |  |
| 葡萄牙文 (巴西) | pt-Br.microsoft | pt-Br.lucene |  |
| 葡萄牙文 (葡萄牙) | pt-Pt.microsoft | pt-Pt.lucene |  |
| 旁遮普文 | pa.microsoft |  |  |
| 羅馬尼亞文 | ro.microsoft | ro.lucene |  |
| 俄文 | ru.microsoft | ru.lucene |  |
| 塞爾維亞文 (斯拉夫) | sr-cyrillic.microsoft |  |  |
| 塞爾維亞文 (拉丁) | sr-latin.microsoft |  |  |
| 斯洛伐克文 | sk.microsoft |  |  |
| 斯洛維尼亞文 | sl.microsoft |  |  |
| 西班牙文 | es.microsoft | es.lucene |  |
| 瑞典文 | sv.microsoft | sv.lucene |  |
| 坦米爾文 | ta.microsoft |  |  |
| 泰盧固文 | te.microsoft |  |  |
| 泰文 | th.microsoft | th.lucene |  |
| 土耳其文 | tr.microsoft | tr.lucene |  |
| 烏克蘭文 | uk.microsoft |  |  |
| 烏都文 | ur.microsoft |  |  |
| 越南文 | vi.microsoft |  |  |

 所有名稱加上 **Lucene** 註解的分析器都是由 [Apache Lucene 的語言分析器](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ) \(英文\) 所提供。

## <a name="see-also"></a>請參閱  

+ [建立索引 &#40;Azure 認知搜尋 REST API&#41;](/rest/api/searchservice/create-index)  

+ [LexicalAnalyzerName 類別](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)