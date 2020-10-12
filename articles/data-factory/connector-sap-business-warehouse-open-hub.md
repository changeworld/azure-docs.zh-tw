---
title: 透過 Open Hub 從 SAP Business 倉儲複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，透過 Open Hub 將資料從 SAP Business Warehouse (BW) 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 930c7e7881a00cd0cb1f4abc6b219c0fbdeebac5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533405"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>使用 Azure Data Factory 透過 Open Hub 從 SAP Business Warehouse 複製資料
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，透過 Open Hub 從 SAP Business Warehouse (BW) 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

>[!TIP]
>若要瞭解 ADF 對 SAP 資料整合案例的整體支援，請參閱 [使用 Azure Data Factory 白皮書的 sap 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ，其中包含每個 sap 連接器、比較和指引的詳細簡介。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援透過 Open Hub connector 進行此 SAP 商務倉儲：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以透過 Open Hub 將資料從 SAP Business Warehouse 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，此 SAP Business Warehouse Open Hub 連接器支援：

- SAP Business 倉儲 **7.01 版或更高版本 (于2015年) 之後發行的最新 Sap 支援封裝堆疊中 **。 此連接器不支援 SAP BW4/HANA。
- 透過 Open Hub Destination 本機資料表複製資料，其下可能是 DSO、InfoCube、MultiProvider、DataSource 等等。
- 使用基本驗證來複製資料。
- 連接至 SAP 應用程式伺服器或 SAP 訊息伺服器。
- 正在透過 RFC 抓取資料。

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub 整合 

[SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) 可讓您有效地從 SAP BW 中擷取資料。 下圖顯示客戶在其 SAP 系統中的常見流程之一，其資料流程為 SAP ECC -> PSA -> DSO -> Cube。

SAP BW Open Hub Destination (OHD) 會定義轉送 SAP 資料的目標。 SAP 資料傳輸程式)  (的任何物件都可以當做開放式中樞資料來源使用，例如 DSO、InfoCube、DataSource 等等。開啟中樞目的地類型-儲存轉送資料的位置，可以是資料庫資料表 (本機或遠端) 和一般檔案。 此 SAP BW Open Hub 連接器支援從 BW 中的 OHD 本機資料表複製資料。 如果您使用其他類型，您可以使用其他連接器直接連線到資料庫或檔案系統。

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>差異解壓縮流程

ADF SAP BW Open Hub Connector 提供兩個選擇性屬性： `excludeLastRequest` 和 `baseRequestId` 可用來處理開啟中樞的差異負載。 

- **excludeLastRequestId**：是否要排除最後一個要求的記錄。 預設值為 true。 
- **baseRequestId**： delta 載入要求的識別碼。 設定之後，將只會擷取 requestId 大於此屬性值的資料。 

整體來說，從 SAP InfoProviders 到 Azure Data Factory (ADF) 的解壓縮是由兩個步驟所組成： 

1. **SAP BW 的資料傳輸程式 (DTP) ** 此步驟會將 SAP BW InfoProvider 中的資料複製到 SAP BW 開啟的中樞資料表 

1. **ADF 資料複製** 在此步驟中，ADF 連接器會讀取開啟的中樞資料表 

![差異解壓縮流程](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

第一個步驟是執行 DTP。 每次執行都會建立新的 SAP 要求識別碼。 要求識別碼會儲存在開啟的中樞資料表中，然後由 ADF 連接器用來識別差異。 這兩個步驟會以非同步方式執行：由 SAP 觸發的 DTP，並透過 ADF 觸發 ADF 資料複製。 

根據預設，ADF 不會從開啟的中樞資料表讀取最新的差異 (選項 [排除最後一個要求] 為 true) 。 特此來說，ADF 中的資料與開啟中樞資料表中的資料不會是100% 的最新狀態 (最後一個差異缺少) 。 在傳回時，此程式可確保不會因為非同步解壓縮而遺失任何資料列。 即使 ADF 正在讀取開啟的中樞表格，而 DTP 仍寫入相同的資料表中，它仍可正常運作。 

您通常會將複製的最大要求識別碼儲存在暫存資料存放區中的 ADF 最後一次執行 (例如上圖中的 Azure Blob) 。 因此，在後續的執行中，ADF 不會第二次讀取相同的要求。 同時，請注意，資料並不會自動從開啟的中樞資料表中刪除。

若要進行適當的差異處理，不允許在相同的開啟中樞資料表中有來自不同 DTPs 的要求識別碼。 因此，您不得為每個開啟的中樞目的地建立多個 DTP (OHD) 。 當需要來自相同 InfoProvider 的完整和差異解壓縮時，您應該為相同的 InfoProvider 建立兩個 OHDs。 

## <a name="prerequisites"></a>Prerequisites

若要使用此 SAP Business Warehouse Open Hub 連接器，您必須：

- 設定 3.13 版或更新版本的「自我裝載 Integration Runtime。 如需詳細資訊，請參閱自我裝載的 [Integration Runtime](create-self-hosted-integration-runtime.md) 文章。

- 從 SAP 的網站下載 **64 位元[ SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)**，並將其安裝在自我裝載的 IR 機器上。 安裝時，請在選擇性的設定步驟視窗中確定您已選取 [將組件安裝到 GAC]**** 選項，如下圖所示。 

    ![安裝 SAP .NET 連接器](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 在 Data Factory BW 連接器中使用的 SAP 使用者必須具有下列權限： 

    - RFC 和 SAP BW 的授權。 
    - 「執行」授權物件 "S_SDSAUTH" 活動的權限。

- 勾選 [技術金鑰] 選項，將 SAP Open Hub Destination 類型建立為 [資料庫資料表]****。  此外也建議您將 [從資料表中刪除資料] 保留為未勾選，但這並非必要動作。 利用 DTP (直接執行或整合到現有的程序鏈結中) 將資料從您所選擇的來源物件 (例如 Cube) 移至 Open Hub Destination 資料表。

## <a name="getting-started"></a>開始使用

> [!TIP]
>
> 如需使用 SAP BW Open Hub connector 的逐步解說，請參閱 [使用 Azure Data Factory 將資料從 SAP Business 倉儲載入 (BW) ](load-sap-bw-data.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SAP Business Warehouse Open Hub 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是 SAP Business Warehouse Open Hub 連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為： **SapOpenHub** | 是 |
| 伺服器 | SAP BW 執行個體所在之伺服器的名稱。 | 是 |
| systemNumber | SAP BW 系統的系統編號。<br/>允許的值：以字串表示的二位數十進位數字。 | 是 |
| messageServer | SAP 訊息伺服器的主機名稱。<br/>用來連接到 SAP 訊息伺服器。 | 否 |
| messageServerService | 訊息伺服器的服務名稱或埠號碼。<br/>用來連接到 SAP 訊息伺服器。 | 否 |
| systemId | 資料表所在 SAP 系統的識別碼。<br/>用來連接到 SAP 訊息伺服器。 | 否 |
| logonGroup | SAP 系統的登入群組。<br/>用來連接到 SAP 訊息伺服器。 | 否 |
| clientId | SAP W 系統中用戶端的用戶端識別碼。<br/>允許的值：以字串表示的三位數十進位數字。 | 是 |
| 語言 | SAP 系統使用的語言。 | 否 (預設值為 **EN**)|
| userName | 能夠存取 SAP 伺服器的使用者名稱。 | 是 |
| 密碼 | 使用者的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**範例︰**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 SAP BW 開放式中樞資料集所支援的屬性清單。

若要從 SAP BW Open Hub 複製資料以及將資料複製到該處，請將資料集的 type 屬性設為 **SapOpenHubTable**。 以下是支援的屬性。

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **SapOpenHubTable**。  | 是 |
| openHubDestinationName | 要從中複製資料的 Open Hub Destination 名稱。 | 是 |

如果您是 `excludeLastRequest` 在資料集中設定和，則仍會依原樣 `baseRequestId` 受到支援，但建議您繼續使用活動來源中的新模型。

**範例︰**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SAP BW Open Hub 來源所支援的屬性清單。

### <a name="sap-bw-open-hub-as-source"></a>以 SAP BW Open Hub 作為來源

若要從 SAP BW 開啟的中樞複製資料，[複製活動 **來源** ] 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設為 **SapOpenHubSource**。 | 是 |
| excludeLastRequest | 是否要排除最後一個要求的記錄。 | 否 (預設值為 **true**) |
| baseRequestId | 差異載入的要求識別碼。 設定之後，將只會擷取 requestId **大於**此屬性值的資料。  | 否 |

>[!TIP]
>如果您的 Open Hub 資料表僅包含單一要求識別碼所產生的資料 (例如，您一律執行完整負載並覆寫資料表中的現有資料，或您只在測試時執行 DTP 一次)，請務必取消勾選 "excludeLastRequest" 選項，以複製資料。

若要加速資料載入，您可以在 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 複製活動上設定，以平行方式從 SAP BW 開放式中樞載入資料。 例如，如果您設定 `parallelCopies` 為四，Data Factory 會同時執行四個 rfc 呼叫，而每個 rfc 呼叫都會從您的 SAP BW 開放式中樞資料表中，取得由 DTP 要求識別碼和套件識別碼所分割的部分資料。 當唯一的 DTP 要求識別碼 + 封裝識別碼的數目大於的值時，就會套用這項功能 `parallelCopies` 。 將資料複製到以檔案為基礎的資料存放區時，也會建議以多個檔案的形式寫入資料夾， (只指定資料夾名稱) ，在這種情況下，效能優於寫入至單一檔案。

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>SAP BW Open Hub 的資料類型對應

從 SAP BW Open Hub 複製資料時，會使用下列從 SAP BW 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| SAP ABAP 類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| C (字串) | String |
| I (整數) | Int32 |
| F (浮點數) | Double |
| D (日期) | String |
| T (時間) | String |
| P (BCD 封裝、貨幣、小數、數量) | Decimal |
| N (Numc) | String |
| X (二進位和原始) | String |

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="troubleshooting-tips"></a>疑難排解提示

**徵兆：** 如果您在 HANA 上執行 SAP BW，並只觀察資料子集是使用 ADF 複製活動 (1000000 個數據列) 來複製，可能的原因是您在您的 DTP 中啟用「SAP Hana 執行」選項，在此情況下，ADF 只能取出第一個批次的資料。

**解決方式：** 在 DTP 中停用「SAP Hana 執行」選項，重新處理資料，然後再次嘗試執行複製活動。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
