---
title: 對 SFTP 伺服器來回複製資料
description: 瞭解如何使用 Azure Data Factory 從 SFTP 伺服器複製資料及將資料複製到 SFTP 伺服器。
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
ms.date: 08/18/2020
ms.openlocfilehash: 8ec950ddabd3844618c878471d2e1391979e2056
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88521367"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SFTP 伺服器複製資料或將資料複製到 SFTP 伺服器

> [!div class="op_single_selector" title1="選取您要使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-sftp-connector.md)
> * [目前的版本](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何從安全 FTP (SFTP) 伺服器複製資料及將資料複製到其中。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

SFTP 連接器支援下列活動：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

具體而言，SFTP 連接器支援：

- 使用 *基本* 或 *SSHPUBLICKEY* 驗證從 SFTP 伺服器複製檔案。
- 依原樣複製檔案，或使用 [支援的檔案格式和壓縮編解碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生檔案。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SFTP 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SFTP 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為 *Sftp*。 |是 |
| 主機 | SFTP 伺服器的名稱或 IP 位址。 |是 |
| 連接埠 | SFTP 伺服器正在接聽的埠。<br/>允許的值為整數，預設值為 *22*。 |否 |
| skipHostKeyValidation | 指定是否略過主機金鑰驗證。<br/>允許的值為 *true* 和 *false* (預設) 。  | 否 |
| hostKeyFingerprint | 指定主機金鑰的指紋。 | 是，如果 "skipHostKeyValidation" 設為 false。  |
| authenticationType | 指定驗證類型。<br/>允許的值為 *基本* 和 *SshPublicKey*。 如需其他屬性，請參閱 [使用基本驗證](#use-basic-authentication) 一節。 如需 JSON 範例，請參閱 [使用 SSH 公開金鑰驗證](#use-ssh-public-key-authentication) 一節。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 若要深入瞭解，請參閱「 [必要條件](#prerequisites) 」一節。 如果未指定整合執行時間，服務會使用預設 Azure Integration Runtime。 |No |

### <a name="use-basic-authentication"></a>使用基本驗證

若要使用基本驗證，請將 [ *authenticationType* ] 屬性設定為 [ *基本*]，並除了上一節所引進的 SFTP 連接器泛型屬性之外，還指定下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| userName | 擁有 SFTP 伺服器存取權的使用者。 |是 |
| 密碼 | 使用者 (使用者名稱) 的密碼。 將此欄位標示為 SecureString，以安全地將它儲存在您的 data factory 中，或 [參考儲存在 Azure key vault 中的秘密](store-credentials-in-key-vault.md)。 | 是 |

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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>使用 SSH 公開金鑰驗證

若要使用 SSH 公開金鑰驗證，請將 "authenticationType" 屬性設定為 **SshPublicKey**，然後除了上一節中介紹的 SFTP 連接器泛型屬性之外，再指定下列屬性︰

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| userName | 擁有 SFTP 伺服器存取權的使用者。 |是 |
| privateKeyPath | 指定整合執行時間可以存取之私密金鑰檔案的絕對路徑。 這僅適用于在 "connectVia" 中指定自我裝載類型的整合執行時間時。 | 請指定 `privateKeyPath` 或 `privateKeyContent`。  |
| privateKeyContent | Base64 編碼的 SSH 私密金鑰內容。 SSH 私密金鑰的格式應該是 OpenSSH。 將此欄位標示為 SecureString，以安全地將它儲存在您的 data factory 中，或 [參考儲存在 Azure key vault 中的秘密](store-credentials-in-key-vault.md)。 | 請指定 `privateKeyPath` 或 `privateKeyContent`。 |
| passPhrase | 如果金鑰檔受到片語的保護，請指定用來解密私密金鑰的傳遞片語或密碼。 將此欄位標示為 SecureString，以安全地將它儲存在您的 data factory 中，或 [參考儲存在 Azure key vault 中的秘密](store-credentials-in-key-vault.md)。 | 是，如果私密金鑰檔案受到通行片語的保護。 |

> [!NOTE]
> SFTP 連接器支援 RSA/DSA OpenSSH 金鑰。 確定您的金鑰檔案內容開頭為「-----開始 [RSA/DSA] 私密金鑰-----」。 如果私密金鑰檔案是 PPK 格式檔案，請使用 PuTTY 工具，從 PPK 轉換為 OpenSSH 格式。 

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
            "referenceName": "<name of integration runtime>",
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
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集之區段和屬性的完整清單，請參閱 [資料集](concepts-datasets-linked-services.md) 文章。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

以下是針對 `location` 以格式為基礎的資料集設定下的 SFTP 支援的屬性：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Dataset 中的 *type* 屬性 `location` 必須設為 *SftpLocation*。 | 是      |
| folderPath | 資料夾的路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在 [活動來源設定] 中指定路徑。 | 否       |
| fileName   | 指定之 folderPath 下的檔案名。 如果您想要使用萬用字元來篩選檔案，請略過這項設定，並在 [活動來源設定] 中指定檔案名。 | 否       |

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

如需可用來定義活動之區段和屬性的完整清單，請參閱 [管線](concepts-pipelines-activities.md) 文章。 本節提供 SFTP 來源所支援的屬性清單。

### <a name="sftp-as-source"></a>SFTP 作為來源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

以下是針對 `storeSettings` 以格式為基礎的複製來源中設定下的 SFTP 支援的屬性：

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 下的 *類型* 屬性 `storeSettings` 必須設為 *SftpReadSettings*。 | Yes                                           |
| ***找出要複製的檔案*** |  |  |
| 選項 1：靜態路徑<br> | 從資料集內所指定的資料夾/檔案路徑複製。 若您想要複製資料夾中的所有檔案，請另外將 `wildcardFileName` 指定為 `*`。 |  |
| 選項 2：萬用字元<br>- wildcardFolderPath | 含有萬用字元的資料夾路徑，可用來篩選來源資料夾。 <br>允許的萬用字元 `*` (符合零或多個字元) 和 `?` (符合零或單一字元) ; `^` 如果您的實際資料夾名稱包含萬用字元或此 escape 字元，請使用來進行 escape。 <br>如需更多範例，請參閱 [資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 選項 2：萬用字元<br>- wildcardFileName | 在指定的 folderPath/wildcardFolderPath 下，用來篩選來源檔案的檔案名。 <br>允許的萬用字元 `*` (符合零或多個字元) 和 `?` (符合零或單一字元) ; `^` 如果您的實際資料夾名稱包含萬用字元或此 escape 字元，請使用來進行 escape。  如需更多範例，請參閱 [資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 3：檔案清單<br>- fileListPath | 表示複製指定的檔案集。 指向文字檔，其中包含您想要複製的檔案清單 (每行一個檔案，以及資料集) 中設定之路徑的相對路徑。<br/>當您使用此選項時，請勿在資料集中指定檔案名。 如需更多範例，請參閱檔案 [清單範例](#file-list-examples)。 |No |
| ***其他設定*** |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 <br>允許的值為 *true* (預設值) 和 *false*。<br>設定 `fileListPath` 時，不適用此屬性。 |否 |
| deleteFilesAfterCompletion | 指出是否要在成功移至目的地存放區之後，從來源存放區刪除二進位檔案。 檔案刪除是針對每個檔案，因此當複製活動失敗時，您會看到部分檔案已複製到目的地並從來源刪除，其他檔案仍在來源存放區上。 <br/>這個屬性只適用于二進位複製案例，其中資料來源存放區為 Blob、ADLS Gen1、ADLS Gen2、S3、Google Cloud Storage、檔案、Azure 檔案、SFTP 或 FTP。 預設值： false。 |否 |
| modifiedDatetimeStart    | 檔案會根據 *上次修改*的屬性進行篩選。 <br>如果檔案的上次修改時間在的範圍內，則會選取這些檔案 `modifiedDatetimeStart` `modifiedDatetimeEnd` 。 時間會以 *2018-12-01T05：00： 00Z*的格式套用至 UTC 時區。 <br> 屬性可以是 Null，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 有 datetime 值但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 有 datetime 值但 `modifiedDatetimeStart` 為 Null 時，表示已選取其上次修改屬性小於 datetime 值的檔案。<br/>設定 `fileListPath` 時，不適用此屬性。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 可以同時連線到儲存體存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才需要指定值。 | 否                                            |

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

### <a name="sftp-as-a-sink"></a>SFTP 作為接收

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

以下是以 `storeSettings` 格式為基礎的複製接收中設定下的 SFTP 支援的屬性：

| 屬性                 | 描述                                                  | 必要 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 下的 *類型* 屬性 `storeSettings` 必須設為 *SftpWriteSettings*。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來自來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| maxConcurrentConnections | 可以同時連線到儲存體存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才需要指定值。 | 否       |
| useTempFileRename | 指出是否要上傳至暫存檔並將它們重新命名，或直接寫入目的檔案夾或檔案位置。 根據預設，Azure Data Factory 會先寫入暫存檔，然後在上傳完成時將其重新命名。 如果您有其他處理常式寫入相同的檔案，則此順序有助於 (1) 避免可能導致檔案損毀的衝突，並 (2) 確定檔案的原始版本存在於傳輸期間。 如果您的 SFTP 伺服器不支援重新命名作業，請停用此選項，並確定您沒有對目標檔案的並行寫入。 如需詳細資訊，請參閱此表格結尾的疑難排解提示。 | 否。 預設值為 true。 |
| operationTimeout | 對 SFTP 伺服器發出的每個寫入要求在逾時之前的等待時間。預設值為 60 分鐘 (01:00:00)。|否 |

>[!TIP]
>如果您在將資料寫入 SFTP 時收到錯誤「UserErrorSftpPathNotFound」、「UserErrorSftpPermissionDenied」或「SftpOperationFail」，且您使用的 SFTP *使用者有適當* 的許可權，請查看您的 sftp 伺服器支援檔案重新命名作業是否正常運作。 如果不是，請停 **用 [使用暫存檔案上傳** (] `useTempFileRename`) 選項，然後再試一次。 若要深入瞭解此屬性，請參閱上表。 如果您針對複製活動使用自我裝載整合執行時間，請務必使用4.6 版或更新版本。

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

本節說明使用萬用字元篩選器搭配資料夾路徑和檔案名所產生的行為。

| folderPath | fileName | 遞迴 | 來源資料夾結構和篩選結果 (會擷取以**粗體**顯示的檔案)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (空白，使用預設值) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空白，使用預設值) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>檔案清單範例

下表描述使用複製活動來源中的檔案清單路徑所產生的行為。 它會假設您有下列源資料夾結構，而且想要複製粗體字型的檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | Azure Data Factory 設定                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;中繼資料<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在資料集中：**<br>- 資料夾路徑：`root/FolderA`<br><br>**在複製活動來源中：**<br>- 檔案清單路徑：`root/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向相同資料存放區中的文字檔，其中包含您想要複製的檔案清單 (每行一個檔案，以及資料集) 中設定之路徑的相對路徑。 |

## <a name="lookup-activity-properties"></a>查閱活動屬性

如需查閱活動屬性的詳細資訊，請參閱 [Azure Data Factory 中的查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

如需 GetMetadata 活動屬性的相關資訊，請參閱 [中的 GetMetadata 活動 Azure Data Factory](control-flow-get-metadata-activity.md)。 

## <a name="delete-activity-properties"></a>刪除活動屬性

如需刪除活動屬性的相關資訊，請參閱 [Azure Data Factory 中的刪除活動](delete-activity.md)。

## <a name="legacy-models"></a>舊版模型

>[!NOTE]
>為了回溯相容性，仍支援下列模型。 建議您使用先前討論的新模型，因為 Azure Data Factory 撰寫 UI 已切換為產生新的模型。

### <a name="legacy-dataset-model"></a>舊版資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 *type* 屬性必須設定為檔案 *共用*。 |是 |
| folderPath | 資料夾的路徑。 支援萬用字元篩選。 允許的萬用字元 `*` (符合零或多個字元) 和 `?` (符合零或單一字元) ; `^` 如果您的實際檔案名包含萬用字元或此 escape 字元，請使用來進行 escape。 <br/><br/>範例：rootfolder/subfolder/，如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |是 |
| fileName |  指定 "folderPath" 下之檔案的**名稱或萬用字元篩選**。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>若為篩選，允許的萬用字元 `*` (符合零或多個字元) ，而且 `?` (符合零或單一字元) 。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果實際資料夾名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。 |否 |
| modifiedDatetimeStart | 檔案會根據 *上次修改*的屬性進行篩選。 如果檔案的上次修改時間在的範圍內，則會選取這些檔案 `modifiedDatetimeStart` `modifiedDatetimeEnd` 。 時間會以 *2018-12-01T05：00： 00Z*的格式套用至 UTC 時區。 <br/><br/> 當您想要從大量檔案執行檔案篩選時，會啟用此設定，以影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 有 datetime 值但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 有 datetime 值但 `modifiedDatetimeStart` 為 Null 時，表示已選取其上次修改屬性小於 datetime 值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案會根據 *上次修改*的屬性進行篩選。 如果檔案的上次修改時間在的範圍內，則會選取這些檔案 `modifiedDatetimeStart` `modifiedDatetimeEnd` 。 時間會以 *2018-12-01T05：00： 00Z*的格式套用至 UTC 時區。 <br/><br/> 當您想要從大量檔案執行檔案篩選時，會啟用此設定，以影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 有 datetime 值但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 有 datetime 值但 `modifiedDatetimeStart` 為 Null 時，表示已選取其上次修改屬性小於 datetime 值的檔案。| 否 |
| format | 如果您想要在以檔案為基礎的存放區之間依原樣複製檔案 (二進位複製)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析特定格式的檔案，則支援下列檔案格式類型： *TextFormat*、 *JsonFormat*、 *AvroFormat*、 *OrcFormat*和 *ParquetFormat*。 將格式下的 *type* 屬性設定為這些值其中之一。 如需詳細資訊，請參閱 [文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、 [Json 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、 [Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、 [Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)，以及 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 區段。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：GZip、Deflate、BZip2 及 ZipDeflate。<br/>支援的層級為 *Optimal* 和 *Fastest*。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 *folderPath*。<br>若要複製具有指定名稱的單一檔案，請使用資料夾部分和*檔案名*來指定*folderPath* 。<br>若要複製資料夾下的檔案子集，請使用具有萬用字元篩選的資料夾部分和*檔案名*來指定*folderPath* 。

>[!NOTE]
>如果您在檔案篩選器中使用 *fileFilter* 屬性，它仍會受到支援，但建議您從現在開始使用新的篩選功能，並新增至 *檔案名* 。

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
| type | 複製活動來源的 *type* 屬性必須設定為 *>filesystemsource* |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 *true* 且接收是檔案型存放區時，就不會在接收時複製或建立空的資料夾和子資料夾。<br/>允許的值為 *true* (預設值) 和 *false* | 否 |
| maxConcurrentConnections | 可以同時連線到儲存體存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定數位。 | 否 |

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
如需 Azure Data Factory 中複製活動所支援作為來源和接收器的資料存放區清單，請參閱 [支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
