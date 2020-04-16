---
title: Azure Data Factory 中的連結服務
description: 了解數據工廠中的鏈接服務。 已連結的服務會將計算/資料存放區連結至資料處理站。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: a6002ed173ca5358df4257f4c8b41c88bcf60ad8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418366"
---
# <a name="linked-services-in-azure-data-factory"></a>Azure Data Factory 中的連結服務

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](v1/data-factory-create-datasets.md)
> * [目前版本](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介紹了鏈接服務是什麼,如何以 JSON 格式定義它們,以及如何在 Azure 數據工廠管道中使用它們。

如果您不熟悉 Data Factory，請參閱 [Azure Data Factory 簡介](introduction.md)來概略了解。

## <a name="overview"></a>概觀

資料處理站可以有一或多個管線。 **管道**是共同執行任務**的活動**的邏輯分組。 管線中的活動會定義要在資料上執行的動作。 例如，您可以使用複製活動將資料從內部部署 SQL Server 複製到 Azure Blob 儲存體。 接著，您可以使用在 Azure HDInsight 叢集上執行 Hive 指令碼的 Hive 活動，來處理來自 Blob 儲存體的資料以產生輸出資料。 最後，您可以使用第二個複製活動將輸出資料複製到「Azure SQL 資料倉儲」，以在該處建置商業智慧 (BI) 報表解決方案。 如需有關管線和活動的詳細資訊，請參閱 Azure Data Factory 中的[管線和活動](concepts-pipelines-activities.md)。

現在，「資料集」**** 是一個具名的資料檢視，指向或參考您想要在「活動」**** 中用來作為輸入或輸出的資料。

在您建立資料集之前，您必須建立一個「已連結的服務」****，以將資料存放區連結到資料處理站。 已連結的服務非常類似連接字串，可定義 Data Factory 連接到外部資源所需的連線資訊。 這麼說吧：資料集代表已連結之資料存放區內的資料結構，而已連結的服務則定義與資料來源的連線。 例如，「Azure 儲存體」已連結服務會將儲存體帳戶連結到 Data Factory。 Azure Blob 資料集表示 Blob 容器以及該 Azure 儲存帳戶中的資料夾,其中包含要處理的輸入 Blob。

以下是一個範例案例。 要將資料從 Blob 儲存複製到 SQL 資料庫,請建立兩個連結的服務:Azure 儲存和 Azure SQL 資料庫。 接著，建立兩個資料集：Azure Blob 資料集 (此資料集參考「Azure 儲存體」已連結服務) 和「Azure SQL 資料表」資料集 (此資料集參考 Azure SQL Database 已連結服務)。 「Azure 儲存體」和 Azure SQL Database 已連結服務包含 Data Factory 在執行階段分別用來連接到「Azure 儲存體」和 Azure SQL Database 的連接字串。 Azure Blob 資料集會指定包含 Blob 儲存體中輸入 Blob 的 Blob 容器和 Blob 資料夾。 Azure SQL 表資料集指定要將資料複製到 SQL 資料庫中的 SQL 表。

下圖顯示 Data Factory 中管線、活動、資料集及已連結服務之間的關聯性：

![管線、活動、資料集、已連結的服務之間的關聯性](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>連結服務 JSON

Data Factory 中的連結服務會以 JSON 格式定義如下：

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

下表描述上述 JSON 的屬性：

屬性 | 描述 | 必要 |
-------- | ----------- | -------- |
NAME | 連結服務的名稱。 請參閱 [Azure Data Factory - 命名規則](naming-rules.md)。 |  是 |
type | 連結服務的類型。 例如:Azure 儲存(資料存儲)或 Azure Batch(計算)。 請參閱 typeProperties 的描述。 | 是 |
typeProperties | 每個資料存放區和計算的類型屬性都不同。 <br/><br/> 如需支援的資料存放區類型及其類型屬性，請參閱本文章的[資料集類型](concepts-datasets-linked-services.md#dataset-type)表格。 請瀏覽資料存放區連接器的文章，以了解資料存放區特有的類型屬性。 <br/><br/> 如需支援的計算類型與其類型屬性，請參閱[計算連結服務](compute-linked-services.md)。 | 是 |
connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 否

## <a name="linked-service-example"></a>已連結的服務範例

以下連結服務是 Azure 儲存體連結服務。 請注意,類型已設置為 Azure 存儲。 Azure 儲存體連結服務的類型屬性包含連接字串。 Data Factory 服務會在執行階段使用連接字串來連線至資料存放區。

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>建立連結的服務

您可以使用這些工具或 SDK 之一建立連結的服務[:.NET](quickstart-create-data-factory-dot-net.md)API、PowerShell、REST [PowerShell](quickstart-create-data-factory-powershell.md) [API、Azure](quickstart-create-data-factory-rest-api.md)資源管理員範本和 Azure 門戶

## <a name="data-store-linked-services"></a>資料儲存連結服務

您可以在[連接器概述](copy-activity-overview.md#supported-data-stores-and-formats)文章中找到資料工廠支援的數據儲存清單。 單擊數據存儲以瞭解支援的連接屬性。

## <a name="compute-linked-services"></a>計算連結服務

有關可以從資料工廠連線到的不同計算環境以及不同設定的詳細資訊,[都支援參考計算環境](compute-linked-services.md)。

## <a name="next-steps"></a>後續步驟

如需使用上述其中一個工具或 SDK 來建立管線和資料集的逐步指示，請參閱下列教學課程。

- [快速入門：使用 .NET 來建立資料處理站](quickstart-create-data-factory-dot-net.md)
- [快速入門：使用 PowerShell 來建立資料處理站](quickstart-create-data-factory-powershell.md)
- [快速入門：使用 REST API 來建立資料處理站](quickstart-create-data-factory-rest-api.md)
- [快速入門：使用 Azure 入口網站來建立資料處理站](quickstart-create-data-factory-portal.md)
