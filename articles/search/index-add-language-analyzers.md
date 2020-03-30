---
title: 將語言分析器添加到字串欄位
titleSuffix: Azure Cognitive Search
description: Azure 認知搜索中非英語查詢和索引的多語言詞彙文本分析。
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283143"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>將語言分析器添加到 Azure 認知搜索索引中的字串欄位

「語言分析器」** 是特定類型的[文字分析器](search-analyzers.md)，能使用目標語言的語言規則來執行語彙分析。 每個可搜尋的欄位都會有 **analyzer** 屬性。 如果您的索引包含已翻譯的字串 (例如適用於英文和中文文字的個別欄位)，您可以在每個欄位上指定語言分析器，以存取由那些分析器所提供的豐富語言功能。  

Azure 認知搜索支援由 Lucene 支援的 35 個分析器，以及由 Office 和必應中使用的專有 Microsoft 自然語言處理技術支援的 50 個分析器。

## <a name="comparing-analyzers"></a>比較分析器

一些開發人員可能偏好使用更熟悉、簡單且開放原始碼的 Lucene 解決方案。 Lucene 語言分析器速度較快，但 Microsoft 分析器具備進階功能，例如詞形歸併還原、複合字詞拆分 (適用於德文、丹麥文、荷蘭文、瑞典文、挪威文、愛沙尼亞文、芬蘭文、匈牙利文、斯洛伐克文等語言) 和實體辨識 (URL、電子郵件、日期、數字)。 如果可以，您應該進行 Microsoft 和 Lucene 分析器的比較，以決定哪一個比較適合。 

使用 Microsoft 分析器編製索引，平均速度約比其 Lucene 對等項目慢兩到三倍，視語言而定。 平均大小的查詢應不至大幅影響搜尋效能。 

### <a name="english-analyzers"></a>英文分析器

預設分析器是標準 Lucene，其可用於英文，但效果較不如 Lucene 的英文分析器或 Microsoft 的英文分析器。 
 
+ Lucene 的英文分析器能擴充標準分析器。 它會從字詞中移除所有格 (結尾的 's)、為每個 Porter 詞幹演算法套用詞幹，然後移除英文停用字詞。  

+ Microsoft 的英文分析器會執行詞形的歸併還原，而不是詞幹分析。 這意味著它可以更好地處理變形和不規則的單詞形式，從而產生更相關的搜尋結果 

## <a name="configuring-analyzers"></a>設定分析器

語言分析器是以現況使用。 針對索引定義中的每一個欄位，您可以將 **analyzer** 屬性設為能指定語言和語言堆疊 (Microsoft 或 Lucene) 的分析器名稱。 搜尋與編制該欄位索引時會套用相同的分析器。 例如，您可以有個別適用於英文、法文及西班牙文旅館說明的欄位，這些欄位會在相同的索引中並列存在。

> [!NOTE]
> 在索引時間不能使用與欄位查詢時間不同的語言分析器。 此功能保留給[自訂分析器](index-add-custom-analyzers.md)。 因此，如果您嘗試將**searchAnalyzer**或**indexAnalyzer**屬性設置為語言分析器的名稱，REST API 將返回錯誤回應。 您必須改用**分析器**屬性。

使用 **searchFields** 查詢參數來指定針對查詢所要搜尋的語言特定欄位。 您可以查看在[搜索文檔](https://docs.microsoft.com/rest/api/searchservice/search-documents)中包含分析器屬性的查詢示例。 

有關索引屬性的詳細資訊，請參閱[創建索引&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)。 有關 Azure 認知搜索中分析的詳細資訊，請參閱[Azure 認知搜索 中的分析器](https://docs.microsoft.com/azure/search/search-analyzers)。

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>語言分析器清單 
 以下是支援的語言清單與 Lucene 和 Microsoft 的分析器名稱。  

|語言|Microsoft 分析器名稱|Lucene 分析器名稱|  
|--------------|-----------------------------|--------------------------|  
|阿拉伯文|ar.microsoft|ar.lucene|  
|亞美尼亞文||hy.lucene|  
|孟加拉文|bn.microsoft||  
|巴斯克文||eu.lucene|  
|保加利亞文|bg.microsoft|bg.lucene|  
|卡達隆尼亞文|ca.microsoft|ca.lucene|  
|簡體中文|zh-Hans.microsoft|zh-Hans.lucene|  
|繁體中文|zh-Hant.microsoft|zh-Hant.lucene|  
|克羅埃西亞文|hr.microsoft||  
|捷克文|cs.microsoft|cs.lucene|  
|丹麥文|da.microsoft|da.lucene|  
|荷蘭文|nl.microsoft|nl.lucene|  
|英文|en.microsoft|en.lucene|  
|愛沙尼亞文|et.microsoft||  
|芬蘭文|fi.microsoft|fi.lucene|  
|法文|fr.microsoft|fr.lucene|  
|加利西亞文||gl.lucene|  
|德文|de.microsoft|de.lucene|  
|希臘文|el.microsoft|el.lucene|  
|古吉拉特文|gu.microsoft||  
|Hebrew|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|匈牙利文|hu.microsoft|hu.lucene|  
|冰島文|is.microsoft||  
|印尼文 (Bahasa)|id.microsoft|id.lucene|  
|愛爾蘭文||ga.lucene|  
|義大利文|it.microsoft|it.lucene|  
|日文|ja.microsoft|ja.lucene|  
|坎那達文|kn.微軟||  
|韓文|ko.microsoft|ko.lucene|  
|拉脫維亞文|lv.microsoft|lv.lucene|  
|立陶宛文|lt.microsoft||  
|馬來亞拉姆文|ml.microsoft||  
|馬來文 (拉丁)|ms.microsoft||  
|馬拉地文|mr.microsoft||  
|挪威文|nb.microsoft|no.lucene|  
|波斯文||fa.lucene|  
|波蘭文|pl.microsoft|pl.lucene|  
|葡萄牙文 (巴西)|pt-Br.microsoft|pt-Br.lucene|  
|葡萄牙文 (葡萄牙)|pt-Pt.microsoft|pt-Pt.lucene|  
|旁遮普文|pa.microsoft||  
|羅馬尼亞文|ro.microsoft|ro.lucene|  
|俄文|ru.microsoft|ru.lucene|  
|塞爾維亞文 (斯拉夫)|sr-cyrillic.microsoft||  
|塞爾維亞文 (拉丁)|sr-latin.microsoft||  
|斯洛伐克文|sk.microsoft||  
|斯洛維尼亞文|sl.microsoft||  
|西班牙文|es.microsoft|es.lucene|  
|瑞典文|sv.microsoft|sv.lucene|  
|坦米爾文|ta.microsoft||  
|泰盧固文|te.microsoft||  
|泰文|th.microsoft|th.lucene|  
|土耳其文|tr.microsoft|tr.lucene|  
|烏克蘭文|uk.microsoft||  
|烏都文|ur.microsoft||  
|越南文|vi.microsoft||  

 所有名稱加上 **Lucene** 註解的分析器都是由 [Apache Lucene 的語言分析器](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ) \(英文\) 所提供。

## <a name="see-also"></a>另請參閱  

+ [創建索引&#40;Azure 認知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName 類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername) \(英文\)  

