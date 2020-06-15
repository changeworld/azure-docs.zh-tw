---
title: 資料集
description: 深入瞭解 Data Factory 中的資料集。 資料集代表輸入/輸出資料。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/25/2019
ms.openlocfilehash: 122725bff616a49d27981b88f465e04418db9526
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826106"
---
# <a name="datasets-in-azure-data-factory"></a>Azure Data Factory 中的資料集
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-create-datasets.md)
> * [目前的版本](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


本文說明什麼是資料集、如何以 JSON 格式定義它們，以及如何在 Azure Data Factory 管線中使用它們。

如果您不熟悉 Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)來概略了解。

## <a name="overview"></a>概觀
資料處理站可以有一或多個管線。 「管線」是一起執行某個工作的「活動」所組成的邏輯群組。 管線中的活動會定義要在資料上執行的動作。 現在，「資料集」是一個具名的資料檢視，指向或參考您想要在「活動」中用來作為輸入或輸出的資料。 資料集可識別資料表、檔案、資料夾和文件等各種資料存放區中的資料。 例如，Azure Blob 資料集會指定活動應從中讀取資料之 Blob 儲存體中的 Blob 容器和資料夾。

在您建立資料集之前，您必須建立一個[**連結服務**](concepts-linked-services.md)，以將資料存放區連結到資料處理站。 已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 這麼說吧：資料集代表已連結之資料存放區內的資料結構，而已連結的服務則定義與資料來源的連線。 例如，「Azure 儲存體」已連結服務會將儲存體帳戶連結到 Data Factory。 Azure Blob 資料集代表該 Azure 儲存體帳戶內包含要處理之輸入 Blob 的 Blob 容器和資料夾。

以下是一個範例案例。 若要將資料從 Blob 儲存體複製到 SQL Database，您要建立兩個連結服務：類型為 Azure 儲存體和 Azure SQL Database。 接著，建立兩個資料集：Azure Blob 資料集 (此資料集參考 Azure 儲存體連結服務) 和 Azure SQL 資料表資料集 (此資料集參考 Azure SQL Database 連結服務)。 「Azure 儲存體」和 Azure SQL Database 已連結服務包含 Data Factory 在執行階段分別用來連接到「Azure 儲存體」和 Azure SQL Database 的連接字串。 Azure Blob 資料集會指定包含 Blob 儲存體中輸入 Blob 的 Blob 容器和 Blob 資料夾。 「Azure SQL 資料表」資料集會指定做為資料複製目的地的 SQL Database 中 SQL 資料表。

下圖顯示 Data Factory 中管線、活動、資料集及已連結服務之間的關聯性：

![管線、活動、資料集、已連結的服務之間的關聯性](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>資料集 JSON
Data Factory 中的資料集會以下列 JSON 格式定義：

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
下表描述上述 JSON 的屬性：

屬性 | 描述 | 必要 |
-------- | ----------- | -------- |
NAME | 資料集的名稱。 請參閱 [Azure Data Factory - 命名規則](naming-rules.md)。 |  是 |
type | 資料集的類型。 指定 Data Factory 支援的其中一種類型 (例如︰AzureBlob、AzureSqlTable)。 <br/><br/>如需詳細資料，請參閱[資料集類型](#dataset-type)。 | 是 |
structure | 資料集的結構描述。 如需詳細資料，請參閱[資料集結構描述](#dataset-structure-or-schema)。 | 否 |
typeProperties | 每個類型的類型屬性都不同 (例如：Azure Blob、Azure SQL 資料表)。 如需有關支援的類型及其屬性的詳細資料，請參閱[資料集類型](#dataset-type)。 | 是 |

### <a name="data-flow-compatible-dataset"></a>相容資料流的資料集



如需相容[資料流](concepts-data-flow-overview.md)的資料集類型清單，請參閱[支援的資料集類型](#dataset-type)。 與資料流相容的資料集需要更細緻的資料集定義來進行轉換。 因此，JSON 定義會稍有不同。 與資料流相容的資料集沒有 _property_ 屬性，而是有 _schema_ 屬性。

在資料流中，會在來源和接收轉換中使用資料集。 資料集會定義基本資料結構描述。 如果您的資料沒有結構描述，您可以對來源和接收使用結構描述漂移。 資料集中的結構描述代表實體資料類型和圖形。

透過從資料集定義結構描述，您將會取得來自相關聯「已連結服務」的相關資料類型、資料格式、檔案位置和連接資訊。 資料集中的中繼資料會顯示在來源轉換中，做為來源「投影」。 來源轉換中的投影代表了具有定義名稱和類型的資料流資料。

當您匯入資料流資料集的結構描述時，請選取 [匯入結構描述] 按鈕，然後選擇從來源或從本機檔案匯入。 在大部分情況下，您會直接從來源匯入結構描述。 但是，如果您已經有本機結構描述檔案 (Parquet 檔案或具有標頭的 CSV 檔案)，您可以將 Data Factory 導向該檔案做為結構描述的基底。


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

下表描述上述 JSON 的屬性：

屬性 | 描述 | 必要 |
-------- | ----------- | -------- |
NAME | 資料集的名稱。 請參閱 [Azure Data Factory - 命名規則](naming-rules.md)。 |  是 |
type | 資料集的類型。 指定 Data Factory 支援的其中一種類型 (例如︰AzureBlob、AzureSqlTable)。 <br/><br/>如需詳細資料，請參閱[資料集類型](#dataset-type)。 | 是 |
結構描述 | 資料集的結構描述。 如需詳細資訊，請參閱[相容資料流的資料集](#dataset-type)。 | 否 |
typeProperties | 每個類型的類型屬性都不同 (例如：Azure Blob、Azure SQL 資料表)。 如需有關支援的類型及其屬性的詳細資料，請參閱[資料集類型](#dataset-type)。 | 是 |


## <a name="dataset-example"></a>資料集範例
在下列範例中，資料集代表 SQL Database 中名為 MyTable 的資料表。

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
請注意下列幾點：

- 類型設為 AzureSqlTable。
- tableName 類型屬性 (針對 AzureSqlTable 類型) 設定為 MyTable。
- linkedServiceName 係指 AzureSqlDatabase 類型的已連結服務，在接下來的 JSON 程式碼片段中將會定義。

## <a name="dataset-type"></a>資料集類型
有許多不同類型的資料集，視您使用的資料存放區而定。 您可以從[連接器概觀](connector-overview.md)文章中，找到 Data Factory 所支援的已儲存資料清單。 按一下某個資料存放區，即可了解如何為該資料存放區建立已連結的服務和資料集。

在上一節的範例中，資料集的類型是設定為 **AzureSqlTable**。 同樣地，針對 Azure Blob 資料集，資料集的類型是設定為 **AzureBlob**，如以下 JSON 所示：

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>資料集結構或結構描述
**結構**區段或**結構描述** (與資料流相容) 區段的資料集屬於選擇性。 它可透過包含資料行之名稱和資料類型的集合，定義資料集的結構描述。 您可以使用 structure 區段來提供類型資訊，此資訊會用來轉換類型並將資料行從來源對應到目的地。

structure 中的每個資料行都包含下列屬性︰

屬性 | 描述 | 必要
-------- | ----------- | --------
NAME | 資料行的名稱。 | 是
type | 資料行的資料類型。 Data Factory 支援以下列過渡資料類型當作值：**Int16、Int32、Int64、Single、Double、Decimal、Byte[]、Boolean、String、Guid、Datetime、Datetimeoffset 及 Timespan** | 否
culture | 當類型為 .NET 類型 (`Datetime` 或 `Datetimeoffset`) 時，所要使用的 .NET 型文化特性。 預設值為 `en-us`。 | 否
format | 當類型為 .NET 類型 (`Datetime` 或 `Datetimeoffset`) 時，所要使用的格式字串。 有關如何格式化日期時間的資訊，請參閱[自訂日期和時間格式字串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 | 否

### <a name="example"></a>範例
在下列範例中，假設來源 Blob 資料是 CSV 格式，而且包含三個資料行：userid、name 和 lastlogindate。 這些是含有自訂日期時間格式 (使用法文縮寫星期幾名稱) 的 Int64、String 和 Datetime 類型。

請依下列方式，定義 Blob 資料集結構及資料行的類型定義：

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>指引

下列指引可協助您了解何時要包括 structure 資訊，以及在 **structure** 區段中要包含哪些資訊。 從[結構描述和類型對應](copy-activity-schema-and-type-mapping.md)深入了解資料處理站如何將來源資料對應至接收，以及何時該指定結構資訊。

- **針對強式結構描述的資料來源**，請只有在您想要將來源資料行對應到接收資料行且其名稱不相同時，才指定 structure 區段。 這類結構化資料來源會將資料結構描述和類型資訊與資料本身儲存在一起。 結構化資料來源的範例包括 SQL Server、Oracle 及 Azure SQL Database。<br/><br/>由於結構化資料來源已經有可用的類型資訊，因此當您包含 structure 區段時，便不應包含類型資訊。
- **針對無/弱式結構描述的資料來源，例如 Blob 儲存體中的文字檔案**，請在資料集是複製活動的輸入，並且來源資料集的資料類型應該轉換成接收器的原生類型時，包含結構。 並且也在您想要將來源資料行與接收資料行對應時，包含結構

## <a name="create-datasets"></a>建立資料集
您可以使用下列其中一個工具或 SDK 來建立資料集：[.NET API](quickstart-create-data-factory-dot-net.md)、[PowerShell](quickstart-create-data-factory-powershell.md)、[REST API](quickstart-create-data-factory-rest-api.md)、Azure Resource Manager 範本及 Azure 入口網站

## <a name="current-version-vs-version-1-datasets"></a>目前版本與版本 1 資料集

以下是 Data Factory 與 Data Factory 版本 1 資料集之間的一些差異：

- 目前版本中不支援外部屬性。 已由[觸發程序](concepts-pipeline-execution-triggers.md)取代它。
- 目前版本中不支援原則和可用性屬性。 管線的開始時間取決於[觸發程序](concepts-pipeline-execution-triggers.md)。
- 目前版本中不支援範圍資料集 (管線中定義的資料集)。

## <a name="next-steps"></a>後續步驟
如需使用上述其中一個工具或 SDK 來建立管線和資料集的逐步指示，請參閱下列教學課程。

- [快速入門：使用 .NET 來建立資料處理站](quickstart-create-data-factory-dot-net.md)
- [快速入門：使用 PowerShell 來建立資料處理站](quickstart-create-data-factory-powershell.md)
- [快速入門：使用 REST API 來建立資料處理站](quickstart-create-data-factory-rest-api.md)
- [快速入門：使用 Azure 入口網站來建立資料處理站](quickstart-create-data-factory-portal.md)
