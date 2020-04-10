---
title: 使用 Azure 資料工廠從 REST 源複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從雲端或內部部署 REST 來源將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: ca913a4c197e04e20c962c4a4a7a1e479a3cdf92
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990883"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 REST 端點複製資料

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 REST 端點複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

此 REST 連接器、[HTTP 連接器](connector-http.md)和 [Web 資料表連接器](connector-web-table.md)之間的差異是：

- **REST 連接器**特別支援從 RESTful API 複製資料; 
- **HTTP 連接器**一般用來從任何 HTTP 端點擷取資料，例如下載檔案。 在此 REST 連接器可供使用之前，您可能會使用 HTTP 連接器從 RESTful API 複製資料，這是可支援的方式，但功能性比 REST 連接器低。
- **Web 資料表連接器**會從 HTML 網頁擷取資料表內容。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 REST 來源複製到任何支援的接收資料存放區。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

具體而言，此泛型 REST 連接器支援：

- 使用 **GET** 或 **POST** 方法，從 REST 端點擷取資料。
- 使用以下身份驗證的匿名身份驗證 :**Azure**資源的匿名身份驗證、**基本**身份驗證 **、AAD 服務主體**與**託管識別**。
- REST API 中的**[分頁](#pagination-support)**。
- 複製 REST JSON 回應的[原狀](#export-json-response-as-is)或使用[結構描述對應](copy-activity-schema-and-type-mapping.md#schema-mapping)加以剖析。 僅支援 **JSON** 格式的回應承載。

> [!TIP]
> 若要在 Data Factory 中設定 REST 連接器之前，測試擷取資料的要求，請先了解 API 規格中的標頭和本文需求。 您可以使用 Postman 或網頁瀏覽器之類的工具進行驗證。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，您可使用這些屬性來定義 REST 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 REST 連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **類型**屬性必須設定為**RestService**。 | 是 |
| url | REST 服務的基底 URL。 | 是 |
| enableServerCertificateValidation | 在連接到終結點時是否驗證伺服器端 TLS/SSL 憑證。 | 否<br /> ( 預設值為**true)** |
| authenticationType | 用來連線到 REST 服務的驗證類型。 允許的值為 **Anonymous**、**Basic**、**AadServicePrincipal** 和 **ManagedServiceIdentity**。 請分別參閱下列有關更多屬性和範例的對應區段。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 從[先決條件](#prerequisites)部分瞭解更多資訊。 如果未指定，此屬性會使用預設的 Azure Integration Runtime。 |否 |

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
| aadResourceId | 指定您要求授權的 AAD 資源，例如 `https://management.core.windows.net`。| 是 |

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
| aadResourceId | 指定您要求授權的 AAD 資源，例如 `https://management.core.windows.net`。| 是 |

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
| relativeUrl | 包含資料之資源的相對 URL。 若未指定此屬性，則只會使用在連結服務定義中指定的 URL。 HTTP 連接器從組合網址`[URL specified in linked service]/[relative URL specified in dataset]`複製資料 : 。 | 否 |

如果設置`requestMethod``additionalHeaders``requestBody`,`paginationRules`並在 資料集中,它仍然支援" 以「 以」 ,同時建議您今後在活動來源中使用新模型。

**範例:**

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

複製活動**來源**部份支援以下屬性:

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
>REST 連接器忽略`additionalHeaders`中 指定的任何"接受"標頭。 由於 REST 連接器僅支援 JSON 中的回應,`Accept: application/json`它將自動生成標頭。

**範例 1: 將 Get 方法與分形一起使用**

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

通常,REST API 在合理數量下限制單個請求的回應負載大小;在返回大量數據的同時,它會將結果拆分為多個頁面,並要求調用方發送連續請求以獲取結果的下一頁。 通常，單一頁面要求是動態的，並且由前頁回應所傳回的資訊所組成。

此泛型 REST 連接器支援下列分頁模式： 

* 下一個要求的絕對或相對網址 = 目前回應正文中的屬性值
* 下一個要求的絕對或相對網址 = 目前回應標頭中的標頭值
* 下一個要求的查詢參數 = 目前回應本文中的屬性值
* 下一個要求的查詢參數 = 目前回應標頭中的標頭值
* 下一個要求的標頭 = 目前回應本文中的屬性值
* 下一個要求的標頭 = 目前回應標頭中的標頭值

**暫定規則**被定義為數據集中的字典,其中包含一個或多個區分大小寫的鍵值對。 此設定將用來產生從第二個頁面開始的要求。 當連接器獲得 HTTP 狀態代碼 204(無內容)時將停止反覆運算,或者「暫停規則」中的任何 JSONPath 運算式傳回 null。

分頁規則中的**支援金鑰**：

| Key | 描述 |
|:--- |:--- |
| AbsoluteUrl | 指示 URL 發出下一個要求。 它可以是絕對**URL 或相對 URL。** |
| QueryParameters.*request_query_parameter* 或 QueryParameters['request_query_parameter'] | 使用者定義的 "request_query_parameter" 會參考下一個 HTTP 要求 URL 中的一個查詢參數名稱。 |
| Headers.*request_header* 或 Headers['request_header'] | 使用者定義的 "request_header" 會參考下一個 HTTP 要求中的一個標頭名稱。 |

分頁規則中的**支援值**：

| 值 | 描述 |
|:--- |:--- |
| Headers.*response_header* 或 Headers['response_header'] | 使用者定義的 "response_header" 會參考目前 HTTP 回應中的一個標頭名稱，其值會用來發出下一個要求。 |
| JSONPath 運算式會以 "$" 開頭 (代表回應本文的根) | 回應本文應只包含一個 JSON 物件。 JSONPath 運算式應會傳回單一基本值，而這會用來發出下一個要求。 |

**範例:**

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

相應的 REST 複製活動來源`paginationRules`設定尤其如下:

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
本節介紹如何使用解決方案範本使用 OAuth 將數據從 REST 連接器複製到以 JSON 格式複製到 Azure 數據湖儲存中。 

### <a name="about-the-solution-template"></a>關於解決方案範本

此樣本包含兩個活動:
- **Web**活動檢索無記名令牌,然後將其作為授權傳遞給後續複製活動。
- **將**活動複製資料從 REST 複製到 Azure 資料湖儲存。

範本定義兩個參數：
- **Sink 容器**是將資料複製到 Azure 資料湖存儲中的根資料夾路徑。 
- **SinkDirectory**是根目錄下的目錄路徑,將數據複製到 Azure 數據湖儲存中。 

### <a name="how-to-use-this-solution-template"></a>如何使用此解決方案範本

1. **使用 OAuth**樣本轉到從 REST 或 HTTP 複製。 為源連接創建新連接。 
    ![建立新連線](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    以下是新連結服務 (REST) 設定的關鍵步驟:
    
     1. 在**基本 URL**下 ,指定您自己的源 REST 服務的 URL 參數。 
     2. 對於**驗證類型**,請選擇 *「匿名*」。
        ![新的 REST 連線](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. 為目標連接創建新連接。  
    ![新第 2 代連線](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. 選擇**此樣本**。
    ![使用此範本](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. 您將看到建立的導管,例如以下範例的![管道](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. 選擇**Web**活動。 在 **「設定」中**,指定相應的**URL、****方法**、**標題**和**正文**,以便從要從中複製資料的服務的登入 API 中檢索 OAuth 承載權杖。 樣本中的佔位符顯示 Azure 活動目錄 (AAD) OAuth 的範例。 注意 AAD 身份驗證由 REST 連接器本機支援,這裡只是 OAuth 流的範例。 

    | 屬性 | 描述 |
    |:--- |:--- |:--- |
    | URL |指定要從中檢索 OAuth 承載權杖的網址。 在樣品中,它https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | 方法 | HTTP 方法。 允許的值是 **「過帳」** 和「**獲取**」。 | 
    | headers | 標頭是使用者定義的,它引用 HTTP 請求中的一個標頭名稱。 | 
    | body | HTTP 要求的主體。 | 

    ![管線](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. 在 **「複製資料**活動」 中,選擇 *「源*」選項卡,您可以看到從上一步中檢索的無記名權杖 (access_token) 將作為「其他標頭下**的授權」** 傳遞給複製數據活動。 在啟動管道運行之前,確認以下屬性的設置。

    | 屬性 | 描述 |
    |:--- |:--- |:--- | 
    | 要求方法 | HTTP 方法。 允許的值為 **Get** (預設值) 和 **Post**。 | 
    | 其他標頭 | 其他 HTTP 要求標頭。| 

   ![複製來源身分驗證](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. 選擇 **「除錯**」,輸入**參數**,然後選擇 **"完成**"。
   ![管線執行](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. 當導管執行成功完成時,您將看到類似於以下範例的結果![: 導管執行結果](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. 按下 **「操作**中 Web 活動」的「輸出」圖示,您將看到服務返回access_token。

   ![權杖輸出](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. 按下 **「操作**中複製活動」的「輸入」圖示,您將看到 WebActivity 檢索access_token傳遞給複製活動進行身份驗證。 

    ![權杖輸入](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >為避免以純文本記錄權杖,請在 Web 活動中啟用「安全輸出」,在「複製」活動中啟用「安全輸入」。


## <a name="export-json-response-as-is"></a>匯出 JSON 回應的原狀

您可以使用此 REST 連接器將 REST API 的 JSON 回應原狀匯出到各種檔案型存放區。 若要完成這種跨平台結構描述的複製，請跳過資料集中的「結構」(也稱為「結構描述」**) 區段，以及複製活動中的結構描述對應。

## <a name="schema-mapping"></a>結構描述對應

若要將資料從 REST 端點複製到表格式接收器中，請參閱[結構描述對應](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
