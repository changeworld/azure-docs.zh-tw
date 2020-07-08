---
title: 使用 Azure Data Factory 從 Google Cloud Storage 複製資料
description: 了解如何使用 Azure Data Factory 將資料從 Google Cloud Storage 複製到支援的接收資料存放區。
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: jingwang
ms.openlocfilehash: 9ecb703f8c8f75939d8d796bdd5f687795145f74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85101042"
---
# <a name="copy-data-from-google-cloud-storage-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Google Cloud Storage 複製資料
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何從 Google Cloud Storage (GCS) 複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此 Google Cloud Storage 連接器支援下列活動：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

具體而言，此 Google 雲端儲存體連接器支援依自己的方式複製檔案，或使用[支援的檔案格式和壓縮編解碼器](supported-file-formats-and-compression-codecs.md)來剖析檔案。 它會利用 GC 的 S3 相容互通性。

## <a name="prerequisites"></a>Prerequisites

您的 Google 雲端儲存體帳戶需要下列設定：

1. 啟用 Google Cloud Storage 帳戶的互通性
2. 設定預設專案，其中包含您想要從 [目標 GC] bucket 複製的資料。
3. 建立服務帳戶，並使用 GCP 上的雲端 IAM 定義正確的許可權層級。 
4. 產生此服務帳戶的存取金鑰。

![取得 Google Cloud Storage 的存取金鑰](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>所需的權限

若要從 Google Cloud Storage 複製資料，請確定您已獲得所需的權限。 服務帳戶中定義的許可權可能包含 `storage.buckets.get` `storage.buckets.list` 物件作業的、或 `storage.objects.get` 。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Google Cloud Storage 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Google Cloud Storage 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **Type**屬性必須設定為**GoogleCloudStorage**。 | 是 |
| accessKeyId | 密碼存取金鑰的識別碼。 若要尋找存取金鑰和秘密，請參閱[必要條件](#prerequisites)。 |是 |
| secretAccessKey | 密碼存取金鑰本身。 將此欄位標記為**SecureString** ，將它安全地儲存在 Data Factory 中，或[參考儲存在 Azure Key Vault 中的秘密](store-credentials-in-key-vault.md)。 |是 |
| serviceUrl | 將自訂 S3 端點指定為 `https://storage.googleapis.com` 。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure integration runtime 或自我裝載整合執行時間（如果您的資料存放區位於私人網路中）。 如果未指定此屬性，服務就會使用預設的 Azure integration runtime。 |否 |

以下是範例：

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

下列屬性在以 `location` 格式為基礎的資料集的 [設定] 下支援 Google 雲端儲存體：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 資料集內的**類型**屬性 `location` 必須設定為**GoogleCloudStorageLocation**。 | 是      |
| bucketName | S3 貯體名稱。                                          | 是      |
| folderPath | 所指定貯體下的資料夾路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在 [活動來源設定] 中指定。 | 否       |
| fileName   | 給定值區和資料夾路徑下的檔案名。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在 [活動來源設定] 中指定。 | 否       |

**範例︰**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Google Cloud Storage 來源所支援的屬性清單。

### <a name="google-cloud-storage-as-a-source-type"></a>作為來源類型的 Google 雲端儲存體

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

下列屬性在以 `storeSettings` 格式為基礎的複製來源的 [設定] 下支援 Google 雲端儲存體：

| 屬性                 | 描述                                                  | 必要                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | 底下的**type**屬性 `storeSettings` 必須設定為**GoogleCloudStorageReadSettings**。 | 是                                                         |
| 找到要複製的檔案： |  |  |
| 選項 1：靜態路徑<br> | 從在資料集內指定的貯體或資料夾/檔案路徑複製。 如果您想要複製 bucket 或資料夾中的所有檔案，請另外將指定 `wildcardFileName` 為 `*` 。 |  |
| 選項 2：GCS 前置詞<br>- 前置詞 | 在資料集內設定的指定值區下，GC 金鑰名稱的前置詞，用來篩選來源 GC 檔案。 已選取其名稱開頭為的 GC 金鑰 `bucket_in_dataset/this_prefix` 。 它會利用 GC 的服務端篩選，提供比萬用字元篩選更好的效能。 | 否 |
| 選項 3：萬用字元<br>- wildcardFolderPath | 在資料集內設定的指定值區底下，具有萬用字元的資料夾路徑，用來篩選來源資料夾。 <br>允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。 `^`如果您的資料夾名稱包含萬用字元或內的此 escape 字元，請使用來進行 escape。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | No                                            |
| 選項 3：萬用字元<br>- wildcardFileName | 在指定的值區和資料夾路徑（或萬用字元資料夾路徑）底下，含有萬用字元的檔案名，用來篩選來源檔案。 <br>允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。 `^`如果您的資料夾名稱包含萬用字元或內的此 escape 字元，請使用來進行 escape。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 3：檔案清單<br>- fileListPath | 表示要複製指定的檔案集。 指向包含您要複製之檔案清單的文字檔，每行一個檔案，這是在資料集中設定之路徑的相對路徑。<br/>當您使用此選項時，請勿指定資料集中的檔案名。 [檔案清單範例](#file-list-examples) (英文) 有更多範例可供參閱。 |No |
| 其他設定： |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當**遞迴**設定為**true**且接收是以檔案為基礎的存放區時，不會在接收時複製或建立空的資料夾或子資料夾。 <br>允許的值為 **true** (預設值) 和 **false**。<br>設定 `fileListPath` 時，不適用此屬性。 |No |
| deleteFilesAfterCompletion | 指出在成功移至目的地存放區之後，是否會從來源存放區刪除二進位檔案。 檔案刪除是每個檔案，因此當複製活動失敗時，您會看到某些檔案已複製到目的地，並從來源刪除，而其他檔案仍在來源存放區上剩餘。 <br/>此屬性只在二進位複製案例中有效，其中資料來源存放區為 Blob、ADLS Gen1、ADLS Gen2、S3、Google Cloud Storage、File、Azure 檔案、SFTP 或 FTP。 預設值： false。 |No |
| modifiedDatetimeStart    | 檔案會根據屬性進行篩選：上次修改。 <br>若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是**Null**，這表示不會將檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值 `modifiedDatetimeEnd` ，但為**Null**時，將會選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值 `modifiedDatetimeStart` ，但為**Null**時，將會選取上次修改屬性小於日期時間值的檔案。<br/>設定 `fileListPath` 時，不適用此屬性。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                                          |
| maxConcurrentConnections | 對儲存體的並行連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | No                                                          |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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
                    "type": "GoogleCloudStorageReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>資料夾和檔案篩選範例

本節描述含有萬用字元篩選之資料夾路徑和檔案名稱所產生的行為。

| 貯體 | 索引鍵 | 遞迴 | 來源資料夾結構和篩選結果 (會擷取以粗體顯示的檔案)|
|:--- |:--- |:--- |:--- |
| 貯體 | `Folder*/*` | false | 貯體<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| 貯體 | `Folder*/*` | true | 貯體<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| 貯體 | `Folder*/*.csv` | false | 貯體<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| 貯體 | `Folder*/*.csv` | true | 貯體<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>檔案清單範例

本節說明在複製活動來源中使用檔案清單路徑所產生的行為。

假設您有下列源資料夾結構，而且想要以粗體複製檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | Data Factory 設定                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| 貯體<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;中繼資料<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在資料集中：**<br>- 貯體：`bucket`<br>- 資料夾路徑：`FolderA`<br><br>**在複製活動來源內：**<br>- 檔案清單路徑：`bucket/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向相同資料存放區中的文字檔，其中包含您想要複製的檔案清單（每行一個檔案），其中包含資料集中所設定路徑的相對路徑。 |

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要瞭解屬性的詳細資料，請檢查[GetMetadata 活動](control-flow-get-metadata-activity.md)。 

## <a name="delete-activity-properties"></a>刪除活動屬性

若要瞭解有關屬性的詳細資料，請核取 [[刪除活動](delete-activity.md)]。

## <a name="legacy-models"></a>舊版模型

如果您使用 Amazon S3 連接器從 Google Cloud Storage 複製資料，則仍會支援它，以提供回溯相容性。 我們建議您使用先前所述的新模型。 Data Factory 撰寫 UI 已切換為產生新的模型。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
