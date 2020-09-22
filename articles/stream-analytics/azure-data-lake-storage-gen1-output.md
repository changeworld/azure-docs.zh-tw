---
title: 從 Azure 串流分析 Azure Data Lake Storage Gen 1 輸出
description: 本文說明 Azure Data Lake Storage Gen 1 做為 Azure 串流分析的輸出選項。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: a642f7dfc470a695f96967cad0ed738d45b11efb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90881935"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>從 Azure 串流分析 Azure Data Lake Storage Gen 1 輸出

串流分析支援 [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) 輸出。 Azure Data Lake Storage 是容納巨量資料分析工作負載的企業級超大規模存放庫。 您可以使用 Data Lake Store 來存放任何大小、類型和擷取速度的資料，以便進行運作和探究分析。 串流分析需要經過授權，才能存取 Data Lake Storage。

Azure Data Lake Storage 來自串流分析的輸出，在 Azure 中國世紀和 Azure 德國 (的 T 系統國際) 區域中無法使用。

## <a name="output-configuration"></a>輸出設定

下表是設定 Data Lake Storage Gen 1 輸出的屬性名稱及其描述的清單。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 此為易記名稱，用於在查詢中將查詢輸出指向 Data Lake Store。 |
| 訂用帳戶 | 包含 Azure Data Lake Storage 帳戶的訂用帳戶。 |
| 帳戶名稱 | 您傳送輸出的 Data Lake Store 帳戶名稱。 您會看到您訂用帳戶中可用 Data Lake Store 帳戶的下拉式清單。 |
| 路徑前置詞模式 | 用來在指定 Data Lake Store 帳戶中寫入檔案的檔案路徑。 您可以指定一或多個 {date} 和 {time} 變數的執行個體：<br /><ul><li>範例 1：folder1/logs/{date}/{time}</li><li>範例 2：folder1/logs/{date}</li></ul><br />所建立資料夾結構的時間戳記遵循 UTC 而非當地時間。<br /><br />如果檔案路徑模式不包含尾端斜線 (/)，則會將檔案路徑中的最後一個模式視為檔案名稱前置詞。 <br /><br />系統會在下列情況下建立新檔案：<ul><li>輸出結構描述中出現變更</li><li>從外部或內部重新啟動作業</li></ul> |
| 日期格式 | 選擇性。 如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD |
|時間格式 | 選擇性。 如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。|
| 編碼 | 如果您使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。|
| 分隔符號 | 僅適用於 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。|
| [格式] | 僅適用於 JSON 序列化。 **分隔的行**會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 如果您選取**分隔的行**，JSON 會一次讀取一個物件。 整個內容本身不是有效的 JSON。  **陣列**會指定輸出將會格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 一般情況下，最好使用分行的 JSON，因為其不需要任何特殊處理，同時仍會寫入輸出檔案。|
| 驗證模式 | 您可以使用[受控識別](stream-analytics-managed-identities-adls.md)或使用者權杖來授權 Data Lake Storage 帳戶的存取權。 授與存取權之後，您可以藉由變更使用者帳戶密碼、刪除此作業的 Data Lake Storage 輸出，或刪除串流分析作業，來撤銷存取權。 |

## <a name="partitioning"></a>資料分割

針對分割區索引鍵，請在路徑前置詞模式中使用 {date} 和 {time} 標記。 選擇日期格式，例如 YYYY/MM/DD、DD/MM/YYYY 或 MM-DD-YYYY。 使用 HH 作為時間格式。 輸出寫入器的數目會遵循 [完整可並行查詢](stream-analytics-scale-jobs.md)的輸入資料分割。

## <a name="output-batch-size"></a>輸出批次大小

如需訊息大小上限，請參閱 [Data Lake Storage 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits)。 若要將批次大小優化，請使用每個寫入作業最多 4 MB。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure CLI 建立 Azure 串流分析作業](quick-create-azure-cli.md)
* [快速入門：使用 ARM 範本建立 Azure 串流分析作業](quick-create-azure-resource-manager.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)
* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)