---
title: Azure Data Factory 中的二進位格式
description: 本主題說明如何在 Azure Data Factory 中處理二進位格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 297fb51dd1dd8f1dabdcf2fe9e0d2ead5c906c6f
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531826"
---
# <a name="binary-format-in-azure-data-factory"></a>Azure Data Factory 中的二進位格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

下列連接器支援二進位格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、 [檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

您可以在 [複製活動](copy-activity-overview.md)、 [GetMetadata 活動](control-flow-get-metadata-activity.md)或 [刪除活動](delete-activity.md)中使用二進位資料集。 使用二進位資料集時，ADF 不會剖析檔案內容，但會將它視為原樣處理。 

>[!NOTE]
>在複製活動中使用二進位資料集時，您只能從二進位資料集複製到二進位資料集。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供二進位資料集所支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的 type 屬性必須設定為 **Binary**。 | 是      |
| location         | 檔案 (s) 的位置設定。 每個以檔案為基礎的連接器都有自己的位置類型和支援的屬性 `location` 。 **請參閱連接器文章中的詳細資料-> 資料集屬性一節**。 | 是      |
| compression | 用來設定檔案壓縮的屬性群組。 當您想要在活動執行期間執行壓縮/解壓縮時，請設定此區段。 | 否 |
| type | 用來讀取/寫入二進位檔案的壓縮編解碼器。 <br>允許的值為 **bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**或 **TarGzip**。 <br>**請注意**，使用複製活動來將**ZipDeflate** / **TarGzip**檔解壓縮 (s) 並寫入至檔案型接收資料存放區時，預設會將檔案解壓縮到資料夾： `<path specified in dataset>/<folder named as source compressed file>/` ， `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` 在[複製活動來源](#binary-as-source)上使用，以控制是否要將壓縮檔案的名稱保留為資料夾結構 () 。| 否       |
| 等級 | 壓縮比例。 適用于複製活動接收中使用資料集時。<br>允許的值為 **最佳** 或 **最快速**。<br>- **最快：** 即使產生的檔案未以最佳方式壓縮，壓縮作業也應該儘快完成。<br>- **最佳**：即使作業需要較長的時間才能完成，壓縮作業也應以最佳方式壓縮。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

以下是 Azure Blob 儲存體上的二進位資料集範例：

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供二進位來源和接收所支援的屬性清單。

>[!NOTE]
>在複製活動中使用二進位資料集時，您只能從二進位資料集複製到二進位資料集。

### <a name="binary-as-source"></a>二進位作為來源

[複製活動*** \* 來源 \* *** ] 區段支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設為 **BinarySource**。 | 是      |
| formatSettings | 屬性的群組。 請參閱下面的 **二進位讀取設定** 表格。 | 否       |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 **請參閱連接器文章中的詳細資料-> 複製活動屬性一節**。 | 否       |

支援的 **二進位讀取設定** `formatSettings` ：

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings 的類型必須設定為 **BinaryReadSettings**。 | 是      |
| compressionProperties | 一組屬性，說明如何將指定壓縮編解碼器的資料解壓縮。 | 否       |
| preserveZipFileNameAsFolder<br> (*下 `compressionProperties` -> `type` 的 `ZipDeflateReadSettings` *)  | 適用于設定 **ZipDeflate** 壓縮的輸入資料集時。 指出複製期間是否要將來源 ZIP 檔案名保留為資料夾結構。<br>-當設定為 **true (預設) **時，Data Factory 會將解壓縮的檔案寫入 `<path specified in dataset>/<folder named as source zip file>/` 。<br>-當設定為 **false**時，Data Factory 會將解壓縮的檔案直接寫入至 `<path specified in dataset>` 。 請確定您在不同的來源 zip 檔案中沒有重複的檔案名，以避免發生賽車或非預期的行為。  | 否 |
| preserveCompressionFileNameAsFolder<br> (*下 `compressionProperties` -> `type` 的 `TarGZipReadSettings` *)  | 適用于設定 **TarGzip** 壓縮的輸入資料集時。 指出複製期間是否要將來源壓縮檔案名稱保留為資料夾結構。<br>-當設定為 **true (預設) **時，Data Factory 會將解壓縮檔案寫入至 `<path specified in dataset>/<folder named as source compressed file>/` 。 <br>-當設定為 **false**時，Data Factory 會將解壓縮的檔案直接寫入至 `<path specified in dataset>` 。 請確定您在不同的來源檔案中沒有重複的檔案名，以避免發生賽車或非預期的行為。 | 否 |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="binary-as-sink"></a>二進位 as 接收

複製活動*** \* 接收 \* ***區段支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設為 **BinarySink**。 | 是      |
| storeSettings | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在下都有自己支援的寫入設定 `storeSettings` 。 **請參閱連接器文章中的詳細資料-> 複製活動屬性一節**。 | 否       |

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)
