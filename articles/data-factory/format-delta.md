---
title: Azure Data Factory 中的差異格式
description: 使用差異格式從 delta lake 轉換和移動資料
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: daperlov
ms.openlocfilehash: 794c9a0768a7b649ce4fb123c85f6cc0120764c8
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854966"
---
# <a name="delta-format-in-azure-data-factory"></a>Azure Data Factory 中的差異格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文重點說明如何使用差異格式，將資料複製到儲存在 [Azure Data Lake 存放區 Gen2](connector-azure-data-lake-storage.md) 或 [Azure Blob 儲存體](connector-azure-blob-storage.md) 中的 delta lake。 此連接器以 [內嵌資料集](data-flow-source.md#inline-datasets) 的形式提供，可同時將資料流程對應為來源和接收。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

此連接器以 [內嵌資料集](data-flow-source.md#inline-datasets) 的形式提供，可同時將資料流程對應為來源和接收。

### <a name="source-properties"></a>來源屬性

下表列出差異來源所支援的屬性。 您可以在 [ **來源選項** ] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| [格式] | 格式必須是 `delta` | 是 | `delta` | format |
| 檔案系統 | Delta lake 的容器/檔案系統 | 是 | 字串 | fileSystem |
| 資料夾路徑 | Delta lake 的直接 | 是 | 字串 | folderPath |
| 壓縮類型 | Delta 資料表的壓縮類型 | 否 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 壓縮等級 | 選擇壓縮是否儘快完成，或是否應以最佳方式壓縮所產生的檔案。 | 如果 `compressedType` 指定，則為必要項。 | `Optimal` 或 `Fastest` | compressionLevel |
| 時間移動 | 選擇是否要查詢較舊的 delta 資料表快照集 | 否 | 依時間戳記查詢： Timestamp <br> 依版本查詢：整數 | timestampAsOf <br> versionAsOf |
| 不允許找到任何檔案 | 若為 true，如果找不到任何檔案，就不會擲回錯誤 | 否 | `true` 或 `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>匯入架構

Delta 只能做為內嵌資料集使用，而且預設不會有相關聯的架構。 若要取得資料行中繼資料，請按一下 [**預測**] 索引標籤的 [匯 **入架構**] 按鈕這可讓您參考主體所指定的資料行名稱和資料類型。 若要匯入架構， [資料流程](concepts-data-flow-debug-mode.md) 的偵測會話必須是作用中，而且您必須要有現有的 CDM 實體定義檔才能指向。
 

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

下表列出 delta 接收所支援的屬性。 您可以在 [ **設定** ] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| [格式] | 格式必須是 `delta` | 是 | `delta` | format |
| 檔案系統 | Delta lake 的容器/檔案系統 | 是 | 字串 | fileSystem |
| 資料夾路徑 | Delta lake 的直接 | 是 | 字串 | folderPath |
| 壓縮類型 | Delta 資料表的壓縮類型 | 否 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 壓縮等級 | 選擇壓縮是否儘快完成，或是否應以最佳方式壓縮所產生的檔案。 | 如果 `compressedType` 指定，則為必要項。 | `Optimal` 或 `Fastest` | compressionLevel |
| 真空 | 針對較舊版本的資料表指定保留閾值（以小時為單位）。 0或小於0的值預設為30天 | 是 | 整數 | 真空 |
| Update 方法 | 指定 delta lake 允許的更新作業。 針對不是插入的方法，需要有先前的 alter row 轉換來標記資料列。 | 是 | `true` 或 `false` | 刪除 <br> 插入 <br> 更新 <br> merge |

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

寫入 delta 接收時，有一個已知的限制，也就是不會在監視輸出中傳回寫入的資料列數目。

## <a name="next-steps"></a>後續步驟

* 在對應的資料流程中建立 [來源轉換](data-flow-source.md) 。
* 在對應資料流程中建立 [接收轉換](data-flow-sink.md) 。
* 建立 [alter row 轉換](data-flow-alter-row.md) ，將資料列標示為 insert、update、upsert 或 delete。
