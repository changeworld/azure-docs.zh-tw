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
ms.date: 01/14/2021
ms.openlocfilehash: 82871a09916b2b64f74e25088f5e75ac60a40678
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202499"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Amazon Simple Storage Service 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
>
> * [第 1 版](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [目前的版本](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何從 Amazon Simple Storage Service (Amazon S3) 複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

>[!TIP]
>若要深入瞭解 Amazon S3 到 Azure 儲存體的資料移轉案例，請參閱 [使用 Azure Data Factory 將資料從 Amazon s3 遷移至 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)。

## <a name="supported-capabilities"></a>支援的功能

此 Amazon S3 連接器支援下列活動：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

具體而言，此 Amazon S3 連接器支援依原樣複製檔案，或使用 [支援的檔案格式和壓縮編解碼器](supported-file-formats-and-compression-codecs.md)來剖析檔案。 您也可以選擇 [在複製期間保留檔案中繼資料](#preserve-metadata-during-copy)。 連接器會使用 [AWS Signature 第 4 版](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)來驗證對 S3 的要求。

>[!TIP]
>您可以使用此 Amazon S3 連接器，從任何與 *S3 相容的儲存體提供者*（例如 [Google Cloud storage](connector-google-cloud-storage.md)）複製資料。 在連結服務設定中指定相對應的服務 URL。

## <a name="required-permissions"></a>所需的權限

若要從 Amazon S3 複製資料，請確定您已授與 Amazon S3 物件作業的下列許可權： `s3:GetObject` 和 `s3:GetObjectVersion` 。

如果您使用 Data Factory UI 來撰寫，則 `s3:ListAllMyBuckets` `s3:ListBucket` / `s3:GetBucketLocation` 作業需要額外的和許可權，例如測試與連結服務的連接，以及從根流覽。 如果您不想要授與這些許可權，您可以從 UI 選擇 [測試與檔案路徑的連接] 或 [從指定的路徑流覽] 選項。

如需 Amazon S3 許可權的完整清單，請參閱在 AWS 網站上 [指定原則中的許可權](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) 。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Amazon S3 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Amazon S3 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **Type** 屬性必須設為 **AmazonS3**。 | 是 |
| authenticationType | 指定用來連接到 Amazon S3 的驗證類型。 您可以選擇使用 AWS 身分識別和存取管理 (IAM) 帳戶或 [暫時性安全性認證](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)的存取金鑰。<br>允許的值為： `AccessKey` (預設) 和 `TemporarySecurityCredentials` 。 |否 |
| accessKeyId | 密碼存取金鑰的識別碼。 |是 |
| secretAccessKey | 密碼存取金鑰本身。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或 [參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| sessionToken | 適用于使用 [暫時性安全性](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) 認證驗證時。 瞭解如何從 AWS [要求暫時性的安全性認證](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html#api_getsessiontoken) 。<br>注意 AWS 根據設定，暫存認證會在15分鐘到36小時之間到期。 當活動執行時，請確定您的認證有效，尤其是針對實際運作工作負載，例如，您可以定期重新整理，並將其儲存在 Azure Key Vault 中。<br>將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或 [參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |否 |
| serviceUrl | 如果您是從與 S3 相容的儲存體提供者（而非官方 Amazon S3 服務）複製資料，請指定自訂的 S3 端點。 例如，若要從 Google Cloud Storage 中複製資料，請指定 `https://storage.googleapis.com`。 | 否 |
| forcePathStyle | 指出是否要使用 S3 [路徑樣式存取](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#path-style-access) ，而不是使用虛擬 hosted 樣式存取。 允許的值為： **false** (預設) ， **true**。<br>如果您要連線到與 S3 相容的儲存提供者，而非官方 Amazon S3 服務，且該資料存放區需要路徑樣式存取 (例如 [Oracle Cloud storage](https://docs.oracle.com/iaas/Content/Object/Tasks/s3compatibleapi.htm)) ，請將此屬性設定為 true。 檢查每個資料存放區的檔，以瞭解是否需要存取路徑樣式。 |否 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路) ，您可以使用 Azure integration runtime 或自我裝載整合執行時間 (。 如果未指定此屬性，服務會使用預設的 Azure integration runtime。 |否 |

>[!TIP]
>如果您是從與 S3 相容的儲存體（而非官方 Amazon S3 服務）複製資料，請指定自訂 S3 服務 URL。

**範例：使用存取金鑰驗證**

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

**範例：使用暫時性安全性認證驗證**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "authenticationType": "TemporarySecurityCredentials",
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "sessionToken": {
                "type": "SecureString",
                "value": "<session token>"
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

以下是以 `location` 格式為基礎的資料集設定中的 Amazon S3 支援的屬性：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 資料集中的 **類型** 屬性 `location` 必須設定為 **AmazonS3Location**。 | 是      |
| bucketName | S3 貯體名稱。                                          | 是      |
| folderPath | 給定值區下的資料夾路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在 [活動來源] 設定中指定該設定。 | 否       |
| fileName   | 指定之值區和資料夾路徑底下的檔案名。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在 [活動來源] 設定中指定該設定。 | 否       |
| version | 如果已啟用 S3 版本設定功能，則為 S3 物件的版本。 如果未指定，將會提取最新版本。 |否 |

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

### <a name="amazon-s3-as-a-source-type"></a>Amazon S3 作為來源類型

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

以下是針對以 `storeSettings` 格式為基礎的複製來源設定下的 Amazon S3 支援的屬性：

| 屬性                 | 描述                                                  | 必要                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | 下的 **類型** 屬性 `storeSettings` 必須設為 **AmazonS3ReadSettings**。 | 是                                                         |
| **_找出要複製的檔案：_* _ |  |  |
| 選項 1：靜態路徑<br> | 從在資料集內指定的貯體或資料夾/檔案路徑複製。 如果您想要複製值區或資料夾中的所有檔案，請另外指定 `wildcardFileName` as `_` 。 |  |
| 選項 2：S3 前置詞<br>- 前置詞 | 在資料集內設定的指定值區下，S3 金鑰名稱的前置詞，以篩選來源 S3 檔案。 系統會選取其名稱開頭為的 S3 金鑰 `bucket_in_dataset/this_prefix` 。 它會使用 S3's 服務端篩選器，提供比萬用字元篩選器更佳的效能。<br/><br/>當您使用前置詞，並選擇複製到具有保留階層的檔案型接收時，請注意在前置詞中最後一個 "/" 之後的子路徑將會保留。 例如，您有來源  `bucket/folder/subfolder/file.txt` ，並將前置詞設定為 `folder/sub` ，則保留的檔案路徑為 `subfolder/file.txt` 。 | 否 |
| 選項 3：萬用字元<br>- wildcardFolderPath | 在資料集內設定的指定值區下，具有萬用字元的資料夾路徑，可篩選來源資料夾。 <br>允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。 `^`如果您的資料夾名稱裡面有萬用字元或這個 escape 字元，請使用來進行 escape。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 選項 3：萬用字元<br>- wildcardFileName | 在指定的值區和資料夾路徑下具有萬用字元的檔案名 (或萬用字元資料夾路徑，) 來篩選原始程式檔。 <br>允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。 `^`如果您的檔案名中有萬用字元或這個 escape 字元，請使用來進行 escape。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 4：檔案清單<br>- fileListPath | 表示要複製指定的檔案集。 指向文字檔，其中包含您要複製的檔案清單，每行一個檔案，也就是在資料集中設定之路徑的相對路徑。<br/>當您使用此選項時，請勿在資料集中指定檔案名。 [檔案清單範例](#file-list-examples) (英文) 有更多範例可供參閱。 |否 |
| ***其他設定：** _ |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當 _ *遞迴** 設定為 **true** 且接收是檔案型存放區時，不會在接收時複製或建立空的資料夾或子資料夾。 <br>允許的值為 **true** (預設值) 和 **false**。<br>設定 `fileListPath` 時，不適用此屬性。 |否 |
| deleteFilesAfterCompletion | 指出是否要在成功移至目的地存放區之後，從來源存放區刪除二進位檔案。 檔案刪除是針對每個檔案，因此當複製活動失敗時，您會看到部分檔案已複製到目的地並從來源刪除，其他檔案仍在來源存放區上。 <br/>這個屬性只在二進位檔案複製案例中有效。 預設值： false。 |否 |
| modifiedDatetimeStart    | 檔案會根據屬性進行篩選：上次修改。 <br>若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 時間會以 "2018-12-01T05：00： 00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 **Null**，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 有 datetime 值但 `modifiedDatetimeEnd` 為 **Null** 時，將會選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 有 datetime 值但 `modifiedDatetimeStart` 為 **Null** 時，將會選取上次修改屬性小於 datetime 值的檔案。<br/>設定 `fileListPath` 時，不適用此屬性。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                                          |
| enablePartitionDiscovery | 針對已分割的檔案，指定是否從檔案路徑剖析分割區，並將它們新增為其他來源資料行。<br/>允許的值為 **false** (預設) 和 **true**。 | 否                                            |
| partitionRootPath | 當資料分割探索已啟用時，請指定絕對根路徑，以便將分割的資料夾讀取為數據行。<br/><br/>如果未指定，則預設為<br/>-當您在資料集或來源上的檔案清單中使用檔案路徑時，資料分割根路徑是在資料集中設定的路徑。<br/>-當您使用萬用字元資料夾篩選時，資料分割根路徑是第一個萬用字元之前的子路徑。<br/>-當您使用前置詞時，資料分割根路徑是最後一個 "/" 之前的子路徑。 <br/><br/>例如，假設您將資料集中的路徑設定為 "root/folder/year = 2020/month = 08/day = 27"：<br/>-如果您將資料分割根路徑指定為 "root/folder/year = 2020"，則除了檔案內的資料行之外，複製活動會分別產生兩個數據行， `month` 以及 `day` 值為 "08" 和 "27" 的資料行。<br/>-如果未指定資料分割根路徑，將不會產生額外的資料行。 | 否                                            |
| maxConcurrentConnections | 與資料存放區的並行連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否                                                          |

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

假設您有下列源資料夾結構，而且想要以粗體複製檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | Data Factory 設定                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| 貯體<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;中繼資料<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在資料集中：**<br>- 貯體：`bucket`<br>- 資料夾路徑：`FolderA`<br><br>**在複製活動來源中：**<br>- 檔案清單路徑：`bucket/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向相同資料存放區中的文字檔，其中包含您要複製的檔案清單、每行一個檔案，以及在資料集中設定之路徑的相對路徑。 |

## <a name="preserve-metadata-during-copy"></a>複製期間保留中繼資料

當您將檔案從 Amazon S3 複製到 Azure Data Lake Storage Gen2 或 Azure Blob 儲存體時，您可以選擇保留檔案中繼資料以及資料。 深入了解[保留中繼資料](copy-activity-preserve-metadata.md#preserve-metadata)。

## <a name="lookup-activity-properties"></a>查詢活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要瞭解有關屬性的詳細資料，請檢查 [GetMetadata 活動](control-flow-get-metadata-activity.md)。 

## <a name="delete-activity-properties"></a>刪除活動屬性

若要瞭解有關屬性的詳細資料，請檢查 [刪除活動](delete-activity.md)。

## <a name="legacy-models"></a>舊版模型

>[!NOTE]
>為了回溯相容性，仍支援下列模型。 建議您使用稍早所述的新模型。 Data Factory 撰寫 UI 已切換為產生新的模型。

### <a name="legacy-dataset-model"></a>舊版資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 **type** 屬性必須設為 **>amazons3object**。 |是 |
| bucketName | S3 貯體名稱。 不支援萬用字元篩選。 |適用于複製或查閱活動，GetMetadata 活動不是 |
| 索引鍵 | 指定之值區下 S3 物件索引鍵的名稱或萬用字元篩選準則。 只有在未指定 **prefix** 屬性時才適用。 <br/><br/>資料夾元件和檔案名部分都支援萬用字元篩選。 允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"key": "rootfolder/subfolder/*.csv"`<br/>- 範例 2：`"key": "rootfolder/subfolder/???20180427.txt"`<br/>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 `^`如果您的實際資料夾或檔案名中有萬用字元或這個 escape 字元，請使用來進行 escape。 |否 |
| prefix | S3 物件索引鍵的前置詞。 系統會選取索引鍵以此前置詞開頭的物件。 只有在未指定索引 **鍵** 屬性時才適用。 |否 |
| version | 如果已啟用 S3 版本設定功能，則為 S3 物件的版本。 如果未指定版本，將會提取最新版本。 |否 |
| modifiedDatetimeStart | 檔案會根據屬性進行篩選：上次修改。 若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要篩選大量的檔案時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 **Null**，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 有 datetime 值但 `modifiedDatetimeEnd` 為 **Null** 時，將會選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 有 datetime 值但 `modifiedDatetimeStart` 為 Null 時，將會選取上次修改屬性小於 datetime 值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案會根據屬性進行篩選：上次修改。 若檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要篩選大量的檔案時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 **Null**，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 有 datetime 值但 `modifiedDatetimeEnd` 為 **Null** 時，將會選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 有 datetime 值但 `modifiedDatetimeStart` 為 **Null** 時，將會選取上次修改屬性小於 datetime 值的檔案。| 否 |
| format | 如果您想要在以檔案為基礎的存放區之間依原樣複製檔案 (二進位複製)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析或產生特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 將 [format] 下的 [type] 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)小節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：GZip、Deflate、BZip2 及 ZipDeflate。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請指定 **bucketName** 做為資料夾部分的 bucket 和 **前置** 詞。
>
>若要複製指定名稱的單一檔案，請指定 **bucketName** 做為值區和索引 **鍵** 的資料夾部分加上檔案名。
>
>若要複製資料夾下的檔案子集，請為資料夾部分的 bucket 和索引 **鍵** 指定 **bucketName** ，再加上萬用字元篩選。

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

### <a name="legacy-source-model-for-the-copy-activity"></a>複製活動的舊版來源模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設為 **>filesystemsource**。 |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當 **遞迴** 設定為 **true** 且接收是檔案型存放區時，將不會在接收時複製或建立空的資料夾或子資料夾。<br/>允許的值為 **true** (預設值) 和 **false**。 | 否 |
| maxConcurrentConnections | 可同時連線到資料存放區的連線數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否 |

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
如需 Azure Data Factory 中複製活動支援作為來源和接收的資料存放區清單，請參閱 [支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
