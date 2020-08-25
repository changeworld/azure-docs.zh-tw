---
title: 使用 Azure 入口網站部署 Azure SQL Edge (預覽)
description: 了解如何使用 Azure 入口網站部署 Azure SQL Edge (預覽)
keywords: 部署 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7af4264860f8d9950515cd5302f03822e7cbac39
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816859"
---
# <a name="deploy-azure-sql-edge-preview"></a>部署 Azure SQL Edge (預覽) 

Azure SQL Edge (預覽) 是專為 IoT 和 Azure IoT Edge 部署而最佳化的關聯式資料庫引擎。 其功能可為 IoT 應用程式和解決方案建立高效能的資料儲存和處理層。 本快速入門說明如何使用 Azure 入口網站，透過 Azure IoT Edge 開始建立 Azure SQL Edge 模組。

## <a name="before-you-begin"></a>開始之前

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。
* 登入 [Azure 入口網站](https://portal.azure.com/)。
* 建立 [Azure IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* [從 Azure 入口網站註冊 IoT Edge 裝置](../iot-edge/how-to-register-device-portal.md)。
* 準備 IoT Edge 裝置，以[從 Azure 入口網站部署 IoT Edge 模組](../iot-edge/how-to-deploy-modules-portal.md)。

> [!NOTE]
> 若要將 Azure Linux VM 部署為 IoT Edge 裝置，請參閱此[快速入門手冊](../iot-edge/quickstart-linux.md)。

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>從 Azure Marketplace 部署 SQL Edge 模組

Azure Marketplace 是集合了應用程式和服務的線上市集，您可以在其中瀏覽各式各樣的企業應用程式和解決方案，這些項目皆已通過認證而可在 Azure 上執行並獲得最佳效能，包括 [IoT Edge 模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。 Azure SQL Edge 可以透過 Marketplace 部署至邊緣裝置。

1. 在 Azure Marketplace 上尋找 Azure SQL Edge 模組。<br><br>

   ![MarketPlace 中的 SQL Edge](media/deploy-portal/find-offer-marketplace.png)

2. 挑選最符合您個人需求的軟體方案，然後按一下 [建立]。 <br><br>

   ![挑選正確的軟體方案](media/deploy-portal/pick-correct-plan.png)

3. 在 [IoT Edge 模組的目標裝置] 頁面上，指定下列詳細資料，然後按一下 [建立]

   |**欄位**  |**說明**  |
   |---------|---------|
   |訂用帳戶  |  用來建立 IoT 中樞的 Azure 訂用帳戶 |
   |IoT 中樞   |  選取註冊 IoT Edge 裝置的 IoT 中樞名稱，然後選取 [部署至裝置] 選項|
   |IoT Edge 裝置名稱  |  要部署 SQL Edge 的 IoT Edge 裝置名稱 |

4. 在 [設定模組] 頁面上，瀏覽至部署模組的區段，然後針對 SQL Edge 模組按一下 [設定]。 

5. 在 [IoT Edge 自訂模組] 窗格中，指定環境變數所需的值，並 (或) 自訂模組的建立選項和所需屬性。 如需支援的環境變數完整清單，請參閱 [SQL Server 容器環境變數](/sql/linux/sql-server-linux-configure-environment-variables/)。

   |**參數**  |**說明**|
   |---------|---------|
   | 名稱 | 模組的名稱。 |
   |SA_PASSWORD  | 為 SQL Edge 管理帳戶指定強式密碼。 |
   |MSSQL_LCID   | 為 SQL Server 設定要使用的語言識別碼。 例如，1036 是法文。 |
   |MSSQL_COLLATION | 設定 SQL Server 的預設定序。 此設定會覆寫語言識別碼 (LCID) 與定序的預設對應。 |

   > [!NOTE]
   > 請勿變更或更新模組上的 [映像 URI] 或 [ACCEPT_EULA] 設定。

6. 在 [IoT Edge 自訂模組] 窗格上，為 [主機連接埠] 更新容器建立選項所需的值。 如果您需要部署多個 SQL DB Edge 模組，請務必更新掛接選項，為永續性磁碟區建立新的來源/目標配對。 如需掛接和磁碟區的詳細資訊，請參閱 Docker 文件上的[使用磁碟區](https://docs.docker.com/storage/volumes/)。 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. 在 [IoT Edge 自訂模組] 窗格上，更新 [設定模組對應項所需的屬性]，以包含 SQL 套件的位置和串流分析作業資訊。 這兩個欄位是選擇性的，如果您想要以資料庫和串流作業來部署 SQL Edge 模組，則應使用。

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. 在 [IoT Edge 自訂模組] 窗格上，將 [重新啟動原則] 設定為「一律」，並將 [需要的狀態] 設為「執行中」。
9. 在 [IoT Edge 自訂模組] 窗格上，按一下 [儲存]。
10. 在 [設定模組] 頁面上按 [下一步]。
11. 在 [設定模組] 頁面的 [指定路由 (選擇性)] 上，指定模組對模組或模組對 IoT Edge 中樞通訊的路由；相關資訊請參閱[在 IoT Edge中部署模組和建立路由](../iot-edge/module-composition.md)。
12. 按 [下一步] 。
13. 按一下 [提交] 。

## <a name="connect-to-azure-sql-edge"></a>連線到 Azure SQL Edge

下列步驟會使用容器內的 Azure SQL Edge 命令列工具（ **sqlcmd**）來連線到 Azure sql edge。

> [!NOTE]
> sqlcmd 工具無法在 ARM64 版本的 SQL Edge 容器內使用。

1. 使用 `docker exec -it` 命令在您執行的容器中啟動互動式 Bash 殼層。 在下列範例中， `azuresqledge` 是由 IoT Edge 模組的參數所指定的名稱 `Name` 。

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

您可以從支援 SQL 連線的任何外部 Linux、Windows 或 macOS 工具，連接到您的 Azure SQL Edge 實例，並執行 SQL 查詢。 如需從外部連接至 SQL Edge 容器的詳細資訊，請參閱 [Connect 和查詢 AZURE SQL edge](https://docs.microsoft.com/azure/azure-sql-edge/connect)。

在本快速入門中，您已在 IoT Edge 裝置上部署 SQL Edge 模組。 

## <a name="next-steps"></a>後續步驟

- [SQL Edge 中採用 ONNX 的機器學習與人工智慧](onnx-overview.md)。
- [使用 SQL Edge 和 IoT Edge 建置端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge 中的資料串流](stream-data.md)
