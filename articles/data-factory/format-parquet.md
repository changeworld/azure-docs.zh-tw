---
title: Azure 資料工廠中的鑲木地板格式
description: 本主題介紹如何在 Azure 資料工廠中處理鑲木地板格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 81bbd476cea0472647ca183fb188fc13725d1469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597621"
---
# <a name="parquet-format-in-azure-data-factory"></a>Azure 資料工廠中的鑲木地板格式

當您要**解析 Parquet 檔或將資料寫入 Parquet 格式時，** 請按照本文進行操作。 

支援以下連接器的 Parquet 格式[：Amazon S3、Azure](connector-amazon-simple-storage-service.md) [Blob、Azure 資料存儲湖存儲第 1 代](connector-azure-data-lake-store.md)[、Azure 資料存儲湖存儲第 2 代](connector-azure-data-lake-storage.md)[、Azure 檔存儲](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)[、FTP、Google](connector-ftp.md)[雲存儲](connector-google-cloud-storage.md)[、HDFS、HTTP](connector-hdfs.md)和[SFTP。](connector-sftp.md) [Azure Blob](connector-azure-blob-storage.md) [HTTP](connector-http.md)

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Parquet 資料集支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設置為**Parquet**。 | 是      |
| location         | 檔的位置設置。 每個基於檔的連接器都有自己的位置類型和受支援的屬性。 `location` **請參閱連接器文章 ->資料集屬性部分的詳細資訊**。 | 是      |
| 壓縮碼 | 寫入 Parquet 檔時使用的壓縮編解碼器。 從 Parquet 檔讀取時，資料工廠根據檔中繼資料自動確定壓縮編解碼器。<br>支援的類型是"**無****"，"gzip"，"****快速**"（預設）和 **"lzo"。** 注意 當前複製活動不支援 LZO 讀取/寫入 Parquet 檔。 | 否       |

> [!NOTE]
> Parquet 檔不支援列名中的空格。

下面是 Azure Blob 存儲上的 Parquet 資料集示例：

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Parquet 源和接收器支援的屬性清單。

### <a name="parquet-as-source"></a>鑲木地板作為來源

複製活動***\*源\**** 部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動源的類型屬性必須設置為**ParquetSource**。 | 是      |
| 商店設置 | 有關如何從資料存儲讀取資料的一組屬性。 每個基於檔的連接器在 下`storeSettings`都有自己的受支援的讀取設置。 **請參閱連接器文章 -> 複製活動屬性部分的詳細資訊**。 | 否       |

### <a name="parquet-as-sink"></a>鑲木地板作為水槽

複製活動***\*接收器\**** 部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動源的類型屬性必須設置為**ParquetSink**。 | 是      |
| 商店設置 | 有關如何將資料寫入資料存儲的一組屬性。 每個基於檔的連接器在 下`storeSettings`都有自己的支援的寫入設置。 **請參閱連接器文章 -> 複製活動屬性部分的詳細資訊**。 | 否       |

## <a name="mapping-data-flow-properties"></a>映射資料流程屬性

在映射資料流程中從[源轉換](data-flow-source.md)和[接收器轉換](data-flow-sink.md)中瞭解詳細資訊。

## <a name="data-type-support"></a>資料類型支援

目前不支援鑲有複雜資料類型（例如 MAP、LIST、STRUCT）。

## <a name="using-self-hosted-integration-runtime"></a>使用自託管集成運行時

> [!IMPORTANT]
> 對於由自託管集成運行時（例如本地資料存儲和雲資料存儲之間）授權的副本，如果您未**按本身份**複製 Parquet 檔，則需要在 IR 電腦上安裝**64 位 JRE 8（JAVA 運行時環境）或 OpenJDK**和**Microsoft Visual C++ 2010 可轉散布包**。 有關詳細資訊，請查看以下段落。

對於在具有 Parquet 檔序列化/反序列化的自承載 IR 上運行的副本，ADF 首先檢查註冊表*`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 以查找 JRE（如果未找到）來定位 JAVA 運行時*`JAVA_HOME`*，其次檢查 OpenJDK 的系統變數。

- **要使用 JRE**： 64 位 IR 需要 64 位 JRE。 您可以從[這裡](https://go.microsoft.com/fwlink/?LinkId=808605)找到該程式。
- **使用 OpenJDK**： 自 IR 版本 3.13 以來，它一直支援它。 請將 jvm.dll 與所有其他必要的 OpenJDK 組件一起封裝至自我裝載 IR 機器，然後相應地設定 JAVA_HOME 系統環境變數。
- **要安裝 Visual C++ 2010 可分發包**： 可視C++ 2010 可分發包未安裝自託管的 IR 安裝。 您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=14632)找到該程式。

> [!TIP]
> 如果您使用自我裝載 Integration Runtime 將資料複製到 Parquet 格式 (或從該格式複製資料)，而且遇到錯誤顯示 [叫用 Java 時發生錯誤。訊息: **java.lang.OutOfMemoryError:Java heap space**]，您可以在裝載自我裝載 IR 的機器中新增環境變數 `_JAVA_OPTIONS`，以調整 JVM 的堆積大小下限/上限，使系統能執行這樣的複製，然後重新執行管線。

![在自我裝載 IR 上設定 JVM 堆積大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

範例：將變數 `_JAVA_OPTIONS` 的值設定為 `-Xms256m -Xmx16g`。 旗標 `Xms` 指定 JAVA 虛擬機器 (JVM) 的初始記憶體配置集區，而 `Xmx` 指定記憶體配置集區的最大值。 這表示 JVM 啟動時有 `Xms` 數量的記憶體，且最多可以使用 `Xmx` 數量的記憶體。 預設情況下，ADF 使用最小 64 MB 和最大 1G。

## <a name="next-steps"></a>後續步驟

- [複製活動概述](copy-activity-overview.md)
- [映射資料流程](concepts-data-flow-overview.md)
- [查找活動](control-flow-lookup-activity.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)
