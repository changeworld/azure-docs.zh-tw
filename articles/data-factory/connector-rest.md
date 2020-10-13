---
title: 使用 Azure Data Factory 從 REST 來源複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從雲端或內部部署 REST 來源將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jingwang
ms.openlocfilehash: 7b6fa2395e81089e8b4523929a4a7a583b0788a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360764"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 REST 端點複製資料
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 REST 端點複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

此 REST 連接器、 [HTTP 連接器](connector-http.md)和 [Web 資料表連接器](connector-web-table.md) 之間的差異如下：

- **REST 連接器** 專門支援從 RESTful api 複製資料; 
- **Http 連接器** 是一般的，可從任何 HTTP 端點取出資料，例如下載檔案。 在此 REST 連接器可供使用之前，您可能會使用 HTTP 連接器從 RESTful API 複製資料，這是可支援的方式，但功能性比 REST 連接器低。
- **Web 資料表連接器**會從 HTML 網頁擷取資料表內容。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 REST 來源複製到任何支援的接收資料存放區。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

具體而言，此泛型 REST 連接器支援：

- 使用 **GET** 或 **POST** 方法，從 REST 端點擷取資料。
- 使用下列其中一種驗證來抓取資料： **匿名**、 **基本**、 **AAD 服務主體**和 **適用于 Azure 資源的受控**識別。
- REST API 中的**[分頁](#pagination-support)**。
- 複製 REST JSON 回應的[原狀](#export-json-response-as-is)或使用[結構描述對應](copy-activity-schema-and-type-mapping.md#schema-mapping)加以剖析。 僅支援 **JSON** 格式的回應承載。

> [!TIP]
> 若要在 Data Factory 中設定 REST 連接器之前，測試擷取資料的要求，請先了解 API 規格中的標頭和本文需求。 您可以使用 Postman 或網頁瀏覽器之類的工具進行驗證。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，您可使用這些屬性來定義 REST 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 REST 連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **Type**屬性必須設為**RestService**。 | 是 |
| url | REST 服務的基底 URL。 | 是 |
| enableServerCertificateValidation | 是否要在連接到端點時驗證服務器端 TLS/SSL 憑證。 | 否<br />  (預設值為 **true**)  |
| authenticationType | 用來連線到 REST 服務的驗證類型。 允許的值為 **Anonymous**、 **Basic**、 **>aadserviceprincipal**和 **ManagedServiceIdentity**。 請分別參閱下列有關更多屬性和範例的對應區段。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 從[必要條件](#prerequisites)一節深入了解。 如果未指定，此屬性會使用預設的 Azure Integration Runtime。 |否 |

### <a name="use-basic-authentication"></a>使用基本驗證

將 **authenticationType** 屬性設定為 [Basic]****。 除了上一節所述的一般屬性以外，請指定下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| userName | 用來存取 REST 端點的使用者名稱。 | 是 |
| 密碼 | 使用者 (**userName** 值) 的密碼。 將此欄位標記為 **SecureString** 類型，將它安全地儲存在 Data Factory 中。 您也可以[參考 Azure Key Vault 中儲存的認證](store-credentials-in-key-vault.md)。 | 是 |

**範例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-aad-service-principal-authentication"></a>使用 AAD 服務主體驗證

將 **authenticationType** 屬性設定為 [AadServicePrincipal]****。 除了上一節所述的一般屬性以外，請指定下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定 Azure Active Directory 應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定 Azure Active Directory 應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 是 |
| aadResourceId | 指定您要求授權的 AAD 資源，例如 `https://management.core.windows.net` 。| 是 |
| azureCloudType | 針對服務主體驗證，請指定您的 AAD 應用程式所註冊的 Azure 雲端環境類型。 <br/> 允許的值為 **AzurePublic**、 **AzureChina**、 **AzureUsGovernment**和 **AzureGermany**。 根據預設，會使用 data factory 的雲端環境。 | 否 |

**範例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>使用 Azure 資源的受控識別驗證

將 **authenticationType** 屬性設定為 [ManagedServiceIdentity]****。 除了上一節所述的一般屬性以外，請指定下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| aadResourceId | 指定您要求授權的 AAD 資源，例如 `https://management.core.windows.net` 。| 是 |

**範例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

本節提供 REST 資料集所支援的屬性清單。 

如需定義資料集的區段和屬性完整清單，請參閱[資料集和連結服務](concepts-datasets-linked-services.md)。 

若要從 REST 複製資料，以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 **type** 屬性必須設定為 [RestResource]****。 | 是 |
| relativeUrl | 包含資料之資源的相對 URL。 若未指定此屬性，則只會使用在連結服務定義中指定的 URL。 HTTP 連接器會從結合的 URL 複製資料： `[URL specified in linked service]/[relative URL specified in dataset]` 。 | 否 |

如果您 `requestMethod` `additionalHeaders` `requestBody` 在資料集中設定、和， `paginationRules` 仍會依原樣受到支援，但建議您繼續使用活動來源中的新模型。

**範例︰**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 REST 來源所支援的屬性清單。

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 

### <a name="rest-as-source"></a>REST 作為來源

複製活動的 **source** 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設定為 [RestSource]****。 | 是 |
| requestMethod | HTTP 方法。 允許的值為 **Get** (預設值) 和 **Post**。 | 否 |
| additionalHeaders | 其他 HTTP 要求標頭。 | 否 |
| requestBody | HTTP 要求的主體。 | 否 |
| paginationRules | 用來撰寫下一個頁面要求的分頁規則。 請參閱[分頁支援](#pagination-support)區段以取得詳細資料。 | 否 |
| httpRequestTimeout | 用來取得回應的 HTTP 要求會有的逾時值 (**TimeSpan** 值)。 此值是取得回應的逾時值，而非讀取回應資料的逾時值。 預設值為 **00:01:40**。  | 否 |
| requestInterval | 傳送下一個頁面要求之前的等候時間。 預設值為 [00:00:01]**** |  否 |

>[!NOTE]
>REST 連接器會忽略中指定的任何「接受」標頭 `additionalHeaders` 。 因為 REST 連接器只支援 JSON 中的回應，所以會自動產生的標頭 `Accept: application/json` 。

**範例1：使用 Get 方法搭配分頁**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**範例 2︰使用 Post 方法**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>分頁支援

一般來說，REST API 會以合理的數目限制單一要求的回應承載大小;雖然傳回大量資料，但是它會將結果分割成多個頁面，並且要求呼叫端傳送連續要求以取得結果的下一頁。 通常，單一頁面要求是動態的，並且由前頁回應所傳回的資訊所組成。

此泛型 REST 連接器支援下列分頁模式： 

* 下一個要求的絕對或相對 URL = 目前回應主體中的屬性值
* 下一個要求的絕對或相對 URL = 目前回應標頭中的標頭值
* 下一個要求的查詢參數 = 目前回應本文中的屬性值
* 下一個要求的查詢參數 = 目前回應標頭中的標頭值
* 下一個要求的標頭 = 目前回應本文中的屬性值
* 下一個要求的標頭 = 目前回應標頭中的標頭值

**分頁規則** 會定義為資料集內的字典，其中包含一或多個區分大小寫的索引鍵/值組。 此設定將用來產生從第二個頁面開始的要求。 連接器會在收到 HTTP 狀態碼 204 (沒有內容) ，或 "paginationRules" 中的任何 JSONPath 運算式傳回 null 時，停止反復進行反覆運算。

分頁規則中的**支援金鑰**：

| Key | 描述 |
|:--- |:--- |
| AbsoluteUrl | 指示 URL 發出下一個要求。 它可以是 **絕對 url 或相對 url**。 |
| QueryParameters.*request_query_parameter* 或 QueryParameters['request_query_parameter'] | 「request_query_parameter」是使用者定義的，它會在下一個 HTTP 要求 URL 中參考一個查詢參數名稱。 |
| Headers.*request_header* 或 Headers['request_header'] | 「request_header」是使用者定義的，會在下一個 HTTP 要求中參考一個標頭名稱。 |

分頁規則中的**支援值**：

| 值 | 描述 |
|:--- |:--- |
| Headers.*response_header* 或 Headers['response_header'] | "response_header" 是使用者定義的，它會參考目前 HTTP 回應中的一個標頭名稱，其值將用來發出下一個要求。 |
| JSONPath 運算式會以 "$" 開頭 (代表回應本文的根) | 回應本文應只包含一個 JSON 物件。 JSONPath 運算式應會傳回單一基本值，而這會用來發出下一個要求。 |

**範例︰**

Facebook 圖形 API 會傳回採用下列結構的回應，在該案例中，下個頁面的 URL 會在 ***paging.next*** 中指出：

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

相對來說，對應的 REST 複製活動來源設定如下所示 `paginationRules` ：

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>使用 OAuth
本節說明如何使用解決方案範本，以使用 OAuth 將資料從 REST 連接器複製到 JSON 格式的 Azure Data Lake Storage。 

### <a name="about-the-solution-template"></a>關於解決方案範本

範本包含兩個活動：
- **Web** 活動會抓取持有人權杖，然後將其傳遞至後續的複製活動作為授權。
- **複製** 活動會將資料從 REST 複製到 Azure Data Lake Storage。

範本定義兩個參數：
- **SinkContainer** 是在 Azure Data Lake Storage 中將資料複製到其中的根資料夾路徑。 
- **SinkDirectory** 是在您的 Azure Data Lake Storage 中將資料複製到其中的根目錄下的目錄路徑。 

### <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. 移至 **使用 OAuth 範本從 REST 或 HTTP 複製** 。 建立來源連線的新連接。 
    ![建立新的連接](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    以下是新連結服務 (REST) 設定的主要步驟：
    
     1. 在 [ **基底 URL**] 底下，指定您自己的來源 REST 服務的 URL 參數。 
     2. 在 [ **驗證類型**] 中，選擇 [ *匿名*]。
        ![新增 REST 連接](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. 建立目的地連接的新連接。  
    ![新增 Gen2 連接](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. 選取 [使用此範本]。
    ![使用此範本](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. 您會看到建立的管線，如下列範例所示：  ![ 螢幕擷取畫面顯示從範本建立的管線。](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. 選取 [ **Web** 活動]。 在 [ **設定**] 中，指定對應的 **URL**、 **方法**、 **標頭**和 **主體** ，從您想要從中複製資料的服務登入 API 取得 OAuth 持有人權杖。 範本中的預留位置會展示 Azure Active Directory (AAD) OAuth 的範例。 請注意，REST 連接器原本就支援 AAD 驗證，以下是 OAuth 流程的範例。 

    | 屬性 | 描述 |
    |:--- |:--- |:--- |
    | URL |指定要從中取出 OAuth 持有人權杖的 url。 例如，在此範例中，它是 https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | 方法 | HTTP 方法。 允許的值為 **Post** 和 **Get**。 | 
    | headers | 標頭是使用者定義的，它會參考 HTTP 要求中的一個標頭名稱。 | 
    | 主體 | HTTP 要求的主體。 | 

    ![管線](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. 在 [ **複製資料** 活動] 的 [選取 *來源* ] 索引標籤中，您可以看到從上一個步驟中取出的持有人權杖 (Access_token) 會在其他標頭下傳遞，以將資料複製為 **授權** 。 開始執行管線之前，請先確認下列屬性的設定。

    | 屬性 | 描述 |
    |:--- |:--- |:--- | 
    | 要求方法 | HTTP 方法。 允許的值為 **Get** (預設值) 和 **Post**。 | 
    | 其他標頭 | 其他 HTTP 要求標頭。| 

   ![複製來源驗證](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. 選取 [偵錯]，輸入 [參數]，然後選取 [完成]。
   ![管線執行](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. 當管線執行成功完成時，您會看到類似下列範例的結果： ![ 管線執行結果](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. 按一下 [ **動作** ] 資料行中 WebActivity 的 [輸出] 圖示，您會看到服務所傳回的 access_token。

   ![標記輸出](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. 按一下 [ **動作** ] 資料行中 CopyActivity 的 [輸入] 圖示，您會看到 WebActivity 所抓取的 access_token 會傳遞至 CopyActivity 進行驗證。 

    ![權杖輸入](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >若要避免以純文字記錄權杖，請在 Web 活動中啟用「安全輸出」，並在複製活動中啟用「安全輸入」。


## <a name="export-json-response-as-is"></a>匯出 JSON 回應的原狀

您可以使用此 REST 連接器將 REST API 的 JSON 回應原狀匯出到各種檔案型存放區。 若要完成這種跨平台結構描述的複製，請跳過資料集中的「結構」(也稱為「結構描述」**) 區段，以及複製活動中的結構描述對應。

## <a name="schema-mapping"></a>結構描述對應

若要將資料從 REST 端點複製到表格式接收器中，請參閱[結構描述對應](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
