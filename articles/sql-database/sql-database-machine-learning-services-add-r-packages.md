---
title: 將 R 套件新增至機器學習服務 (預覽)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 本文說明如何安裝尚未安裝在 Azure SQL Database 機器學習服務 (預覽) 中的 R 套件。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ab066609bff773ceacb06be604e386eed5cdf7ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453331"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>將 R 套件新增至 Azure SQL Database 機器學習服務 (預覽)

本文說明如何將 R 套件新增至 Azure SQL Database 機器學習服務 (預覽)。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

- 將 [R](https://www.r-project.org) 和 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) 安裝在本機電腦上。 R 適用於 Windows、MacOS 和 Linux。 本文假設您使用 Windows。

- 本文包含使用 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 或 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) 在 Azure SQL Database 中執行 R 指令碼的範例。 您可以使用其他資料庫管理或查詢工具來執行 R 指令碼，但本快速入門將使用 Azure Data Studio 或 SSMS。
   
> [!NOTE]
> 在 Azure Data Studio 或 SSMS 中，您無法使用 **sp_execute_external_script** 執行 R 指令碼來安裝套件。 您只能依照本文中的說明，使用 R 命令列和 RStudio 來安裝和移除套件。 安裝套件之後，您可以使用 **sp_execute_external_script** 存取 R 指令碼中的套件函式。

## <a name="list-r-packages"></a>列出 R 套件

Microsoft 會在提供多個隨機器學習服務預先安裝在 Azure SQL 資料庫中的 R 套件。
您可以在 Azure Data Studio 或 SSMS 中執行下列命令，以查看已安裝的 R 套件清單。

1. 開啟 Azure Data Studio 或 SSMS，並連線至您的 Azure SQL Database。

1. 執行以下命令：

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

輸出應該會顯示如下。

**結果**

![R 中已安裝的套件](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>使用 sqlmlutils 新增套件

如果您需要使用尚未安裝在 Azure SQL Database 中的套件，您可以使用 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 加以安裝。 **sqlmlutils** 是一個套件，可協助使用者與 SQL 資料庫 (SQL Server 和 Azure SQL Database) 互動，以及從 R 或 Python 用戶端執行 SQL 中的 R 或 Python 程式碼。 目前在 Azure SQL Database 中僅支援 R 版本的 **sqlmlutils**。

在下列範例中，您將安裝可格式化及插補字串的 **[glue](https://cran.r-project.org/web/packages/glue/)** 套件。 這些步驟會安裝 **sqlmlutils** 和 **RODBCext** (**sqlmlutils** 的必要條件)，並新增 **glue** 套件。

### <a name="install-sqlmlutils"></a>安裝 **sqlmlutils**

1. 從 **將最新的**sqlmlutils https://github.com/Microsoft/sqlmlutils/tree/master/R/dist zip 檔案下載到您的本機電腦。 您不需要解壓縮此檔案。

1. 開啟**命令提示字元**並執行下列命令，將 **RODBCext** 和 **sqlmlutils** 安裝在您的本機電腦上。 請替換為您下載 **sqlmlutils** zip 檔案的完整路徑 (此範例假設該檔案位於您的 Documents 資料夾中)。
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    您應該會看到如下的輸出。

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > 如果您收到「'R' 未辨識為內部或外部命令、可執行程式或批次檔」的錯誤，可能表示 R.exe 的路徑未包含在 Windows 上的 **PATH** 環境變數中。 您可以將路徑新增至環境變數，或在命令提示字元中瀏覽至該資料夾 (例如 `cd C:\Program Files\R\R-3.5.3\bin`)，然後重試命令。

### <a name="add-the-package"></a>新增套件

1. 開啟 RStudio，並建立新的 **R 指令碼**檔案。 

1. 使用下列 R 程式碼透過 **sqlmlutils** 安裝 **glue** 套件。 請替換為您自己的 Azure SQL Database 連線資訊。

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > **scope** 可以是 **PUBLIC** 或 **PRIVATE**。 資料庫管理員可以使用公開範圍來安裝所有使用者都可以使用的套件。 私人範圍可讓套件僅供安裝套件的使用者使用。 若未指定範圍，預設範圍是**私人**。

### <a name="verify-the-package"></a>確認套件

在 RStudio 中執行下列 R 指令碼，以確認 **glue** 套件已安裝。 請使用您在先前的步驟中定義的相同**連線**。

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**結果**

![RTestData 資料表的內容](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>使用套件

安裝套件之後，您可以透過 **sp_execute_external_script** 在 R 指令碼中使用該套件。

1. 開啟 Azure Data Studio 或 SSMS，並連線至您的 Azure SQL Database。

1. 執行以下命令：

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    您會在 [訊息]  索引標籤中看到下列結果。

    **結果**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>移除套件

如果您想要移除套件，請在 RStudio 中執行下列 R 指令碼。 請使用您先前定義的相同**連線**。

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> 將 R 套件安裝到 Azure SQL 資料庫的另一個方法，是使用 **CREATE EXTERNAL LIBRARY** T-SQL 陳述式從位元組資料流上傳 R 套件。 請參閱 [CREATE EXTERNAL LIBRARY](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) 參考文件中的[從位元組資料流建立程式庫](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)。

## <a name="next-steps"></a>後續步驟

如需有關「搭配 R 的 Azure SQL Database 機器學習服務 (預覽)」的詳細資訊，請參閱下列文章。

- [搭配 R 的 Azure SQL Database 機器學習服務 (預覽)](sql-database-machine-learning-services-overview.md)
- [使用機器學習服務在 Azure SQL Database 中撰寫進階的 R 函式 (預覽)](sql-database-machine-learning-services-functions.md)
- [在 Azure SQL Database 機器學習服務 (預覽) 中使用 R 和 SQL 資料](sql-database-machine-learning-services-data-issues.md)