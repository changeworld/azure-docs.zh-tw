---
title: Common Data Model 格式
description: 使用 Common Data Model 中繼資料系統轉換資料
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: daperlov
ms.openlocfilehash: 452aa3406ac09dd8342d8ade0b56b126067b7582
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636403"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Azure Data Factory 中的 Common Data Model 格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Common Data Model (CDM) 中繼資料系統，可讓您輕鬆地在應用程式和商務程式之間共用資料和其意義。 若要深入瞭解，請參閱 [Common Data Model](/common-data-model/) 總覽。

在 Azure Data Factory 中，使用者可以使用對應資料流程，從儲存在 [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) model.js中的 CDM 實體轉換資料。 您也可以使用 CDM 實體參考，以 CDM 格式接收資料，以將資料以 CSV 或 Parquet 格式傳入資料分割資料夾。 

> [!NOTE]
> 適用于 ADF 資料流程的 Common Data Model (CDM) 格式連接器目前以公開預覽形式提供。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

Common Data Model 是以 [內嵌資料集](data-flow-source.md#inline-datasets) 的形式提供，同時將資料流程對應為來源和接收。

> [!NOTE]
> 當您撰寫 CDM 的實體時，您必須有現有的 CDM 實體定義 (中繼資料架構，) 已定義為做為參考使用。 ADF 資料流程接收會讀取該 CDM 實體檔案，並將架構匯入您的接收以進列欄位對應。

### <a name="source-properties"></a>來源屬性

下表列出 CDM 來源所支援的屬性。 您可以在 [ **來源選項** ] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必須是 `cdm` | 是 | `cdm` | format |
| 元資料格式 | 資料所在的實體參考。 如果使用 CDM 版本1.0，請選擇 [資訊清單]。 如果使用1.0 之前的 CDM 版本，請選擇 [開啟 model.js]。 | 是 | `'manifest'` 或 `'model'` | manifestType |
| 根位置：容器 | CDM 資料夾的容器名稱 | 是 | String | fileSystem |
| 根位置：資料夾路徑 | CDM 資料夾的根資料夾位置 | 是 | String | folderPath |
| 資訊清單檔：實體路徑 | 根資料夾內實體的資料夾路徑 | 否 | String | entityPath |
| 資訊清單檔：資訊清單名稱 | 資訊清單檔的名稱。 預設值為 ' default '  | 否 | String | manifestName |
| 依上次修改篩選 | 選擇根據上次修改檔案的時間進行篩選 | 否 | 時間戳記 | modifiedAfter <br> modifiedBefore | 
| 架構連結服務 | 主體所在的連結服務 | 是，如果使用資訊清單 | `'adlsgen2'` 或 `'github'` | corpusStore | 
| 實體參考容器 | 容器主體位於 | 是，如果在 ADLS Gen2 中使用資訊清單和主體 | String | adlsgen2_fileSystem |
| 實體參考存放庫 | GitHub 存放庫名稱 | 是，如果使用 GitHub 中的資訊清單和主體 | String | github_repository |
| 實體參考分支 | GitHub 存放庫分支 | 是，如果使用 GitHub 中的資訊清單和主體 | String |  github_branch |
| 主體資料夾 | 主體的根位置 | 是，如果使用資訊清單 | String | corpusPath |
| 主體實體 | 實體參考的路徑 | 是 | String | 實體 |
| 不允許找到任何檔案 | 若為 true，如果找不到任何檔案，就不會擲回錯誤 | 否 | `true` 或 `false` | ignoreNoFilesFound |

如果您想要在來源轉換中使用的實體定義位於與您的資料檔案夾相同的目錄中，您可以取消選取 [使用主體的實體]，並只輸入您想要做為實體參考之實體的實體。

### <a name="sink-settings"></a>接收設定

* 指向 CDM 實體參考檔案，其中包含您想要寫入之實體的定義。

![實體設定](media/data-flow/common-data-model-111.png "實體參考")

* 定義您希望 ADF 用來寫入實體之輸出檔的磁碟分割路徑和格式。

![實體格式](media/data-flow/common-data-model-222.png "實體格式")

* 設定資訊清單檔案的輸出檔位置和位置和名稱。

![cdm 位置](media/data-flow/common-data-model-333.png "CDM 位置")


#### <a name="import-schema"></a>匯入架構

CDM 只能做為內嵌資料集使用，而且預設不會有相關聯的架構。 若要取得資料行中繼資料，請按一下 [ **預測** ] 索引標籤的 [匯 **入架構** ] 按鈕這可讓您參考主體所指定的資料行名稱和資料類型。 若要匯入架構， [資料流程](concepts-data-flow-debug-mode.md) 的偵測會話必須是作用中，而且您必須要有現有的 CDM 實體定義檔才能指向。

將資料流程資料行對應到接收轉換中的實體屬性時，請按一下 [對應] 索引標籤，然後選取 [匯入架構]。 ADF 會讀取您在接收選項中指向的實體參考，讓您能夠對應到目標 CDM 架構。

![CDM 接收設定](media/data-flow/common-data-model-444.png "CDM 對應")

> [!NOTE]
>  在源自 Power BI 或 Power Platform 資料流程的來源類型上使用 model.js時，您可能會遇到來自來源轉換的「主體路徑為 null 或空白」錯誤。 這可能是因為格式化 model.js檔案中的分割區位置路徑有問題。 若要修正此問題，請遵循下列步驟： 

1. 在文字編輯器中開啟檔案 model.js
2. 尋找磁碟分割。Location 屬性 
3. 將 "blob.core.windows.net" 變更為 "dfs.core.windows.net"
4. 將 URL 中的任何 "% 2F" 編碼修正為 "/"
 

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

下表列出 CDM 接收所支援的屬性。 您可以在 [ **設定** ] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必須是 `cdm` | 是 | `cdm` | format |
| 根位置：容器 | CDM 資料夾的容器名稱 | 是 | String | fileSystem |
| 根位置：資料夾路徑 | CDM 資料夾的根資料夾位置 | 是 | String | folderPath |
| 資訊清單檔：實體路徑 | 根資料夾內實體的資料夾路徑 | 否 | String | entityPath |
| 資訊清單檔：資訊清單名稱 | 資訊清單檔的名稱。 預設值為 ' default ' | 否 | String | manifestName |
| 架構連結服務 | 主體所在的連結服務 | 是 | `'adlsgen2'` 或 `'github'` | corpusStore | 
| 實體參考容器 | 容器主體位於 | 是，如果 ADLS Gen2 中的主體 | String | adlsgen2_fileSystem |
| 實體參考存放庫 | GitHub 存放庫名稱 | 是，如果是 GitHub 中的主體 | String | github_repository |
| 實體參考分支 | GitHub 存放庫分支 | 是，如果是 GitHub 中的主體 | String |  github_branch |
| 主體資料夾 | 主體的根位置 | 是 | String | corpusPath |
| 主體實體 | 實體參考的路徑 | 是 | String | 實體 |
| 分割路徑 | 將寫入分割區的位置 | 否 | String | partitionPath |
| 清除資料夾 | 如果在寫入之前清除目的資料夾 | 否 | `true` 或 `false` | truncate |
| 格式類型 | 選擇指定 parquet 格式 | 否 | `parquet` 如果已指定 | subformat |
| 資料行分隔符號 | 如果寫入 DelimitedText，如何分隔資料行 | 是，如果寫入 DelimitedText | String | columnDelimiter |
| 第一個資料列做為標頭 | 如果使用 DelimitedText，是否要將資料行名稱加入為標頭 | 否 | `true` 或 `false` | columnNamesAsHeader |

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

在對應的資料流程中建立 [來源轉換](data-flow-source.md) 。