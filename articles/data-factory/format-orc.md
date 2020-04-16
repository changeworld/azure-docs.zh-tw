---
title: Azure 資料工廠中的 ORC 格式
description: 本主題介紹如何在 Azure 數據工廠中處理 ORC 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 9b68d3724c6390fc5d30745924451e27ef9855b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417720"
---
# <a name="orc-format-in-azure-data-factory"></a>Azure 資料工廠中的 ORC 格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果要**解析 ORC 檔或將資料寫入 ORC 格式,** 請按照本文操作。 

支援以下連接器的 ORC 格式[:Amazon S3、Azure](connector-amazon-simple-storage-service.md) [Blob、Azure 資料儲存湖儲存第 1 代](connector-azure-data-lake-store.md)[、Azure 資料儲存湖儲存第 2 代](connector-azure-data-lake-storage.md)[、Azure 檔案儲存](connector-azure-file-storage.md),[檔案系統](connector-file-system.md)[、FTP、Google](connector-ftp.md)[雲端儲存](connector-google-cloud-storage.md)[、HDFS、HTTP](connector-hdfs.md)和[SFTP。](connector-sftp.md) [Azure Blob](connector-azure-blob-storage.md) [HTTP](connector-http.md)

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 ORC 數據集支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設定為**Orc**。 | 是      |
| location         | 檔的位置設置。 每個基於檔的連接器都有自己的位置類型和受支援的屬性。 `location` **請參考連接器文章 ->数据集属性部分的详细信息**。 | 是      |

下面是 Azure Blob 儲存上的 ORC 資料集的範例:

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

請注意下列幾點：

* 不支援複雜資料類型 (STRUCT、MAP、LIST、UNION)。
* 不支援資料行名稱中的空白字元。
* ORC 檔案有 3 種 [壓縮相關選項](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)︰NONE、ZLIB、SNAPPY。 Data Factory 支援以這些壓縮格式的任一項從 ORC 檔案讀取資料。 它會使用中繼資料裡的壓縮轉碼器來讀取資料。 不過，寫入 ORC 檔案時，Data Factory 會選擇 ZLIB，這是 ORC 的預設值。 目前沒有任何選項可覆寫這個行為。

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 ORC 源和接收器支援的屬性清單。

### <a name="orc-as-source"></a>ORC 為來源

複製活動***\*\*源***部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**OrcSource**。 | 是      |
| 商店設置 | 有關如何從數據存儲讀取數據的一組屬性。 每個基於檔的連接器在`storeSettings`下 都有自己的受支援的讀取設置。 **請參考連接器文章 -> 複製活動屬性部份的詳細資訊**。 | 否       |

### <a name="orc-as-sink"></a>ORC 為接收器

複製活動***\*接收\*器***部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**OrcSink**。 | 是      |
| 商店設置 | 有關如何將數據寫入數據存儲的一組屬性。 每個基於檔的連接器在`storeSettings`下 都有自己的支援的寫入設置。 **請參考連接器文章 -> 複製活動屬性部份的詳細資訊**。 | 否       |

## <a name="using-self-hosted-integration-runtime"></a>使用自託管整合時

> [!IMPORTANT]
> 對於從自託管整合時(例如本地資料儲存與雲端資料儲存之間)授權的複本,如果您未**按本身份**複製 ORC 檔案,則需要在 IR 電腦上安裝**64 位元 JRE 8(Java 執行時環境)或 OpenJDK**和**Microsoft Visual C++ 2010 可再分發套件**。 有關詳細資訊,請查看以下段落。

對於在具有 ORC 檔序列化/反序列化的自承載 IR 上運行的副本,ADF*`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 首先檢查註冊表 以查找 JRE(如果未找到*`JAVA_HOME`*)來定位 Java 運行時 ,其次檢查 OpenJDK 的系統變數。

- **要使用 JRE**: 64 位 IR 需要 64 位 JRE。 您可以從[這裡](https://go.microsoft.com/fwlink/?LinkId=808605)找到該程式。
- **使用 OpenJDK**: 自 IR 版本 3.13 以來,它一直支援它。 請將 jvm.dll 與所有其他必要的 OpenJDK 組件一起封裝至自我裝載 IR 機器，然後相應地設定 JAVA_HOME 系統環境變數。
- **要安裝 Visual C++ 2010 可分發包**: 可視C++ 2010 可分發包未安裝自託管的 IR 安裝。 您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=14632)找到該程式。

> [!TIP]
> 如果使用自託管整合時將資料複製到/從 ORC 格式,並點擊錯誤說"調用 java 時發生錯誤,消息 **:java.lang.OutMemoryError:Java 堆空間**",則可以在`_JAVA_OPTIONS`承載自託管 IR 的機器中添加一個環境變數,以調整 JVM 的最小/最大堆大小以授權此類複製,然後重新運行管道。

![在自我裝載 IR 上設定 JVM 堆積大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

範例：將變數 `_JAVA_OPTIONS` 的值設定為 `-Xms256m -Xmx16g`。 旗標 `Xms` 指定 JAVA 虛擬機器 (JVM) 的初始記憶體配置集區，而 `Xmx` 指定記憶體配置集區的最大值。 這表示 JVM 啟動時有 `Xms` 數量的記憶體，且最多可以使用 `Xmx` 數量的記憶體。 默認情況下,ADF 使用最小 64 MB 和最大 1G。

## <a name="next-steps"></a>後續步驟

- [複製活動概述](copy-activity-overview.md)
- [尋找活動](control-flow-lookup-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
