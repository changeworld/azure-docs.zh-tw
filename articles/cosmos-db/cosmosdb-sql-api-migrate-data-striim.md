---
title: 使用 Striim 將資料移轉至 Azure Cosmos DB SQL API 帳戶
description: 瞭解如何使用 Striim 將資料從 Oracle 資料庫移轉至 Azure Cosmos DB SQL API 帳戶。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: a77f039ea266e10130b6460855f989ab317a20ba
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097663"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>使用 Striim 將資料移轉至 Azure Cosmos DB SQL API 帳戶
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Azure marketplace 中的 Striim 映射提供從資料倉儲和資料庫到 Azure 的連續即時資料移動。 移動資料時，您可以執行內嵌反正規化、資料轉換、啟用即時分析和資料包表案例。 您可以輕鬆地開始使用 Striim，持續將企業資料移至 Azure Cosmos DB SQL API。 Azure 提供 marketplace 供應專案，可讓您輕鬆地部署 Striim，並將資料移轉至 Azure Cosmos DB。 

本文說明如何使用 Striim 將 **Oracle 資料庫** 中的資料移轉至 **Azure Cosmos DB SQL API 帳戶** 。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 [Azure 訂用帳戶](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 在內部部署執行且有一些資料的 Oracle 資料庫。

## <a name="deploy-the-striim-marketplace-solution"></a>部署 Striim marketplace 解決方案

1. 登入[Azure 入口網站](https://portal.azure.com/)。

1. 選取 [ **建立資源** ]，並在 Azure marketplace 中搜尋 **Striim** 。 選取第一個選項，然後 **建立** 。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="尋找 Striim marketplace 專案":::

1. 接著，輸入 Striim 實例的設定屬性。 Striim 環境會部署在虛擬機器中。 在 [ **基本** ] 窗格中，輸入 **vm 使用者名稱** 、 **vm 密碼** (此密碼用來透過 SSH 連線到 VM) 。 選取您要部署 Striim 的 **訂** 用帳戶、 **資源群組** 和 **位置詳細資料** 。 完成之後，請選取 **[確定]** 。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="尋找 Striim marketplace 專案":::

1. 在 [ **Striim 叢集設定** ] 窗格中，選擇 Striim 部署的類型和虛擬機器大小。

   |設定 | 值 | 描述 |
   | ---| ---| ---|
   |Striim 部署類型 |獨立 | Striim 可在 **獨立****或叢集** 部署類型中執行。 獨立模式會將 Striim 伺服器部署在單一虛擬機器上，而且您可以根據您的資料量來選取 Vm 的大小。 叢集模式會將 Striim 伺服器部署在具有所選大小的兩部或多部 Vm 上。 具有2個以上節點的叢集環境提供自動高可用性和容錯移轉。</br></br> 在本教學課程中，您可以選取 [獨立] 選項。 使用預設的「Standard_F4s」大小的 VM。  | 
   | Striim 叢集的名稱|    <Striim_cluster_Name>|  Striim 叢集的名稱。|
   | Striim 叢集密碼|   <Striim_cluster_password>|  叢集的密碼。|

   填滿表單之後，選取 **[確定]** 以繼續。

1. 在 [ **Striim 存取設定** ] 窗格中，設定 [ **公用 IP 位址** ] (選擇預設值) 、您要用來登入 Striim UI 的 [ **Striim 的功能變數名稱** ]、[ **管理員密碼** ]。 設定 VNET 和子網 (選擇) 的預設值。 填入詳細資料之後，選取 **[確定]** 以繼續。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="尋找 Striim marketplace 專案":::

1. Azure 會驗證部署，並確定一切看起來良好;驗證需要幾分鐘的時間才能完成。 驗證完成之後，請選取 **[確定]** 。
  
1. 最後，請參閱使用條款，然後選取 [ **建立** ] 以建立您的 Striim 實例。 

## <a name="configure-the-source-database"></a>設定源資料庫 

在本節中，您會將 Oracle 資料庫設定為數據移動的來源。  您必須要有 [ORACLE JDBC 驅動程式](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) ，才能連接到 oracle。 若要從來源 Oracle 資料庫讀取變更，您可以使用 [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) 或 [XStream api](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)。 Oracle JDBC 驅動程式必須存在於 Striim 的 JAVA 類別路徑中，才能從 Oracle 資料庫讀取、寫入或保存資料。

將 [ojdbc8 .jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) 驅動程式下載到您的本機電腦。 您稍後會將其安裝在 Striim 叢集中。

## <a name="configure-the-target-database"></a>設定目標資料庫

在本節中，您會將 Azure Cosmos DB SQL API 帳戶設定為數據移動的目標。

1. 使用 Azure 入口網站建立 [AZURE COSMOS DB SQL API 帳戶](create-cosmosdb-resources-portal.md) 。

1. 流覽至 Azure Cosmos 帳戶中的 [ **資料總管** ] 窗格。 選取 [ **新增容器** ] 以建立新的容器。 假設您要將 *產品* 和 *訂單* 資料從 Oracle database 遷移至 Azure Cosmos DB。 使用名為 **Orders** 的容器來建立名為 **StriimDemo** 的新資料庫。 使用 **1000** ru 布建容器 (此範例使用 1000 ru，但您應該使用針對您的工作負載所估計的輸送量) 和 **/ORDER_ID** 作為分割區索引鍵。 這些值會根據您的來源資料而有所不同。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="尋找 Striim marketplace 專案":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>設定 Oracle 以 Azure Cosmos DB 資料流程

1. 現在，讓我們回到 Striim。 與 Striim 互動之前，請先安裝您稍早下載的 Oracle JDBC 驅動程式。

1. 流覽至您在 Azure 入口網站中部署的 Striim 實例。 選取上方功能表列中的 [連線 **]** 按鈕，然後從 [ **SSH** ] 索引標籤中，複製 [ **使用 VM 本機帳戶登** 入] 欄位中的 URL。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="尋找 Striim marketplace 專案":::

1. 開啟新的終端機視窗，並執行您從 Azure 入口網站複製的 SSH 命令。 本文使用 MacOS 中的終端機，您可以在 Windows 電腦上使用 PuTTY 或不同的 SSH 用戶端來遵循類似的指示。 出現提示時，請輸入 [ **是]** 以繼續，然後輸入您在上一個步驟中為虛擬機器設定的 **密碼** 。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="尋找 Striim marketplace 專案":::

1. 現在，開啟新的 [終端機] 索引標籤，以複製您先前下載的 **ojdbc8 .jar** 檔案。 使用下列 SCP 命令將 jar 檔案從本機電腦複製到在 Azure 中執行之 Striim 實例的 tmp 資料夾：

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="尋找 Striim marketplace 專案":::

1. 接下來，流覽回到您在其中執行 SSH 至 Striim 實例的視窗，並以 sudo 的身分登入。 使用下列命令，將 **ojdbc8 .jar** 檔案從 **/tmp** 目錄移至 Striim 實例的 **lib** 目錄中：

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="尋找 Striim marketplace 專案":::


1. 從相同的終端機視窗中，執行下列命令來重新開機 Striim 伺服器：

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

1. 現在，流覽回 Azure 並複製您 Striim VM 的公用 IP 位址。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="尋找 Striim marketplace 專案":::

1. 若要流覽至 Striim 的 Web UI，請在瀏覽器中開啟新的索引標籤，然後複製 [公用 IP]，然後再複製：9080。 使用系統 **管理員** 使用者名稱，以及您在 Azure 入口網站中指定的系統管理員密碼來登入。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="尋找 Striim marketplace 專案":::

1. 現在您將抵達 Striim 的首頁。 有三個不同的窗格– **儀表板** 、 **應用程式** 和 **SourcePreview** 。 [儀表板] 窗格可讓您即時移動資料並將其視覺化。 [應用程式] 窗格包含您的串流資料管線或資料流程。 在頁面右側 SourcePreview，您可以在其中預覽資料，然後再進行移動。

1. 選取 [ **應用程式** ] 窗格，我們現在會將焦點放在此窗格上。 您可以使用各種範例應用程式來瞭解 Striim，不過在本文中，您將建立自己的應用程式。 選取右上角的 [ **新增應用程式** ] 按鈕。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="尋找 Striim marketplace 專案":::

1. 有幾種不同的方式可以建立 Striim 的應用程式。 選取 [ **從範本開始** ]，從現有的範本開始。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="尋找 Striim marketplace 專案" 並選取 [ **目標： Azure Cosmos DB** ，然後選取 [ **Oracle CDC] 以 Azure Cosmos DB** 。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="尋找 Striim marketplace 專案":::

1. 在下一個頁面中，為您的應用程式命名。 您可以提供名稱（例如 **oraToCosmosDB** ），然後選取 [ **儲存** ]。

1. 接下來，輸入來源 Oracle 實例的來源設定。 輸入 **來源名稱** 的值。 來源名稱只是 Striim 應用程式的命名慣例，您可以使用類似 **src_onPremOracle** 的內容。 輸入 [來源參數 **URL** ]、[使用者 **名稱** ]、[ **密碼** ] 的其餘值，選擇 [ **LogMiner** ] 做為讀取器以讀取 Oracle 資料的讀取器。 選取 [下一步] 以繼續進行操作。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="尋找 Striim marketplace 專案":::

1. Striim 會檢查您的環境，並確定它可以連線到您的來源 Oracle 實例、擁有正確的許可權，而且 CDC 的設定是否正確。 所有值都經過驗證之後，請選取 **[下一步]** 。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="尋找 Striim marketplace 專案":::

1. 從您想要遷移的 Oracle 資料庫中選取資料表。 例如，讓我們選擇 Orders 資料表，然後選取 **[下一步]** 。 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="尋找 Striim marketplace 專案":::

1. 選取來源資料表之後，您可以進行更複雜的作業，例如對應和篩選。 在此情況下，您只會在 Azure Cosmos DB 中建立來源資料表的複本。 因此，請選取 **[下一步]** 以設定目標

1. 現在，讓我們來設定目標：

   * **目標名稱** -提供目標的易記名稱。 
   * **輸入來源** -從下拉式清單中，選取您在來源 Oracle 設定中建立的輸入資料流程。 
   * **集合** -輸入目標 Azure Cosmos DB 設定屬性。 集合語法為 **SourceSchema. SourceTable、>targetdatabase. TargetContainer** 。 在此範例中，值會是 "SYSTEM。ORDERS、StriimDemo。 
   * **AccessKey** -Azure Cosmos 帳戶的 PrimaryKey。
   * **ServiceEndpoint** – Azure Cosmos 帳戶的 URI，可以在 Azure 入口網站的 [ **金鑰** ] 區段下找到。 

   選取 **Save** [儲存 **] 和 [下一步]** 。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="尋找 Striim marketplace 專案":::


1. 接下來，您將抵達流程設計工具，您可以在其中拖放現成的連接器，以建立串流應用程式。 此時您不會對流程進行任何修改。 所以請繼續進行，並選取 [ **部署應用** 程式] 按鈕來部署應用程式。
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="尋找 Striim marketplace 專案":::

1. 在 [部署] 視窗中，您可以指定是否要在部署拓撲的特定部分執行應用程式的特定部分。 因為我們是透過 Azure 在簡單的部署拓撲中執行，所以我們會使用預設選項。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="尋找 Striim marketplace 專案":::

1. 部署之後，您可以預覽串流以查看流經的資料。 選取 **wave** 圖示和旁邊的眼睛。 在頂端功能表列中選取 [已 **部署** ] 按鈕，然後選取 [ **啟動應用程式** ]。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="尋找 Striim marketplace 專案":::

1. 藉由使用 **CDC (變更 Data Capture)** 讀取器，Striim 只會在資料庫上挑選新的變更。 如果您的資料流程經來源資料表，您將會看到它。 不過，由於這是示範表格，因此來源不會連接到任何應用程式。 如果您使用範例資料產生器，您可以將一連串的事件插入 Oracle 資料庫。

1. 您會看到資料流程經 Striim 平臺。 Striim 也會挑選與您的資料表相關聯的所有中繼資料，這有助於監視資料並確保資料落在正確的目標上。

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="尋找 Striim marketplace 專案":::

1. 最後，讓我們登入 Azure，並流覽至您的 Azure Cosmos 帳戶。 重新整理資料總管，您會看到資料已抵達。  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="尋找 Striim marketplace 專案":::

藉由在 Azure 中使用 Striim 解決方案，您可以持續將資料移轉至不同來源的 Azure Cosmos DB，例如 Oracle、Cassandra、MongoDB 和其他各種來源，以 Azure Cosmos DB。 若要深入瞭解，請造訪 [Striim 網站](https://www.striim.com/)、 [下載免費的30天試用版 Striim](https://go2.striim.com/download-free-trial)，以及在使用 Striim 設定遷移路徑時的任何問題，並 [提出支援要求。](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>下一步

* 如果您要將資料移轉至 Azure Cosmos DB SQL API，請參閱 [如何使用 Striim 將資料移轉至 Cassandra API 帳戶](cosmosdb-cassandra-api-migrate-data-striim.md)

* [使用 Azure Cosmos DB 計量來監視和偵測您的資料](use-metrics.md)