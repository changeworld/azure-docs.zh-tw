---
title: 快速入門：使用 Python 建立搜尋索引
titleSuffix: Azure Cognitive Search
description: 說明如何使用 Python、Jupyter Notebook 與 Azure.Documents.Search 程式庫建立索引、載入資料以及執行查詢。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/19/2020
ms.custom: devx-track-python
ms.openlocfilehash: 528d29f3b285c2583fd1bb52e1de7c24fdc9e28a
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917081"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>快速入門：使用 Jupyter Notebook 在 Python 中建立 Azure 認知搜尋索引

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](./search-get-started-powershell.md)
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [入口網站](search-get-started-portal.md)
>

建置 Jupyter Notebook，以使用 Python 和 [azure-search-documents 程式庫](/python/api/overview/azure/search-documents-readme)在適用於 Python 的 Azure SDK 中建立、載入和查詢 Azure 認知搜尋索引。 本文將說明如何逐步建立筆記本。 或是，您可以[下載並執行已完成的 Jupyter Python 筆記本](https://github.com/Azure-Samples/azure-search-python-samples)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

此快速入門需要下列服務和工具。

* [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section)，提供 Python 3.x 和 Jupyter Notebook。

* [azure-search-documents 套件](https://pypi.org/project/azure-search-documents/)

* [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用免費層來進行本快速入門。 

## <a name="copy-a-key-and-url"></a>複製金鑰和 URL

REST 呼叫需要服務 URL 和每個要求的存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀]  頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定]   >  [金鑰]  中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

![取得 HTTP 端點和存取金鑰](media/search-get-started-rest/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="connect-to-azure-cognitive-search"></a>連線至 Azue 認知搜尋

在這項工作中，請啟動 Jupyter Notebook，確認您可以連線至 Azure 認知搜尋。 您將藉由從服務中要求索引清單來執行此動作。 在具有 Anaconda3 的 Windows 上，您可以使用 Anaconda Navigator 來啟動筆記本。

1. 建立新的 Python3 筆記本。

1. 在第一個儲存格中，從適用於 Python 的 Azure SDK 載入程式庫，包括 [azure-search-documents](/python/api/azure-search-documents)。

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents

    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. 在第二個資料格中，輸入將在每個要求上成為常數的要求元素。 提供在上一個步驟中複製的搜尋服務名稱、管理 API 金鑰和查詢 API 金鑰。 此儲存格也會設定您將用於特定作業的用戶端：[SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) 用來建立索引，而 [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) 則用來查詢索引。

   ```python
    service_name = ["SEARCH_ENDPOINT - do not include search.windows.net"]
    admin_key = ["Cognitive Search Admin API Key"]

    index_name = "hotels-quickstart"

    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))

    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. 在第三個儲存格中，執行 delete_index 作業，以清除任何現有的 hotels-quickstart 索引。 刪除索引可讓您建立另一個相同名稱的 hotels-quickstart 索引。

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. 執行每個步驟。

## <a name="1---create-an-index"></a>1 - 建立索引

索引的必要元素包括名稱、欄位集合和索引鍵。 欄位集合會定義邏輯搜尋文件的結構，用於載入資料和傳回結果。 

每個欄位都有名稱、類型和用於決定欄位使用方式的屬性 (例如，該欄位是否可在搜尋結果中進行全文檢索搜尋、篩選或擷取)。 在索引內，類型為 `Edm.String` 的其中一個欄位必須指定為識別文件所需的「索引鍵」  。

如下所示，此索引名為 "hotels-quickstart"，並且具有欄位定義。 這是較大型[飯店索引](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) (用於其他逐步解說) 的子集。 為了簡潔起見，我們已在本快速入門中予以修剪。

1. 在下一個資料格中，將下列範例貼入資料格中，以提供結構描述。

    ```python
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),

            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),

            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),

            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. 在另一個資料格中，制訂要求。 此 create_index 要求會以您搜尋服務的索引集合為目標，並根據您在上一個資料格中提供的索引結構描述來建立 [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex)。

   ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)

    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
   ```

1. 執行每個步驟。

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 載入文件

若要載入文件，請使用[索引動作](/python/api/azure-search-documents/azure.search.documents.models.indexaction)，針對作業類型 (上傳、合併和上傳等等) 建立文件集合。 這些文件來自 GitHub 上的 [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON)。

1. 在新的資料格中，請提供符合索引結構描述的四份文件。 指定每份文件的上傳動作。

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```  

1. 在另一個資料格中，制訂要求。 此 upload_documents 要求會以 hotels-quickstart 索引的文件集合為目標，並將上一個步驟中所提供的文件推送至認知搜尋索引。


   ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
   ```

1. 請執行每個步驟，將文件推送至搜尋服務中的索引。

## <a name="3---search-an-index"></a>3 - 搜尋索引

此步驟將說明如何使用[搜尋文件 REST API](/rest/api/searchservice/search-documents) 來查詢索引。

1. 針對此作業，請使用 search_client。 此查詢會執行空的搜尋 (`search=*`)，並傳回未排名的雜亂文件清單 (搜尋分數 = 1.0)。 因為沒有任何準則，所以所有文件都會包含在結果中。 此查詢只會列印每份文件中的兩個欄位， 同時也會新增 `include_total_count=True` 以取得結果中所有文件 (4) 的計數。

   ```python
    results =  search_client.search(search_text="*", include_total_count=True)

    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
   ```

1. 下一個查詢會將整個字詞新增至搜尋運算式 ("wifi")。 此查詢會指定結果只包含 `select` 陳述式中的那些欄位。 限制傳回的欄位可將透過網路傳送的資料量降至最低，並減少搜尋延遲。

   ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')

    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
   ```

1. 接下來，套用篩選條件運算式，以便只選取星級評等高於 4 的飯店，並從高至低以遞減順序排序。

   ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')

    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
   ```

1. 新增 `search_fields` ，將符合的查詢範圍限定為單一欄位。

   ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')

    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
   ```

1. Facet 是可以用來撰寫 Facet 導覽結構的標籤。 此查詢會傳回類別的 Facet 和計數。

   ```python
    results =  search_client.search(search_text="*", facets=["Category"])

    facets = results.get_facets()

    for facet in facets["Category"]:
        print("    {}".format(facet))
   ```

1. 在此範例中，根據特定文件的索引鍵來查詢。 當使用者在搜尋結果中按一下文件時，通常表示想要傳回文件。

   ```python
    result = search_client.get_document(key="3")

    print("Details for hotel '3' are:")
    print("        Name: {}".format(result["HotelName"]))
    print("      Rating: {}".format(result["Rating"]))
    print("    Category: {}".format(result["Category"]))
   ```

1. 在此範例中，我們將使用自動完成功能。 此函式通常會在搜尋方塊中使用，以協助使用者在搜尋方塊中輸入內容時，讓系統自動完成可能的相符項目。

   建立索引時，也會建立名為 "sg" 的建議工具作為要求的一部分。 建議工具定義會指定哪些欄位可以用來尋找可能符合建議工具要求的項目。 在此範例中，這些欄位是「標記」、「地址/城市」、「地址/國家/地區」。 若要模擬自動完成功能，請輸入 "sa" 作為部分字串。 [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) 的自動完成方法會傳回可能的相符字詞。

   ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')

    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
   ```

## <a name="clean-up"></a>清除

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源]  或 [資源群組]  連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

為達到簡化目的，本快速入門使用了「飯店索引」的縮減版本。 您可以建立完整版本來嘗試更有趣的查詢。 若要取得完整版本和所有 50 個文件，請執行 [匯入資料]  精靈，從內建的範例資料來源中選取 [hotels-sample]  。

> [!div class="nextstepaction"]
> [快速入門：在 Azure 入口網站中建立索引](search-get-started-portal.md)