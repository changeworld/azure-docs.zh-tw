---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789186"
---
* 如果您是使用 Azure SQL Database，請遵循[連線到 Azure SQL Database](#connect-azure-sql-db) 下的步驟。

* 如果您是使用 SQL Server，請遵循[連線到 SQL Server](#connect-sql-server) 下的步驟。

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>連線到 Azure SQL Database

當 SQL 觸發程式或動作提示您輸入連線資訊時，請遵循這些步驟，這適用于觸發程式和動作。

1. 針對 [**連接名稱**]，建立連線的名稱。

1. 在 [ **SQL Server 名稱**] 底下，選取您的 Azure SQL 伺服器。 當 [ **SQL Database 名稱**] 清單出現時，請選取您的資料庫。 提供 Azure SQL 伺服器的使用者名稱和密碼。

   您也可以在 SQL database 屬性底下的 Azure 入口網站中，或在您的連接字串中找到這項資訊：

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![建立 Azure SQL Database 的連接](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 當您完成時，選取 [建立]  。

1. 建立連線之後，請繼續[新增 sql 觸發](#add-sql-trigger)程式或[新增 sql 動作](#add-sql-action)。

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>連接至 SQL Server

當 SQL 觸發程式或動作提示您輸入連線資訊時，請遵循這些步驟，這適用于觸發程式和動作。 針對需要在本機電腦上安裝內部[部署資料閘道](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install)，並[建立 Azure 資料閘道資源](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)的案例，請務必先完成這些需求。 否則，當您建立連線時，您的閘道資源不會出現在閘道清單中。

此外，若要在[整合服務環境（ISE）](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)中搭配使用 Windows 驗證與 SQL Server 連接器，請使用連接器的非 ISE 版本和內部部署資料閘道。 ISE 標記的版本不支援 Windows 驗證。

1. 針對 [**連接名稱**]，建立連線的名稱。

1. 在觸發程序或動作中，選取**透過內部部署資料閘道連線**，如此就會顯示 SQL 伺服器選項。

1. 針對 **[sql Server 名稱**] 和 **[sql database 名稱**]，提供您的 SQL server 位址和資料庫名稱。 針對 **[使用者名稱]** 和 [**密碼**]，提供您伺服器的使用者名稱和密碼。

   您也可以在連接字串中找到這項資訊：

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![建立 SQL Server 的連接](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. 如果您的 SQL server 使用 Windows 或基本驗證，請選取**驗證類型**。

1. 在 [**閘道**] 底下，選取與您先前建立的內部部署資料閘道相關聯的 Azure 訂用帳戶，然後選取內部部署資料閘道的名稱。

   如果您的閘道未出現在清單中，請檢查您是否正確[設定閘道](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)。

   ![建立 SQL Server 連接已完成](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 當您完成時，選取 [建立]  。

1. 您在建立連線之後，請繼續[新增 SQL 觸發程序](#add-sql-trigger)或[新增 SQL 動作](#add-sql-action)。
