---
title: 資料集
description: 深入瞭解 Data Factory 中的資料集。 資料集代表輸入/輸出資料。
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 6a9de6ff174adc5f6be9647560ad4e26367cb4ed
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500116"
---
# <a name="datasets-in-azure-data-factory"></a>Azure Data Factory 中的資料集
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-create-datasets.md)
> * [目前的版本](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


本文說明什麼是資料集、如何以 JSON 格式定義它們，以及如何在 Azure Data Factory 管線中使用它們。

如果您不熟悉 Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)來概略了解。

## <a name="overview"></a>概觀
資料處理站可以有一或多個管線。 「管線」是一起執行某個工作的「活動」所組成的邏輯群組。 管線中的活動會定義要在資料上執行的動作。 現在，「資料集」是一個具名的資料檢視，指向或參考您想要在「活動」中用來作為輸入或輸出的資料。 資料集可識別資料表、檔案、資料夾和文件等各種資料存放區中的資料。 例如，Azure Blob 資料集會指定活動應從中讀取資料之 Blob 儲存體中的 Blob 容器和資料夾。

在您建立資料集之前，您必須建立一個 [**連結服務**](concepts-linked-services.md)，以將資料存放區連結到資料處理站。 已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 這麼說吧：資料集代表已連結之資料存放區內的資料結構，而已連結的服務則定義與資料來源的連線。 例如，「Azure 儲存體」已連結服務會將儲存體帳戶連結到 Data Factory。 Azure Blob 資料集代表該 Azure 儲存體帳戶內包含要處理之輸入 Blob 的 Blob 容器和資料夾。

以下是一個範例案例。 若要將資料從 Blob 儲存體複製到 SQL Database，您可以建立兩個連結服務： Azure Blob 儲存體和 Azure SQL Database。 接著，建立兩個資料集：分隔的文字資料集 (參考 Azure Blob 儲存體連結服務，假設您有文字檔作為來源) ，而 Azure SQL 資料表資料集 (參考 Azure SQL Database 連結服務) 。 Azure Blob 儲存體和 Azure SQL Database 連結的服務包含連接字串，Data Factory 在執行時間分別用來連接到您的 Azure 儲存體和 Azure SQL Database。 分隔的文字資料集會指定 blob 容器和 blob 資料夾，其中包含您 Blob 儲存體中的輸入 blob，以及與格式相關的設定。 「Azure SQL 資料表」資料集會指定做為資料複製目的地的 SQL Database 中 SQL 資料表。

下圖顯示 Data Factory 中管線、活動、資料集及已連結服務之間的關聯性：

![管線、活動、資料集、已連結的服務之間的關聯性](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>資料集 JSON
Data Factory 中的資料集會以下列 JSON 格式定義：

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

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
type | 資料集的類型。 指定 Data Factory (所支援的其中一種類型，例如： DelimitedText、AzureSqlTable) 。 <br/><br/>如需詳細資料，請參閱[資料集類型](#dataset-type)。 | 是 |
結構描述 | 資料集的架構，表示實體資料類型和圖形。 | 否 |
typeProperties | 每種類型的類型屬性都不同。 如需有關支援的類型及其屬性的詳細資料，請參閱[資料集類型](#dataset-type)。 | 是 |

當您匯入資料集的架構時，請選取 [匯 **入架構** ] 按鈕，然後選擇從來源或從本機檔案匯入。 在大部分情況下，您會直接從來源匯入結構描述。 但是，如果您已經有本機結構描述檔案 (Parquet 檔案或具有標頭的 CSV 檔案)，您可以將 Data Factory 導向該檔案做為結構描述的基底。

在複製活動中，資料集會用於來源和接收。 資料集中定義的架構是選擇性的參考。 如果您想要套用來源與接收之間的資料行/欄位對應，請參閱 [架構和類型對應](copy-activity-schema-and-type-mapping.md)。

在資料流中，會在來源和接收轉換中使用資料集。 資料集會定義基本資料結構描述。 如果您的資料沒有結構描述，您可以對來源和接收使用結構描述漂移。 資料集中的中繼資料會顯示在來源轉換中，做為來源「投影」。 來源轉換中的投影代表了具有定義名稱和類型的資料流資料。

## <a name="dataset-type"></a>資料集類型

Azure Data Factory 支援許多不同類型的資料集，視您使用的資料存放區而定。 您可以從 [連接器的總覽](connector-overview.md) 文章中找到 Data Factory 所支援的資料存放區清單。 按一下資料存放區，以瞭解如何建立連結服務和資料集。

例如，如果是分隔的文字資料集，資料集類型會設定為 **DelimitedText** ，如下列 JSON 範例所示：

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

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
