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
ms.openlocfilehash: ad26fca94527864af10bb0051336c372ea65b3e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81413806"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP ECC 複製資料
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 SAP Enterprise Central Component （ECC）複製資料。 如需詳細資訊，請參閱[複製活動總覽](copy-activity-overview.md)。

>[!TIP]
>若要瞭解 ADF 對於 SAP 資料整合案例的整體支援，請參閱[使用 Azure Data Factory 白皮書的 SAP 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，其中包含詳細的簡介、comparsion 和指引。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 SAP ECC 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 SAP ECC 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱支援的[資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，此 SAP ECC 連接器支援：

- 在 SAP NetWeaver 7.0 版和更新版本上從 SAP ECC 複製資料。
- 從 SAP ECC OData 服務所公開的任何物件複製資料，例如：

  - SAP 資料表或 views。
  - 商務應用程式開發介面 [BAPI] 物件。
  - 資料擷取器。
  - 傳送至 SAP 進程整合（PI）的資料或中繼檔（Idoc），可透過相對介面卡以 OData 的形式接收。

- 使用基本驗證來複製資料。

>[!TIP]
>若要透過 SAP 資料表或 view 從 SAP ECC 複製資料，請使用[sap 資料表](connector-sap-table.md)連接器，這會更快速且更具擴充性。

## <a name="prerequisites"></a>先決條件

一般而言，SAP ECC 會透過 OData 服務經由 SAP 閘道公開實體。 若要使用此 SAP ECC 連接器，您必須：

- **設定 SAP 閘道**。 針對 SAP NetWeaver 版本超過7.4 的伺服器，已安裝 SAP 閘道。 在舊版中，您必須先安裝內嵌的 SAP 閘道或 SAP 閘道中樞系統，才能透過 OData 服務公開 SAP ECC 資料。 若要設定 SAP 閘道，請參閱[安裝指南](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)。

- **啟用和設定 SAP OData 服務**。 您可以透過 TCODE SICF 來啟動 OData 服務（以秒為單位）。 您也可以設定需要公開的物件。 如需詳細資訊，請參閱[逐步指導](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)方針。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SAP ECC 連接器特定的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是 SAP ECC 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| `type` | `type` 屬性必須設為 `SapEcc`。 | 是 |
| `url` | SAP ECC OData 服務的 URL。 | 是 |
| `username` | 用來連接到 SAP ECC 的使用者名稱。 | 否 |
| `password` | 用來連接到 SAP ECC 的純文字密碼。 | 否 |
| `connectVia` | 要用來連接到資料存放區的[整合運行](concepts-integration-runtime.md)時間。 深入瞭解[必要條件](#prerequisites)一節。 如果您未指定執行時間，則會使用預設的 Azure integration runtime。 | 否 |

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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。 下一節提供 SAP ECC 資料集所支援的屬性清單。

若要從 SAP ECC 複製資料，請`type`將資料集的屬性`SapEccResource`設定為。

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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 下一節提供 SAP ECC 來源所支援的屬性清單。

### <a name="sap-ecc-as-a-source"></a>作為來源的 SAP ECC

若要從 SAP ECC 複製資料，請`type`將複製活動`source`的區段中的屬性設定`SapEccSource`為。

複製活動的`source`區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| `type` | 複製`type`活動`source`區段的屬性必須設定為`SapEccSource`。 | 是 |
| `query` | 用來篩選資料的 OData 查詢選項。 例如：<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC 連接器會從合併的 URL 複製資料：<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>如需詳細資訊，請參閱 [OData URL 元件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |

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

## <a name="data-type-mappings-for-sap-ecc"></a>SAP ECC 的資料類型對應

當您從 SAP ECC 複製資料時，會使用下列從 SAP ECC 資料的 OData 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

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
> 目前不支援複雜資料類型。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
