---
title: 使用 Azure 資料工廠從 HTTP 源複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從雲端或內部部署 HTTP 來源將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 730efb552ef218cc5a5ce6a984d20b4e23b364ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416948"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 HTTP 端點複製資料 | Microsoft Docs

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](v1/data-factory-http-connector.md)
> * [目前版本](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 HTTP 端點複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

此 HTTP 連接器、[REST 連接器](connector-rest.md)和 [Web 資料表連接器](connector-web-table.md)之間的差異是：

- **REST 連接器**專門支援從 RESTful API 複製資料； 
- **HTTP 連接器**一般用來從任何 HTTP 端點擷取資料，例如下載檔案。 在 REST 連接器可供使用之前，您可能會使用 HTTP 連接器從 RESTful API 複製資料，這是可支援的方式，但功能性比 REST 連接器低。
- **Web 資料表連接器**會從 HTML 網頁擷取資料表內容。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 HTTP 連線器:

- 使用[支援的來源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [尋找活動](control-flow-lookup-activity.md)

您可以將資料從 HTTP 來源複製到任何支援的接收資料存放區。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

您可以使用此 HTTP 連接器來：

- 使用 HTTP **GET** 或 **POST** 方法，從 HTTP/S 端點擷取資料。
- 使用下列其中一種驗證來擷取資料︰**匿名**、**基本**、**摘要**、**Windows** 或 **ClientCertificate**。
- 依原樣複製 HTTP 回應，或使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析回應。

> [!TIP]
> 若要在 Data Factory 中設定 HTTP 連接器之前，測試擷取資料的 HTTP 要求，請先了解 API 規格中的標頭和本文需求。 您可以使用 Postman 或網頁瀏覽器之類的工具進行驗證。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，您可使用這些屬性來定義 HTTP 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 HTTP 連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **類型**屬性必須設定為**Httttt**。 | 是 |
| url | Web 伺服器的基底 URL。 | 是 |
| enableServerCertificateValidation | 指定在連接到 HTTP 終結點時是否啟用伺服器 TLS/SSL 憑證驗證。 如果 HTTPS 伺服器使用自我簽署的憑證，請將此屬性設定為 **false**。 | 否<br /> ( 預設值為**true)** |
| authenticationType | 指定驗證類型。 允許的值為**匿名**、**基本**、**摘要**、**Windows** 和 **ClientCertificate**。 <br><br> 如需更多關於這些驗證類型的屬性和 JSON 範例，請參閱此表格後面幾節。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 從[先決條件](#prerequisites)部分瞭解更多資訊。 如果未指定，則會使用預設的 Azure Integration Runtime。 |否 |

### <a name="using-basic-digest-or-windows-authentication"></a>使用基本、摘要或 Windows 驗證

將 **authenticationType** 屬性設定為 [基本]****、[摘要]**** 或 [Windows]****。 除了上一節所述的一般屬性以外，請指定下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| userName | 用來存取 HTTP 端點的使用者名稱。 | 是 |
| 密碼 | 使用者 (**userName** 值) 的密碼。 將此欄位標記為 **SecureString** 類型，將它安全地儲存在 Data Factory 中。 您也可以[參考 Azure Key Vault 中儲存的認證](store-credentials-in-key-vault.md)。 | 是 |

**範例**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>使用 ClientCertificate 驗證

若要使用 ClientCertificate 驗證，請將 **authenticationType** 屬性設定為 **ClientCertificate**。 除了上一節所述的一般屬性以外，請指定下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| embeddedCertData | Base64 編碼的憑證資料。 | 指定 **embeddedCertData** 或 **certThumbprint**。 |
| certThumbprint | 憑證指紋已安裝在自我裝載整合執行階段機器的憑證存放區上。 只有當 **connectVia** 屬性中已指定自我裝載整合執行階段時才適用。 | 指定 **embeddedCertData** 或 **certThumbprint**。 |
| 密碼 | 與憑證相關聯的密碼。 將此欄位標記為 **SecureString** 類型，將它安全地儲存在 Data Factory 中。 您也可以[參考 Azure Key Vault 中儲存的認證](store-credentials-in-key-vault.md)。 | 否 |

如果您使用 **certThumbprint** 進行驗證，且憑證已安裝在本機電腦的個人存放區中，請將讀取權限授予自我裝載整合執行階段︰

1. 開啟 Microsoft Management Console (MMC)。 新增目標為 [本機電腦]**** 的 [憑證]**** 嵌入式管理單元。
2. 展開**憑證** > **個人**,然後選擇**憑證**。
3. 右鍵按一下個人儲存中的證書,然後選擇 **「所有任務** > **管理私鑰**」。
3. 在 [安全性]**** 索引標籤上，新增使用憑證讀取存取全執行整合執行階段主機服務 (DIAHostService) 的使用者帳戶。

**範例 1: 使用憑證指紋**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例 2:使用嵌入式資料**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基於格式的資料集中的設定`location`下 ,HTTP 支援以下屬性:

| 屬性    | 描述                                                  | 必要 |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | 資料集中的類型`location`屬性必須設定為**Httttation 的屬性**。 | 是      |
| relativeUrl | 包含資料之資源的相對 URL。 HTTP 連接器從組合網址`[URL specified in linked service][relative URL specified in dataset]`複製資料 : 。   | 否       |

> [!NOTE]
> 支援的 HTTP 要求承載大小是大約 500 KB。 如果您希望傳遞至 Web 端點的承載大小大於 500 KB，請考慮將承載分批處理成較小的區塊。

**範例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 HTTP 來源所支援的屬性清單。

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 

### <a name="http-as-source"></a>HTTP 作為來源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在基於格式的複本來源中的設定下`storeSettings`,HTTP 支援以下屬性:

| 屬性                 | 描述                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 下`storeSettings`的類型屬性必須設定為**HttpReadSettings**。 | 是      |
| requestMethod            | HTTP 方法。 <br>允許的值為 **Get** (預設值) 和 **Post**。 | 否       |
| 附加標題         | 其他 HTTP 要求標頭。                             | 否       |
| requestBody              | HTTP 要求的主體。                               | 否       |
| httpRequestTimeout           | 用來取得回應的 HTTP 要求會有的逾時值 (**TimeSpan** 值)。 此值是取得回應的逾時值，而非讀取回應資料的逾時值。 預設值為 **00:01:40**。 | 否       |
| maxConcurrentConnections | 要同時連接到存儲存儲的連接數。 僅當要將併發連接限制到數據存儲時,才指定。 | 否       |

**範例:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>尋找活動屬性

要瞭解有關屬性的詳細資訊,請檢查[。](control-flow-lookup-activity.md)

## <a name="legacy-models"></a>舊模型

>[!NOTE]
>以下型號仍支援以下型號,以便進行向後相容性。 建議您今後使用上述各節中提及的新模型,並且 ADF 創作 UI 已切換到生成新模型。

### <a name="legacy-dataset-model"></a>舊資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集**的類型**屬性必須設定為**HttpFile**。 | 是 |
| relativeUrl | 包含資料之資源的相對 URL。 若未指定此屬性，則只會使用在連結服務定義中指定的 URL。 | 否 |
| requestMethod | HTTP 方法。 允許的值為 **Get** (預設值) 和 **Post**。 | 否 |
| additionalHeaders | 其他 HTTP 要求標頭。 | 否 |
| requestBody | HTTP 要求的主體。 | 否 |
| format | 如果您想要依原樣擷取 HTTP 端點的資料而不進行剖析，然後將資料複製到以檔案為基礎的存放區，請略過輸入和輸出資料集定義中的 **format** 區段。<br/><br/>如果您想要在複製期間剖析 HTTP 回應內容，支援下列檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 在 **format** 之下，將 **type** 屬性設定為上述其中一個值。 如需詳細資訊，請參閱 [JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)。 |否 |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/><br/>支援的類型：**GZip**、**Deflate**、**BZip2** 及 **ZipDeflate**。<br/>支援等級:**最佳**與**最快**。 |否 |

> [!NOTE]
> 支援的 HTTP 要求承載大小是大約 500 KB。 如果您希望傳遞至 Web 端點的承載大小大於 500 KB，請考慮將承載分批處理成較小的區塊。

**範例 1：使用 Get 方法 (預設值)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**範例 2︰使用 Post 方法**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>舊複製活動來源模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源**的類型**屬性必須設定為**Hctce**。 | 是 |
| httpRequestTimeout | 用來取得回應的 HTTP 要求會有的逾時值 (**TimeSpan** 值)。 此值是取得回應的逾時值，而非讀取回應資料的逾時值。 預設值為 **00:01:40**。  | 否 |

**範例**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
