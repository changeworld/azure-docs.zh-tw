---
title: 使用 Azure 資料工廠從 Web 表複製資料
description: 了解 Azure Data Factory 服務的「Web 資料表連接器」，此連接器可讓您將資料從 Web 資料表複製到 Data Factory 所支援作為接收器的資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 76f0dbb48ca5e250a383e8427ce2dd0c9dd618c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930939"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Web 資料表複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](v1/data-factory-web-table-connector.md)
> * [當前版本](connector-web-table.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Web 資料表資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

此 Web 資料表連接器、[REST 連接器](connector-rest.md)和 [HTTP 連接器](connector-http.md)之間的差異如下：

- **Web 資料表連接器**會從 HTML 網頁擷取資料表內容。
- **REST 連接器**特別支援從 RESTful API 複製資料。
- **HTTP 連接器**一般用來從任何 HTTP 端點擷取資料，例如下載檔案。 

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 Web 表連接器：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)

您可以將資料從 Web 資料表資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Web 資料表連接器支援**從 HTML 頁面擷取資料表內容**。

## <a name="prerequisites"></a>Prerequisites

若要使用此 Web 資料表連接器，您需要設定「自我裝載 Integration Runtime」。 有關詳細資訊[，請參閱自託管集成運行時](create-self-hosted-integration-runtime.md)文章。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Web 資料表連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Web 資料表已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為： **Web** |是 |
| url | Web 來源的 URL |是 |
| authenticationType | 允許的值為：**Anonymous** (匿名)。 |是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**例子：**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

有關可用於定義資料集的節和屬性的完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Web 資料表資料集所支援的屬性清單。

若要從 Web 資料表複製資料，請將資料集的類型屬性設定為 **WebTable**。 以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**WebTable** | 是 |
| path |包含資料表之資源的相對 URL。 |否。 當路徑未指定時，則只會使用在連結服務定義中指定的 URL。 |
| 索引 |資源中資料表的索引。 有關在 HTML 頁中獲取表索引的步驟，請參閱[獲取 HTML 頁部分中的表索引](#get-index-of-a-table-in-an-html-page)。 |是 |

**例子：**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Web 資料表來源所支援的屬性清單。

### <a name="web-table-as-source"></a>Web 資料表作為來源

若要從 Web 資料表複製資料，請將複製活動中的來源類型設定為 **WebSource**，不支援任何其他屬性。

**例子：**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>取得 HTML 網頁中資料表的索引

若要取得您要在[資料集屬性](#dataset-properties)中設定的資料表索引，可以使用 Excel 2016 等軟體 (如下所示) 做為工具：

1. 啟動 **Excel 2016**，然後切換到 [資料]**** 索引標籤。
2. 按一下工具列上的 [開新查詢]****、指向 [從其他來源]****，然後按一下 [從 Web]****。

    ![Power Query 功能表](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. 在 [從 Web]**** 對話方塊中，輸入您要在連結服務 JSON 中使用的 **URL** (例如：https://en.wikipedia.org/wiki/)，以及您為資料集指定的路徑 (例如：AFI%27s_100_Years...100_Movies)，然後按一下 [確定]****。

    ![[從 Web] 對話方塊](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    此範例使用的 URL：https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. 當您看到 [存取 Web 內容]**** 對話方塊時，選取右側的 **URL**、**驗證方式**，然後按一下 [連線]****。

   ![[存取 Web 內容] 對話方塊](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. 按一下樹狀檢視中的某個**資料表**項目來查看資料表內容，然後按一下底部的 [編輯]**** 按鈕。  

   ![[導覽器] 對話方塊](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. 在 [查詢編輯器]**** 視窗中，按一下工具列上的 [進階編輯器]**** 按鈕。

    ![[進階編輯器] 按鈕](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. 在 [進階編輯器] 對話方塊中，「Source」旁的數字就是索引。

    ![進階編輯器及索引](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

如果您使用的是 Excel 2013，請使用 [Microsoft Power Query for Excel](https://www.microsoft.com/download/details.aspx?id=39379) 來取得索引。 如需詳細資訊，請參閱 [連線至網頁](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) 一文。 如果您使用 [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)，步驟就很類似。


## <a name="lookup-activity-properties"></a>查找活動屬性

要瞭解有關屬性的詳細資訊，請檢查[查找活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
