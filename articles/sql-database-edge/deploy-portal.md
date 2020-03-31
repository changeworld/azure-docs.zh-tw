---
title: 使用 Azure 門戶部署 SQL 資料庫邊緣預覽 |微軟文檔
description: 瞭解如何使用 Azure 門戶部署 Azure SQL 資料庫邊緣
keywords: 部署 sql 資料庫邊緣
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246716"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>部署 Azure SQL 資料庫邊緣預覽

Azure SQL 資料庫邊緣預覽是一個關係資料庫引擎，專為 IoT 和 Azure IoT 邊緣部署進行了優化。 它提供了為 IoT 應用程式和解決方案創建高效能資料存儲和處理層的功能。 此快速入門演示如何開始使用 Azure 門戶通過 Azure IoT 邊緣創建 Azure SQL 資料庫邊緣模組。

## <a name="before-you-begin"></a>開始之前

* 如果沒有 Azure 訂閱，請創建[一個免費帳戶](https://azure.microsoft.com/free/)。
* 登錄到 Azure[門戶](https://portal.azure.com/)。
* [在此處](https://azure.microsoft.com/services/sql-database-edge/#contact)提交請求，以啟用用於部署 SQL 資料庫邊緣的訂閱。
* 創建[Azure IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 從[Azure 門戶註冊 IoT 邊緣設備](../iot-edge/how-to-register-device-portal.md)。
* 準備 IoT 邊緣設備以[從 Azure 門戶部署 IoT 邊緣模組](../iot-edge/how-to-deploy-modules-portal.md)。

> [!NOTE]
> 要將 Azure Linux VM 部署為 IoT 邊緣設備，請參閱此[快速入門手冊](../iot-edge/quickstart-linux.md)。

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>從 Azure 應用商店部署 SQL 資料庫邊緣模組

Azure Marketplace 是集合了應用程式和服務的線上市集，您可以在其中瀏覽各式各樣的企業應用程式和解決方案，這些項目皆已通過認證而可在 Azure 上執行並獲得最佳效能，包括 [IoT Edge 模組](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。 Azure SQL 資料庫邊緣可以通過市場部署到邊緣設備。

1. 在 Azure 應用商店中查找 Azure SQL 資料庫邊緣模組。<br><br>

   ![市場中的 SQL 資料庫邊緣](media/deploy-portal/find-offer-marketplace.png)

2. 選擇最符合您要求的軟體計畫，然後按一下"**創建**"。 <br><br>

   ![選擇正確的軟體計畫](media/deploy-portal/pick-correct-plan.png)

3. 在 IoT 邊緣模組的目標設備頁面上，指定以下詳細資訊，然後按一下"**創建"**

   |**領域**  |**描述**  |
   |---------|---------|
   |訂用帳戶  |  創建 IoT 中心下的 Azure 訂閱 |
   |IoT 中樞   |  註冊 IoT 邊緣設備的 IoT 中心的名稱，然後選擇"部署到設備"選項|
   |IoT 邊緣設備名稱  |  將部署 SQL 資料庫邊緣的 IoT 邊緣設備的名稱 |

4. 在 **"設置模組"** 頁上，導航到有關部署模組的部分，然後按一下 SQL 資料庫邊緣模組的 **"配置**"。 

5. 在**IoT 邊緣自訂模組**窗格中，指定環境變數的所需值和/或自訂模組的創建選項和所需屬性。 有關受支援環境變數的完整清單，請參閱[SQL Server 容器環境變數](/sql/linux/sql-server-linux-configure-environment-variables/)。

   |**參數**  |**描述**|
   |---------|---------|
   | 名稱 | 模組的名稱。 |
   |SA_PASSWORD  | 為 SQL 資料庫邊緣管理員帳戶指定強式密碼。 |
   |MSSQL_LCID   | 為 SQL Server 設定要使用的語言識別碼。 例如，1036 是法語。 |
   |MSSQL_COLLATION | 設定 SQL Server 的預設定序。 此設置將覆蓋語言識別碼 （LCID） 到排序的預設映射。 |

   > [!NOTE]
   > 請不要更改或更新**模組上的映射 URI**或**ACCEPT_EULA**設置。

6. 在**IoT 邊緣自訂模組**窗格中，更新容器為**主機埠**創建所需的值選項。 如果需要部署多個 SQL DB Edge 模組，請確保更新裝載選項，以創建持久卷的新源&目標對。 有關裝載和卷的詳細資訊，請參閱 Docker 文檔中[的"使用卷](https://docs.docker.com/storage/volumes/)"。 

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

7. 在**IoT 邊緣自訂模組**窗格中，更新 *"設置"模組孿生的所需屬性*，以包括 SQL 包的位置和流分析作業資訊。 這兩個欄位是可選的，如果要使用資料庫和流式處理作業部署 SQL 資料庫邊緣模組，則應使用。

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. 在**IoT 邊緣自訂模組**窗格中，將 *"重新開機策略*"設置為"始終 *"，並將"所需狀態*"設置為"始終運行"。
9. 在**IoT 邊緣自訂模組**窗格中，按一下"**保存**"。
10. 在 **"設置模組"** 頁上按一下 **"下一步**"。
11. 在 **"設置模組"** 頁的 **"指定路由（可選"）** 上，指定模組到模組或模組到 IoT 邊緣中心通信的路由，請參閱[部署模組並在 IoT 邊緣中建立路由](../iot-edge/module-composition.md)。
12. 按 [下一步]****。
13. 按一下 [提交]****。

在此快速入門中，您將 SQL 資料庫邊緣模組部署在 IoT 邊緣設備上。

## <a name="next-steps"></a>後續步驟

- [機器學習和人工智慧與 SQL 資料庫邊緣中的 ONNX。](onnx-overview.md)
- 使用 IoT 邊緣使用 SQL 資料庫邊緣構建端到端 IoT 解決方案。
