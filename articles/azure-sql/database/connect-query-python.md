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
ms.date: 12/19/2020
ms.openlocfilehash: 8fb6d319cacf85630b2c400cd18d14487725f925
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703957"
---
# <a name="quickstart-use-python-to-query-a-database"></a>快速入門：使用 Python 查詢資料庫
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

在本快速入門中，您將使用 Python 連線至 Azure SQL Database、Azure SQL 受控執行個體或 Synapse SQL 資料庫，並使用 T-SQL 陳述式查詢資料。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- 您將在其中執行查詢的資料庫。

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 和相關軟體

  # <a name="macos"></a>[macOS](#tab/macos)

  若要安裝 Homebrew 和 Python、ODBC 驅動程式和 SQLCMD，以及適用於 SQL Server 的 Python 驅動程式，請使用 [在 macOS 上使用 SQL Server 上建立 Python 應用程式](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)中的 **1.2**、**1.3** 和 **2.1** 步驟。

  如需詳細資訊，請參閱 [macOS 上的 Microsoft ODBC 驅動程式](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  若要安裝 Python 和其他必要套件，請使用 `sudo apt-get install python python-pip gcc g++ build-essential`。

  若要安裝 ODBC 驅動程式、SQLCMD 和適用於 SQL Server 的 Python 驅動程式，請參閱[設定適用於 pyodbc Python 開發的環境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)。

  如需詳細資訊，請參閱 [Linux 上的 Microsoft ODBC 驅動程式](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)。

  # <a name="windows"></a>[Windows](#tab/windows)

  若要安裝 Python、ODBC 驅動程式和 SQLCMD，以及適用於 SQL Server 的 Python 驅動程式，請參閱[設定適用於 pyodbc Python 開發的環境](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows)。

  如需詳細資訊，請參閱 [Microsoft ODBC 驅動程式](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)。

---
若要進一步探索 Python 和 Azure SQL Database 中的資料庫，請參閱[適用於 Python 的 Azure SQL Database 程式庫](/python/api/overview/azure/sql)、[pyodbc 存放庫](https://github.com/mkleehammer/pyodbc/wiki/)，以及 [pyodbc 範例](https://github.com/mkleehammer/pyodbc/wiki/Getting-started)。

## <a name="create-code-to-query-your-database"></a>建立查詢您資料庫的程式碼 

1. 在文字編輯器中，建立名為 *sqltest.py* 的新檔案。  
   
1. 加入下列程式碼。 從必要條件小節中取得連線資訊，並以您自己的值取代 \<server>、\<database>、\<username> 和 \<password>。
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
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

1. 確認已傳回資料庫及其定序，然後關閉命令視窗。

## <a name="next-steps"></a>後續步驟

- [設計您在 Azure SQL Database 的第一個資料庫](design-first-database-tutorial.md)
- [Microsoft Python Drivers for SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Python 開發人員中心](https://azure.microsoft.com/develop/python/?v=17.23h)
