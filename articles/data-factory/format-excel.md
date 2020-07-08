---
title: Azure Data Factory 中的 Excel 格式
description: 本主題描述如何在 Azure Data Factory 中處理 Excel 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: 8b4876377501209e19ac496d605d228208d2323d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84670841"
---
# <a name="excel-format-in-azure-data-factory"></a>Azure Data Factory 中的 Excel 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要**剖析 Excel**檔案時，請遵循這篇文章。 Azure Data Factory 支援 ".xls" 和 ".xlsx"。

下列連接器支援 Excel 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。 它支援作為來源，但不是接收。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Excel 資料集所支援的屬性清單。

| 屬性         | 說明                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設定為**Excel**。   | Yes      |
| location         | 檔案的位置設定。 每個以檔案為基礎的連接器在下都有自己的位置類型和支援的屬性 `location` 。 | Yes      |
| sheetName        | 要讀取資料的 Excel 工作表名稱。                       | Yes      |
| range            | 給定工作表中的儲存格範圍，用來尋找選擇性資料，例如 `A3:H5` （從 a3 到 H5 的資料表）、（從 `A3` 儲存格 a3 開始的資料表）、 `A3:A3` （單一資料格）。 如果未指定，ADF 會以資料表的形式讀取整個工作表。 | No       |
| firstRowAsHeader | 指定是否要將給定工作表/範圍中的第一個資料列視為具有資料行名稱的標題列。<br>允許的值為**true**和**false** （預設值）。 | No       |
| nullValue        | 指定 null 值的字串表示。 <br>預設值為**空字串**。 | No       |
| compressionCodec | 用來讀取 Excel 檔案的壓縮編解碼器。 <br>允許的值為**bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **snappy**或**lz4**。 預設值為 [未壓縮]。 <br>**注意：** 目前的複製活動不支援 "snappy" & "lz4"，而且對應資料流程不支援 "ZipDeflate"。 <br>**請注意**，使用複製活動來解壓縮**ZipDeflate**檔案並寫入檔案型接收資料存放區時，會將檔案解壓縮至資料夾： `<path specified in dataset>/<folder named as source zip file>/` 。 | No       |
| compressionLevel | 壓縮比例。 <br>允許的值為**最佳**或**最快**。<br>- **最快速：** 即使產生的檔案未以最佳方式壓縮，壓縮作業也應該儘快完成。<br>- **最佳**：即使作業需要較長的時間才能完成，壓縮作業也應以最佳方式壓縮。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | No       |

以下是 Azure Blob 儲存體上的 Excel 資料集範例：

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Excel 來源所支援的屬性清單。

### <a name="excel-as-source"></a>Excel 做為來源 

複製活動的 [ *** \* 來源 \* *** ] 區段支援下列屬性。

| 屬性      | 說明                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**ExcelSource**。 | Yes      |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 | 否       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
