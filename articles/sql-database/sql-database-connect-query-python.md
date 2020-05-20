---
title: 使用 Python 查詢資料庫
description: 本主題說明如何使用 Python 來建立連線到 Azure SQL 資料庫的程式，並使用 Transact-SQL 陳述式查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768563"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>快速入門：使用 Python 查詢 Azure SQL 資料庫

在本快速入門中，您會使用 Python 來連線至 Azure SQL 資料庫，並使用 T-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure SQL 資料庫](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 和相關軟體

  # <a name="macos"></a>[macOS](#tab/macos)

  若要安裝 Homebrew 和 Python、ODBC 驅動程式和 SQLCMD，以及適用於 SQL Server 的 Python 驅動程式，請使用[在 macOS 上使用 SQL Server 上建立 Python 應用程式](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)中的 **1.2**、**1.3** 和 **2.1** 步驟。

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
> 若要建立和設定，請使用 [Azure 入口網站](sql-database-managed-instance-get-started.md)、[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然後設定[現場](sql-database-managed-instance-configure-p2s.md)或 [VM](sql-database-managed-instance-configure-vm.md) 連線能力。
>
> 若要載入資料，請參閱[使用 BACPAC 檔案還原](sql-database-import.md) (搭配 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works))，或參閱[還原 Wide World Importers 資料庫](sql-database-managed-instance-get-started-restore.md)。

若要進一步探索 Python 和 Azure SQL 資料庫，請參閱[適用於 Python 的 Azure SQL 資料庫程式庫](/python/api/overview/azure/sql)、[pyodbc 存放庫](https://github.com/mkleehammer/pyodbc/wiki/)，以及 [pyodbc 範例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

取得連線到 Azure SQL 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 移至 [SQL 資料庫]  或 [SQL 受控執行個體]  頁面。

3. 在 [概觀]  頁面上，針對單一資料庫檢閱 [伺服器名稱]  旁的完整伺服器名稱，若為受控執行個體，則檢閱 [主機]  旁的完整伺服器名稱。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製]  圖示。

## <a name="create-code-to-query-your-sql-database"></a>建立程式碼以查詢您的 SQL Database 

1. 在文字編輯器中，建立名為 *sqltest.py* 的新檔案。  
   
1. 加入下列程式碼。 用您自己的值取代 \<server>、\<database>、\<username>，以及 \<password>。
   
   >[!IMPORTANT]
   >此範例中的程式碼使用範例 AdventureWorksLT 資料，您可以在建立資料庫時選擇這些範例資料作為來源。 如果您的資料庫中有不同的資料，請在 SELECT 查詢中使用來自您自己資料庫的資料表。 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
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

1. 請確認傳回前 20 個的類別/產品資料列，然後關閉命令視窗。

## <a name="next-steps"></a>後續步驟

- [設計您的第一個 Azure SQL 資料庫](sql-database-design-first-database.md)
- [Microsoft Python Drivers for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Python 開發人員中心](https://azure.microsoft.com/develop/python/?v=17.23h)

