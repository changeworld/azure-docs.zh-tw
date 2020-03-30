---
title: Azure 資料工廠中的二進位格式
description: 本主題介紹如何在 Azure 資料工廠中處理二進位格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 8ebb4f0d1a06a7bf29dc46cd696b6acfd2527095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260692"
---
# <a name="binary-format-in-azure-data-factory"></a>Azure 資料工廠中的二進位格式

支援以下連接器的二進位格式[：Amazon S3、Azure](connector-amazon-simple-storage-service.md) [Blob、Azure](connector-azure-blob-storage.md)[資料存儲湖存儲第 1 代](connector-azure-data-lake-store.md)[、Azure 資料存儲湖存儲第 2 代](connector-azure-data-lake-storage.md)[、Azure 檔存儲](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)[、FTP、Google](connector-ftp.md)[雲存儲](connector-google-cloud-storage.md)[、HDFS、HTTP](connector-hdfs.md)和[SFTP。](connector-sftp.md) [HTTP](connector-http.md)

您可以在[複製活動](copy-activity-overview.md)、[獲取中繼資料活動](control-flow-get-metadata-activity.md)或刪除[活動](delete-activity.md)中使用二進位資料集。 使用二進位資料集時，ADF 不會解析檔內容，而是將其視為""。 

>[!NOTE]
>在複製活動中使用二進位資料集時，只能從二進位資料集複製到二進位資料集。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供二進位資料集支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設置為**二進位**。 | 是      |
| location         | 檔的位置設置。 每個基於檔的連接器都有自己的位置類型和受支援的屬性。 `location` **請參閱連接器文章 ->資料集屬性部分的詳細資訊**。 | 是      |
| compression | 用於配置檔案壓縮的屬性組。 如果要在活動執行期間執行壓縮/解壓縮，請配置此部分。 | 否 |
| type | 用於讀取/寫入二進位檔案的壓縮編解碼器。 <br>允許的值是 bzip2、gzip、deflate、ZipDeflate 。 **bzip2** **gzip** **deflate** **ZipDeflate** 保存檔時使用。<br>注意當使用複製活動來解壓縮 ZipDeflate 檔並寫入基於檔的接收器資料存儲時，檔將提取到資料夾： `<path specified in dataset>/<folder named as source zip file>/`。 | 否       |
| 層級 | 壓縮比。 在複製活動接收器中使用資料集時應用。<br>允許的值是**最佳**值或**最快**值。<br>- **最快：** 壓縮操作應儘快完成，即使生成的檔未以最佳方式壓縮也是如此。<br>- **最佳**： 壓縮操作應以最佳方式壓縮，即使操作需要更長的時間才能完成。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

下面是 Azure Blob 存儲上的二進位資料集的示例：

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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供二進位源和接收器支援的屬性清單。

>[!NOTE]
>在複製活動中使用二進位資料集時，只能從二進位資料集複製到二進位資料集。

### <a name="binary-as-source"></a>二進位作為源

複製活動***\*源\**** 部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動源的類型屬性必須設置為**二進位源**。 | 是      |
| 商店設置 | 有關如何從資料存儲讀取資料的一組屬性。 每個基於檔的連接器在 下`storeSettings`都有自己的受支援的讀取設置。 **請參閱連接器文章 -> 複製活動屬性部分的詳細資訊**。 | 否       |

### <a name="binary-as-sink"></a>二進位作為接收器

複製活動***\*接收器\**** 部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動源的類型屬性必須設置為**BinarySink**。 | 是      |
| 商店設置 | 有關如何將資料寫入資料存儲的一組屬性。 每個基於檔的連接器在 下`storeSettings`都有自己的支援的寫入設置。 **請參閱連接器文章 -> 複製活動屬性部分的詳細資訊**。 | 否       |

## <a name="next-steps"></a>後續步驟

- [複製活動概述](copy-activity-overview.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)
