---
title: 使用 Docker 部署 Azure SQL Edge - Azure SQL Edge
description: 了解如何使用 Docker 部署 Azure SQL Edge
keywords: SQL Edge, 容器, docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: ff14f8a9f236701889aea95911f2a1e381eabf83
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943647"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>使用 Docker 部署 Azure SQL Edge

在本快速入門中，您將使用 Docker 提取與執行 Azure SQL Edge 容器映像。 然後與 **sqlcmd**連線來建立您的第一個資料庫並執行查詢。

此映像由 Ubuntu 18.04 為基礎的 Azure SQL Edge 組成。 您可於適用於 Mac/Windows 的 Docker 上將其與 Docker 引擎 1.8 以上版本搭配使用。

## <a name="prerequisites"></a>Prerequisites

- 在任何支援的 Linux 發行版本或適用於 Mac/Windows 上的 Docker 安裝 Docker 引擎 1.8 以上版本。 如需詳細資訊，請參閱[安裝 Docker](https://docs.docker.com/engine/installation/)。 由於 Azure SQL Edge 映像是以 Ubuntu 18.04 為基礎，因此建議使用 Ubuntu 18.04 Docker 主機。
- Docker **overlay2** 儲存體驅動程式。 這是大部分使用者的預設值。 如果您發現您未使用此儲存提供者且需要進行變更，請參閱[用於設定 overlay2 的 Docker 文件](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver)中指示和警告。
- 至少 10 GB 的磁碟空間。
- 至少 1 GB 的 RAM。
- [Azure SQL Edge 的硬體需求](https://docs.microsoft.com/azure/azure-sql-edge/features#hardware-support)。


## <a name="pull-and-run-the-container-image"></a>提取及執行容器映像

開始下列步驟之前，請確定您已在本文頂端選取您慣用的 Shell (Bash、PowerShell 或 cmd)。

1. 從 Microsoft Container Registry 提取 Azure SQL Edge 容器映像。

    - 提取 Azure SQL Edge 容器映像
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> 本文中的 Bash 命令會使用 `sudo`。 在 macOS 與 windows 上，可能不需要 sudo。 在 Linux 上，如果您不想使用 sudo 執行 Docker，可以設定 Docker 群組，並將使用者新增至該群組。 如需詳細資訊，請參閱 [Post-installation steps for Linux](https://docs.docker.com/engine/install/linux-postinstall/) (適用於 Linux 的安裝後步驟)。

先前的命令會提取最新的 Azure SQL Edge 容器映像。 若要查看所有可用的映像，請參閱 [azure-sql-egde Docker Hub 頁面](https://hub.docker.com/_/microsoft-azure-sql-edge)。

2. 若要以 Docker 執行容器映像，您可以從 Bash 殼層 (Linux/macOS) 或提高權限的 PowerShell 命令提示字元使用下列命令。
    
    - 開啟作為開發人員版本執行的 Azure SQL Edge 執行個體
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - 開啟作為進階版本執行的 Azure SQL Edge 執行個體
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > 若正在 Windows 上使用 PowerShell 執行這些命令，請使用雙引號，不要使用單引號。


    > [!NOTE]
    > 密碼應遵循 Microsoft SQL Database 引擎預設密碼原則，否則容器將無法設定 SQL 引擎並停止運作。 根據預設，密碼的長度至少必須是 8 個字元，包含下列四種集合的其中三種字元：大寫字母、小寫字母、以 10 為底數的數字，以及符號。 執行 [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) 命令即可查看錯誤記錄。
    
    下表提供了前述 `docker run` 範例的參數描述：

    | 參數 | 描述 |
    |-----|-----|
    | **-e "ACCEPT_EULA=Y"** |  將 **ACCEPT_EULA** 變數設為任意值可確認您接受[終端使用者授權合約](https://go.microsoft.com/fwlink/?linkid=2139274)。 Azure SQL Edge 映像的必要設定。 |
    | **-e "MSSQL_SA_PASSWORD=yourStrong(!)Password"** | 指定您自己的強式密碼，該密碼長度至少需為 8 個字元且符合 [Azure SQL Edge 密碼需求](https://docs.microsoft.com/sql/relational-databases/security/password-policy)。 Azure SQL Edge 映像的必要設定。 |
    | **-p 1433:1433** | 將主機環境上的 TCP 連接埠 (第一個值) 對應至容器中的 TCP 連接埠 (第二個值)。 在本範例中，Azure SQL Edge 正在接聽容器中的 TCP 1433 且對主機上的連接埠 1433 公開。 |
    | **--name azuresqledge** | 為容器指定自訂名稱，而不使用隨機產生的名稱。 若您執行數個容器，就無法使用此相同名稱。 |
    | **-d** | 在背景執行容器 (精靈) |

    如需所有 Azure SQL Edge 環境變數的完整清單，請參閱[使用環境變數設定 Azure SQL Edge](configure.md#configure-by-using-environment-variables)。您也可以使用 [mssql.conf 檔案](configure.md#configure-by-using-an-mssqlconf-file)來設定 Azure SQL Edge 容器。

3. 若要檢視 Docker 容器，請使用 `docker ps` 命令。
   
   ```bash
    sudo docker ps -a
   ```

4. 若 **STATUS** 欄位顯示的狀態含 **Up**，表示 Azure SQL Edge 正在容器中執行且接聽於 **PORTS** 欄位中指定的連接埠。 若 Azure SQL Edge 容器的 **STATUS** 欄位顯示**已結束**，請參閱 Azure SQL Edge 文件的疑難排解。

    `-h` (主機名稱) 參數也相當實用，但為求簡明因此未在本教學課程中使用。 此參數可將容器的內部名稱變更為自訂值。 這是您在下列 Transact-SQL 查詢中會看到的傳回名稱：

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    建議您將 `-h` 與 `--name` 設為相同的值，這會讓識別目標容器更輕鬆。

5. 因為 `SA_PASSWORD` 會顯示在 `ps -eax` 輸出，且儲存在相同名稱的環境變數中，所以最後一個步驟是變更您的 SA 密碼。 請參閱下方步驟。

## <a name="change-the-sa-password"></a>變更 SA 密碼

**SA** 帳戶是在安裝期間建立的 Azure SQL Edge 執行個體系統管理員。 建立您的 Azure SQL Edge 容器之後，在容器中執行 `echo $SA_PASSWORD`，即可探索您指定的 `MSSQL_SA_PASSWORD` 環境變數。 基於安全性考量，請變更您的 SA 密碼。

1. 選擇要為 SA 使用者使用的強式密碼。

2. 使用 `docker exec` 來執行 **sqlcmd**，以使用 Transact-SQL 變更密碼。 在下列範例中，將舊密碼 `<YourStrong!Passw0rd>` 和新密碼 `<YourNewStrong!Passw0rd>` 取代為您自己的密碼值。

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>連線到 Azure SQL Edge

下列步驟會在容器中使用 Azure SQL Edge 命令列工具 **sqlcmd** 以連線至 Azure SQL Edge。

> [!NOTE]
> sqlcmd 工具無法在 SQL Edge 容器的 ARM64 版本中使用。

1. 使用 `docker exec -it` 命令在您執行的容器中啟動互動式 Bash 殼層。 下列範例中的 `azuresqledge` 是您在建立容器時由 `--name` 參數指定的名稱。

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. 進入容器後，以 sqlcmd 進行本機連線。 預設路徑並不包含 sqlcmd，因此您必須指定完整路徑。

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > 您可以在命令列中省略密碼，不要在提示時輸入密碼。

3. 如果成功，您應該會收到 **sqlcmd** 命令提示字元：`1>`。

## <a name="create-and-query-data"></a>建立及查詢資料

下列各節將逐步引導您使用 **sqlcmd** 和 Transact-SQL，來建立新的資料庫、新增資料及執行簡單的查詢。

### <a name="create-a-new-database"></a>建立新資料庫

下列步驟會建立名為 `TestDB` 的新資料庫。

1. 從 **sqlcmd** 命令提示字元，貼上下列 Transact-SQL 命令以建立測試資料庫：

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. 在下一行，撰寫查詢以傳回您伺服器上所有資料庫的名稱：

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>插入資料

接下來，建立新的資料表 `Inventory`，然後插入兩個新的資料列。

1. 從 **sqlcmd** 命令提示字元，將內容切換至 `TestDB` 資料庫：

   ```sql
   USE TestDB
   ```

2. 建立名為 `Inventory` 的新資料表：

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. 將資料插入新的資料表：

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. 鍵入 `GO` 以執行上述命令：

   ```sql
   GO
   ```

### <a name="select-data"></a>選取資料

現在，執行查詢以從 `Inventory` 資料表傳回資料。

1. 從 **sqlcmd** 命令提示字元，輸入查詢以從 `Inventory` 資料表傳回 quantity (數量) 大於 152 的資料列：

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. 執行命令︰

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>結束 sqlcmd 命令提示字元

1. 若要結束您的 **sqlcmd** 工作階段，請鍵入 `QUIT`：

   ```sql
   QUIT
   ```

2. 若要結束容器中的互動式命令提示字元，請鍵入 `exit`。 結束互動式 Bash 殼層後，容器會繼續執行。

## <a name="connect-from-outside-the-container"></a> 從容器外部連線

您也可以從支援 SQL 連線的任何外部 Linux、Windows 或 macOS 工具連線到 Docker 機器上的 Azure SQL Edge 執行個體。 如需從外部連線到 SQL Edge 容器的詳細資訊，請參閱[連線及查詢 Azure SQL Edge](connect.md)。

## <a name="remove-your-container"></a>移除容器

若要移除本教學課程中用到的 Azure SQL Edge 容器，請執行下列命令：

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> 停止及移除容器會永久刪除容器中的所有 Azure SQL Edge 資料。 如須保留資料，[請建立備份檔案並將其複製到容器外](backup-restore.md)，或使用[容器資料持續性技術](configure.md#persist-your-data)。

## <a name="next-steps"></a>後續步驟

- [SQL Edge 中採用 ONNX 的機器學習與人工智慧](onnx-overview.md)。
- [使用 SQL Edge 和 IoT Edge 建置端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge 中的資料串流](stream-data.md)
