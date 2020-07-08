---
title: 使用 Striim 將資料移轉至 Azure Cosmos DB Cassandra API 帳戶
description: 瞭解如何使用 Striim 將 Oracle 資料庫的資料移轉至 Azure Cosmos DB Cassandra API 帳戶。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 7590d40085c3963a95fd251dd1291cf34fbaf4a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262084"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>使用 Striim 將資料移轉至 Azure Cosmos DB Cassandra API 帳戶

Azure marketplace 中的 Striim 映射提供從資料倉儲和資料庫到 Azure 的持續即時資料移動。 移動資料時，您可以執行內嵌反正規化、資料轉換、啟用即時分析，以及資料包告案例。 開始使用 Striim 可讓您輕鬆地將企業資料移至 Azure Cosmos DB Cassandra API。 Azure 提供的 marketplace 供應專案可讓您輕鬆地部署 Striim，並將資料移轉至 Azure Cosmos DB。 

本文說明如何使用 Striim 將**Oracle 資料庫**的資料移轉至**Azure Cosmos DB Cassandra API 帳戶**。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 在內部部署執行的 Oracle 資料庫，其中包含一些資料。

## <a name="deploy-the-striim-marketplace-solution"></a>部署 Striim marketplace 解決方案

1. 登入[Azure 入口網站](https://portal.azure.com/)。

1. 選取 [**建立資源**]，並在 Azure marketplace 中搜尋**Striim** 。 選取第一個選項並**建立**。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="尋找 Striim marketplace 專案":::

1. 接下來，輸入 Striim 實例的設定屬性。 Striim 環境會部署在虛擬機器中。 從 [**基本**] 窗格中，輸入**vm 使用者名稱**、 **vm 密碼**（此密碼用來透過 SSH 連線至 vm）。 選取您想要部署 Striim 的**訂**用帳戶、**資源群組**和**位置詳細資料**。 完成後，選取 **[確定]**。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="設定 Striim 的基本設定":::


1. 在 [ **Striim 叢集設定**] 窗格中，選擇 Striim 部署的類型和虛擬機器大小。

   |設定 | 值 | 說明 |
   | ---| ---| ---|
   |Striim 部署類型 |獨立 | Striim 可以在**獨立****或叢集**部署類型中執行。 獨立模式會在單一虛擬機器上部署 Striim 伺服器，而您可以根據您的資料磁片區來選取 Vm 的大小。 叢集模式會將 Striim 伺服器部署在兩個或多個具有所選大小的 Vm 上。 具有2個以上節點的叢集環境，會提供自動的高可用性和容錯移轉。</br></br> 在本教學課程中，您可以選取 [獨立] 選項。 使用預設的「Standard_F4s」大小 VM。 | 
   | Striim 叢集的名稱|    <Striim_cluster_Name>|  Striim 叢集的名稱。|
   | Striim 叢集密碼|   <Striim_cluster_password>|  叢集的密碼。|

   填寫表單之後，選取 **[確定]** 以繼續。

1. 在 [ **Striim 存取設定**] 窗格中，設定 [**公用 IP 位址**] （選擇預設值）、[**用於 Striim 的功能變數名稱**]、[**管理密碼**] （您想要用來登入 Striim UI）。 設定 VNET 和子網（選擇預設值）。 填入詳細資料之後，請選取 **[確定]** 以繼續。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Striim 存取設定":::

1. Azure 將會驗證部署，並確定一切看起來良好;驗證需要幾分鐘的時間才能完成。 驗證完成之後，請選取 **[確定]**。
  
1. 最後，請檢查使用規定，然後選取 [**建立**] 以建立您的 Striim 實例。 

## <a name="configure-the-source-database"></a>設定源資料庫

在本節中，您會將 Oracle 資料庫設定為數據移動的來源。  您需要[ORACLE JDBC 驅動程式](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)才能連接到 oracle。 若要從來源 Oracle 資料庫讀取變更，您可以使用[透過 logminer](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html)或[XStream api](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)。 Oracle JDBC 驅動程式必須存在於 Striim 的 JAVA 路徑中，才能從 Oracle 資料庫讀取、寫入或保存資料。

將[ojdbc8](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)下載至您的本機電腦。 您稍後會將它安裝在 Striim 叢集中。

## <a name="configure-target-database"></a>設定目標資料庫

在本節中，您會將 Azure Cosmos DB Cassandra API 帳戶設定為數據移動的目標。

1. 使用 Azure 入口網站建立[Azure Cosmos DB Cassandra API 帳戶](create-cassandra-dotnet.md#create-a-database-account)。

1. 流覽至 Azure Cosmos 帳戶中的 [**資料總管**] 窗格。 選取 [**新增資料表**] 以建立新的容器。 假設您要將*產品*和*訂單*資料從 Oracle 資料庫移轉到 Azure Cosmos DB。 建立名為**StriimDemo**的新 Keyspace 與 Orders 容器。 布建具有**1000**ru 的容器（此範例使用 1000 ru，但您應該使用工作負載估計的輸送量）和 **/ORDER_ID**做為主要金鑰。 這些值會根據您的來源資料而有所不同。 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png" alt-text="建立 Cassandra API 帳戶":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>設定 Oracle 以 Azure Cosmos DB 資料流程

1. 現在，讓我們回到 Striim。 在與 Striim 互動之前，請先安裝您稍早下載的 Oracle JDBC 驅動程式。

1. 流覽至您在 Azure 入口網站中部署的 Striim 實例。 選取上方功能表列中的 [連線 **]** 按鈕，然後從 [ **SSH** ] 索引標籤中，複製 [**使用 VM 本機帳戶登**入] 欄位中的 URL。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="取得 SSH URL":::

1. 開啟新的終端機視窗，然後執行您從 Azure 入口網站複製的 SSH 命令。 本文使用 MacOS 中的終端機，您可以在 Windows 電腦上使用 PuTTY 或不同的 SSH 用戶端來遵循類似的指示。 出現提示時，輸入 **[是]** 繼續，並輸入您在上一個步驟中為虛擬機器設定的**密碼**。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="連接到 Striim VM":::

1. 現在，開啟新的 [終端機] 索引標籤，複製您先前下載的**ojdbc8**檔案。 使用下列 SCP 命令，將 jar 檔案從本機電腦複製到在 Azure 中執行之 Striim 實例的 tmp 資料夾：

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="將 Jar 檔案從位置機器複製到 Striim":::

1. 接下來，流覽回到您執行 SSH 至 Striim 實例的視窗，並以 sudo 的身分登入。 使用下列命令，將**ojdbc8**檔案從 [ **/tmp** ] 目錄移到 Striim 實例的**lib**目錄中：

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="將 Jar 檔案移至 lib 資料夾":::


1. 在同一個終端機視窗中，執行下列命令來重新開機 Striim 伺服器：

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim 需要一分鐘的時間才能啟動。 如果您想要查看狀態，請執行下列命令： 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. 現在，流覽回到 Azure，並複製 Striim VM 的公用 IP 位址。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="複製 Striim VM IP 位址":::

1. 若要流覽至 Striim 的 Web UI，請在瀏覽器中開啟新的索引標籤，然後複製公用 IP，後面加上：9080。 使用系統**管理員**使用者名稱，以及您在 Azure 入口網站中指定的系統管理員密碼來登入。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="登入 Striim":::

1. 現在您會抵達 Striim 的首頁。 有三個不同的窗格–**儀表板**、**應用程式**和**SourcePreview**。 [儀表板] 窗格可讓您即時移動資料並將其視覺化。 [應用程式] 窗格包含您的串流資料管線或資料流程。 在頁面右側是 SourcePreview，您可以在其中預覽資料，然後再進行移動。

1. 選取 [**應用程式**] 窗格，我們現在會將焦點放在這個窗格。 您可以使用各種範例應用程式來瞭解 Striim，不過在本文中，您將會自行建立。 選取右上角的 [**新增應用程式**] 按鈕。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="新增 Striim 應用程式":::

1. 有幾種不同的方式可以建立 Striim 應用程式。 針對此案例，請選取 [**從頭開始**]。

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png" alt-text="從頭開始應用程式":::

1. 為您的應用程式提供易記名稱，例如**oraToCosmosDB** ，然後選取 [**儲存**]。

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png" alt-text="建立新的應用程式":::

1. 您會到達流程設計師，您可以在其中拖放現成的連接器，以建立您的串流應用程式。 在搜尋列中輸入**oracle** ，將**oracle CDC**來源拖放到應用程式畫布上。  

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png" alt-text="Oracle CDC 來源":::

1. 輸入 Oracle 實例的來源設定屬性。 來源名稱只是 Striim 應用程式的命名慣例，您可以使用如**src_onPremOracle**的名稱。 此外，也請輸入其他詳細資料，例如介面卡類型、連線 URL、使用者名稱、密碼、資料表名稱。 選取 [**儲存**] 以繼續。

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png" alt-text="設定來源參數":::

1. 現在，按一下資料流程的 wave 圖示來連接目標 Azure Cosmos DB 實例。 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png" alt-text="連線至目標":::

1. 設定目標之前，請確定您已將[巴爾的摩根憑證新增至 Striim 的 JAVA 環境](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store)。

1. 輸入目標 Azure Cosmos DB 實例的設定屬性，然後選取 [**儲存**] 以繼續。 以下是要注意的主要參數：

   * **介面卡**-使用**DatabaseWriter**。 寫入 Azure Cosmos DB Cassandra API 時，需要 DatabaseWriter。 Cassandra driver 3.6.0 隨附于 Striim。 如果 DatabaseWriter 超過 Azure Cosmos 容器上所布建的 ru 數目，應用程式將會損毀。

   * **連接 URL** -指定您的 Azure Cosmos DB JDBC 連接 url。 URL 的格式為`jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **Username** -指定您的 Azure Cosmos 帳戶名稱。
   
   * **密碼**-指定 Azure Cosmos 帳戶的主要金鑰。

   * **資料表**-目標資料表必須有主鍵，而且無法更新主鍵。

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png" alt-text="設定目標屬性":::

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png" alt-text="設定目標屬性":::

1. 現在，我們會繼續執行 Striim 應用程式。 在上方功能表列中選取 [已**建立**]，然後按一下 [**部署應用程式**]。 在 [部署] 視窗中，您可以指定是否要在部署拓撲的特定部分執行應用程式的某些部分。 因為我們是透過 Azure 在簡單的部署拓撲中執行，所以我們將使用預設選項。

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png" alt-text="部署應用程式":::


1. 現在，我們將繼續預覽串流，以查看流經 Striim 的資料。 按一下 [wave] 圖示，然後按一下旁邊的眼睛圖示。 部署之後，您可以預覽串流，以查看流經的資料。 選取**wave**圖示和其旁邊的 [**眼睛**]。 在頂端功能表列中選取 [已**部署**] 按鈕，然後選取 [**啟動應用程式**]。

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png" alt-text="啟動應用程式":::

1. 藉由使用**CDC （變更資料捕獲）** 讀取器，Striim 只會在資料庫上收取新的變更。 如果您的資料流程經您的來源資料表，您會看到它。 不過，因為這是範例資料表，所以未連接到任何應用程式的來源。 如果您使用範例資料產生器，您可以在 Oracle 資料庫中插入一鏈事件。

1. 您會看到資料流程經 Striim 平臺。 Striim 也會挑選與您的資料表相關聯的所有中繼資料，這有助於監視資料並確保資料落在正確的目標上。

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png" alt-text="設定 CDC 管線":::

1. 最後，讓我們登入 Azure 並流覽至您的 Azure Cosmos 帳戶。 重新整理資料總管，您就可以看到資料已抵達。 

藉由在 Azure 中使用 Striim 解決方案，您可以持續將資料從各種不同的來源（例如 Oracle、Cassandra、MongoDB 等）遷移到 Azure Cosmos DB，以 Azure Cosmos DB。 若要深入瞭解，請造訪[Striim 網站](https://www.striim.com/)、[下載免費的30天試用版 Striim](https://go2.striim.com/download-free-trial)，以及在使用 Striim 設定遷移路徑時的任何問題，[提出支援要求。](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>後續步驟

* 如果您要將資料移轉至 Azure Cosmos DB SQL API，請參閱[如何使用 Striim 將資料移轉至 Cassandra API 帳戶](cosmosdb-sql-api-migrate-data-striim.md)

* [使用 Azure Cosmos DB 計量來監視和調試資料](use-metrics.md)
