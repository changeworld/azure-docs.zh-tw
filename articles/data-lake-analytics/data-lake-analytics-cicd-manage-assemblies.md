---
title: 管理 CI/CD 管線中的 U-SQL 元件-Azure Data Lake
description: 了解透過 Azure DevOps 管理 CI/CD 管線中 U-SQL C# 組件的最佳做法。
author: liudan66
ms.author: liud
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: 4bb6ee60df291c1939d3bb0d72a9b3992be9b3c0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132138"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>管理 CI/CD 管線中 U-SQL 組件的最佳做法

在本文中，您會了解如何搭配新推出的 U-SQL 資料庫專案來管理 U-SQL 組件原始程式碼。 您也可了解如何使用 Azure DevOps，設定持續整合和部署 (CI/CD) 管線以進行組件註冊。

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>使用 U-SQL 資料庫專案來管理組件原始程式碼

[U-SQL 資料庫專案](data-lake-analytics-data-lake-tools-develop-usql-database.md)是 Visual Studio 中的專案類型，能協助開發人員快速而輕鬆地開發、管理及部署 U-SQL 資料庫。 您可以透過 U-SQL 資料庫專案管理所有的 U-SQL 資料庫專案 (除了認證以外)。 

若要管理 C# 組件原始程式碼和組件註冊 DDL U-SQL 指令碼，請使用：

* U-SQL 資料庫專案來管理組件註冊 U-SQL 指令碼。
* 類別庫 (適用於 U-SQL 應用程式) 來管理適用於使用者定義運算子、函式及彙總工具 (UDO、UDF 和 UDAG) 的 C# 原始程式碼和相依性。
* U-SQL 資料庫專案來參考「類別庫」專案。 

U-SQL 資料庫專案可以參考「類別庫 (適用於 U-SQL 應用程式)」專案。 您可以使用來自此此「類別庫 (適用於 U-SQL 應用程式)」專案的參考 C# 原始程式碼，建立在 U-SQL 資料庫中註冊的組件。

請依照下列步驟來建立專案及新增參考。
1. **選取 [** 檔案] [新增] [  >  **New**  >  **專案**]，以建立類別庫（適用于 U-SQL 應用程式）專案。 該專案位於 [Azure Data Lake] > [U-SQL]**** 節點底下。

   ![Data Lake Tools for Visual Studio--建立 C# 類別庫專案](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. 將您的使用者定義 C# 程式碼新增到「類別庫 (適用於 U-SQL 應用程式)」專案中。

1. **選取 [** 檔案] [新增] [  >  **New**  >  **專案**] 來建立一個 U-SQL 專案。 專案位於 [Azure Data Lake 的**Azure Data Lake**  >  **U-SQL** ] 節點底下。

   ![Data Lake Tools for Visual Studio--建立 U-SQL 資料庫專案](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. 新增適用於該 U-SQL 資料庫專案的 C# 類別庫專案參考。

    ![Data Lake Tools for Visual Studio--新增 U-SQL 資料庫專案參考](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools for Visual Studio--新增 U-SQL 資料庫專案參考](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. 以滑鼠右鍵按一下 U-SQL 資料庫專案並選取 [新增項目]****，在該專案中建立組件指令碼。

   ![Data Lake Tools for Visual Studio--新增組件指令碼](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. 在組件設計檢視中開啟組件指令碼。 在 [從參考建立組件]**** 下拉式功能表中選取參考組件。

    ![Data Lake Tools for Visual Studio--從參考建立組件](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. 新增 [受控相依性]**** 和 [其他檔案]**** \(若有的話)。 在新增其他檔案時，工具會使用相對路徑來確保能在本機電腦和稍後的組建電腦上找到組件。

底部 [編輯器] 視窗中的** \@ _DeployTempDirectory**是預先定義的變數，會將工具指向組建輸出檔案夾。 在建置輸出資料夾中，每個組件都具有以組件名稱命名的子資料夾。 所有 Dll 和其他檔案都在該子資料夾中。

## <a name="build-a-u-sql-database-project"></a>建置 U-SQL 資料庫專案

U-SQL 資料庫專案的建置輸出是一個 U-SQL 資料庫部署套件。 其名稱會加上 `.usqldbpack` 尾碼。 `.usqldbpack` 套件是一個 ZIP 檔案，其中的 DDL 資料夾含有單一 U-SQL 指令碼中的所有 DDL 陳述式。 組件的所有已建置 .dll 檔案和額外檔案都位於 Temp 資料夾中。

## <a name="deploy-a-u-sql-database"></a>部署 U-SQL 資料庫

您可以將 `.usqldbpack` 套件部署到本機帳戶或 Azure Data Lake Analytics 帳戶。 使用 Visual Studio 或部署 SDK。 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>在 Visual Studio 中部署 U-SQL 資料庫

您可以在 Visual Studio 中使用 U-SQL 資料庫專案或 `.usqldbpack` 套件部署 U-SQL 資料庫。

#### <a name="deploy-by-using-a-u-sql-database-project"></a>使用 U-SQL 資料庫專案部署

1.  以滑鼠右鍵按一下 U-SQL 資料庫專案，然後選取 [部署]****。
2.  在 [部署 U-SQL 資料庫]**** 精靈中，選擇要部署資料庫的目的地 [ADLA 帳戶]****。 本機帳戶和 ADLA 帳戶兩者均可支援。
3.  系統會自動填入 [資料庫來源]****。 它會指向專案建置輸出資料夾中的 .usqldbpack 套件。
4.  在 [資料庫名稱]**** 中輸入名稱，以建立資料庫。 如果目標 Azure Data Lake Analytics 帳戶已有相同名稱的資料庫，在資料庫專案中定義的所有物件均會建立，但不會重新建立資料庫。
5.  若要部署 U-SQL 資料庫，請按一下 [提交]****。 所有資源 (例如組件和其他檔案) 都已上傳。 已提交包含所有 DDL 陳述式的 U-SQL 作業。

    ![Data Lake Tools for Visual Studio--部署 U-SQL 資料庫專案](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools for Visual Studio--部署 U-SQL 資料庫專案精靈](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>在 Azure DevOps 中部署 U-SQL 資料庫

`PackageDeploymentTool.exe` 提供程式設計和命令列介面，協助您部署 U-SQL 資料庫。 SDK 包含在 [U-SQL SDK Nuget 套件](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)中，位於 `build/runtime/PackageDeploymentTool.exe`。

在 Azure DevOps 中，您可以使用命令列工作及此 SDK 來設定自動化管線以進行 U-SQL 資料庫重新整理。 [深入了解 SDK，以及如何為 U-SQL 資料庫部署設定 CI/CD 管線](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines)。

## <a name="next-steps"></a>後續步驟

* [設定 Azure Data Lake Analytics 的 CI/CD 管線](data-lake-analytics-cicd-overview.md)
* [測試 Azure Data Lake Analytics 程式碼](data-lake-analytics-cicd-test.md)
* [在本機電腦上執行 U-SQL 指令碼](data-lake-analytics-data-lake-tools-local-run.md)
