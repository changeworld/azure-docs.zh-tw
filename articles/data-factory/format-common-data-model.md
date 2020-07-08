---
title: Common Data Model 格式
description: 使用通用資料模型中繼資料系統轉換資料
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: daperlov
ms.openlocfilehash: 5e75f2203552a69e50ed16176525429c6c9d8810
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807806"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Azure Data Factory 中的 Common Data Model 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Common Data Model （CDM）中繼資料系統可讓資料和其意義輕鬆地跨應用程式和商務進程共用。 若要深入瞭解，請參閱[Common Data Model](https://docs.microsoft.com/common-data-model/)總覽。

在 Azure Data Factory 中，使用者可以使用對應的資料流程，在儲存于[Azure Data Lake 存放區 Gen2](connector-azure-data-lake-storage.md) （ADLS Gen2）中的 CDM 實體之間進行轉換。

> [!NOTE]
> 適用于 ADF 資料流程的 Common Data Model （CDM）格式連接器目前以公開預覽形式提供。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

Common Data Model 是以[內嵌資料集](data-flow-source.md#inline-datasets)的形式提供，可將資料流程當做來源和接收來進行對應。

### <a name="source-properties"></a>來源屬性

下表列出 CDM 來源所支援的屬性。 您可以在 [**來源選項**] 索引標籤中編輯這些屬性。

| Name | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必須是`cdm` | 是 | `cdm` | format |
| 元資料格式 | 資料的實體參考所在位置。 如果使用 CDM 版本1.0，請選擇 [資訊清單]。 如果使用1.0 之前的 CDM 版本，請選擇 [model.js開啟]。 | Yes | `'manifest'` 或 `'model'` | manifestType |
| 根位置：容器 | CDM 資料夾的容器名稱 | 是 | String | fileSystem |
| 根位置：資料夾路徑 | CDM 資料夾的根資料夾位置 | 是 | String | folderPath |
| 資訊清單檔案：實體路徑 | 根資料夾中實體的資料夾路徑 | 不可以 | String | entityPath |
| 資訊清單檔：資訊清單名稱 | 資訊清單檔的名稱。 預設值為 [預設]  | 否 | String | manifestName |
| 依上次修改時間篩選 | 選擇根據上次修改檔案的時間來篩選檔案 | 不可以 | 時間戳記 | modifiedAfter <br> modifiedBefore | 
| 架構連結服務 | 主體所在的連結服務 | 是，如果使用資訊清單 | `'adlsgen2'` 或 `'github'` | corpusStore | 
| 實體參考容器 | 容器主體位於 | 是，如果在 ADLS Gen2 中使用資訊清單和主體 | String | adlsgen2_fileSystem |
| 實體參考存放庫 | GitHub 存放庫名稱 | 是，如果在 GitHub 中使用資訊清單和主體 | String | github_repository |
| 實體參考分支 | GitHub 存放庫分支 | 是，如果在 GitHub 中使用資訊清單和主體 | String |  github_branch |
| 主體資料夾 | 主體的根位置 | 是，如果使用資訊清單 | String | corpusPath |
| 主體實體 | 實體參考的路徑 | 是 | String | 實體 |
| 不允許找到任何檔案 | 若為 true，如果找不到任何檔案，就不會擲回錯誤。 | 不可以 | `true` 或 `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>匯入架構

CDM 僅以內嵌資料集的形式提供，而且根據預設，沒有相關聯的架構。 若要取得資料行中繼資料，按一下 [**預測**] 索引標籤中的 [匯**入架構**] 按鈕這可讓您參考主體所指定的資料行名稱和資料類型。 若要匯入架構，[資料流程](concepts-data-flow-debug-mode.md)的「資料流程」（debug）會話必須為作用中。


### <a name="cdm-source-data-flow-script-example"></a>CDM 來源資料流腳本範例

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>接收屬性

下表列出 CDM 接收所支援的屬性。 您可以在 [**設定**] 索引標籤中編輯這些屬性。

| Name | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必須是`cdm` | 是 | `cdm` | format |
| 根位置：容器 | CDM 資料夾的容器名稱 | 是 | String | fileSystem |
| 根位置：資料夾路徑 | CDM 資料夾的根資料夾位置 | 是 | String | folderPath |
| 資訊清單檔案：實體路徑 | 根資料夾中實體的資料夾路徑 | 不可以 | String | entityPath |
| 資訊清單檔：資訊清單名稱 | 資訊清單檔的名稱。 預設值為 [預設] | 否 | String | manifestName |
| 架構連結服務 | 主體所在的連結服務 | 是 | `'adlsgen2'` 或 `'github'` | corpusStore | 
| 實體參考容器 | 容器主體位於 | 是，如果 ADLS Gen2 中的主體 | String | adlsgen2_fileSystem |
| 實體參考存放庫 | GitHub 存放庫名稱 | 是，如果 GitHub 中的主體 | String | github_repository |
| 實體參考分支 | GitHub 存放庫分支 | 是，如果 GitHub 中的主體 | String |  github_branch |
| 主體資料夾 | 主體的根位置 | 是 | String | corpusPath |
| 主體實體 | 實體參考的路徑 | 是 | String | 實體 |
| 分割路徑 | 將寫入資料分割的位置 | 不可以 | String | partitionPath |
| 清除資料夾 | 如果在寫入之前清除目的資料夾 | 不可以 | `true` 或 `false` | truncate |
| 格式類型 | 選擇指定 parquet 格式 | 不可以 | `parquet`若已指定 | subformat |
| 資料行分隔符號 | 如果寫入 DelimitedText，如何分隔資料行 | 是，如果寫入 DelimitedText | String | columnDelimiter |
| 第一個資料列做為標頭 | 如果使用 DelimitedText，是否將資料行名稱加入為標頭 | 不可以 | `true` 或 `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>CDM 接收資料流程腳本範例

相關聯的資料流程腳本如下：

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>後續步驟

在對應的資料流程中建立[來源轉換](data-flow-source.md)。
