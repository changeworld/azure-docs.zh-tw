---
title: 使用 Azure Databricks 查詢 SQL Server Linux Docker 容器
description: 本文說明如何將 Azure Databricks 部署至您的虛擬網路，也稱為 VNet 插入。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73747656"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>教學課程：從 Azure Databricks 筆記本查詢虛擬網路中的 SQL Server Linux Docker 容器

本教學課程會教您如何將 Azure Databricks 與虛擬網路中 SQL Server Linux Docker 容器整合。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 將 Azure Databricks 工作區部署到虛擬網路
> * 在公用網路中安裝 Linux 虛擬機器
> * 安裝 Docker
> * 安裝 Linux 上的 Microsoft SQL Server docker 容器
> * 使用 Databricks 筆記本中的 JDBC 查詢 SQL Server

## <a name="prerequisites"></a>Prerequisites

* [在虛擬網路中建立 Databricks 工作區](quickstart-create-databricks-workspace-vnet-injection.md)。

* 安裝[適用于 Windows 的 Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)。

* 下載[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)。

## <a name="create-a-linux-virtual-machine"></a>建立 Linux 虛擬機器

1. 在 [Azure 入口網站中，選取**虛擬機器**的圖示。 然後選取 [+ 新增]****。

    ![新增 Azure 虛擬機器](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. 在 [**基本**] 索引標籤上，選擇 [Ubuntu SERVER 18.04 LTS]，並將 VM 大小變更為 [B2s]。 選擇系統管理員使用者名稱和密碼。

    ![新虛擬機器設定的 [基本] 索引標籤](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. 流覽至 [**網路**] 索引標籤。選擇包含您 Azure Databricks 叢集的虛擬網路和公用子網。 選取 [審核] [ **+ 建立**]，然後選取 [**建立**] 以部署虛擬機器。

    ![新虛擬機器設定的 [網路功能] 索引標籤](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. 當部署完成時，流覽至虛擬機器。 請注意 [公用 IP 位址] 和 [虛擬網路/子網] 的**總覽**。 選取**公用 IP 位址**

    ![虛擬機器概觀](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. 將 [**指派**] 變更為 [**靜態**]，然後輸入**DNS 名稱標籤**。 選取 [**儲存**]，然後重新開機虛擬機器。

    ![公用 IP 位址設定](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. 選取 [**設定**] 底下的 [**網路**] 索引標籤。 請注意，在 Azure Databricks 部署期間所建立的網路安全性群組會與虛擬機器相關聯。 選取 [**新增輸入連接埠規則**]。

7. 新增規則以開啟 SSH 的埠22。 套用下列設定：
    
    |設定|建議的值|描述|
    |-------|---------------|-----------|
    |來源|IP 位址|IP 位址指定此規則將允許或拒絕來自特定來源 IP 位址的連入流量。|
    |來源 IP 位址|<您的公用 ip\>|輸入您的公用 IP 位址。 您可以造訪[bing.com](https://www.bing.com/)並搜尋「**我的 IP**」來尋找您的公用 IP 位址。|
    |來源連接埠範圍|*|允許來自任何埠的流量。|
    |Destination|IP 位址|IP 位址指定此規則將允許或拒絕特定來源 IP 位址的連出流量。|
    |目的地 IP 位址|<您的 vm 公用 ip\>|輸入您虛擬機器的公用 IP 位址。 您可以在虛擬機器的 [**總覽**] 頁面上找到此資訊。|
    |目的地連接埠範圍|22|開啟 SSH 的埠22。|
    |優先順序|290|為規則提供優先順序。|
    |名稱|ssh-databricks-教學課程-vm|為規則命名。|


    ![新增埠22的輸入安全性規則](./media/vnet-injection-sql-server/open-port.png)

8. 新增規則，以使用下列設定開啟適用于 SQL 的埠1433：

    |設定|建議的值|描述|
    |-------|---------------|-----------|
    |來源|任意|來源指定此規則將允許或拒絕來自特定來源 IP 位址的連入流量。|
    |來源連接埠範圍|*|允許來自任何埠的流量。|
    |Destination|IP 位址|IP 位址指定此規則將允許或拒絕特定來源 IP 位址的連出流量。|
    |目的地 IP 位址|<您的 vm 公用 ip\>|輸入您虛擬機器的公用 IP 位址。 您可以在虛擬機器的 [**總覽**] 頁面上找到此資訊。|
    |目的地連接埠範圍|1433|針對 SQL Server 開啟埠22。|
    |優先順序|300|為規則提供優先順序。|
    |名稱|databricks-教學課程-vm|為規則命名。|

    ![新增埠1433的輸入安全性規則](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>在 Docker 容器中執行 SQL Server

1. 開啟[適用于 Windows 的 Ubuntu](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)，或可讓您透過 SSH 連線到虛擬機器的任何其他工具。 流覽至 Azure 入口網站中的虛擬機器，然後選取 **[連線]** 以取得您需要連接的 SSH 命令。

    ![連線至虛擬機器](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. 在您的 Ubuntu 終端機中輸入命令，然後輸入您在設定虛擬機器時所建立的系統管理員密碼。

    ![Ubuntu 終端 SSH 登入](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. 使用下列命令在虛擬機器上安裝 Docker。

    ```bash
    sudo apt-get install docker.io
    ```

    使用下列命令確認 Docker 的安裝：

    ```bash
    sudo docker --version
    ```

4. 安裝映射。

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    檢查映射。

    ```bash
    sudo docker images
    ```

5. 從映射執行容器。

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    確認容器正在執行。

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>建立 SQL 資料庫

1. 開啟 SQL Server Management Studio，並使用伺服器名稱和 SQL 驗證連接到伺服器。 登入使用者名稱是**SA** ，而密碼則是在 Docker 命令中設定的密碼。 範例命令中的密碼為`Password1234`。

    ![使用 SQL Server Management Studio 連接到 SQL Server](./media/vnet-injection-sql-server/ssms-login.png)

2. 成功連接之後，請選取 [追加**查詢**]，然後輸入下列程式碼片段來建立資料庫、資料表，並在資料表中插入一些記錄。

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

    ![建立 SQL Server 資料庫的查詢](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>從 Azure Databricks 查詢 SQL Server

1. 流覽至您的 Azure Databricks 工作區，並確認您已建立叢集作為必要條件的一部分。 然後，選取 [**建立筆記本**]。 為筆記本命名，選取 [ *Python* ] 做為語言，然後選取您所建立的叢集。

    ![新的 Databricks 筆記本設定](./media/vnet-injection-sql-server/create-notebook.png)

2. 使用下列命令來 ping SQL Server 虛擬機器的內部 IP 位址。 此 ping 應該會成功。 如果不是，請確認容器正在執行，並檢查網路安全性群組（NSG）設定。

    ```python
    %sh
    ping 10.179.64.4
    ```

    您也可以使用 nslookup 命令來進行檢查。

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. 成功 ping SQL Server 之後，您就可以查詢資料庫和資料表。 執行下列 python 程式碼：

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

前進到下一篇文章，以瞭解如何使用 Azure Databricks 來解壓縮、轉換和載入資料。
> [!div class="nextstepaction"]
> [教學課程：使用 Azure Databrick 擷取、轉換和載入資料](databricks-extract-load-sql-data-warehouse.md)
