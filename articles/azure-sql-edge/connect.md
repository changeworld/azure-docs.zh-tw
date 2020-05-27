---
title: 連線及查詢 Azure SQL Edge (預覽)
description: 了解如何連線及查詢 Azure SQL Edge (預覽)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 17bb5afebe0335bcdcf40298490f94999c04a621
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593947"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>連線及查詢 Azure SQL Edge (預覽)

部署 Azure SQL Edge 容器之後，您就可以從下列任何位置連線到 SQL 資料庫引擎。

- 容器內
- 從在相同主機上執行的另一個 Docker 容器。
- 從主機電腦
- 從網路上任何其他用戶端電腦。

## <a name="tools-to-connect-to-azure-sql-edge"></a>用來連線到 Azure SQL Edge 的工具

您可以使用以下所述的任何常用工具來與 Azure SQL Edge 執行個體建立連線。

* [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) - sqlcmd 用戶端工具已包含在 Azure SQL Edge 容器映像中。 如果您使用互動式 Bash Shell 來附加至正在執行的容器，則您可以在本機執行這些工具。
* [Transact-SQL](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

若要從網路電腦連線到 Azure SQL Edge 資料庫引擎，您需要下列項目

- 主機電腦的 IP 位址或網路名稱 - 這是 Azure SQL Edge 容器執行所在的主機電腦。
- Azure SQL Edge 容器的主機連接埠對應 - 這是 Docker 容器連接埠與主機上連接埠的連接埠對應。 在容器中，SQL Edge 一律會對應到連接埠 1433。 這可在 Azure SQL Edge 部署中變更。 若要變更連接埠號碼，請在 Azure IoT Edge 中更新 SQL Edge 模組的 [容器建立選項]。 在下面提供的範例中，容器上的連接埠 1433 會對應到主機上的連接埠 1600。

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

- SQL Edge 執行個體的 SA 密碼 - 這是在 SQL Edge 部署期間為 **SA_PASSWORD** 環境變數所指定的值。

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>從容器內連線到資料庫引擎

[SQL Server 命令列工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)會包含在 Azure SQL Edge 容器映像中。 如果您使用互動式命令提示字元來附加至容器，則可在本機執行這些工具。

1. 使用 `docker exec -it` 命令在您執行的容器中啟動互動式 Bash 殼層。 在下列範例中，`e69e056c702d` 是容器識別碼。

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > 您不一定要指定整個容器識別碼。您只需指定足夠的字元來唯一識別它。 因此，在此範例中，使用 `e6` 或 `e69` 可能就已足夠，而不需使用完整識別碼。

2. 進入容器後，以 sqlcmd 進行本機連線。 預設路徑並不包含 sqlcmd，因此您必須指定完整路徑。

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. 完成使用 sqlcmd 時，輸入 `exit`。

4. 完成使用互動式命令提示字元時，輸入 `exit`。 結束互動式 Bash 殼層後，容器會繼續執行。

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>從相同主機上的另一個容器連線到 SQL Edge

由於在相同主機上執行的兩個容器都位於相同的 Docker 網路上，因此您可以使用服務的容器名稱和連接埠位址，輕鬆地存取這兩個容器。 例如，如果您要從相同主機上的另一個 python 模組 (容器) 連線到 SQL Edge 執行個體，您可以使用類似下列的連接字串。 下列範例假設 SQL Edge 已設定為接聽預設的連接埠。

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>從另一部網路電腦連線到 SQL Edge

若要從網路上的另一部電腦連線到 SQL Edge 執行個體，您必須使用 Docker 主機的 IP 位址，以及 SQL Edge 容器對應的主機連接埠。 例如，如果 Docker 主機的 IP 位址是 *xxx.xxx.xxx.xxx"，而 SQL Edge 容器對應到主機連接埠 1600，則 SQL Edge 執行個體的伺服器位址會是 **xxx.xxx.xxx.xxx,1600**。 更新的 python 指令碼會是

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

若要使用在 Windows 電腦上執行的 SQL Server Management Studio 來連線到 SQL Edge 執行個體，請參閱 [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms)。

若要使用 Windows、Mac 或 Linux 電腦上的 Visual Studio Code 連線到 SQL Edge 執行個體，請參閱 [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)。

若要使用 Windows、Mac 或 Linux 電腦上的 Azure Data Studio 連線到 SQL Edge 執行個體，請參閱 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server)。

## <a name="see-also"></a>另請參閱

[連線和查詢](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[在 Linux 上安裝 SQL Server 工具](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
