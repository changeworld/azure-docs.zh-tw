---
title: 從 ODBC 資料存放區移動資料
description: 深入了解如何使用 Azure Data Factory 從 ODBC 資料存放區移動資料
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e1735c2d2ed107f7ec65d68a6826267ee83a93f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707373"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>使用 Azure Data Factory 從 ODBC 資料存放區移動資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](data-factory-odbc-connector.md)
> * [第 2 版 (目前的版本)](../connector-odbc.md)

> [!NOTE]
> 本文適用於 Data Factory 第 1 版。 如果您使用目前版本的 Data Factory 服務，請參閱[第 2 版中的 ODBC 連接器](../connector-odbc.md)。


本文說明如何使用 Azure Data Factory 中的「複製活動」，從內部部署的 ODBC 資料存放區移動資料。 它是以「 [資料移動活動](data-factory-data-movement-activities.md) 」一文為基礎，提供使用複製活動來移動資料的一般總覽。

您可以將資料從 ODBC 資料存放區複製到任何支援的接收資料存放區。 如需複製活動所支援作為接收器的資料存放區清單，請參閱 [支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 資料表。 Data Factory 目前只支援將資料從 ODBC 資料存放區移到其他資料存放區，而不支援將資料從其他資料存放區移到 ODBC 資料存放區。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>啟用連線
Data Factory 服務支援使用資料管理閘道器連接至內部部署 ODBC 來源。 請參閱 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文來了解資料管理閘道和設定閘道的逐步指示。 即使 ODBC 資料存放區裝載於 Azure IaaS VM 中，仍請使用閘道來與其連接。

您可以將閘道安裝在與 ODBC 資料存放區相同的內部部署機器或 Azure VM 上。 不過，建議您將閘道安裝在個別的機器/Azure IaaS VM 上，除了可避免發生資源爭用的情況之外，也可獲得較佳的效能。 當您在不同機器上安裝閘道器時，機器應該能夠存取具有 ODBC 資料存放區的機器。

除了資料管理閘道器，您也需要在閘道器機器上的資料存放區安裝 ODBC 驅動程式。

> [!NOTE]
> 如需連接/閘道器相關問題的疑難排解秘訣，請參閱 [針對閘道問題進行疑難排解](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) 。

## <a name="getting-started"></a>開始使用
您可以藉由使用不同的工具/API，建立內含複製活動的管線，以從 ODBC 資料存放區移動資料。

若要建立管線，最簡單的方式就是使用「 **複製嚮導**」。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

您也可以使用下列工具來建立管線： **Visual Studio**、 **Azure PowerShell**、 **Azure Resource Manager 範本**、 **.net API**和 **REST API**。 請參閱「 [複製活動」教學](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 課程，以取得使用複製活動建立管線的逐步指示。

不論您是使用工具還是 API，都需執行下列步驟來建立將資料從來源資料存放區移到接收資料存放區的管線：

1. 建立**連結服務**，將輸入和輸出資料存放區連結到資料處理站。
2. 建立 **資料集** 以代表複製作業的輸入和輸出資料。
3. 建立具有複製活動的 **管線** ，該活動會採用資料集做為輸入，並使用資料集做為輸出。

使用精靈時，精靈會自動為您建立這些 Data Factory 實體 (已連結的服務、資料集及管線) 的 JSON 定義。 使用工具/API (.NET API 除外) 時，您需使用 JSON 格式來定義這些 Data Factory 實體。 如需相關範例，其中含有用來從 ODBC 資料存放區複製資料之 Data Factory 實體的 JSON 定義，請參閱本文的 [JSON 範例：將資料從 ODBC 資料存放區複製到 Azure Blob](#json-example-copy-data-from-odbc-data-store-to-azure-blob) 一節。

下列各節提供 JSON 屬性的相關詳細資料，這些屬性是用來定義 ODBC 資料存放區特定的 Data Factory 實體：

## <a name="linked-service-properties"></a>連結服務屬性
下表提供 ODBC 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| type |類型屬性必須設為： **OnPremisesOdbc** |是 |
| connectionString |連接字串的非存取認證部分和選擇性的加密認證。 請參閱下列幾節中的範例。 <br/><br/>您可以用 `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` 模式指定連接字串，或使用您在閘道電腦上以 `"DSN=<name of the DSN>;"` 設定的系統 DSN (資料來源名稱) (仍需要據此指定連結的服務中的認證部分)。 |是 |
| 認證 (credential) |以驅動程式特定「屬性-值」格式指定之連接字串的存取認證部分。 範例： `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |否 |
| authenticationType |用來連接到 ODBC 資料存放區的驗證類型。 可能的值為：Anonymous 和 Basic。 |是 |
| userName |如果您是使用基本驗證，請指定使用者名稱。 |否 |
| 密碼 |指定您為使用者名稱指定之使用者帳戶的密碼。 |否 |
| gatewayName |Data Factory 服務應該用來連接到 ODBC 資料存放區的閘道器名稱。 |是 |

### <a name="using-basic-authentication"></a>使用基本驗證

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>使用基本驗證與加密認證
您可以使用 [新的-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) (1.0 版的 Azure PowerShell) Cmdlet 或 [才能 new-azuredatafactoryencryptvalue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 或舊版的 Azure PowerShell) 來加密認證。

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>使用匿名驗證

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **>typeproperties** 區段都不同，並提供資料存放區中資料位置的相關資訊。 **RelationalTable** 資料集類型的 typeProperties 區段 (包含 ODBC 資料集) 具有下列屬性

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| tableName |ODBC 資料存放區中資料表的名稱。 |是 |

## <a name="copy-activity-properties"></a>複製活動屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

另一方面，活動的 **>typeproperties** 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

在複製活動中，如果來源的類型為 **RelationalSource** (包括 ODBC)，則 typeProperties 區段中可使用下列屬性：

| 屬性 | 描述 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| 查詢 |使用自訂查詢來讀取資料。 |SQL 查詢字串。 例如：select * from MyTable。 |是 |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON 範例：將資料從 ODBC 資料存放區複製到 Azure Blob
此範例提供 JSON 定義，可讓您用來建立使用 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)的管線。 它示範如何將資料從 ODBC 來源複製到「Azure Blob 儲存體」。 不過，您可以在 Azure Data Factory 中使用複製活動，將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。

此範例具有下列 Data Factory 實體：

1. [OnPremisesOdbc](#linked-service-properties)類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)類型的連結服務。
3. [RelationalTable](#dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有複製活動的管線，該 [管線](data-factory-create-pipelines.md) 會使用 [>relationalsource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此範例會每個小時將資料從 ODBC 資料存放區中的查詢結果複製到 Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

第一步是設定資料管理閘道。 如需相關指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 。

**ODBC 連結服務** 此範例會使用 Basic 驗證。 請參閱 [ODBC 連結服務](#linked-service-properties) 章節以了解您可以使用的各種不同類型的驗證。

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure 儲存體連結服務**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**ODBC 輸入資料集**

此範例假設您已在 ODBC 資料庫中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。

設定 “external”: ”true” 會通知 Data Factory 服務：這是 Data Factory 外部的資料集而且不是由 Data Factory 中的活動所產生。

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure Blob 輸出資料集**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**具有 ODBC 來源 (RelationalSource) 和 Blob 接收器 (BlobSink) 的管線中複製活動**

此管線包含「複製活動」，該活動已設定為使用這些輸入和輸出資料集，並且排定為每小時執行。 在管線 JSON 定義中，已將 **source** 類型設為 **RelationalSource**，並將 **sink** 類型設為 **BlobSink**。 針對 **query** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>ODBC 的類型對應
如同 [資料移動活動](data-factory-data-movement-activities.md) 文章中所述，複製活動會使用下列兩個步驟的方法，執行從來源類型轉換成接收類型的自動類型轉換：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 ODBC 資料存放區移動資料時，ODBC 資料類型會對應至 .NET 類型，如 [ODBC 資料類型對應](https://msdn.microsoft.com/library/cc668763.aspx) 主題中所述。

## <a name="map-source-to-sink-columns"></a>將來源對應到接收資料行
若要了解如何將來源資料集內的資料行與接收資料集內的資料行對應，請參閱[在 Azure Data Factory 中對應資料集資料行](data-factory-map-columns.md)。

## <a name="repeatable-read-from-relational-sources"></a>從關聯式來源進行可重複的讀取
從關聯式資料存放區複製資料時，請將可重複性謹記在心，以避免產生非預期的結果。 在 Azure Data Factory 中，您可以手動重新執行配量。 您也可以為資料集設定重試原則，使得在發生失敗時，重新執行配量。 以上述任一方式重新執行配量時，您必須確保不論將配量執行多少次，都會讀取相同的資料。 請參閱 [關聯式來源的可重複讀取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)。

## <a name="troubleshoot-connectivity-issues"></a>疑難排解連線問題
若要針對連線問題進行疑難排解，請使用 [資料管理閘道器組態管理員]**** 的 [診斷]**** 索引標籤。

1. 啟動 **資料管理閘道器組態管理員**。 您可以直接執行 "C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe" (或) 搜尋**閘道**，以尋找 **Microsoft 資料管理閘道**應用程式的連結，如下圖所示。

    ![搜尋閘道器](./media/data-factory-odbc-connector/search-gateway.png)
2. 切換至 [診斷] **** 索引標籤。

    ![閘道診斷](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. 選取資料存放區的 **類型** (連結的服務)。
4. 指定 [驗證]****，然後輸入用來連線到資料存放區的**認證** (或) 輸入**連接字串**。
5. 按一下 [測試連線] **** 以測試資料存放區連線。

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。
