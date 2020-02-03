---
title: 使用 R 和 SQL 資料類型和物件
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 瞭解如何使用 Machine Learning 服務（預覽）中的 Azure SQL Database 來使用 R 中的資料類型和資料物件，包括您可能會遇到的常見問題。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 7dfd12729c5697d1935d098cbd4ed863a4551acd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719869"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>在 Azure SQL Database Machine Learning 服務（預覽）中使用 R 和 SQL 資料

本文討論當您在 Azure SQL Database 中的[Machine Learning 服務（使用 r）](sql-database-machine-learning-services-overview.md)中，于 r 和 SQL Database 之間移動資料時，可能會遇到的一些常見問題。 透過此練習所獲得的體驗，可提供您在使用自己指令碼資料時的基本背景。

您可能會遇到的常見問題包括：

- 資料類型有時不相符
- 可能會發生隱含轉換
- 有時需要轉型和轉換作業
- R 和 SQL 會使用不同的資料物件

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/)再開始。

- 若要在這些練習中執行範例程式碼，您必須具有已啟用機器學習服務 (搭配 R) 的 Azure SQL 資料庫。 在公開預覽期間，Microsoft 會將您加入，並為您現有的或新的資料庫啟用機器學習服務。 請遵循[註冊預覽版](sql-database-machine-learning-services-overview.md#signup)中的步驟。

- 確定您已安裝最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 您可以使用其他資料庫管理或查詢工具來執行 R 指令碼，但是在本快速入門中，您將使用 SSMS。

## <a name="working-with-a-data-frame"></a>使用資料框架

當您的腳本從 R 傳回結果到 SQL 時，它必須傳回資料做為**資料框架**。 您在指令碼中產生的任何其他類型物件 (不論是清單、因數、向量或二進位資料)，如果您想要輸出它以做為預存程序結果的一部分，就必須將它轉換為資料框架。 幸好有多個 R 函數可用來支援將其他物件變更為資料框架。 您甚至可以序列化二進位模型，並將它傳回資料框架中，這將在本文稍後進行。

首先，讓我們來實驗一些基本的 R 物件-向量、矩陣和清單，並瞭解轉換至資料框架如何變更傳遞至 SQL 的輸出。

在 R 中比較這兩個 "Hello World" 指令碼。這兩個指令碼看起來幾乎完全相同，但第一個會傳回三個值的單一資料行，而第二個會傳回三個資料行，每個均含單一值。

**範例 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**範例 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

為何結果如此不同？

您通常可以使用 R `str()` 命令來找到答案。 在 R 指令碼中的任一處加入函數 `str(object_name)`，以傳回指定 R 物件的資料結構描述做為資訊訊息。 您可以在 SSMS 的 [**訊息**] 索引標籤中查看訊息。

若要找出範例1和範例2有這類不同結果的原因，請在每個語句的 `@script` 變數定義結尾插入 `str(OutputDataSet)` 行，如下所示：

**已加入 str 函數的範例 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**已加入 str 函數的範例 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

現在，檢視 [訊息] 中的文字，以查看為何會有不同的輸出。

**結果 - 範例 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**結果 - 範例 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

如您所見，稍微變化一下 R 語法，就會對結果的結構描述產生極大的影響。 如需所有詳細資訊，R 資料類型的差異會在[Hadley wickham 針對的 "Advanced R"](http://adv-r.had.co.nz)中的*資料結構*一節中詳細說明。

現在只需注意，您需要在將 R 物件強制轉型為資料框架時檢查預期的結果。

> [!TIP]
> 您也可以使用 R 識別函式（例如 `is.matrix`、`is.vector`）來傳回內部資料結構的相關資訊。

## <a name="implicit-conversion-of-data-objects"></a>資料物件的隱含轉換

如果兩個數據物件具有相同的維度數目，或任何資料物件包含異質資料類型，則每個 R 資料物件都有自己的規則，以便在結合其他資料物件時處理值。

例如，假設您想要使用 R 來執行矩陣乘法。您想要將具有三個值的單一資料行矩陣乘以具有四個值的陣列，並預期4x3 矩陣的結果為。

首先，建立測試資料的小型資料表。

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

現在，請執行下列腳本。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

其中，具有三個值的資料行會轉換成單一資料行矩陣。 因為矩陣只是 R 中的特殊陣列案例，所以，陣列 `y` 會隱含地強制轉型為單一資料行的矩陣，以使這兩個引數相符。

**結果**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1200|1300|1400|1500|

不過，請注意您變更陣列 `y` 大小時會發生的情況。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

R 現在會傳回單一值做為結果。

**結果**
    
|Col1|
|---|
|1542|

原因為何？ 在此案例中，因為這兩個引數可以當做長度相同的向量來處理，所以 R 會傳回內積做為矩陣。  這是根據線性代數的規則所預期的行為。 不過，如果您的下游應用程式預期輸出架構永遠不會變更，則可能會造成問題！

## <a name="merge-or-multiply-columns-of-different-length"></a>合併或相乘不同長度的資料行

R 提供絕佳彈性來使用不同大小的向量，以及將這些類似資料行的結構結合至資料框架中。 向量的清單看起來像是一個資料表，但它們不遵循用以管理資料庫資料表的所有規則。

例如，下列指令碼定義長度為 6 的數值陣列，並將它儲存在 R 變數 `df1` 中。 然後，數值陣列會與 RTestData 資料表（如上所建立）的整數結合，其中包含三（3）個值，以建立新的資料框架，`df2`。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

為了填滿資料框架，R 會根據比對陣列 `df1` 中元素數目所需的次數，多次重複使用擷取自 RTestData 的元素。

**結果**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

請記住，資料框架只看起來像是資料表，但實際上是一份向量清單。

## <a name="cast-or-convert-sql-data"></a>轉換或轉換 SQL 資料

R 和 SQL 不會使用相同的資料類型，因此當您在 SQL 中執行查詢以取得資料，然後將其傳遞至 R 執行時間時，通常會發生某種類型的隱含轉換。 當您將資料從 R 傳回到 SQL 時，會發生另一組轉換。

- SQL 會將查詢中的資料推送至 R 進程，並將它轉換成內部表示，以提高效率。
- R 執行階段會將資料載入 data.frame 變數，並對資料執行自己的作業。
- 資料庫引擎會使用安全的內部連接將資料傳回 SQL，並根據 SQL 資料類型來呈現資料。
- 您可以使用可發出 SQL 查詢及處理表格式資料集的用戶端或網路程式庫來連接到 SQL，藉以取得資料。 此用戶端應用程式可能會以其他方式影響資料。

若要查看其運作方式，請在 [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) 資料倉儲上執行這類查詢。 此檢視會傳回用來建立預測的銷售資料。

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> 您可以使用任何版本的 AdventureWorks，或使用您自己的資料庫建立不同查詢。 重點是嘗試處理一些包含文字、日期時間和數值的資料。

現在，請嘗試使用此查詢做為預存程式的輸入。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

如果您收到錯誤，您可能需要對查詢文字進行一些編輯。 例如，WHERE 子句中的字串述詞必須以兩組單引號括住。

讓查詢能夠正常運作之後，檢閱 `str` 函數的結果，以查看 R 如何處理輸入資料。

**結果**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- 已使用 R 資料類型 **POSIXct** 來處理 datetime 資料行。
- 已將文字資料行 "ProductSeries" 識別為一個**因數**，這表示類別變數。 預設會處理字串值以做為因數。 如果您將字串傳遞至 R，則會將它轉換為整數，以供內部使用，接著對應回輸出上的字串。

## <a name="summary"></a>摘要

您可以從這些簡短的範例中了解到，將 SQL 查詢做為輸入進行傳遞時，需檢查資料轉換的效果。 由於 R 不支援某些 SQL 資料類型，因此請考慮使用下列方式來避免發生錯誤：

- 事先測試您的資料，並確認結構描述中可能會在傳遞至 R 程式碼發生問題的資料行或值。
- 個別指定輸入資料來源中的資料行，而不是使用 `SELECT *`，並了解將如何處理每個資料行。
- 在準備輸入資料時視需要執行明確的轉型，以避免出現意外狀況。
- 避免傳遞會造成錯誤且不適用模型化的資料行 (例如 GUID 或 rowguids)。

如需支援和不支援的 R 資料類型的詳細資訊，請參閱[R 程式庫和資料類型](/sql/advanced-analytics/r/r-libraries-and-data-types)。
