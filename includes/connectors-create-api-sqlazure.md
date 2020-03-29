---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789186"
---
* 如果您是使用 Azure SQL Database，請遵循[連線到 Azure SQL Database](#connect-azure-sql-db) 下的步驟。

* 如果您是使用 SQL Server，請遵循[連線到 SQL Server](#connect-sql-server) 下的步驟。

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>連線到 Azure SQL Database

當 SQL 觸發器或操作提示您獲得連接資訊時，請按照以下步驟操作觸發器和操作。

1. 對於**連接名稱**，為連接創建名稱。

1. 在**SQL 伺服器名稱**下，選擇 Azure SQL 伺服器。 當**SQL 資料庫名稱**清單出現時，選擇資料庫。 提供 Azure SQL 伺服器的使用者名和密碼。

   您還可以在 SQL 資料庫屬性下的 Azure 門戶或連接字串中找到此資訊：

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![創建與 Azure SQL 資料庫的連接](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 當您完成時，選取 [建立]****。

1. 創建連接後，繼續[添加 SQL 觸發器](#add-sql-trigger)或添加[SQL 操作](#add-sql-action)。

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>連接至 SQL Server

當 SQL 觸發器或操作提示您獲得連接資訊時，請按照以下步驟操作觸發器和操作。 對於要求在本地電腦上安裝[本地資料閘道](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install)並[創建 Azure 資料閘道資源](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)的情況，請確保首先完成這些要求。 否則，創建連接時閘道資源將不會顯示在閘道清單中。

此外，要在[整合服務環境 （ISE）](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)中使用 SQL Server 連接器的 Windows 身份驗證，請使用連接器的非 ISE 版本和本地資料閘道。 ISE 標記的版本不支援 Windows 身份驗證。

1. 對於**連接名稱**，為連接創建名稱。

1. 在觸發程序或動作中，選取**透過內部部署資料閘道連線**，如此就會顯示 SQL 伺服器選項。

1. 對於**SQL 伺服器名稱**和**SQL 資料庫名稱**，請提供 SQL 伺服器的位址和資料庫的名稱。 對於**使用者名和密碼**，**Password**請提供伺服器的使用者名和密碼。

   您還可以在連接字串中找到此資訊：

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![創建與 SQL 伺服器的連接](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. 如果您的 SQL 伺服器使用 Windows 或基本驗證，請選擇**身份驗證類型**。

1. 在**閘道**下，選擇與以前創建的本地資料閘道關聯的 Azure 訂閱，然後選擇本地資料閘道的名稱。

   如果您的閘道未出現在清單中，請檢查您是否正確[設定閘道](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)。

   ![創建已完成的 SQL 伺服器連接](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 當您完成時，選取 [建立]****。

1. 您在建立連線之後，請繼續[新增 SQL 觸發程序](#add-sql-trigger)或[新增 SQL 動作](#add-sql-action)。
