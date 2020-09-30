---
title: 搭配 Azure SQL Database 機器學習服務 (預覽) 使用 R來查詢資料庫
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 本文說明如何搭配 Azure SQL Database 機器學習服務使用 R 指令碼，來連線至 Azure SQL Database 中的資料庫，並使用 Transact-SQL 陳述式進行查詢。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2 
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, sstein
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 3a939c816cac44ed85802ecfa591564effc1ee73
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328827"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>快速入門：搭配 Azure SQL Database 機器學習服務 (預覽) 使用 R來查詢資料庫 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在本快速入門中，您會搭配 Azure SQL Database 機器學習服務使用 R，來連線至 Azure SQL Database 中的資料庫，並使用 T-SQL 陳述式來查詢資料。

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>必要條件

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure SQL Database](single-database-create-quickstart.md)
- 已啟用 R 的[機器學習服務](machine-learning-services-overview.md)。
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> 本文中已撰寫的指令碼會使用 **Adventure Works** 資料庫。

搭配 R 的機器學習服務是 Azure SQL 資料庫的功能，用來執行資料庫內 R 指令碼。 如需詳細資訊，請參閱 [R 專案](https://www.r-project.org/)。

## <a name="get-the-sql-server-connection-information"></a>取得 SQL Server 連線資訊

取得連線至 Azure SQL Database 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 瀏覽至 [SQL 資料庫] 或 [SQL 受控執行個體] 頁面。

3. 在 [概觀] 頁面上，針對 Azure SQL Database 中的資料庫檢閱 [伺服器名稱] 旁的完整伺服器名稱，若為 Azure SQL 受控執行個體中的受控執行個體，則檢閱 [主機] 旁的完整伺服器名稱。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製] 圖示。

## <a name="create-code-to-query-your-database"></a>建立程式碼來查詢您的資料庫

1. 開啟 **SQL Server Management Studio** 並連線至您的資料庫。

   如果您需要連線方面的協助，請參閱[快速入門：使用 SQL Server Management Studio 來連線及查詢 Azure SQL Database 中的資料庫](connect-query-ssms.md) (英文)。

1. 將完整的 R 指令碼傳遞至 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 預存程序。

   指令碼會透過 `@script` 引數傳遞。 `@script` 引數內的所有一切都必須是有效的 R 程式碼。
   
   >[!IMPORTANT]
   >此範例中的程式碼使用範例 AdventureWorksLT 資料，您可以在建立資料庫時選擇這些範例資料作為來源。 如果您的資料庫中有不同的資料，請在 SELECT 查詢中使用來自您自己資料庫的資料表。 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > 如果發生任何錯誤，有可能是因為您的資料庫未啟用公開預覽版的機器學習服務 (搭配 R)。 請參閱上述[必要條件](#prerequisites)。

## <a name="run-the-code"></a>執行程式碼

1. 執行 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 預存程序。

1. 確認 [訊息] 視窗中已傳回前 20 個類別/產品資料列。

## <a name="next-steps"></a>後續步驟

- [設計您在 Azure SQL Database 的第一個資料庫](design-first-database-tutorial.md)
- [Azure SQL Database 機器學習服務 (搭配 R)](machine-learning-services-overview.md)
- [在 Azure SQL Database 機器學習服務中建立和執行簡單的 R 指令碼 (預覽)](r-script-create-quickstart.md)
