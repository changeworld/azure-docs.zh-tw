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
ms.date: 08/03/2020
ms.openlocfilehash: 9088b36acead9f47e94949ee102d66a8aff2d226
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529597"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP ECC 複製資料
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的複製活動，從 SAP Enterprise Central Component (ECC) 複製資料。 如需詳細資訊，請參閱[複製活動概觀](copy-activity-overview.md)。

>[!TIP]
>若要瞭解 ADF 對於 SAP 資料整合案例的整體支援，請參閱[使用 Azure Data Factory 白皮書的 SAP 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，以及每個 sap 連接器、comparsion 和指引的詳細介紹。

## <a name="supported-capabilities"></a>支援的功能

此 SAP ECC 連接器支援下列活動：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 SAP ECC 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，此 SAP ECC 連接器支援：

- 在 SAP NetWeaver 7.0 版和更新版本上從 SAP ECC 複製資料。
- 從 SAP ECC OData 服務所公開的任何物件複製資料，例如：

  - SAP 資料表或檢視表。
  - 商務應用程式開發介面 [BAPI] 物件。
  - 資料擷取器。
  - 傳送至 SAP 程序整合 (PI) 且可透過相對配接器以 OData 形式接收的資料或中繼文件 (IDOC)。

- 使用基本驗證來複製資料。

>[!TIP]
>若要透過 SAP 資料表或檢視表從 SAP ECC 複製資料，請使用更快速且更具可擴縮性的 [SAP 資料表](connector-sap-table.md)連接器。

## <a name="prerequisites"></a>必要條件

若要使用此 SAP ECC 連接器，您必須透過 SAP 閘道，經由 OData 服務公開 SAP ECC 實體。 具體而言：

- **設定 SAP 閘道**。 如果伺服器使用 7.4 以後的 SAP NetWeaver 版本，則必須已安裝 SAP Gateway。 針對舊版，您必須先安裝內嵌 SAP Gateway 或 SAP Gateway 中樞系統，再透過 OData 服務公開 SAP ECC 資料。 若要安裝 SAP Gateway，請參閱 [Installation Guide](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm) (安裝指南)。

- **啟動及設定 SAP OData 服務**。 您可透過 TCODE SICF，在短短數秒內啟動 OData 服務。 您也可以設定哪些物件需要公開。 如需詳細資訊，請參閱 [Step-by-step guide](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/) (逐步指南)。

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節將針對用來定義 SAP ECC 連接器特定 Data Factory 實體的屬性提供相關詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

SAP ECC 連結服務支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| `type` | `type` 屬性必須設定為 `SapEcc`。 | 是 |
| `url` | SAP ECC OData 服務的 URL。 | 是 |
| `username` | 用來連線到 SAP ECC 的使用者名稱。 | 否 |
| `password` | 用來連線到 SAP ECC 的純文字密碼。 | 否 |
| `connectVia` | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 深入了解[必要條件](#prerequisites)一節。 如果未指定執行階段，則會使用預設的 Azure Integration Runtime。 | 否 |

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

若要從 SAP ECC 複製資料，請將資料集的 `type` 屬性設定為 `SapEccResource`。

以下是支援的屬性：

| 屬性 | 說明 | 必要 |
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

### <a name="sap-ecc-as-a-source"></a>以 SAP ECC 作為來源

若要從 SAP ECC 複製資料，請將複製活動 `source` 區段中的 `type` 屬性設定為 `SapEccSource`。

複製活動的 `source` 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| `type` | 複製活動 `source` 區段的 `type` 屬性必須設定為 `SapEccSource`。 | 是 |
| `query` | 用來篩選資料的 OData 查詢選項。 例如：<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC 連接器會從以下組合的 URL 複製資料：<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>如需詳細資訊，請參閱 [OData URL 元件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |
| `sapDataColumnDelimiter` | 用來當做分隔符號的單一字元，會傳遞給 SAP RFC 以分割輸出資料。 | 否 |
| `httpRequestTimeout` | 用來取得回應的 HTTP 要求會有的逾時值 (**TimeSpan** 值)。 此值是取得回應的逾時值，而非讀取回應資料的逾時值。 如果未指定，預設值為**00:30:00** （30分鐘）。 | 否 |

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

當從 SAP ECC 複製資料時會使用下列對應，其從 SAP ECC 資料的 OData 資料類型對應到 Azure Data Factory 過渡期資料類型。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

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

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
