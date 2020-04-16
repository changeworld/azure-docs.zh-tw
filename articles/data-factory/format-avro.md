---
title: Azure 資料工廠中的 Avro 格式
description: 本主題介紹如何在 Azure 數據工廠中處理 Avro 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 931287fa2a4104069b101236bec9f76bb7193e8d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416342"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure 資料工廠中的 Avro 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要**解析 Avro 檔或將資料寫入 Avro 格式時,** 請按照本文進行操作。 

Avro 格式支援以下連接器: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure 資料儲存湖儲存第一代](connector-azure-data-lake-store.md), [Azure 資料儲存第 2 代](connector-azure-data-lake-storage.md), Azure[檔案儲存](connector-azure-file-storage.md),[檔案系統](connector-file-system.md), [FTP,](connector-ftp.md)Google[雲儲存](connector-google-cloud-storage.md), [HDFS,](connector-hdfs.md) [HTTP](connector-http.md), 與[SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Avro 數據集支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設定為**Avro**。 | 是      |
| location         | 檔的位置設置。 每個基於檔的連接器都有自己的位置類型和受支援的屬性。 `location` **請參考連接器文章 ->数据集属性部分的详细信息**。 | 是      |
| 阿夫羅加斯科代碼 | 寫入 Avro 檔時使用的壓縮編解碼器。 從 Avro 檔案讀取時,資料工廠根據檔元數據自動確定壓縮編解碼器。<br>支援的類型是「**無**」(預設 **),"deflate",「****快速**」 。 注意 目前複製活動不支援 Snappy 讀取/ 寫入 Avro 檔案。 | 否       |

> [!NOTE]
> Avro 檔案不支援列名中的空格。

下面是 Azure Blob 儲存上的 Avro 資料集的範例:

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

複製活動***\*\*源***部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**AvroSource**。 | 是      |
| 商店設置 | 有關如何從數據存儲讀取數據的一組屬性。 每個基於檔的連接器在`storeSettings`下 都有自己的受支援的讀取設置。 **請參考連接器文章 -> 複製活動屬性部份的詳細資訊**。 | 否       |

### <a name="avro-as-sink"></a>阿夫羅作為水槽

複製活動***\*接收\*器***部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**AvroSink**。 | 是      |
| 商店設置 | 有關如何將數據寫入數據存儲的一組屬性。 每個基於檔的連接器在`storeSettings`下 都有自己的支援的寫入設置。 **請參考連接器文章 -> 複製活動屬性部份的詳細資訊**。 | 否       |

## <a name="data-type-support"></a>資料類型支援

### <a name="copy-activity"></a>複製活動
複製活動中不支援 Avro[複雜數據類型](https://avro.apache.org/docs/current/spec.html#schema_complex)(記錄、枚舉、陣列、地圖、聯合和固定)。

### <a name="data-flows"></a>資料流程
在資料流中處理 Avro 檔時,可以讀取和寫入複雜的數據類型,但請務必首先從數據集中清除物理架構。 在資料流中,您可以設置邏輯投影和派生是複雜結構的列,然後將這些欄位自動映射到 Avro 檔。

## <a name="next-steps"></a>後續步驟

- [複製活動概述](copy-activity-overview.md)
- [尋找活動](control-flow-lookup-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
