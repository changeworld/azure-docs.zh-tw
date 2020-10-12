---
title: Azure 串流分析的 Blob 儲存體和 Azure Data Lake Gen2 輸出
description: 本文說明 blob 儲存體和 Azure Data Lake Gen 2 作為 Azure 串流分析的輸出。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: d9805c45b7c0af0cfe6410defaab7ea7725691d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907202"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Azure 串流分析的 Blob 儲存體和 Azure Data Lake Gen2 輸出

Data Lake Storage Gen2 讓 Azure 儲存體成為在 Azure 上打造企業 Data Lake 的基礎。 Data Lake Storage Gen2 從一開始就設計為服務數 PB 的資訊，同時可以維持數百 GB 的輸送量，可讓您輕鬆地管理大量資料。Data Lake Storage Gen2 的基本功能是將階層式命名空間新增至 Blob 儲存體。

若要將大量非結構化資料儲存於雲端，Azure Blob 儲存體提供具有成本效益且可擴充的解決方案。 如需 Blob 儲存體及其使用方式的簡介，請參閱[使用 Azure 入口網站上傳、下載及列出 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

## <a name="output-configuration"></a>輸出設定

下表列出屬性名稱及其描述以建立 Blob 或 ADLS Gen2 輸出。

| 屬性名稱       | 描述                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 輸出別名        | 此為易記名稱，用於在查詢中將查詢輸出指向這個 blob 儲存體。 |
| 儲存體帳戶     | 您傳送輸出的儲存體帳戶名稱。               |
| 儲存體帳戶金鑰 | 與儲存體帳戶相關聯的密碼金鑰。                              |
| 儲存體容器   | 針對儲存在 Azure Blob 服務中 Blob 的邏輯群組。 當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。 |
| 路徑模式 | 選擇性。 用來在指定容器中寫入 Blob 的檔案路徑模式。 <br /><br /> 在路徑模式中，您可以選擇使用日期時間變數的一或多個執行個體，來指定 blob 的寫入頻率： <br /> {date}、{time} <br /><br />您可以使用自訂 Blob 資料分割，指定事件資料中的一個自訂 {field} 名稱來分割 Blob。 欄位名稱是英數字元，可以包含空格、連字號和底線。 自訂欄位的限制包含下列各項： <ul><li>欄位名稱不區分大小寫。 例如，服務無法分辨資料行 "ID" 和資料行 "id"。</li><li>不允許使用巢狀欄位。 改為在作業查詢中使用別名來「壓平」欄位。</li><li>運算式不能作為欄位名稱使用。</li></ul> <br />這項功能允許在路徑中使用自訂日期/時間格式的指定名稱設定。 自訂日期和時間格式必須一次指定一個，並以 {datetime： \<specifier> } 關鍵字括住。 允許的輸入為 \<specifier> yyyy、MM、M、dd、d、HH、H、MM、M、ss 或 s。 {Datetime： \<specifier> } 關鍵字可以在路徑中多次使用，以形成自訂日期/時間設定。 <br /><br />範例： <ul><li>範例 1：cluster1/logs/{date}/{time}</li><li>範例 2：cluster1/logs/{date}</li><li>範例 3：cluster1/{client_id}/{date}/{time}</li><li>範例 4：cluster1/{datetime:ss}/{myField}，其中查詢為：SELECT data.myField AS myField FROM Input;</li><li>範例 5：cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />所建立資料夾結構的時間戳記遵循 UTC 而非當地時間。<br /><br />檔案命名會使用下列慣例： <br /><br />{路徑前置詞模式}/schemaHashcode_Guid_Number.extension<br /><br />範例輸出檔案︰<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />如需有關這項功能的詳細資訊，請參閱 [Azure 串流分析自訂 Blob 輸出資料分割](stream-analytics-custom-path-patterns-blob-storage-output.md)。 |
| 日期格式 | 選擇性。 如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD |
| 時間格式 | 選擇性。 如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV、Avro 和 Parquet。 |
|最小資料列 |這是每一批次中的資料列數目下限。 針對 Parquet，每個批次都會建立新的檔案。 目前的預設值為 2,000 個資料列，允許的上限為 10,000 個資料列。|
|最長時間 |每個批次的等候時間上限。 在此時間後，即使未符合資料列下限需求，也會將批次寫入輸出。 目前的預設值為 1 分鐘，允許的上限為 2 小時。 如果您的 Blob 輸出具有路徑模式頻率，則等候時間不得高於分割區時間範圍。|
| 編碼    | 如果您使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號   | 僅適用於 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| [格式]      | 僅適用於 JSON 序列化。 **分隔的行**會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 如果您選取**分隔的行**，JSON 會一次讀取一個物件。 整個內容本身不是有效的 JSON。 **陣列**會指定輸出將會格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 一般情況下，最好使用分行的 JSON，因為其不需要任何特殊處理，同時仍會寫入輸出檔案。 |

## <a name="blob-output-files"></a>Blob 輸出檔

當您使用 Blob 儲存體作為輸出時，在下列情況下 Blob 中會建立新檔案：

* 如果檔案超過允許的區塊數目上限 (目前為 50,000 個)。 您可以在未達到允許 Blob 大小上限的情況下，達到允許的區塊數目上限。 比方說，如果輸出速率很高，您可看到每個區塊有更多位元組，且檔案大小更大。 如果輸出速率很低，每個區塊都會具有較少的資料，且檔案大小會較小。
* 如果輸出中有結構描述變更，且輸出格式需要固定的結構描述 (CSV 與 Avro)。
* 如果作業已重新啟動，無論是由使用者於外部停止並啟動它，或是於內部重新啟動以進行系統維護或錯誤復原。
* 如果完全分割查詢，則系統會為每個輸出分割區建立新檔案。
* 如果使用者刪除儲存體帳戶的檔案或容器。
* 如果使用路徑前置詞模式將輸出進行時間分割，則會在查詢移至下一個小時的時候使用新的 Blob。
* 如果輸出是由自訂欄位進行分割，系統會針對每個分割區索引鍵建立新的 blob (若不存在的話)。
* 如果輸出是由自訂欄位進行分割，其中分割區索引鍵基數超過 8000，則系統可能會針對每個分割區索引鍵建立新的 Blob。

## <a name="partitioning"></a>資料分割

針對分割區索引鍵，請使用路徑模式中事件欄位的 {date} 和 {time} 標記。 選擇日期格式，例如 YYYY/MM/DD、DD/MM/YYYY、MM-DD-YYYY。 HH 用於時間格式。 Blob 輸出可以依單一自訂事件屬性 {fieldname} 或 {datetime：} 來分割 \<specifier> 。 輸出寫入器的數目會遵循 [完整可並行查詢](stream-analytics-scale-jobs.md)的輸入資料分割。

## <a name="output-batch-size"></a>輸出批次大小

如需訊息大小上限，請參閱 [Azure 儲存體限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 Blob 區塊大小上限為 4 MB，最大 blob bock 計數為50000。 |

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure CLI 建立 Azure 串流分析作業](quick-create-azure-cli.md)
* [快速入門：使用 ARM 範本建立 Azure 串流分析作業](quick-create-azure-resource-manager.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)
* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)
