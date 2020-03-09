---
title: Azure Data Factory 中的二進位格式
description: 本主題描述如何在 Azure Data Factory 中處理二進位格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 8ebb4f0d1a06a7bf29dc46cd696b6acfd2527095
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386695"
---
# <a name="binary-format-in-azure-data-factory"></a>Azure Data Factory 中的二進位格式

下列連接器支援二進位格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

您可以在[複製活動](copy-activity-overview.md)、 [GetMetadata 活動](control-flow-get-metadata-activity.md)或[刪除活動](delete-activity.md)中使用二進位資料集。 使用二進位資料集時，ADF 不會剖析檔案內容，而是將它視為一樣。 

>[!NOTE]
>在複製活動中使用二進位資料集時，您只能從二進位資料集複製到二進位資料集。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供二進位資料集所支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設定為**Binary**。 | 是      |
| location         | 檔案的位置設定。 每個以檔案為基礎的連接器在 `location`之下都有自己的位置類型和支援的屬性。 **請參閱連接器文章-> 資料集屬性一節中的詳細資訊**。 | 是      |
| compression | 用來設定檔案壓縮的屬性群組。 當您想要在活動執行期間執行壓縮/解壓縮時，請設定此區段。 | 否 |
| type | 用來讀取/寫入二進位檔案的壓縮編解碼器。 <br>允許的值為**bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**。 表示在儲存檔案時使用。<br>請注意，使用複製活動來解壓縮 ZipDeflate 檔案並寫入檔案型接收資料存放區時，檔案將會解壓縮到資料夾： `<path specified in dataset>/<folder named as source zip file>/`。 | 否       |
| 層級 | 壓縮比例。 適用于複製活動接收器中使用資料集時。<br>允許的值為**最佳**或**最快**。<br>- **最快：** 即使未以最佳方式壓縮所產生的檔案，壓縮作業還是應該儘快完成。<br>- **最佳**：即使作業需要較長的時間才能完成，壓縮作業也應以最佳方式壓縮。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

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

### <a name="binary-as-source"></a>二進位做為來源

複製活動***\*來源\**** 區段中支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**BinarySource**。 | 是      |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在 `storeSettings`之下都有自己支援的讀取設定。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | 否       |

### <a name="binary-as-sink"></a>作為接收器的二進位

複製活動***\*接收\**** 區段中支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**BinarySink**。 | 是      |
| storeSettings | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在 `storeSettings`之下都有它自己的支援寫入設定。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | 否       |

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)
