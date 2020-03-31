---
title: Azure 資料工廠中的 Avro 格式
description: 本主題介紹如何在 Azure 資料工廠中處理 Avro 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 1717969aeb24a153f986c70ef60db1aac5c840fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267780"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure 資料工廠中的 Avro 格式

當您想要**解析 Avro 檔或將資料寫入 Avro 格式時，** 請按照本文進行操作。 

Avro 格式支援以下連接器： [Amazon S3，](connector-amazon-simple-storage-service.md) [Azure Blob，](connector-azure-blob-storage.md) [Azure 資料存儲湖存儲第一代](connector-azure-data-lake-store.md)， [Azure 資料存儲第 2 代](connector-azure-data-lake-storage.md)， Azure[檔存儲](connector-azure-file-storage.md)，[檔案系統](connector-file-system.md)， [FTP，](connector-ftp.md)[谷歌雲存儲](connector-google-cloud-storage.md)， [HDFS，](connector-hdfs.md) [HTTP](connector-http.md)， 和[SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Avro 資料集支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設置為**Avro**。 | 是      |
| location         | 檔的位置設置。 每個基於檔的連接器都有自己的位置類型和受支援的屬性。 `location` **請參閱連接器文章 ->資料集屬性部分的詳細資訊**。 | 是      |
| 阿夫羅加斯科代碼 | 寫入 Avro 檔時使用的壓縮編解碼器。 從 Avro 檔讀取時，資料工廠根據檔中繼資料自動確定壓縮編解碼器。<br>支援的類型是"**無**"（預設 **），"deflate"，"****快速**"。 注意 當前複製活動不支援 Snappy 讀取/寫入 Avro 檔。 | 否       |

> [!NOTE]
> Avro 檔不支援列名中的空格。

下面是 Azure Blob 存儲上的 Avro 資料集的示例：

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Avro 源和接收器支援的屬性清單。

### <a name="avro-as-source"></a>阿夫羅作為來源

複製活動***\*源\**** 部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動源的類型屬性必須設置為**AvroSource**。 | 是      |
| 商店設置 | 有關如何從資料存儲讀取資料的一組屬性。 每個基於檔的連接器在 下`storeSettings`都有自己的受支援的讀取設置。 **請參閱連接器文章 -> 複製活動屬性部分的詳細資訊**。 | 否       |

### <a name="avro-as-sink"></a>阿夫羅作為水槽

複製活動***\*接收器\**** 部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動源的類型屬性必須設置為**AvroSink**。 | 是      |
| 商店設置 | 有關如何將資料寫入資料存儲的一組屬性。 每個基於檔的連接器在 下`storeSettings`都有自己的支援的寫入設置。 **請參閱連接器文章 -> 複製活動屬性部分的詳細資訊**。 | 否       |

## <a name="data-type-support"></a>資料類型支援

### <a name="copy-activity"></a>複製活動
複製活動中不支援 Avro[複雜資料類型](https://avro.apache.org/docs/current/spec.html#schema_complex)（記錄、枚舉、陣列、地圖、聯合和固定）。

### <a name="data-flows"></a>資料流程
在資料流程中處理 Avro 檔時，可以讀取和寫入複雜的資料類型，但請務必首先從資料集中清除物理架構。 在資料流程中，您可以設置邏輯投影和派生是複雜結構的列，然後將這些欄位自動映射到 Avro 檔。

## <a name="next-steps"></a>後續步驟

- [複製活動概述](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)
