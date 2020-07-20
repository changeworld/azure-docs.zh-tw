---
title: Azure Data Factory 中的差異格式
description: 使用差異格式從 delta lake 轉換和移動資料
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: daperlov
ms.openlocfilehash: 74c2e738153b1afa5c90f4769b6d9b0e982af363
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225154"
---
# <a name="delta-format-in-azure-data-factory"></a>Azure Data Factory 中的差異格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文重點說明如何使用差異格式，將資料複製到儲存在[Azure Data Lake 存放區 Gen2](connector-azure-data-lake-storage.md)或[Azure Blob 儲存體](connector-azure-blob-storage.md)中的差異 lake。 此連接器以[內嵌資料集](data-flow-source.md#inline-datasets)的形式提供，可將資料流程當做來源和接收來進行對應。

> [!NOTE]
> 用於對應資料流程的差異格式連接器目前以公開預覽形式提供。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

此連接器以[內嵌資料集](data-flow-source.md#inline-datasets)的形式提供，可將資料流程當做來源和接收來進行對應。

### <a name="source-properties"></a>來源屬性

下表列出差異來源所支援的屬性。 您可以在 [**來源選項**] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必須是`delta` | 是 | `delta` | format |
| 檔案系統 | Delta lake 的容器/檔案系統 | 是 | String | fileSystem |
| 資料夾路徑 | Delta lake 的直接 | 是 | String | folderPath |
| 壓縮類型 | 差異資料表的壓縮類型 | 否 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 壓縮等級 | 選擇壓縮是否儘快完成，或是否應該以最佳方式壓縮所產生的檔案。 | 如果 `compressedType` 指定，則為必要。 | compressionLevel |
| 時間移動 | 選擇是否要查詢較舊的差異資料表快照集 | 否 | 依時間戳記查詢：時間戳記 <br> 依版本查詢：整數 | timestampAsOf <br> versionAsOf |

#### <a name="import-schema"></a>匯入架構

Delta 僅以內嵌資料集的形式提供，而且根據預設，沒有相關聯的架構。 若要取得資料行中繼資料，按一下 [**預測**] 索引標籤中的 [匯**入架構**] 按鈕這可讓您參考主體所指定的資料行名稱和資料類型。 若要匯入架構，[資料流程](concepts-data-flow-debug-mode.md)的「資料流程」（debug）會話必須是作用中的，而且您必須具有指向的現有 CDM 實體定義檔。
 

### <a name="delta-source-script-example"></a>差異來源腳本範例

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>接收屬性

下表列出差異接收器所支援的屬性。 您可以在 [**設定**] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必須是`delta` | 是 | `delta` | format |
| 檔案系統 | Delta lake 的容器/檔案系統 | 是 | String | fileSystem |
| 資料夾路徑 | Delta lake 的直接 | 是 | String | folderPath |
| 壓縮類型 | 差異資料表的壓縮類型 | 否 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 壓縮等級 | 選擇壓縮是否儘快完成，或是否應該以最佳方式壓縮所產生的檔案。 | 如果 `compressedType` 指定，則為必要。 | compressionLevel |
| 真空 | 指定舊版資料表的保留閾值（以小時為單位）。 0或以下的預設值為30天 | 是 | 整數 | 真空 |
| Update 方法 | 指定在 delta lake 上允許的更新作業。 對於不是插入的方法，需要前面的 alter row 轉換來標示資料列。 | 是 | `true` 或 `false` | 可以刪除 <br> 插入 <br> 份 <br> upsertable |

### <a name="delta-sink-script-example"></a>差異接收器腳本範例

相關聯的資料流程腳本如下：

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>已知限制

寫入差異接收時，有已知的限制，其中所寫入的資料列數目不會在監視輸出中傳回。

## <a name="next-steps"></a>後續步驟

* 在對應的資料流程中建立[來源轉換](data-flow-source.md)。
* 在對應的資料流程中建立[接收轉換](data-flow-sink.md)。
* 建立[alter row 轉換](data-flow-alter-row.md)，以將資料列標示為 insert、update、upsert 或 delete。
