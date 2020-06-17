---
title: 對 SFTP 伺服器來回複製資料
description: 了解如何使用 Azure Data Factory 對 SFTP 伺服器來回複製資料。
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
ms.date: 05/15/2020
ms.openlocfilehash: f61560b01c2ac7bc4db18c31399fcce1743f4824
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653750"
---
# <a name="copy-data-from-and-to-sftp-server-using-azure-data-factory"></a>使用 Azure Data Factory 對 SFTP 伺服器來回複製資料

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-sftp-connector.md)
> * [目前的版本](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何對 SFTP 伺服器來回複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此 SFTP 連接器支援下列活動：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

具體而言，這個 SFTP 連接器支援：

- 使用**基本**或 **SshPublicKey** 驗證對 SFTP 來回複製檔案。
- 依原樣複製檔案，或使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析/產生檔案。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SFTP 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SFTP 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**Sftp**。 |是 |
| 主機 | SFTP 伺服器的名稱或 IP 位址。 |是 |
| 連接埠 | SFTP 伺服器所接聽的連接埠。<br/>允許的值為：整數，預設值為 **22**。 |否 |
| skipHostKeyValidation | 指定是否略過主機金鑰驗證。<br/>允許的值為：**true**、**false** (預設值)。  | 否 |
| hostKeyFingerprint | 指定主機金鑰的指紋。 | 如果 "skipHostKeyValidation" 設定為 false，則為必要。  |
| authenticationType | 指定驗證類型。<br/>允許的值包括：**Basic**、**SshPublicKey**。 請參閱[使用基本驗證](#using-basic-authentication)和[使用 SSH 公開金鑰驗證](#using-ssh-public-key-authentication)章節，分別取得更多屬性和 JSON 範例。 |是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 深入了解[必要條件](#prerequisites)一節。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

### <a name="using-basic-authentication"></a>使用基本驗證

若要使用基本驗證，請將 "authenticationType" 屬性設定為 **Basic**，然後除了上一節中介紹的 SFTP 連接器泛型屬性之外，再指定下列屬性︰

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| userName | 可存取 SFTP 伺服器的使用者。 |是 |
| 密碼 | 使用者 (userName) 的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |

**範例︰**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
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

### <a name="using-ssh-public-key-authentication"></a>使用 SSH 公用金鑰驗證

若要使用 SSH 公開金鑰驗證，請將 "authenticationType" 屬性設定為 **SshPublicKey**，然後除了上一節中介紹的 SFTP 連接器泛型屬性之外，再指定下列屬性︰

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| userName | 可存取 SFTP 伺服器的使用者 |是 |
| privateKeyPath | 指定 Integration Runtime 可存取的私密金鑰檔案絕對路徑。 只有當 "connectVia" 中已指定「自我裝載」類型的 Integration Runtime 時才適用。 | 指定 `privateKeyPath` 或 `privateKeyContent`。  |
| privateKeyContent | Base64 編碼的 SSH 私密金鑰內容。 SSH 私密金鑰的格式應該是 OpenSSH。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 指定 `privateKeyPath` 或 `privateKeyContent`。 |
| passPhrase | 如果金鑰檔案受到複雜密碼保護，請指定複雜密碼/密碼以將私密金鑰解密。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 如果私密金鑰檔案受到複雜密碼保護，則為 [是]。 |

> [!NOTE]
> SFTP 連接器支援 RSA/DSA OpenSSH 金鑰。 確定您的金鑰檔案內容是以 "-----BEGIN [RSA/DSA] PRIVATE KEY-----" 開頭。 如果私密金鑰檔案是 ppk 格式檔案，請使用 Putty 工具，從 .ppk 轉換為 OpenSSH 格式。 

**範例 1：使用私密金鑰 filePath 的 SshPublicKey 驗證**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例 2：使用私密金鑰內容的 SshPublicKey 驗證**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

在格式型資料集內的 `location` 設定下，SFTP 支援下列屬性：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 在資料集的 `location` 下，type 屬性必須設定為 **SftpLocation**。 | 是      |
| folderPath | 資料夾的路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在活動來源設定中指定。 | 否       |
| fileName   | 所指定 folderPath 下的檔案名稱。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在活動來源設定中指定。 | 否       |

**範例︰**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SFTP 來源所支援的屬性清單。

### <a name="sftp-as-source"></a>SFTP 作為來源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在格式型複製來源的 `storeSettings` 設定下，SFTP 支援下列屬性：

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為 **SftpReadSettings**。 | 是                                           |
| 找出要複製的檔案： |  |  |
| 選項 1：靜態路徑<br> | 從資料集內指定的特定資料夾/檔案路徑複製。 如果您想要複製資料夾中的所有檔案，請另外將 `wildcardFileName` 指定為 `*`。 |  |
| 選項 2：萬用字元<br>- wildcardFolderPath | 含有萬用字元的資料夾路徑，用於篩選來源資料夾。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 選項 2：萬用字元<br>- wildcardFileName | 在特定 folderPath/wildcardFolderPath 下含有萬用字元的檔案名稱，用於篩選來源檔案。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 3：檔案清單<br>- fileListPath | 表示複製指定的檔案集。 請指向文字檔，內有您要複製的檔案清單，每行一個檔案，以資料集內所設定路徑的相對路徑表示。<br/>使用此選項時，請勿在資料集內指定檔案名稱。 [檔案清單範例](#file-list-examples)有更多範例可供參閱。 |否 |
| 其他設定： |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 <br>允許的值為 **true** (預設值) 和 **false**。<br>設定 `fileListPath` 時，此屬性不適用。 |否 |
| modifiedDatetimeStart    | 檔案篩選會根據以下屬性：上次修改時間。 <br>如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 NULL，這意謂著不會在資料集套用任何檔案屬性篩選。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。<br/>設定 `fileListPath` 時，此屬性不適用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否                                            |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sftp-as-sink"></a>SFTP 作為接收

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在格式型複製接收的 `storeSettings` 設定下，SFTP 支援下列屬性：

| 屬性                 | 描述                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為 **SftpWriteSettings**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來自來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| maxConcurrentConnections | 可同時連線到資料存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否       |
| useTempFileRename | 指出要上傳至暫存檔案並重新命名，還是直接寫入目標資料夾/檔案位置。 根據預設，ADF 會先寫入暫存檔案，然後在上傳完成時重新命名檔案，用意在於：1) 如果有其他程序寫入相同的檔案，則避免衝突寫入造成檔案損毀，2) 確保在整個傳輸期間，檔案的原始版本都存在。 如果 SFTP 伺服器不支援重新命名作業，請停用此選項，並確定您不會同時寫入目標檔案。 請參閱此表格下方的疑難排解秘訣。 | 否。 預設值為 true。 |
| operationTimeout | 對 SFTP 伺服器發出的每個寫入要求在逾時之前的等待時間。預設值為 60 分鐘 (01:00:00)。|否 |

>[!TIP]
>將資料寫入 SFTP 時，如果您遇到 "UserErrorSftpPathNotFound"、"UserErrorSftpPermissionDenied" 或 "SftpOperationFail" 的錯誤，而且您使用的 SFTP 使用者具有適當的權限，請檢查 SFTP 伺服器是否支援檔案重新命名作業 - 如果不是，請停用 [以暫存檔案上傳] (`useTempFileRename`) 選項，然後再試一次。 請從上表深入了解這個屬性。 如果您使用「自我裝載整合執行階段」進行複製，請確定使用的是 4.6 版或更新版本。

**範例︰**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>資料夾和檔案篩選範例

本節描述含有萬用字元篩選之資料夾路徑和檔案名稱所產生的行為。

| folderPath | fileName | 遞迴 | 來源資料夾結構和篩選結果 (會擷取以**粗體**顯示的檔案)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (空白，使用預設值) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空白，使用預設值) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>檔案清單範例

本節說明在複製活動來源中使用檔案清單路徑時所產生的行為。

假設您有下列來源資料夾結構，且想要複製以粗體標示的檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | ADF 設定                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在資料集內：**<br>- 資料夾路徑：`root/FolderA`<br><br>**在複製活動來源中：**<br>- 檔案清單路徑：`root/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向同一個資料存放區中的文字檔，內有您想複製的檔案清單，每行一個檔案，以資料集內所設定路徑的相對路徑表示。 |

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要了解屬性的詳細資料，請參閱 [GetMetadata 活動](control-flow-get-metadata-activity.md)。 

## <a name="delete-activity-properties"></a>刪除活動屬性

若要了解屬性的詳細資料，請參閱[刪除活動](delete-activity.md)。

## <a name="legacy-models"></a>舊版模型

>[!NOTE]
>基於回溯相容性，仍照現狀支援下列模型。 建議您往後使用以上各節所提及的新模型，ADF 製作使用者介面已轉為產生新模型。

### <a name="legacy-dataset-model"></a>舊版資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**FileShare** |是 |
| folderPath | 資料夾的路徑。 支援萬用字元篩選，允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際檔案名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br/><br/>範例：rootfolder/subfolder/，如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |是 |
| fileName |  在指定 "folderPath" 之下檔案的**名稱或萬用字元篩選**。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>針對篩選，允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果實際資料夾名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。 |否 |
| modifiedDatetimeStart | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案中篩選檔案時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，這意謂著不會對資料集套用任何檔案屬性篩選條件。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案中篩選檔案時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，這意謂著不會對資料集套用任何檔案屬性篩選條件。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| format | 如果您想要在以檔案為基礎的存放區之間**依原樣複製檔案** (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 將格式下的 **type** 屬性設定為這些值其中之一。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)章節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：**GZip**、**Deflate**、**BZip2** 及 **ZipDeflate**。<br/>支援的層級為：**Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製指定名稱的單一檔案，請以資料夾部分指定 **folderPath**，並以檔案名稱指定 **fileName**。<br>若要複製資料夾下的檔案子集，請以資料夾部分指定 **folderPath**，並以萬用字元篩選指定 **fileName**。

>[!NOTE]
>如果您使用 "fileFilter" 屬性於檔案篩選，雖然仍舊支援，不過會建議您之後使用加入 "fileName" 的新篩選功能。

**範例︰**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>舊版複製活動來源模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**FileSystemSource** |是 |
| 遞迴 | 表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當 recursive 設定為 true，而接收器為檔案型存放區時，系統不會在接收器複製/建立空資料夾/子資料夾。<br/>允許的值為：**true** (預設值)、**false** | 否 |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
