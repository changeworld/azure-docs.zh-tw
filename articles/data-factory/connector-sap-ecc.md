---
title: 從 SAP ECC 複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 SAP ECC 複製到支援的接收資料存放區。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: f875d8f4603a8f51b8b8fed2438e6f3a30c87aeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931180"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>使用 Azure 資料工廠從 SAP ECC 複製資料

本文概述了如何使用 Azure 資料工廠中的複製活動從 SAP 企業中央元件 （ECC） 複製資料。 有關詳細資訊，請參閱[複製活動概述](copy-activity-overview.md)。

>[!TIP]
>要瞭解 ADF 對 SAP 資料整合方案的總體支援，請參閱[使用 Azure 資料工廠白皮書進行 SAP 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，並詳細說明介紹、比較和指導。

## <a name="supported-capabilities"></a>支援的功能

此 SAP ECC 連接器支援以下活動：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)

您可以將資料從 SAP ECC 複製到任何支援的接收資料存放區。 有關複製活動支援為源或接收器的資料存儲的清單，請參閱[支援資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)表。

具體而言，此 SAP ECC 連接器支援：

- 在 SAP NetWeaver 版本 7.0 及更高版本中從 SAP ECC 複製資料。
- 從 SAP ECC OData 服務公開的任何物件複製資料，例如：

  - SAP 表或視圖。
  - 業務應用程式開發介面 [BAPI] 物件。
  - 資料提取器。
  - 發送到 SAP 流程集成 （PI） 的資料或中間文檔 （IDOC），可通過相對配接器作為 OData 接收。

- 使用基本驗證複製資料。

>[!TIP]
>要通過 SAP 表或視圖從 SAP ECC 複製資料，請使用[SAP 表](connector-sap-table.md)連接器，該連接器更快、更具可擴充性。

## <a name="prerequisites"></a>Prerequisites

一般而言，SAP ECC 會透過 OData 服務經由 SAP 閘道公開實體。 若要使用此 SAP ECC 連接器，您必須：

- **設定 SAP 閘道**。 對於具有 7.4 晚版本的 SAP NetWeaver 的伺服器，已安裝 SAP 閘道。 對於早期版本，您必須安裝嵌入式 SAP 閘道或 SAP 閘道集線器系統，然後才能通過 OData 服務公開 SAP ECC 資料。 要設置 SAP 閘道，請參閱[安裝指南](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)。

- **啟動和配置 SAP OData 服務**。 您可以通過 TCODE SICF 在幾秒鐘內啟動 OData 服務。 您還可以配置需要公開的物件。 有關詳細資訊，請參閱分步[指南](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定于 SAP ECC 連接器的資料工廠實體的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

SAP ECC 連結服務支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| `type` | `type` 屬性必須設為 `SapEcc`。 | 是 |
| `url` | SAP ECC OData 服務的 URL。 | 是 |
| `username` | 用於連接到 SAP ECC 的使用者名。 | 否 |
| `password` | 用於連接到 SAP ECC 的純文字密碼。 | 否 |
| `connectVia` | 用於連接到資料存儲的[集成運行時](concepts-integration-runtime.md)。 從[先決條件](#prerequisites)部分瞭解更多資訊。 如果不指定運行時，將使用預設的 Azure 集成運行時。 | 否 |

### <a name="example"></a>範例

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

有關可用於定義資料集的節和屬性的完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。 以下部分提供了 SAP ECC 資料集支援的屬性的清單。

要從 SAP ECC 複製資料`type`，請將資料集的屬性設置為`SapEccResource`。

以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| `path` | SAP ECC OData 實體的路徑。 | 是 |

### <a name="example"></a>範例

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

有關可用於定義活動的節和屬性的完整清單，請參閱[管道](concepts-pipelines-activities.md)。 以下部分提供了 SAP ECC 源支援的屬性的清單。

### <a name="sap-ecc-as-a-source"></a>SAP ECC 作為源

要從 SAP ECC 複製資料`type`，請將複製`source`活動部分中的屬性設置為`SapEccSource`。

複製活動的`source`部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| `type` | 必須`type`將複製活動部分的屬性`source`設置為`SapEccSource`。 | 是 |
| `query` | 用於篩選資料的 OData 查詢選項。 例如：<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC 連接器從組合 URL 複製資料：<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>如需詳細資訊，請參閱 [OData URL 元件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |

### <a name="example"></a>範例

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>SAP ECC 的資料類型映射

從 SAP ECC 複製資料時，將使用以下映射從用於 SAP ECC 資料的 OData 資料類型到 Azure 資料工廠臨時資料類型。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| OData 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> 當前不支援複雜的資料類型。

## <a name="lookup-activity-properties"></a>查找活動屬性

要瞭解有關屬性的詳細資訊，請檢查[查找活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

有關 Azure 資料工廠中複製活動支援為源和接收器的資料存儲的清單，請參閱[支援資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)。
