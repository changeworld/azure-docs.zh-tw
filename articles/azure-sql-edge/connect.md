---
title: 連線及查詢 Azure SQL Edge (預覽)
description: 瞭解如何連接及查詢 Azure SQL Edge （預覽）。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: bc7410325bbcf3086c4ac2054b7bc663629a29e5
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373108"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>連線及查詢 Azure SQL Edge (預覽)

在 Azure SQL Edge 中，部署容器之後，您可以從下列任何位置連接到資料庫引擎：

- 容器內
- 從在相同主機上執行的另一個 Docker 容器
- 從主機電腦
- 從網路上的任何其他用戶端電腦

## <a name="tools-to-connect-to-azure-sql-edge"></a>用來連線到 Azure SQL Edge 的工具

您可以從下列任何一個常用工具連接到 Azure SQL Edge 實例的實例：

* [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)： sqlcmd 用戶端工具已包含在 Azure SQL Edge 的容器映射中。 如果您使用互動式 Bash Shell 來附加至正在執行的容器，則您可以在本機執行這些工具。 SQL 用戶端工具無法在 ARM64 平臺上使用，因為它們不包含在 SQL Edge 容器的 ARM64 版本中。 
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

若要從網路電腦連接到 Azure SQL Edge database engine，您需要下列各項：

- **主機電腦的 IP 位址或網路名稱**：這是 Azure SQL Edge 容器執行所在的主機電腦。
- **AZURE SQL Edge 容器主機埠對應**：這是 Docker 容器埠與主機埠的對應。 在容器內，Azure SQL Edge 一律會對應到埠1433。 您可以視需要加以變更。 若要變更埠號碼，請在 Azure IoT Edge 中更新 Azure SQL Edge 模組的**容器建立選項**。 在下列範例中，容器上的埠1433會對應到主機上的埠1600。

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **AZURE Sql edge 實例的 SA 密碼**：這是 `SA_PASSWORD` 在 azure sql edge 部署期間為環境變數指定的值。

## <a name="connect-to-the-database-engine-from-within-the-container"></a>從容器內連接到資料庫引擎

[SQL Server 命令列工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)會包含在 Azure SQL Edge 的容器映射中。 如果您使用互動式命令提示字元附加至容器，您可以在本機執行這些工具。 SQL 用戶端工具無法在 ARM64 平臺上使用，因為它們不包含在 SQL Edge 容器的 ARM64 版本中。 

1. 使用 `docker exec -it` 命令在您執行的容器中啟動互動式 Bash 殼層。 在下列範例中， `e69e056c702d` 是容器識別碼。

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > 您不一定要指定整個容器識別碼。 您只需指定足夠的字元來唯一識別它。 因此，在此範例中，可能足以使用 `e6` 或 `e69` ，而不是完整識別碼。

2. 當您在容器內時，請使用 sqlcmd 來連接到本機。 根據預設，Sqlcmd 不在路徑中，因此您必須指定完整路徑。

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. 當您完成 sqlcmd 時，請輸入 `exit` 。

4. 當您完成互動式命令提示字元時，請輸入 `exit` 。 結束互動式 Bash 殼層後，容器會繼續執行。

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>從相同主機上的另一個容器連接到 Azure SQL Edge

因為兩個在相同主機上執行的容器位於相同的 Docker 網路上，所以您可以使用服務的容器名稱和埠位址輕鬆地存取它們。 例如，如果您要從相同主機上的另一個 python 模組（容器）連接到 Azure SQL Edge 的實例，您可以使用類似下列的連接字串。 （此範例假設 Azure SQL Edge 已設定為接聽預設通訊埠）。

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>從另一部網路電腦連接到 Azure SQL Edge

您可能想要從網路上的另一部電腦連接到 Azure SQL Edge 的實例。 若要這麼做，請使用 Docker 主機的 IP 位址，以及 Azure SQL Edge 容器對應的主機埠。 例如，如果 Docker 主機的 IP 位址是*xxx.xxx.xxx.xxx*，而 Azure sql edge 容器對應到主機埠*1600*，則 azure sql edge 實例的伺服器位址會是*xxx*. xxx. xxx。 更新的 python 腳本是：

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

若要使用在 Windows 電腦上執行的 SQL Server Management Studio 來連接到 Azure SQL Edge 實例，請參閱[SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms)。

若要使用 Windows、Mac 或 Linux 電腦上的 Visual Studio Code 來連接到 Azure SQL Edge 實例，請參閱[Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)。

若要使用 Windows、Mac 或 Linux 電腦上的 Azure Data Studio 來連接到 Azure SQL Edge 實例，請參閱[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server)。

## <a name="next-steps"></a>後續步驟

[連接和查詢](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[在 Linux 上安裝 SQL Server 工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
