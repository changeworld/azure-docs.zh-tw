---
title: 從 HTTP 來源資料 ─ Azure
description: 了解如何使用 Azure Data Factory 從內部部署或雲端 HTTP 來源移動資料。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 36592151385a08d75b9b34e85bfa9d62342fc8cd
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991564"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>使用 Azure Data Factory 來移動 HTTP 來源的資料

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](data-factory-http-connector.md)
> * [第 2 版 (目前的版本)](../connector-http.md)

> [!NOTE]
> 本文適用於 Data Factory 第 1 版。 如果您使用目前版本的 Azure Data Factory 服務，請參閱[第 2 版中的 HTTP 連接器](../connector-http.md)。


本文概述如何使用 Azure Data Factory 中的複製活動，將內部部署或雲端 HTTP 端點中的資料移動到支援的接收資料存放區。 本文的建置是根據[使用複製活動來移動資料](data-factory-data-movement-activities.md)，該文會提供使用複製活動來移動資料的一般概觀。 本文也會列出複製活動所支援作為來源和接收的資料存放區。

Data Factory 目前只支援將資料從 HTTP 來源移到其他資料存放區。 不支援將資料從其他資料存放區移到 HTTP 目的地。

## <a name="supported-scenarios-and-authentication-types"></a>支援的案例和驗證類型

您可以使用這個 HTTP 連接器，藉由使用 HTTP **GET**或 **POST** 方法，從雲端和內部部署 HTTP/S 端點擷取資料**。 支援下列驗證類型︰**匿名****基本**、**摘要**、**Windows**和 **ClientCertificate**。 請注意此連接器和 [Web 資料表連接器](data-factory-web-table-connector.md)的差異。 Web 資料表連接器可從 HTML 網頁擷取資料表內容。

從內部部署 HTTP 端點複製資料時，您必須在內部部署環境或 Azure VM 中安裝資料管理閘道。 若要了解資料管理閘道和如何設定閘道的逐步指示，請參閱[在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)。

## <a name="get-started"></a>開始使用

您可以建立內含複製活動的管線，使用不同的工具或 API 將資料移出 HTTP 來源：

- 若要建立管線，最簡單的方式就是使用「資料複製」精靈。 如需使用資料複製精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md)。

- 您還可以使用以下工具建立管道:**視覺化工作室****、Azure PowerShell、Azure****資源管理員樣本****、.NET API**或**REST API**。 如需逐步指示來了解如何建立內含複製活動的管線，請參閱[複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 如需將資料從 HTTP 來源複製到 Azure Blob 儲存體的 JSON 範例，請參閱 [JSON 範例](#json-examples)。

## <a name="linked-service-properties"></a>連結服務屬性

下表說明 HTTP 連結服務專屬的 JSON 元素：

| 屬性 | 描述 | 必要 |
| --- | --- | --- |
| type | **類型**屬性必須設定為 **Http**。 | 是 |
| url | Web 伺服器的基底 URL。 | 是 |
| authenticationType | 指定驗證類型。 允許的值為**匿名**、**基本**、**摘要**、**Windows** 和 **ClientCertificate**。 <br><br> 如需更多關於這些驗證類型的屬性和 JSON 範例，請參閱本文後續幾節。 | 是 |
| enableServerCertificateValidation | 指定如果源是 HTH 伺服器,是否啟用伺服器 TLS/SSL 憑證驗證。 當 HTTPS 伺服器使用自我簽署的憑證時，請將此屬性設定為 **false**。 | 否<br /> ( 預設值為**true)** |
| gatewayName | 用來連線至內部部署 HTTP 來源的「資料管理閘道」執行個體名稱。 | 如果要從內部部署 HTTP 來源複製資料，則為是。 |
| encryptedCredential | 用來存取 HTTP 端點的加密認證。 當您在複製精靈或藉由使用 **ClickOnce** 對話方塊設定驗證資訊時，便會自動產生此值。 | 否<br /> (當您從內部部署 HTTP 伺服器複製資料時才適用) |

如需如何設定內部部署 HTTP 連接器資料來源認證的詳細資訊，請參閱[使用資料管理閘道在內部部署來源和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)。

### <a name="using-basic-digest-or-windows-authentication"></a>使用基本、摘要或 Windows 驗證

將 **authenticationType** 設定為**基本**、**摘要**或 **Windows**。 除了上述幾節所述的一般 HTTP 連接器屬性外，也請設定下列屬性：

| 屬性 | 描述 | 必要 |
| --- | --- | --- |
| userName | 用來存取 HTTP 端點的使用者名稱。 | 是 |
| 密碼 | 使用者 (**username**) 的密碼。 | 是 |

**範例︰使用基本、摘要或 Windows 驗證**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>使用 ClientCertificate 驗證

若要使用基本驗證，請將 **authenticationType** 設定為 **ClientCertificate**。 除了上述幾節所述的一般 HTTP 連接器屬性外，也請設定下列屬性：

| 屬性 | 描述 | 必要 |
| --- | --- | --- |
| embeddedCertData | PFX 檔案二進位資料的 Base64 編碼內容。 | 指定**嵌入式 CertData**或**憑證** |
| certThumbprint | 憑證指紋已安裝在您閘道器電腦的憑證存放區上。 當您從內部部署 HTTP 來源複製資料時才適用。 | 指定**嵌入式 CertData**或**憑證** |
| 密碼 | 與憑證相關聯的密碼。 | 否 |

如果您使用 **certThumbprint** 進行驗證且憑證已安裝在本機電腦的個人存放區中，請授與讀取權限給閘道服務︰

1. 開啟 Microsoft Management Console (MMC)。 新增目標為 [本機電腦]**** 的 [憑證]**** 嵌入式管理單元。
2. 展開**憑證** > **個人**,然後選擇**憑證**。
3. 右鍵按一下個人儲存中的證書,然後選擇 **「所有任務** >**管理私鑰**」。
3. 在 [安全性]**** 索引標籤上，新增資料管理閘道主機服務使用憑證讀取存取執行所在的使用者帳戶。  

**範例︰使用用戶端憑證**

此連結服務會將您的資料處理站與內部部署 HTTP web 伺服器連結。 它會使用已安裝資料管理閘道的電腦上所安裝的用戶端憑證。

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

**範例︰使用檔案中的用戶端憑證**

此連結服務會將您的資料處理站與內部部署 HTTP web 伺服器連結。 它會使用資料管理閘道安裝所在電腦上的用戶端憑證檔案。

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

所有資料集類型 (Azure SQL Database、Azure Blob 儲存體、Azure 資料表儲存體) 的資料集 JSON 檔案會有某些區段很類似 (例如結構、可用性及原則)。

如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)。

不同類型資料集的 **typeProperties** 區段不同。 **typeProperties** 區段可提供資料存放區中的資料位置資訊。 類型為 **Http** 的資料集中，其 **typeProperties** 區段有下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的**類型**必須設定為 **Http**。 | 是 |
| relativeUrl | 包含資料之資源的相對 URL。 當路徑未指定時，則只會使用在連結服務定義中指定的 URL。 <br><br> 若要建構動態 URL，您可以使用 [Data Factory 函式和系統變數](data-factory-functions-variables.md)。 範例：**relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)**。 | 否 |
| requestMethod | HTTP 方法。 允許的值為 **GET** 和 **POST**。 | 否 <br />(預設值為 **GET**) |
| additionalHeaders | 其他 HTTP 要求標頭。 | 否 |
| requestBody | HTTP 要求的主體。 | 否 |
| format | 如果您想要「從 HTTP 端點依現狀擷取資料」** 而不剖析它，請略過此**格式**設定。 <br><br> 如果您想要在複製期間剖析 HTTP 回應內容，支援下列格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 如需詳細資訊，請參閱[文字格式](data-factory-supported-file-and-compression-formats.md#text-format)、[JSON 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)。 |否 |
| compression | 指定此資料的壓縮類型和層級。 支援的類型：**GZip**、**Deflate**、**BZip2** 及 **ZipDeflate**。 支援的層級：**Optimal** 和 **Fastest**。 如需詳細資訊，請參閱 [Azure Data Factory 中的檔案和壓縮格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

**範例︰使用 GET (預設值) 方法**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**範例︰使用 POST 方法**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

如需可用來定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)。 

活動 **typeProperties** 區段中的可用屬性，會因為活動類型不同而有所差異。 就複製活動而言，屬性會根據來源和接收器的類型而有所不同。

目前，當複製活動中的來源類型為 **HttpSource** 時，支援下列屬性：

| 屬性 | 描述 | 必要 |
| -------- | ----------- | -------- |
| httpRequestTimeout | 用來取得回應的 HTTP 要求會有的逾時值 (**TimeSpan** 值)。 逾時會取得回應，而非逾時讀取回應資料。 | 否<br />(預設值 **:00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>支援的檔案和壓縮格式

如需詳細資訊，請參閱 [Azure Data Factory 中的檔案和壓縮格式](data-factory-supported-file-and-compression-formats.md)。

## <a name="json-examples"></a>JSON 範例

以下示例提供了使用[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)創建管道的範例 JSON 定義。 這些範例示範如何將資料從 HTTP 來源複製到 Azure Blob 儲存體。 不過，您可以使用 Azure Data Factory 中的「複製活動」，將資料從任何來源「直接」** 複製到[所支援的](data-factory-data-movement-activities.md#supported-data-stores-and-formats)任何接收。

**範例：將資料從 HTTP 來源複製到 Azure Blob 儲存體**

此範例的 Data Factory 方案包含下列資料處理站實體︰

*   一個 [HTTP](#linked-service-properties) 類型的連結服務。
*   [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)類型的連結服務。
*   [Http](#dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
*   [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)類型的輸出[資料集](data-factory-create-datasets.md)。
*   [管線](data-factory-create-pipelines.md)，其擁有的複製活動會使用 [HttpSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

範例會每隔一小時就把 HTTP 來源的資料複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

### <a name="http-linked-service"></a>HTTP 連結服務

此範例會使用匿名驗證來使用 HTTP 連結服務。 請參閱 [HTTP 連結服務](#linked-service-properties)，來了解您可以使用的不同驗證類型。

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure 儲存體連結服務

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP 輸入資料集

將 **external** 設定為 **true** 會通知 Data Factory 服務：這是 Data Factory 外部的資料集而且不是由 Data Factory 中的活動所產生。

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure Blob 輸出資料集

數據每小時寫入一個新的 Blob(**頻率**:**小時**,**間隔**: **1**)。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>使用複製活動的管線

管線包含複製活動，該活動已設定為使用輸入和輸出資料集。 複製活動排程為每小時執行一次。 在管線 JSON 定義中，**source** 類型設為 **HttpSource**，而 **sink** 類型設為 **BlobSink**。

如需 **HttpSource** 所支援屬性的清單，請參閱 [HttpSource](#copy-activity-properties)。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> 若要將來自來源資料集的資料行與來自接收資料集的資料行對應，請參閱[在 Azure Data Factory 中對應資料集資料行](data-factory-map-columns.md)。

## <a name="performance-and-tuning"></a>效能和微調

若要了解 Azure Data Factory 中影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法，請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)。
