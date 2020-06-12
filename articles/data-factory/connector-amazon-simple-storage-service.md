---
title: 從 Amazon Simple Storage Service (S3) 複製資料
description: 了解如何使用 Azure Data Factory 將資料從 Amazon Simple Storage Service (S3) 複製到支援的接收資料存放區。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: 94de2f9043c8c86036331a2cce2d2720e8c9b423
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651007"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>使用 Azure Data Factory 從 Amazon Simple Storage Service 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
>
> * [第 1 版](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [目前的版本](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何從 Amazon Simple Storage Service (Amazon S3) 複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

>[!TIP]
>如需從 Amazon S3 到 Azure 儲存體的資料移轉案例，請從[使用 Azure Data Factory，將資料從 Amazon S3 遷移到 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)深入了解。

## <a name="supported-capabilities"></a>支援的功能

此 Amazon S3 連接器支援下列活動：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

具體而言，這個 Amazon S3 連接器支援依原樣複製檔案，或使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析檔案。 您也可以選擇在[複製期間保留檔案中繼資料](#preserve-metadata-during-copy)。 連接器會使用 [AWS Signature 第 4 版](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)來驗證對 S3 的要求。

>[!TIP]
>您可以使用此 Amazon S3 連接器從**任何與 S3 相容的儲存體提供者** (例如 [Google Cloud Storage](connector-google-cloud-storage.md)) 中複製資料。 在連結服務設定中指定相對應的服務 URL。

## <a name="required-permissions"></a>所需的權限

若要從 Amazon S3 複製資料，請確定您已獲得下列權限︰

- **針對複製活動執行**：「Amazon S3 物件作業」的 `s3:GetObject` 和 `s3:GetObjectVersion`。
- **針對 Data Factory GUI 撰寫**：如果要進行測試連線和瀏覽檔案路徑等作業，則另外需要「Amazon S3 貯體作業」權限的 `s3:ListAllMyBuckets` 和 `s3:ListBucket`/`s3:GetBucketLocation`。 如果您不想要授與這些權限，請跳過已連結的服務建立頁面中的測試連線，然後直接在資料集設定中指定路徑。

如需有關完整 Amazon S3 權限清單的詳細資料，請參閱[在原則中指定權限 (英文)](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Amazon S3 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Amazon S3 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**AmazonS3**。 | 是 |
| accessKeyId | 密碼存取金鑰的識別碼。 |是 |
| secretAccessKey | 密碼存取金鑰本身。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| serviceUrl | 如果您是從與 S3 相容的儲存體提供者中複製資料，而非官方的 Amazon S3 服務，請指定自訂的 S3 端點。 例如，若要從 Google Cloud Storage 中複製資料，請指定 `https://storage.googleapis.com`。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

>[!TIP]
>如果您是從與 S3 相容的儲存體中複製資料，而非官方的 Amazon S3 服務，請指定自訂的 S3 服務 URL。

>[!NOTE]
>此連接器需要 IAM 帳戶的存取金鑰，才能從 Amazon S3 複製資料。 不支援[暫存安全性認證](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)。
>

範例如下：

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
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

在以格式為基礎的資料集中，`location` 設定下方的 Amazon S3 支援下列屬性：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 在資料集中 `location` 下方的 type 屬性必須設定為 **AmazonS3Location**。 | 是      |
| bucketName | S3 貯體名稱。                                          | 是      |
| folderPath | 所指定貯體下的資料夾路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在活動來源設定中指定。 | 否       |
| fileName   | 在所指定貯體 + folderPath 下方的檔案名稱。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在活動來源設定中指定。 | 否       |
| version | 如果已啟用 S3 版本設定功能，則為 S3 物件的版本。 如果未指定，將會擷取最新版本。 |否 |

**範例︰**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Amazon S3 來源所支援的屬性清單。

### <a name="amazon-s3-as-source"></a>Amazon S3 作為來源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在以格式為基礎的複製來源中，`storeSettings` 設定下方的 Amazon S3 支援下列屬性：

| 屬性                 | 描述                                                  | 必要                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | 在 `storeSettings` 下方的 type 屬性必須設定為 **AmazonS3ReadSettings**。 | 是                                                         |
| ***找到要複製的檔案：*** |  |  |
| 選項 1：靜態路徑<br> | 從在資料集內指定的貯體或資料夾/檔案路徑複製。 如果您想要複製貯體/資料夾中的所有檔案，請另外將 `wildcardFileName` 指定為 `*`。 |  |
| 選項 2：S3 前置詞<br>- 前置詞 | 在資料集內設定的指定貯體下，S3 金鑰名稱的前置詞，用來篩選來源 S3 檔案。 已選取名稱開頭為 `bucket_in_dataset/this_prefix` 的 S3 金鑰。 其會利用 S3 的服務端篩選，提供比萬用字元篩選更好的效能。 | 否 |
| 選項 3：萬用字元<br>- wildcardFolderPath | 在資料集內設定的指定貯體底下，具有萬用字元的資料夾路徑，用來篩選來源資料夾。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 選項 3：萬用字元<br>- wildcardFileName | 在指定貯體 + folderPath/wildcardFolderPath 下方含萬用字元的檔案名稱，該萬用字元用來篩選來源檔案。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 3：檔案清單<br>- fileListPath | 表示複製指定的檔案集。 指向包含所要複製檔案清單的文字檔，為資料集中所設定路徑其相對路徑的每一行一個檔案。<br/>使用此選項時，請勿指定資料集中的檔案名稱。 請在[檔案清單範例](#file-list-examples)中參閱更多的範例。 |否 |
| ***其他設定：*** |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 <br>允許的值為 **true** (預設值) 和 **false**。<br>當您設定 `fileListPath` 時，此屬性不適用。 |否 |
| modifiedDatetimeStart    | 檔案篩選會根據以下屬性：上次修改時間。 <br>如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 NULL，這意謂著不會在資料集套用任何檔案屬性篩選。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。<br/>當您設定 `fileListPath` 時，此屬性不適用。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                                          |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有當您想要限制同時連線到資料存放區的連線數目時才指定。 | 否                                                          |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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

假設您有下列來源資料夾結構，且想要複製粗體的檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | ADF 組態                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| 貯體<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;中繼資料<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在資料集中：**<br>- 貯體：`bucket`<br>- 資料夾路徑：`FolderA`<br><br>**在複製活動來源中：**<br>- 檔案清單路徑：`bucket/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向包含所要複製檔案清單的同一個資料存放區，為資料集中所設定路徑其相對路徑的每一行一個檔案。 |

## <a name="preserve-metadata-during-copy"></a>複製期間保留中繼資料

當您將檔案從 Amazon S3 複製到 Azure Data Lake Storage Gen2/Azure Blob 時，可以選擇保留檔案中繼資料和資料。 深入了解[保留中繼資料](copy-activity-preserve-metadata.md#preserve-metadata)。

## <a name="lookup-activity-properties"></a>查詢活動屬性

若要了解屬性的相關詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要了解屬性的相關詳細資料，請參閱 [GetMetadata 活動](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>刪除活動屬性

若要了解屬性的相關詳細資料，請參閱[刪除活動](delete-activity.md)

## <a name="legacy-models"></a>傳統模型

>[!NOTE]
>下列模型仍依原狀支援，以提供回溯相容性。 建議您繼續使用以上各節中提到的新模型，撰寫 UI 的 ADF 已切換以產生新模型。

### <a name="legacy-dataset-model"></a>舊版資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**AmazonS3Object** |是 |
| bucketName | S3 貯體名稱。 不支援萬用字元篩選。 |[是] 適用於複製/查閱活動，[否] 適用於 GetMetadata 活動 |
| 索引鍵 | 指定貯體下 S3 物件索引鍵的**名稱或萬用字元篩選**。 未指定 "prefix" 屬性時才適用。 <br/><br/>針對資料夾部分和檔案名稱部分，都支援萬用字元篩選。 允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"key": "rootfolder/subfolder/*.csv"`<br/>- 範例 2：`"key": "rootfolder/subfolder/???20180427.txt"`<br/>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 如果實際資料夾/檔案名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。 |否 |
| prefix | S3 物件索引鍵的前置詞。 系統會選取索引鍵以此前置詞開頭的物件。 未指定 "key" 屬性時才適用。 |否 |
| version | 如果已啟用 S3 版本設定功能，則為 S3 物件的版本。 如果未指定，將會擷取最新版本。 |否 |
| modifiedDatetimeStart | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案進行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，這意謂著不會在資料集套用任何檔案屬性篩選。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案進行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，這意謂著不會在資料集套用任何檔案屬性篩選。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| format | 如果您想要在以檔案為基礎的存放區之間**依原樣複製檔案** (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析或產生特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 將格式下的 **type** 屬性設定為這些值其中之一。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)章節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：**GZip**、**Deflate**、**BZip2** 及 **ZipDeflate**。<br/>支援的層級為：**Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，以貯體指定 **bucketName**，並以資料夾部分指定 **prefix**。<br>若要使用指定的名稱複製單一檔案，以貯體指定 **bucketName**，並以資料夾部分加上檔案名稱指定 **key**。<br>若要複製資料夾下的檔案的子集，以貯體指定 **bucketName**，並以資料夾部分加上萬用字元指定 **key**。

**範例：使用前置詞**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

**範例：使用索引鍵和版本 (選擇性)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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
| maxConcurrentConnections | 可同時連線到資料存放區的連線數目。 只有當您想要限制同時連線到資料存放區的連線數目時才指定。 | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
