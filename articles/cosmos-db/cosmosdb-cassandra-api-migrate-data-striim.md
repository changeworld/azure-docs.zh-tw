---
title: 使用 Striim 將資料移至 Azure 宇宙 DB Cassandra API 帳號
description: 瞭解如何使用 Striim 將數據從 Oracle 資料庫遷移到 Azure 宇宙 DB Cassandra API 帳戶。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 50028e81c4ca130aa3266c164a431dc935a271cb
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730045"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>使用 Striim 將資料移至 Azure 宇宙 DB Cassandra API 帳號

Azure 應用商店中的 Striim 映射提供從資料倉庫和資料庫到 Azure 的持續即時數據移動。 移動數據時,可以執行在線非規範化、數據轉換、啟用即時分析和數據報告方案。 使用 Striim 可輕鬆地開始將企業數據持續移動到 Azure Cosmos DB Cassandra API。 Azure 提供了一個應用商店產品,使部署 Striim 和將數據遷移到 Azure Cosmos DB 變得容易。 

本文展示如何使用 Striim 將資料從**Oracle 資料庫**移至 Azure**宇宙 DB Cassandra API 帳號**。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 本地運行的 Oracle 資料庫,其中有一些數據。

## <a name="deploy-the-striim-marketplace-solution"></a>部署斯特裡姆市場解決方案

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選擇 **「創建資源並在**Azure 應用商店中搜索**Striim」。。** 選擇第一個選項並**建立**。

   ![查找斯特裡姆市場專案](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. 接下來,輸入 Striim 實例的配置屬性。 斯特裡姆環境部署在虛擬機器中。 在 **「基本」** 窗格中,輸入**VM 使用者名**VM**密碼**(此密碼用於將 SSH 放入 VM)。 選擇要部署 Striim 的**訂閱**、**資源群組**與**位置詳細資訊**。 完成後,選擇 **"確定**"。

   ![設定斯特裡伊姆的基本設置](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. 在 **「斯特裡姆群集設置」** 窗格中,選擇斯特裡姆部署的類型和虛擬機大小。

   |設定 | 值 | 描述 |
   | ---| ---| ---|
   |斯特裡姆部署類型 |獨立 | Striim 可以在**獨立**部署類型或**群集**部署類型中運行。 獨立模式將在單個虛擬機器上部署 Striim 伺服器,您可以根據資料量選擇 VM 的大小。 叢集模式將在具有所選大小的兩個或多個 VM 上部署 Striim 伺服器。 具有 2 個以上節點的群集環境提供自動高可用性和故障轉移。</br></br> 在本教學中,您可以選擇"獨立"選項。 使用預設的"Standard_F4s"大小 VM。 | 
   | 斯特裡姆群集的名稱|    <Striim_cluster_Name>|  斯特裡姆群集的名稱。|
   | 斯特裡姆群集密碼|   <Striim_cluster_password>|  群集的密碼。|

   填寫表單後,選擇 **「確定」** 以繼續。

1. 在 **'Striim 存取設定'** 窗格中,設定**公共 IP 位址**(選擇預設值 **)、Striim 的網域名稱**、要登入 Striim UI 的**管理員密碼**。 配置 VNET 和子網(選擇預設值)。 填寫詳細資訊後,選擇 **「確定」** 以繼續。

   ![斯特裡姆存取設定](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure 將驗證部署並確保一切看起來良好;驗證需要幾分鐘才能完成。 驗證完成後,選擇 **"確定**"。
  
1. 最後,查看使用條款並選擇 **「創建**」以創建 Striim 實例。 

## <a name="configure-the-source-database"></a>設定來源

在本節中,您將 Oracle 資料庫配置為數據移動的源。  您需要[Oracle JDBC 驅動程式](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)才能連接到 Oracle。 要從源 Oracle 資料庫讀取變更,可以使用[LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html)或[XStream API](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)。 Oracle JDBC 驅動程式必須存在於 Striim 的 Java 類路徑中,才能讀取、寫入或持久化來自 Oracle 資料庫的數據。

將[ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html)驅動程式下載到本地電腦上。 稍後將將其安裝在 Striim 群集中。

## <a name="configure-target-database"></a>設定目標資料庫

在本節中,您將配置 Azure 宇宙 DB Cassandra API 帳戶作為數據行動的目標。

1. 使用 Azure 門戶建立[Azure 宇宙 DB Cassandra API 帳號](create-cassandra-dotnet.md#create-a-database-account)。

1. 導航到 Azure Cosmos 帳戶中**的資料資源管理員**窗格。 選擇 **「新建表」** 以建立新容器。 假設您正在遷移*產品*並*訂購*數據從 Oracle 資料庫到 Azure Cosmos DB。 使用訂單容器創建新的鍵空間,名為**StriimDemo。** 使用**1000 個 RA 配置**容器(此範例使用 1000 個 R,但應使用為工作負載估計的輸送量)和 **/ORDER_ID**作為主鍵。 這些值將因源數據而異。 

   ![建立卡桑德拉 API 帳戶](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>將 Oracle 設定為 Azure 宇宙資料庫資料流程

1. 現在,讓我們回到斯特裡姆。 在與 Striim 互動之前,請安裝您之前下載的 Oracle JDBC 驅動程式。

1. 導航到在 Azure 門戶中部署的 Striim 實例。 選擇上選單列中的 **「連接**」按鈕,然後從**SSH**選項卡中,**使用 VM 本地帳戶**欄位在登入中複製 URL。

   ![取得 SSH URL](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. 打開新的終端視窗並運行從 Azure 門戶複製的 SSH 命令。 本文在 MacOS 中使用終端,您可以在 Windows 電腦上使用 PuTTY 或不同的 SSH 用戶端遵循類似的說明。 當出現提示時,鍵入 **「是**「以繼續並輸入在上一步驟中為虛擬機器設定的**密碼**。

   ![連接到斯特裡姆 VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. 現在,打開新的終端選項卡來複製您以前下載的**ojdbc8.jar**檔案。 使用以下 SCP 指令將 jar 檔案從本地電腦複製到 Azure 中執行的 Striim 實體的 tmp 資料夾:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![將 Jar 檔案從位置電腦複製到斯特裡姆](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. 接下來,導航回您執行 SSH 的視窗到 Striim 實例,然後作為 sudo 登錄。 使用以下指令將**ojdbc8.jar**檔案從 **/tmp**目錄移至 Striim 實體的**lib**目錄中:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![將 Jar 檔案移至 lib 資料夾](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. 從同一終端視窗,透過執行以下指令重新啟動 Striim 伺服器:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. 斯特裡姆需要一分鐘才能啟動。 如果要查看狀態,執行以下命令: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. 現在,導航回 Azure 並複製 Striim VM 的公共 IP 位址。 

   ![複製斯特裡姆 VM IP 位址](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. 要瀏覽到 Striim 的 Web UI,請在瀏覽器中打開新選項卡,然後複製公共 IP,然後複製:9080。 使用**管理員**使用者名以及您在 Azure 門戶中指定的管理員密碼登錄。

   ![登錄到斯特裡姆](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. 現在,您將到達斯特裡姆的主頁。 有三個不同的窗格 ─**儀表板**、**應用**與**源預覽**。 "儀錶板"窗格允許您即時移動數據並可視化資料。 "應用"窗格包含流資料管道或資料流。 頁面右側是 SourcePreview,您可以在其中預覽數據,然後再移動數據。

1. 選擇「**應用」** 窗格,我們將立即關注此窗格。 有多種範例應用可用於瞭解 Striim,但在本文中,您將創建自己的應用。 選擇右上角的 **「添加應用」** 按鈕。

   ![新增斯特裡姆應用](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. 創建 Striim 應用程式的方法有很多種。 為此方案選擇 **「從頭開始**」。

   ![從頭開始應用](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. 為您的應用程式指定一個友好的名稱,例如**oraToCosmosDB,** 然後選擇 **「儲存**」 。。

   ![建立新的應用程式](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. 您將到達流設計器,您可以在其中拖放開盒裝連接器以建立流式處理應用程式。 在搜尋列中鍵入**Oracle,** 將**Oracle CDC**源拖放到應用畫布上。  

   ![Oracle CDC 來源](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. 輸入 Oracle 實體設定屬性。 來源名稱只是 Striim 應用程式的命名約定,可以使用名稱,如**src_onPremOracle**。 還要輸入其他詳細資訊,如適配器類型、連接 URL、使用者名、密碼、表名。 選擇 **「保存」** 以繼續。

   ![設定來源參數](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. 現在,按一下流的波形圖示以連接目標 Azure Cosmos DB 實例。 

   ![連線至目標](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. 在設定目標之前,請確保向[Striim 的 Java 環境新增了巴爾的摩根憑證](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store)。

1. 輸入目標 Azure Cosmos DB 實體的設定屬性,然後選擇 **「儲存」** 以繼續。 以下是需要注意的關鍵參數:

   * **配接器**-使用**資料庫寫入器**。 寫入 Azure 宇宙資料庫卡桑德拉 API 時,需要資料庫寫入程式。 卡桑德拉驅動程式 3.6.0 與斯特裡姆捆綁在一起。 如果資料庫寫入器超過 Azure Cosmos 容器上預配的 R,應用程式將崩潰。

   * **連接 URL** - 指定 Azure 宇宙 DB JDBC 連接 URL。 網址採用格式`jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **使用者名稱**- 指定 Azure Cosmos 帳戶名稱。
   
   * **密碼**─ 指定 Azure Cosmos 帳戶的主金鑰。

   * **表**- 目標表必須具有主鍵,主鍵無法更新。

   ![設定目標屬性](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![設定目標屬性](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. 現在,我們將繼續運行 Striim 應用程式。 選擇在上選單列中選擇 **「已建立**」,然後**部署應用**。 在部署視窗中,可以指定是否要在部署拓撲的特定部分上運行應用程式的某些部分。 由於我們通過 Azure 在簡單的部署拓撲中運行,因此我們將使用預設選項。

   ![部署應用程式](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. 現在,我們將繼續預覽流以查看數據流經 Striim。 點擊波浪圖示並按下其旁邊的眼睛圖示。 部署後,可以預覽流以查看數據流經。 選擇**波浪**圖示及其旁邊的**眼球**。 選擇頂部功能表列中的 **「已部署」** 按鈕,然後選擇 **「開始應用**」 。

   ![啟動應用程式](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. 通過使用**CDC(更改資料擷取器,** Striim 將只取得資料庫上的新變更。 如果資料流經源表,您將看到它。 但是,由於這是一個範例表,因此源未連接到任何應用程式。 如果使用範例資料產生器,則可以將事件鏈插入到 Oracle 資料庫中。

1. 您將看到資料流經斯特裡姆平臺。 Striim 還選取了與表關聯的所有元數據,這有助於監視數據並確保數據落在正確的目標上。

   ![設定 CDC 導管](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. 最後,讓我們登錄到 Azure 並導航到 Azure Cosmos 帳戶。 刷新資料資源管理員,您可以看到數據已到達。 

通過使用 Azure 中的 Striim 解決方案,可以從 Oracle、Cassandra、MongoDB 等各種源持續將資料遷移到 Azure Cosmos DB 到 Azure Cosmos DB。 要瞭解更多資訊,請造[訪 Striim 網站](https://www.striim.com/),[下載 Striim 的 30 天免費試用](https://go2.striim.com/download-free-trial)版,以及與 Striim 一起設置遷移路徑時的任何問題,請提交[支援請求。](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>後續步驟

* 如果要將資料移到 Azure Cosmos DB SQL API,請參考[如何使用 Striim 將資料移至 Cassandra API 帳戶](cosmosdb-sql-api-migrate-data-striim.md)

* [使用 Azure Cosmos DB 指標監控與除錯資料](use-metrics.md)
