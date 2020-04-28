---
title: 建立和執行簡單的 R 指令碼
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在 Azure SQL Database 機器學習服務 (預覽) 中執行簡單的 R 指令碼。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bb6cb6d86933166d2427788d697d9cd38cf04bf0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460166"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>快速入門：在 Azure SQL Database 機器學習服務 (預覽) 中建立和執行簡單的 R 指令碼

在本快速入門中，您會使用 Azure SQL Database 中的機器學習服務 (搭配 R)，建立和執行一組 R 指令碼。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 具有[伺服器層級防火牆規則](sql-database-server-level-firewall-rule.md)的 [Azure SQL 資料庫](sql-database-single-database-get-started.md)
- 已啟用 R 的[機器學習服務](sql-database-machine-learning-services-overview.md)。
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

此範例會使用預存程式 [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 來包裝格式正確的 R 指令碼。

## <a name="run-a-simple-script"></a>執行簡單的指令碼

若要執行 R 指令碼，請將它當做引數傳遞至系統預存程式，[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)。

在下列步驟中，您會在 SQL 資料庫中執行此範例 R 指令碼：

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. 開啟 **SQL Server Management Studio**，然後連線至 SQL 資料庫。

   如果您需要連線方面的協助，請參閱[快速入門：使用 SQL Server Management Studio 連線和查詢 Azure SQL Database](sql-database-connect-query-ssms.md)。

1. 將完整的 R 指令碼傳遞至 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 預存程序。

   指令碼會透過 `@script` 引數傳遞。 `@script` 引數內的所有一切都必須是有效的 R 程式碼。

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   如果發生任何錯誤，有可能是因為您的 SQL 資料庫未啟用公開預覽版的機器學習服務 (搭配 R)。 請參閱上述[必要條件](#prerequisites)。

   > [!NOTE]
   > 如果您是系統管理員，您可以自動執行外部程式碼。 您可以使用以下命令，將權限授予其他使用者：
   <br>**GRANT EXECUTE ANY EXTERNAL SCRIPT TO** *\<username\>* 。

2. 系統會計算正確的結果，且 R `print` 函數會將結果傳回至 [訊息]  視窗。

   其外觀應該如下所示。

    **結果**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>執行 Hello World 指令碼

典型的範例指令碼只會輸出字串 "Hello World"。 執行下列命令。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

此預存程序的輸入包括：

| | |
|-|-|
| @language | 定義要呼叫的語言擴充功能，在本例中為 R |
| @script | 定義要傳遞至 R 執行階段的命令。 整個 R 指令碼都必須包含在這個引數中 (作為 Unicode 文字)。 您也可以將文字新增至 **Nvarchar** 類型的變數，並呼叫該變數 |
| @input_data_1 | 查詢所傳回的資料會傳遞到 R 執行階段，它會以資料框架的格式將資料傳回 SQL Server |
|使用結果集 | 子句會定義 SQL Server 傳回資料表的結構描述，然後加入 "Hello World" 做為資料行名稱，並將 **int** 用於資料類型 |

此命令會輸出下列文字：

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>使用者輸入和輸出

根據預設，[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 接受單一資料集作為輸入，這通常是您以有效 SQL 查詢的形式提供的資料集。 然後，它會傳回單一 R 資料框架做為輸出。

我們目前使用 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 的預設輸入和輸出變數：**InputDataSet** 和 **OutputDataSet**。

1. 建立測試資料的小型資料表。

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

1. 使用 `SELECT` 陳述式查詢資料表。
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **結果**

    ![RTestData 資料表的內容](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. 請執行下列 R 指令碼。 它會使用 `SELECT` 陳述式來擷取資料表中的資料、透過 R 執行階段傳遞，然後傳回資料做為資料框架。 `WITH RESULT SETS` 子句會定義為 SQL Database 傳回的資料表所具備的結構描述，並新增資料行名稱 NewColName  。

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **結果**

    ![從資料表傳回資料的 R 指令碼輸出](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. 現在讓我們變更輸入和輸出變數的名稱。 預設的輸入和輸出變數名稱是 **InputDataSet** 和 **OutputDataSet**，此指令碼會將名稱變更為 **SQL_in** 和 **SQL_out**：

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    請注意，R 區分大小寫。 R 指令碼中使用的輸入和輸出變數 (**SQL_out** **SQL_in**) 必須符合使用 `@input_data_1_name` 和 `@output_data_1_name` 定義的值 (包括大小寫)。

   > [!TIP]
   > 只有一個輸入資料集可以傳入作為參數，而且您只能傳回一個資料集。 不過，您可以從 R 程式碼內呼叫其他資料集，而且可以在資料集以外傳回其他類型的輸出。 您也可以為任何參數加上 OUTPUT 關鍵字，使其與結果一起傳回。

1. 您也可以在無輸入資料的情況下 (`@input_data_1` 設為空白)，只使用 R 指令碼產生值。

   下列指令碼輸出文字 "hello" 和 "world"。

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **結果**

    ![使用 @script 做為輸入查詢結果](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>檢查 R 版本

如果您想要查看安裝在 SQL 資料庫中的 R 版本，請執行下列指令碼。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

R `print` 函數會將版本傳回到 [訊息]  視窗。 在下列範例輸出中，您可以看到此案例中的 SQL 資料庫已安裝 R 3.4.4 版。

**結果**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>列出 R 套件

Microsoft 會在提供多個隨機器學習服務預先安裝在 SQL 資料庫中的 R 套件。

若要查看已安裝 R 套件的清單 (包括版本、相依性、授權及程式庫路徑資訊)，請執行下列指令碼。

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

輸出來自 R 中的 `installed.packages()`，並以結果集的形式傳回。

**結果**

![R 中已安裝的套件](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>後續步驟

若要在 SQL Database 中使用 R 建立機器學習模型，請遵循本快速入門：

> [!div class="nextstepaction"]
> [使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中建立和定型預測模型](sql-database-quickstart-r-train-score-model.md)

如需有關「搭配 R 的 Azure SQL Database 機器學習服務 (預覽)」的詳細資訊，請參閱下列文章。

- [搭配 R 的 Azure SQL Database 機器學習服務 (預覽)](sql-database-machine-learning-services-overview.md)
- [使用機器學習服務在 Azure SQL Database 中撰寫進階的 R 函式 (預覽)](sql-database-machine-learning-services-functions.md)
- [在 Azure SQL Database 機器學習服務 (預覽) 中使用 R 和 SQL 資料](sql-database-machine-learning-services-data-issues.md)
