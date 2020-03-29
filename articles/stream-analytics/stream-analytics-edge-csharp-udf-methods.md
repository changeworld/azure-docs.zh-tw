---
title: 為 Azure 流分析作業開發 .NET 標準函數（預覽）
description: 瞭解如何為流分析作業編寫 c# 使用者定義的函數。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426305"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>為 Azure 流分析作業開發 .NET 標準使用者定義的函數（預覽）

Azure 串流分析提供類似 SQL 的查詢語言，以對事件資料的串流執行轉換與計算。 內建許多函式，但某些複雜的案例需要更大的彈性。 透過 .NET Standard 使用者定義的函式 (UDF)，您可以叫用任何以 .NET Standard 語言 (C#、F# 等) 撰寫的函式，以擴充串流分析查詢語言。 UDF 可讓您執行複雜的數學運算、使用 ML.NET 匯入自訂 ML 模型，及為遺漏的資料使用自訂插補邏輯。 適用於串流分析作業的 UDF 功能目前為預覽狀態，不應該用於生產環境工作負載。

.NET 使用者定義的雲作業功能可在：
* 美國中西部
* 北歐
* 美國東部
* 美國西部
* 美國東部 2
* 西歐

如果您有興趣在任何其他區域使用此功能，可以[請求訪問](https://aka.ms/ccodereqregion)。

## <a name="overview"></a>總覽
Azure 串流分析的 Visual Studio 工具可讓您輕鬆地撰寫 UDF、在本機 (甚至離線) 測試工作，並將串流分析工作發佈至 Azure。 發佈至 Azure 後，您便可使用 IoT 中樞將工作發佈至 IoT 裝置。

有三種方式可以實作 UDF：

* ASA 專案中的 CodeBehind 檔案
* 本機檔案的 UDF
* Azure 儲存體帳戶的現有封裝

## <a name="package-path"></a>封裝路徑

任何 UDF 封裝格式的路徑皆為 `/UserCustomCode/CLR/*`。 動態連結程式庫 (DLL) 與資源會複製到 `/UserCustomCode/CLR/*` 資料夾下方，這可幫助將使用者 DLL 與系統和 Azure 串流分析 DLL 隔離。 此套件路徑用於所有函式，而不論用來運用它們的方法為何。

## <a name="supported-types-and-mapping"></a>支援的型別與對應

|**UDF 型別 (C#)**  |**Azure 串流分析型別**  |
|---------|---------|
|long  |  BIGINT   |
|double  |  double   |
|字串  |  nvarchar(max)   |
|dateTime  |  dateTime   |
|struct  |  IRecord   |
|物件 (object)  |  IRecord   |
|陣列\<物件>  |  IArray   |
|dictionary<string, object>  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
您可以使用 **Script.asql** CodeBehind 撰寫使用者定義的函式。 Visual Studio 工具會自動將 CodeBehind 檔案編譯成組件檔。 組件會封裝為 zip 檔案，並在您將工作提交到 Azure 時上傳到您的儲存體帳戶。 您可以遵循 [針對 Azure 串流分析 Edge 作業撰寫 C# UDF](stream-analytics-edge-csharp-udf.md) 教學課程，了解如何使用 CodeBehind 撰寫 C# UDF。 

## <a name="local-project"></a>本機專案
使用者定義的函式可在 Azure 串流分析查詢稍後參照的組件中撰寫。 對於需要 .NET Standard 語言完整功能 (例如程序邏輯或遞迴) 超出其運算式語言之外的複雜函式，建議使用此選項。 本機專案的 UDF 也可用於您必須在數個 Azure 串流分析查詢之間共用函式邏輯的時候。 將 UDF 新增至您的本機專案，讓您能夠使用 Visual Studio 在本機偵錯與測試您的函式。

若要參照本機專案：

1. 在您的解決方案中建立新的類別庫。
2. 在您的類別中撰寫程式碼。 請記住，類別必須定義成*公用*，物件必須定義成*靜態公用*。 
3. 建置您的專案。 工具會將 bin 資料夾中的所有成品封裝為 ZIP 檔案，並將 ZIP 檔案上傳至儲存體帳戶。 針對外部參考，請使用組件參考，而不要使用 NuGet 套件。
4. 在您的 Azure 串流分析專案中參照新類別。
5. 在您的 Azure 串流分析專案中新增新的函式。
6. 在工作組態檔案中設定組件路徑，`JobConfig.json`。 將組件路徑設定為 [本機專案參考或 CodeBehind]****。
7. 重建函式專案與 Azure 串流分析專案。  

### <a name="example"></a>範例

在此示例中 **，UDFTest**是 C# 類庫專案 **，ASAUDFDemo**是 Azure 流分析專案，該專案將引用**UDFTest**。

![Visual Studio 中的 Azure 串流分析 IoT Edge 專案](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. 建置您的 C# 專案，讓您能夠從 Azure 串流分析查詢新增對您 C# UDF 的參照。
    
   ![在 Visual Studio 中建置 Azure 串流分析 IoT Edge 專案](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. 添加對 ASA 專案中的 C# 專案的引用。 以滑鼠右鍵按一下 [參照] 節點，然後選擇 [新增參照]。

   ![在 Visual Studio 中新增對 C# 專案的參照](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. 從清單中選擇 C# 專案名稱。 
    
   ![從參照清單中選擇 C# 專案名稱](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. 您應該會在 [方案總管]**** 中 [參照]**** 的下方看見 [UDFTest]****。

   ![在方案總管中檢視使用者定義的函式參照](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. 以滑鼠右鍵按一下 [函式]**** 資料夾，然後選擇 [新項目]****。

   ![在 Azure 串流分析 Edge 解決方案中將新項目新增至 Functions](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. 將 C# 函式 **SquareFunction.json** 新增至 Azure 串流分析專案。

   ![在 Visual Studio 中，從 Azure 串流分析 Edge 項目中選取 C Sharp 函式](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. 按兩下 [方案總管]**** 中的函式，以開啟設定對話方塊。

   ![Visual Studio 中的 C sharp 函式設定](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. 在 C# 函式設定中，選擇 [從 ASA 專案參照載入]****，然後從下拉式清單中選擇相關的組件、類別與方法名稱。 要引用流分析查詢中的方法、類型和函數，類必須定義為*公共*類，並且物件必須定義為*靜態公共*。

   ![串流分析 C sharp 函式設定](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>現有封裝

您可以在任何您選擇的 IDE 中撰寫 .NET Standard UDF，並從 Azure 串流分析查詢中叫用 UDF。 先編譯程式碼並封裝所有的 DLL。 封裝格式的路徑為 `/UserCustomCode/CLR/*`。 接著，以您的 Azure 儲存體帳戶將 `UserCustomCode.zip` 上傳至容器的根目錄。

當組件 zip 封裝已上傳到您的 Azure 儲存體帳戶後，您可以使用 Azure 串流分析查詢中的函式。 您只需在流分析作業配置中包含存儲資訊。 您無法使用此選項在本機上測試函式，因為 Visual Studio 工具不會下載您的封裝。 系統會直接對服務剖析封裝路徑。 

在工作設定檔 `JobConfig.json` 中設定組件路徑：

展開 [使用者定義的程式碼設定]**** 區段，然後在設定中填入以下建議的值：

   |**設定**|**建議值**|
   |-------|---------------|
   |全域儲存體設定資源|選擇目前帳戶中的資料來源|
   |全域儲存體設定訂用帳戶| < 您的訂用帳戶 >|
   |全域儲存體設定儲存體帳戶| < 您的儲存體帳戶 >|
   |自訂程式碼儲存體設定資源|選擇目前帳戶中的資料來源|
   |自訂程式碼儲存體設定儲存體帳戶|< 您的儲存體帳戶 >|
   |自訂程式碼儲存體設定容器|< 您的儲存體容器 >|
   |自訂代碼程式集源|雲的現有程式集包|
   |自訂代碼程式集源|使用者自訂代碼.zip|

## <a name="limitations"></a>限制
UDF 預覽目前有以下限制：

* .NET Standard UDF 只能在 Visual Studio 中撰寫並發佈至 Azure。 在 Azure 入口網站的 [函式]**** 下方，可檢視 .NET Standard UDF 的唯讀版本。 Azure 入口網站不支援撰寫 .NET Standard 函式。

* 在入口網站中使用 .NET Standard UDF 時，Azure 入口網站查詢編輯器會顯示錯誤。 

* 由於自訂程式碼會與 Azure 串流分析引擎共用內容，因此自訂程式碼不會參照任何命名空間/dll_name 與 Azure 串流分析程式碼衝突的項目。 例如，您無法參照 *Newtonsoft Json*。

## <a name="next-steps"></a>後續步驟

* [教程：為 Azure 流分析作業編寫 C# 使用者定義的函數（預覽）](stream-analytics-edge-csharp-udf.md)
* [教學課程：Azure 串流分析 JavaScript 使用者定義函式](stream-analytics-javascript-user-defined-functions.md)
* [使用 Visual Studio 檢視 Azure 串流分析工作](stream-analytics-vs-tools.md)
