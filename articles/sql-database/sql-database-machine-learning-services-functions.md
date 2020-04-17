---
title: 撰寫進階的 R 函式
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 瞭解如何使用機器學習服務(預覽)在 Azure SQL 資料庫中編寫用於進階統計計算的 R 函數。
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
ROBOTS: NOINDEX
ms.openlocfilehash: ba78267b1c6dc8f0e1bd25bb8ecdb1d8d344d03e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453109"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>使用機器學習服務在 Azure SQL Database 中撰寫進階的 R 函式 (預覽)

本文介紹如何在 SQL 存儲過程中嵌入 R 數學函數和實用程式函數。 在 T-SQL 中難以執行的進階統計函數，只需要單一程式碼就可以在 R 中完成。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/)再開始。

- 要在這些練習中運行範例代碼,必須首先啟用[Azure SQL 資料庫,並啟用機器學習服務(R)。](sql-database-machine-learning-services-overview.md)

- 確定您已安裝最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 您可以使用其他資料庫管理或查詢工具來執行 R 指令碼，但是在本快速入門中，您將使用 SSMS。

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>建立預存程序來產生亂數

為簡單起見,讓我們使用使用機器學習服務`stats`(預覽)使用 Azure SQL 資料庫默認安裝和載入的 R 包。 該套件包含數百個用於常見統計工作的函數,其中包括函數`rnorm`。 給定標準差和平均值,此函數使用正態分佈生成指定數量的隨機數。

例如，下列 R 程式碼會在指定標準差為 3 的情況下，傳回平均值為 50 的 100 個數字。

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

要從 T-SQL 呼叫此行`sp_execute_external_script`R, 請執行 R 函數, 並在 R 文稿參數中新增 R 函數,如下所示:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

想要更輕鬆地產生一組不同的亂數？

與 SQL 結合使用時,這很容易。 您可以定義一個預存程序來取得使用者的引數，然後將這些引數當做變數傳遞至 R 指令碼。

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- 第一行定義執行預存程序時所需要的每個 SQL 輸入參數。

- 開頭為 `@params` 的行會定義 R 程式碼所使用的所有變數，以及對應的 SQL 資料類型。

- 緊接的行會將 SQL 參數名稱對應至相對應的 R 變數名稱。

現在您已將 R 函式包裝於預存程序中，您將可以輕鬆地呼叫函式並傳入不同的值，如下所示︰

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>使用 R 公用程式函式進行疑難排解

默認情況下`utils`安裝的套件提供了用於調查當前 R 環境的各種實用程式功能。 如果發現 R 代碼在 SQL 和外部環境中的表現方式存在差異,則這些函數非常有用。 例如，您可以使用 R `memory.limit()` 函式來取得目前 R 環境的記憶體。

由於 `utils` 套件預設會安裝但不會載入，您必須使用 `library()` 函式先載入它。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> 許多使用者喜歡使用 R 中的系統計時函數`system.time`,`proc.time`例如和,來捕獲 R 進程使用的時間並分析性能問題。
