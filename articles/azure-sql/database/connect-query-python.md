---
title: 使用 Python 查詢資料庫
description: 本主題說明如何使用 Python 建立程式，以連線到 Azure SQL Database 資料庫，並使用 Transact-SQL 陳述式進行查詢。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: b3cbda70fd836482448aa381f6e781ed15666f2c
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675173"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>快速入門：使用 Python 查詢 Azure SQL Database 的資料庫或 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

在本快速入門中，您將使用 Python 連線至 Azure SQL Database 或 Azure SQL 受控執行個體，並會使用 T-SQL 陳述式查詢資料。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

  | 動作 | SQL Database | SQL 受控執行個體 | Azure VM 上的 SQL Server |
  |:--- |:--- |:---|:---|
  | 建立| [入口網站](single-database-create-quickstart.md) | [入口網站](../managed-instance/instance-create-quickstart.md) | [入口網站](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 設定 | [伺服器層級 IP 防火牆規則](firewall-create-server-level-portal-quickstart.md)| [VM 的連線能力](../managed-instance/connect-vm-instance-configure.md)|
  |||[來自內部部署的連線](../managed-instance/point-to-site-p2s-configure.md) | [連線到 SQL Server 執行個體](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |載入資料|每個快速入門載入的 Adventure Works|[還原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [還原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||從 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 的 [BACPAC](database-import.md) 檔案，還原或匯入 Adventure Works| 從 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 的 [BACPAC](database-import.md) 檔案，還原或匯入 Adventure Works|
  |||

- [Python](https://python.org/downloads) 3 和相關軟體

  # <a name="macos"></a>[macOS](#tab/macos)

  若要安裝 Homebrew 和 Python、ODBC 驅動程式和 SQLCMD，以及適用於 SQL Server 的 Python 驅動程式，請使用 [在 macOS 上使用 SQL Server 上建立 Python 應用程式](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)中的 **1.2** 、 **1.3** 和 **2.1** 步驟。

  如需詳細資訊，請參閱 [macOS 上的 Microsoft ODBC 驅動程式](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  若要安裝 Python 和其他必要套件，請使用 `sudo apt-get install python python-pip gcc g++ build-essential`。

  若要安裝 ODBC 驅動程式、SQLCMD 和適用於 SQL Server 的 Python 驅動程式，請參閱[設定適用於 pyodbc Python 開發的環境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)。

  如需詳細資訊，請參閱 [Linux 上的 Microsoft ODBC 驅動程式](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。

  # <a name="windows"></a>[Windows](#tab/windows)

  若要安裝 Python、ODBC 驅動程式和 SQLCMD，以及適用於 SQL Server 的 Python 驅動程式，請參閱[設定適用於 pyodbc Python 開發的環境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)。

  如需詳細資訊，請參閱 [Microsoft ODBC 驅動程式](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)。

---

> [!IMPORTANT]
> 本文中已撰寫的指令碼會使用 **Adventure Works** 資料庫。

> [!NOTE]
> 您也可以視情況選擇使用 Azure SQL 受控執行個體。
>
> 若要建立和設定，請使用 [Azure 入口網站](../managed-instance/instance-create-quickstart.md)、[PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然後設定[內部部署](../managed-instance/point-to-site-p2s-configure.md)或 [VM](../managed-instance/connect-vm-instance-configure.md) 連線能力。
>
> 若要載入資料，請參閱[使用 BACPAC 檔案還原](database-import.md) (搭配 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works))，或參閱[還原 Wide World Importers 資料庫](../managed-instance/restore-sample-database-quickstart.md)。

若要進一步探索 Python 和 Azure SQL Database 中的資料庫，請參閱[適用於 Python 的 Azure SQL Database 程式庫](/python/api/overview/azure/sql)、[pyodbc 存放庫](https://github.com/mkleehammer/pyodbc/wiki/)，以及 [pyodbc 範例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)。

## <a name="get-server-connection-information"></a>取得伺服器連線資訊

取得連線到 Azure SQL Database 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 前往 [SQL 資料庫] 或 [SQL 受控執行個體] 頁面。

3. 在 [概觀] 頁面上，針對 Azure SQL Database 中的資料庫，檢閱 [伺服器名稱] 旁的完整伺服器名稱，若為 Azure SQL 受控執行個體或 Azure VM 上的 SQL Server，則檢閱 [主機] 旁的完整伺服器名稱 (或 IP 位址)。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製] 圖示。

> [!NOTE]
> 如需 Azure VM 上的 SQL Server 連線資訊，請參閱[連線到 SQL Server 執行個體](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server) (英文)。

## <a name="create-code-to-query-your-database"></a>建立查詢您資料庫的程式碼 

1. 在文字編輯器中，建立名為 *sqltest.py* 的新檔案。  
   
1. 加入下列程式碼。 請將 \<server>、\<database>、\<username> 及 \<password> 替換為您自己的值。
   
   >[!IMPORTANT]
   >此範例中的程式碼使用範例 AdventureWorksLT 資料，您可以在建立資料庫時選擇這些範例資料作為來源。 如果您的資料庫中有不同的資料，請在 SELECT 查詢中使用來自您自己資料庫的資料表。 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行下列命令：

   ```cmd
   python sqltest.py
   ```

1. 確認傳回了前 20 個類別/產品資料列，然後關閉命令視窗。

## <a name="next-steps"></a>後續步驟

- [設計您在 Azure SQL Database 的第一個資料庫](design-first-database-tutorial.md)
- [Microsoft Python Drivers for SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Python 開發人員中心](https://azure.microsoft.com/develop/python/?v=17.23h)