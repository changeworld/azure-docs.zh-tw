---
title: 使用 Azure 資料塊查詢 SQL Server Linux Docker 容器
description: 本文介紹如何將 Azure 資料塊部署到虛擬網路（也稱為 VNet 注入）。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747656"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>教程：從 Azure 資料磚塊筆記本查詢虛擬網路中的 SQL Server Linux Docker 容器

本教程將介紹如何在虛擬網路中將 Azure 資料塊與 SQL Server Linux Docker 容器集成。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 將 Azure 資料磚塊工作區部署到虛擬網路
> * 在公共網路中安裝 Linux 虛擬機器
> * 安裝 Docker
> * 在 Linux Docker 容器上安裝微軟 SQL 伺服器
> * 使用資料塊筆記本中的 JDBC 查詢 SQL 伺服器

## <a name="prerequisites"></a>Prerequisites

* [在虛擬網路中創建資料磚塊工作區](quickstart-create-databricks-workspace-vnet-injection.md)。

* 為 Windows 安裝[Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)。

* 下載[SQL 伺服器管理工作室](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)。

## <a name="create-a-linux-virtual-machine"></a>建立 Linux 虛擬機器

1. 在 Azure 門戶中，選擇**虛擬機器**的圖示。 然後選取 [+ 新增]****。

    ![添加新 Azure 虛擬機器](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. 在 **"基本"** 選項卡上，選擇 Ubuntu 伺服器 18.04 LTS 並將 VM 大小更改為 B2。 選擇管理員使用者名和密碼。

    ![新虛擬機器配置的基礎知識選項卡](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. 導航到 **"網路**"選項卡。選擇虛擬網路和包含 Azure 資料磚塊群集的公共子網。 選擇 **"審閱 + 創建**"，然後**創建**以部署虛擬機器。

    ![新虛擬機器配置的網路選項卡](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. 部署完成後，導航到虛擬機器。 請注意**概述**中的公共 IP 位址和虛擬網路/子網。 選擇**公共 IP 位址**

    ![虛擬機器概觀](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. 將 **"分配"** 更改為 **"靜態**"並輸入**DNS 名稱標籤**。 選擇 **"保存**"並重新啟動虛擬機器。

    ![公共 IP 位址配置](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. 選擇 **"設置**"下的 **"網路**"選項卡。 請注意，在 Azure 資料磚塊部署期間創建的網路安全性群組與虛擬機器相關聯。 選擇 **"添加入站連接埠規則**"。

7. 添加規則以打開 SSH 的埠 22。 套用下列設定：
    
    |設定|建議的值|描述|
    |-------|---------------|-----------|
    |來源|IP 位址|IP 位址指定此規則將允許或拒絕來自特定源 IP 位址的傳入流量。|
    |來源 IP 位址|<您的公共 ip\>|輸入您的公共 IP 位址。 您可以通過訪問[bing.com](https://www.bing.com/)並搜索 **"我的 IP"** 來查找您的公共 IP 位址。|
    |來源連接埠範圍|*|允許來自任何埠的流量。|
    |Destination|IP 位址|IP 位址指定此規則將允許或拒絕特定源 IP 位址的傳出流量。|
    |目的地 IP 位址|<vm 公共 ip\>|輸入虛擬機器的公共 IP 位址。 您可以在虛擬機器的 **"概述"** 頁上找到此項。|
    |目的地連接埠範圍|22|為 SSH 打開端口 22。|
    |優先順序|290|使規則具有優先順序。|
    |名稱|ssh-databricks-教程-vm|為規則指定名稱。|


    ![為埠 22 添加入站安全規則](./media/vnet-injection-sql-server/open-port.png)

8. 添加規則以打開端口 1433 用於 SQL，具有以下設置：

    |設定|建議的值|描述|
    |-------|---------------|-----------|
    |來源|任意|源指定此規則將允許或拒絕來自特定源 IP 位址的傳入流量。|
    |來源連接埠範圍|*|允許來自任何埠的流量。|
    |Destination|IP 位址|IP 位址指定此規則將允許或拒絕特定源 IP 位址的傳出流量。|
    |目的地 IP 位址|<vm 公共 ip\>|輸入虛擬機器的公共 IP 位址。 您可以在虛擬機器的 **"概述"** 頁上找到此項。|
    |目的地連接埠範圍|1433|為 SQL 伺服器打開端口 22。|
    |優先順序|300|使規則具有優先順序。|
    |名稱|sql-databricks-教程-vm|為規則指定名稱。|

    ![為埠 1433 添加入站安全規則](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>在 Docker 容器中運行 SQL 伺服器

1. [打開 Ubuntu 的 Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)， 或任何其他工具，將允許您 SSH 進入虛擬機器. 在 Azure 門戶中導航到虛擬機器，然後選擇 **"連接"** 以獲取需要連接的 SSH 命令。

    ![連線至虛擬機器](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. 在 Ubuntu 終端中輸入該命令，並輸入配置虛擬機器時創建的管理員密碼。

    ![Ubuntu 終端 SSH 登錄](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. 使用以下命令在虛擬機器上安裝 Docker。

    ```bash
    sudo apt-get install docker.io
    ```

    使用以下命令驗證 Docker 的安裝：

    ```bash
    sudo docker --version
    ```

4. 安裝映射。

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    檢查圖像。

    ```bash
    sudo docker images
    ```

5. 從映射運行容器。

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    驗證容器是否正在運行。

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>建立 SQL 資料庫

1. 打開 SQL 伺服器管理工作室並使用伺服器名稱和 SQL 身份驗證連接到伺服器。 登錄使用者名為**SA，** 密碼是 Docker 命令中設置的密碼。 示例命令中的密碼為`Password1234`。

    ![使用 SQL 伺服器管理工作室連接到 SQL 伺服器](./media/vnet-injection-sql-server/ssms-login.png)

2. 成功連接後，選擇 **"新建查詢"** 並輸入以下程式碼片段以創建資料庫、表，並在表中插入一些記錄。

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![查詢以創建 SQL Server 資料庫](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>從 Azure 資料塊查詢 SQL 伺服器

1. 導航到 Azure 資料磚塊工作區，並驗證是否創建了群集作為先決條件的一部分。 然後，選擇 **"創建筆記本**"。 為筆記本命名，選擇*Python*作為語言，然後選擇您創建的群集。

    ![新的資料磚筆記本設置](./media/vnet-injection-sql-server/create-notebook.png)

2. 使用以下命令 ping SQL Server 虛擬機器的內部 IP 位址。 此 ping 應該成功。 如果沒有，請驗證容器是否正在運行，並查看網路安全性群組 （NSG） 配置。

    ```python
    %sh
    ping 10.179.64.4
    ```

    您還可以使用 nslookup 命令來查看。

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. 成功 ping SQL 伺服器後，可以查詢資料庫和表。 運行以下 python 代碼：

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、Azure Databricks 工作區和所有相關資源。 刪除作業可避免不必要的計費。 如果您計劃在未來使用 Azure Databricks 工作區，您可以停止叢集且稍後重新啟動。 如果您不再繼續使用此 Azure Databricks 工作區，請使用下列步驟，刪除本教學課程所建立的所有資源：

1. 從 Azure 入口網站的左側功能表，按一下 [資源群組]****，然後按一下您所建立的資源群組名稱。

2. 在資源群組頁面上，選取 [刪除]****，在文字方塊中輸入要刪除的資源名稱，然後再次選取 [刪除]****。

## <a name="next-steps"></a>後續步驟

進入下一篇文章，瞭解如何使用 Azure 資料塊擷取、轉換和下載資料。
> [!div class="nextstepaction"]
> [教程：使用 Azure 資料塊擷取、轉換和下載資料](databricks-extract-load-sql-data-warehouse.md)
