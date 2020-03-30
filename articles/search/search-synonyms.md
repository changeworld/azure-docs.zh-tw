---
title: 搜索索引上查詢擴展的同義字
titleSuffix: Azure Cognitive Search
description: 創建同義字映射以擴展 Azure 認知搜索索引上的搜索查詢範圍。 範圍會擴大納入您清單所提供的對等詞彙。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: aa573e84fa9fff83bd6a894f516ce5f67b3afa79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194337"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure 認知搜索中的同義字

搜尋引擎中與對等詞彙相關聯的同義字，讓使用者不必實際提供詞彙，就能以隱含方式擴充查詢範圍。 例如，給定詞彙「狗」與關聯的同義字「犬科動物」和「小狗」，任何包含「狗」、「犬科動物」和「小狗」的文件都會包含在查詢範圍內。

在 Azure 認知搜索中，同義字擴展在查詢時完成。 您可以在不中斷現有作業的情況下，新增同義字地圖至服務中。 您無需重建索引，就可以將 synonymMaps**** 屬性新增至欄位定義。

## <a name="create-synonyms"></a>創建同義字

創建同義字沒有門戶支援，但您可以使用 REST API 或 .NET SDK。 要開始使用 REST，我們建議使用此 API[使用 Postman](search-get-started-postman.md)和制定請求：[創建同義字映射](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)。 對於 C# 開發人員，可以使用 C# 開始在[Azure 認知搜索中添加同義字](search-synonyms-tutorial-sdk.md)。

或者，如果您使用[客戶管理的金鑰](search-security-manage-encryption-keys.md)進行服務端靜態加密，則可以將該保護應用於同義字映射的內容。

## <a name="use-synonyms"></a>使用同義字

在 Azure 認知搜索中，同義字支援基於您定義並上載到服務的同義字映射。 這些地圖由獨立資源構成 (例如索引或資料資源)，且可以在您搜尋服務索引中的任何可搜尋欄位使用。

同義字地圖和索引會分開維護。 一旦您定義同義字地圖，並上傳至服務後，您可以透過在欄位定義中新增 **synonymMaps** 屬性，啟用同義字功能。 建立、 更新及刪除同義字地圖永遠是全文件的作業，這表示您無法以累加方式建立、 更新或刪除同義字地圖中的部分內容。 即便只是更新一個項目也需要重新載入。

將同義字整合至您的搜尋應用程式需要兩個步驟：

1.  透過下列的 API 將同義字地圖新增至您的搜尋服務。  

2.  在索引定義中設定要使用同義字地圖的可搜尋欄位。

您可以為您的搜尋應用程式建立多個同義字地圖 (例如，如果您的應用程式支援多語言的客戶群，您可以建立不同語言的同義字地圖)。 目前，一個欄位只能使用一個同義字地圖。 您可以隨時更新欄位的 synonymMaps 屬性。

### <a name="synonymmaps-resource-apis"></a>SynonymMaps 資源 API

#### <a name="add-or-update-a-synonym-map-under-your-service-using-post-or-put"></a>使用 POST 或 PUT 在您的服務中新增或更新同義字地圖。

同義地圖會透過 POST 或 PUT 上傳至服務。 每個規則都必須以新行字元 ('\n') 分隔。 您可以在免費服務中定義每個同義字映射最多 5，000 個規則，在所有其他 SKU 中每個映射定義 20，000 個規則。 每條規則最多可以有 20 個擴充詞彙。

同義字地圖必須使用 Apache Solr 格式，以下會加以說明。 如果您有現有的同義字字典使用的是不同格式，而您想直接使用此字典，請透過 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 讓我們知道。

您可以使用 HTTP POST 建立新的同義字地圖，如下列範例所示︰

    POST https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"mysynonymmap",
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

或者，您也可以使用 PUT，並在 URI 中指定同義字地圖名稱。 如果同義字地圖不存在，系統就會加以建立。

    PUT https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

    {
       "format":"solr",
       "synonyms": "
          USA, United States, United States of America\n
          Washington, Wash., WA => WA\n"
    }

##### <a name="apache-solr-synonym-format"></a>Apache Solr 同義字格式

Solr 格式支援對等且明確的對應同義字。 映射規則符合 Apache Solr 的開源同義字篩選器規範，本文檔中所述：[同義字篩檢程式](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter)。 以下是對等同義字的樣本規則。
```
USA, United States, United States of America
```

根據上述規則，搜尋「USA」時，會擴充搜尋「USA」或「United States」以及「United States of America」。

明確的對應由箭號「=>」表示。 指定後，與"_>"左側匹配的搜索查詢的術語序列將替換為右側的備選方案。 根據下列規則，搜尋查詢「Washington」、「Wash.」 或「WA」，都會重寫為「WA」。 明確對應只會套用在指定的方向，而且在此案例中，不會在查詢「WA」時重寫為「Washington」。
```
Washington, Wash., WA => WA
```

#### <a name="list-synonym-maps-under-your-service"></a>您服務中的同義字地圖清單。

    GET https://[servicename].search.windows.net/synonymmaps?api-version=2019-05-06
    api-key: [admin key]

#### <a name="get-a-synonym-map-under-your-service"></a>在您的服務中取得同義字地圖。

    GET https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

#### <a name="delete-a-synonyms-map-under-your-service"></a>刪除您服務中的同義字地圖。

    DELETE https://[servicename].search.windows.net/synonymmaps/mysynonymmap?api-version=2019-05-06
    api-key: [admin key]

### <a name="configure-a-searchable-field-to-use-the-synonym-map-in-the-index-definition"></a>在索引定義中設定要使用同義字地圖的可搜尋欄位。

您可以使用新的欄位屬性 **synonymMaps**，來指定要在可搜尋欄位中使用的同義字地圖。 同義字地圖屬於服務層級的資源，且服務中的任何索引欄位均可參考。

    POST https://[servicename].search.windows.net/indexes?api-version=2019-05-06
    api-key: [admin key]

    {
       "name":"myindex",
       "fields":[
          {
             "name":"id",
             "type":"Edm.String",
             "key":true
          },
          {
             "name":"name",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"en.lucene",
             "synonymMaps":[
                "mysynonymmap"
             ]
          },
          {
             "name":"name_jp",
             "type":"Edm.String",
             "searchable":true,
             "analyzer":"ja.microsoft",
             "synonymMaps":[
                "japanesesynonymmap"
             ]
          }
       ]
    }

可以為「Edm.String」或「Collection(Edm.String)」類型的可搜尋欄位指定 **synonymMaps**。

> [!NOTE]
> 每一個欄位僅能有一個同義字地圖。 如果您想要使用多個同義字地圖，請透過 [UserVoice](https://feedback.azure.com/forums/263029-azure-search) 讓我們知道。

## <a name="impact-of-synonyms-on-other-search-features"></a>同義字對其他搜尋功能的影響

同義字功能會在 OR 運算子中，使用同義字改寫原始的查詢。 基於這個原因，點閱數醒目提示與評分檔案，會將原始詞彙與同義字視為對等。

同義字功能適用於搜尋查詢，並不適用於篩選條件或面向。 同樣地，搜尋建議僅根據原始詞彙提供，同義字比對結果不會出現在回應中。

同義字擴充不適用於萬用字元搜尋詞彙；也不會擴充前置詞、模糊與 Regex 詞彙。

如果您需要執行適用同義字展開和萬用字元、regex 或模糊搜尋的單一查詢，您可以使用 OR 語法結合查詢。 例如，若要針對簡單查詢語法結合同義字與萬用字元，詞彙可以是 `<query> | <query>*`。

如果您在開發 (非生產) 環境中有現有的索引，可以使用小型的字典進行試驗，看看增加同義字能如何改變搜尋體驗，包含對於評分檔案、點閱數醒目提示以及搜尋建議的影響。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [創建同義字映射](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)