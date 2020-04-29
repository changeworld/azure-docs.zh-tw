---
title: 使用 Azure 入口網站部署 SQL Database Edge 預覽 |Microsoft Docs
description: 瞭解如何使用 Azure 入口網站部署 Azure SQL Database 邊緣
keywords: 部署 sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246716"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>部署 Azure SQL Database Edge 預覽

Azure SQL Database Edge 預覽是針對 IoT 和 Azure IoT Edge 部署優化的關係資料庫引擎。 它提供的功能可為 IoT 應用程式和解決方案建立高效能的資料儲存和處理層。 本快速入門說明如何使用 Azure 入口網站，開始透過 Azure IoT Edge 建立 Azure SQL Database Edge 模組。

## <a name="before-you-begin"></a>開始之前

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。
* 登入 [Azure 入口網站](https://portal.azure.com/)。
* 在[這裡](https://azure.microsoft.com/services/sql-database-edge/#contact)提交要求，讓訂用帳戶啟用以部署 SQL Database Edge。
* 建立[Azure IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* [從 Azure 入口網站註冊 IoT Edge 裝置](../iot-edge/how-to-register-device-portal.md)。
* 準備 IoT Edge 裝置，以[從 Azure 入口網站部署 IoT Edge 模組](../iot-edge/how-to-deploy-modules-portal.md)。

> [!NOTE]
> 若要將 Azure Linux VM 部署為 IoT Edge 裝置，請參閱此[快速入門手冊](../iot-edge/quickstart-linux.md)。

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>從 Azure Marketplace 部署 SQL Database Edge 模組

Azure Marketplace 是集合了應用程式和服務的線上市集，您可以在其中瀏覽各式各樣的企業應用程式和解決方案，這些項目皆已通過認證而可在 Azure 上執行並獲得最佳效能，包括 [IoT Edge 模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。 Azure SQL Database Edge 可以透過 marketplace 部署至邊緣裝置。

1. 尋找 Azure Marketplace 上的 Azure SQL Database Edge 模組。<br><br>

   ![MarketPlace 中的 SQL Database 邊緣](media/deploy-portal/find-offer-marketplace.png)

2. 挑選最符合您需求的軟體方案，然後按一下 [**建立**]。 <br><br>

   ![挑選正確的軟體方案](media/deploy-portal/pick-correct-plan.png)

3. 在 [IoT Edge 模組的目標裝置] 頁面上，指定下列詳細資料，然後按一下 [**建立**]

   |**欄位**  |**說明**  |
   |---------|---------|
   |訂用帳戶  |  用來建立 IoT 中樞的 Azure 訂用帳戶 |
   |IoT 中樞   |  註冊 IoT Edge 裝置的 IoT 中樞名稱，然後選取 [部署至裝置] 選項|
   |IoT Edge 裝置名稱  |  SQL Database Edge 部署所在的 IoT Edge 裝置名稱 |

4. 在 [**設定模組**] 頁面上，流覽至部署模組的區段，然後按一下 [針對 SQL Database Edge 模組進行**設定**]。 

5. 在 [ **IoT Edge 自訂模組**] 窗格中，為環境變數指定所需的值，以及（或）自訂模組的 [建立選項] 和 [所需屬性]。 如需所支援環境變數的完整清單，請參閱[SQL Server 容器環境變數](/sql/linux/sql-server-linux-configure-environment-variables/)。

   |**參數**  |**說明**|
   |---------|---------|
   | Name | 模組的名稱。 |
   |SA_PASSWORD  | 為 SQL Database Edge 管理帳戶指定強式密碼。 |
   |MSSQL_LCID   | 為 SQL Server 設定要使用的語言識別碼。 例如，1036是法文。 |
   |MSSQL_COLLATION | 設定 SQL Server 的預設定序。 此設定會將語言識別項（LCID）的預設對應覆寫為定序。 |

   > [!NOTE]
   > 請不要變更或更新此模組上的**映射 URI**或**ACCEPT_EULA**設定。

6. 在 [ **IoT Edge 自訂模組**] 窗格中，更新**主機埠**所需的容器建立選項值。 如果您需要部署一個以上的 SQL DB Edge 模組，請務必更新 [掛接] 選項，為持續性磁片區建立新的來源 & 目標配對。 如需裝載和磁片區的詳細資訊，請參閱 docker 檔上的[使用磁片](https://docs.docker.com/storage/volumes/)區。 

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

7. 在 [ **IoT Edge 自訂模組**] 窗格中，更新*設定模組對應項的所需屬性*，以包含 SQL 封裝和串流分析作業資訊的位置。 這兩個欄位是選擇性的，如果您想要使用資料庫和串流作業來部署 SQL Database Edge 模組，則應使用此欄位。

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. 在 [ **IoT Edge 自訂模組**] 窗格中，將 [*重新開機原則*] 設定為 [一律]，而 [*預期狀態*]
9. 在 [ **IoT Edge 自訂模組**] 窗格中，按一下 [**儲存**]。
10. 在 [**設定模組**] 頁面上，按 **[下一步]**。
11. 在 [**設定模組**的**指定路由（選擇性）** ] 頁面上，指定模組對模組或模組的路由，以 IoT Edge 中樞通訊，請參閱[在 IoT Edge 中部署模組及建立路由](../iot-edge/module-composition.md)。
12. 按 [下一步]  。
13. 按一下 [提交]  。

在本快速入門中，您已在 IoT Edge 裝置上部署 SQL Database Edge 模組。

## <a name="next-steps"></a>後續步驟

- [使用 SQL Database Edge 中的 ONNX Machine Learning 及人工智慧](onnx-overview.md)。
- 使用 IoT Edge，建立具有 SQL Database 邊緣的端對端 IoT 解決方案。
