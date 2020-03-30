---
title: Azure 資料工廠中的 ORC 格式
description: 本主題介紹如何在 Azure 資料工廠中處理 ORC 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: e104c4c8e976207859b75212d5406558f04c6377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597485"
---
# <a name="orc-format-in-azure-data-factory"></a>Azure 資料工廠中的 ORC 格式

如果要**解析 ORC 檔或將資料寫入 ORC 格式，** 請按照本文操作。 

支援以下連接器的 ORC 格式[：Amazon S3、Azure](connector-amazon-simple-storage-service.md) [Blob、Azure 資料存儲湖存儲第 1 代](connector-azure-data-lake-store.md)[、Azure 資料存儲湖存儲第 2 代](connector-azure-data-lake-storage.md)[、Azure 檔存儲](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)[、FTP、Google](connector-ftp.md)[雲存儲](connector-google-cloud-storage.md)[、HDFS、HTTP](connector-hdfs.md)和[SFTP。](connector-sftp.md) [Azure Blob](connector-azure-blob-storage.md) [HTTP](connector-http.md)

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 ORC 資料集支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設置為**Orc**。 | 是      |
| location         | 檔的位置設置。 每個基於檔的連接器都有自己的位置類型和受支援的屬性。 `location` **請參閱連接器文章 ->資料集屬性部分的詳細資訊**。 | 是      |

下面是 Azure Blob 存儲上的 ORC 資料集的示例：

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

### <a name="orc-as-source"></a>ORC 作為源

複製活動***\*源\**** 部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動源的類型屬性必須設置為**OrcSource**。 | 是      |
| 商店設置 | 有關如何從資料存儲讀取資料的一組屬性。 每個基於檔的連接器在 下`storeSettings`都有自己的受支援的讀取設置。 **請參閱連接器文章 -> 複製活動屬性部分的詳細資訊**。 | 否       |

### <a name="orc-as-sink"></a>ORC 作為接收器

複製活動***\*接收器\**** 部分支援以下屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動源的類型屬性必須設置為**OrcSink**。 | 是      |
| 商店設置 | 有關如何將資料寫入資料存儲的一組屬性。 每個基於檔的連接器在 下`storeSettings`都有自己的支援的寫入設置。 **請參閱連接器文章 -> 複製活動屬性部分的詳細資訊**。 | 否       |

## <a name="using-self-hosted-integration-runtime"></a>使用自託管集成運行時

> [!IMPORTANT]
> 對於由自託管集成運行時（例如本地資料存儲和雲資料存儲之間）授權的副本，如果您未**按本身份**複製 ORC 檔，則需要在 IR 電腦上安裝**64 位 JRE 8（JAVA 運行時環境）或 OpenJDK**和**Microsoft Visual C++ 2010 可轉散布包**。 有關詳細資訊，請查看以下段落。

對於在具有 ORC 檔序列化/反序列化的自承載 IR 上運行的副本，ADF 首先檢查註冊表*`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 以查找 JRE（如果未找到）來定位 JAVA 運行時*`JAVA_HOME`*，其次檢查 OpenJDK 的系統變數。

- **要使用 JRE**： 64 位 IR 需要 64 位 JRE。 您可以從[這裡](https://go.microsoft.com/fwlink/?LinkId=808605)找到該程式。
- **使用 OpenJDK**： 自 IR 版本 3.13 以來，它一直支援它。 請將 jvm.dll 與所有其他必要的 OpenJDK 組件一起封裝至自我裝載 IR 機器，然後相應地設定 JAVA_HOME 系統環境變數。
- **要安裝 Visual C++ 2010 可分發包**： 可視C++ 2010 可分發包未安裝自託管的 IR 安裝。 您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=14632)找到該程式。

> [!TIP]
> 如果使用自託管集成運行時將資料複製到/從 ORC 格式，並點擊錯誤說"調用 java 時發生錯誤，消息 **：java.lang.OutMemoryError：JAVA 堆空間**"，則可以在承載自託管 IR`_JAVA_OPTIONS`的機器中添加一個環境變數，以調整 JVM 的最小/最大堆大小以授權此類複製，然後重新運行管道。

![在自我裝載 IR 上設定 JVM 堆積大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

範例：將變數 `_JAVA_OPTIONS` 的值設定為 `-Xms256m -Xmx16g`。 旗標 `Xms` 指定 JAVA 虛擬機器 (JVM) 的初始記憶體配置集區，而 `Xmx` 指定記憶體配置集區的最大值。 這表示 JVM 啟動時有 `Xms` 數量的記憶體，且最多可以使用 `Xmx` 數量的記憶體。 預設情況下，ADF 使用最小 64 MB 和最大 1G。

## <a name="next-steps"></a>後續步驟

- [複製活動概述](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)
- [獲取中繼資料活動](control-flow-get-metadata-activity.md)
