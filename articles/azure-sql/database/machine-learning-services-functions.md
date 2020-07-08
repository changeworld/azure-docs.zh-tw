---
title: 撰寫進階的 R 函式
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 瞭解如何使用 Machine Learning 服務（預覽），在 Azure SQL Database 中撰寫高階統計計算的 R 函數。
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
ms.openlocfilehash: a7d06b08ce5047c24dd09278a16b9646dcb4e1f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84035639"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>使用機器學習服務在 Azure SQL Database 中撰寫進階的 R 函式 (預覽)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文說明如何在 SQL 預存程式中內嵌 R 數學和公用程式函數。 在 T-SQL 中難以執行的進階統計函數，只需要單一程式碼就可以在 R 中完成。

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/)再開始。

- 若要在這些練習中執行範例程式碼，您必須先啟用具有[Machine Learning 服務（使用 R）的 Azure SQL Database](machine-learning-services-overview.md) 。

- 確定您已安裝最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 您可以使用其他資料庫管理或查詢工具來執行 R 指令碼，但是在本快速入門中，您將使用 SSMS。

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>建立預存程序來產生亂數

為了簡單起見，讓我們使用 `stats` Machine Learning 服務（預覽）以 Azure SQL Database 預設安裝並載入的 R 封裝。 此套件包含數百個函式，可用於一般統計工作，在函式中 `rnorm` 。 此函式會根據標準差和表示，使用一般分佈產生指定數目的亂數字。

例如，下列 R 程式碼會在指定標準差為 3 的情況下，傳回平均值為 50 的 100 個數字。

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

若要從 T-sql 呼叫這行 R，請 `sp_execute_external_script` 在 r 腳本參數中執行並新增 r 函數，如下所示：

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

想要更輕鬆地產生一組不同的亂數？

這在與 SQL 結合時很容易。 您可以定義一個預存程序來取得使用者的引數，然後將這些引數當做變數傳遞至 R 指令碼。

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

`utils`預設安裝的封裝提供各種公用程式函式來調查目前的 R 環境。 如果您的 R 程式碼在 SQL 和外部環境中的執行方式不一致，這些函式會很有用。 例如，您可以使用 R `memory.limit()` 函式來取得目前 R 環境的記憶體。

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
> 許多使用者想要使用 R 中的系統計時函數（例如 `system.time` 和 `proc.time` ）來捕捉 r 進程所使用的時間，並分析效能問題。
