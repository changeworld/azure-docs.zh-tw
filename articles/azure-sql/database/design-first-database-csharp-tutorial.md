---
title: 使用 C# 設計您的第一個關聯式資料庫
description: 了解如何使用 ADO.NET 搭配 C# 在 Azure SQL Database 中設計您的第一個關聯式資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-csharp
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 07/29/2019
ms.openlocfilehash: 4b3235f457f1c6475c18045886c49d3dd2ca2242
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671173"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-cx23-and-adonet"></a>教學課程：使用 C&#x23; 和 ADO.NET 在 Azure SQL Database 中設計關聯式資料庫
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 是 Microsoft Cloud (Azure) 中的關聯式資料庫即服務 (DBaaS)。 在本教學課程裡，您將了解如何搭配使用 Visual Studio 與 Azure 入口網站和 ADO.NET 執行下列操作：

> [!div class="checklist"]
>
> * 使用 Azure 入口網站建立資料庫
> * 使用 Azure 入口網站設定伺服器層級的 IP 防火牆規則
> * 使用 ADO.NET 和 Visual Studio 連線到資料庫
> * 使用 ADO.NET 建立資料表
> * 使用 ADO.NET 插入、更新和刪除資料
> * 使用 ADO.NET 查詢資料

*如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。

> [!TIP]
> 下列 Microsoft Learn 模組可協助您免費學習如何[開發和設定可查詢 Azure SQL Database 的 ASP.NET 應用程式](/learn/modules/develop-app-that-queries-azure-sql/) (包括建立簡易資料庫)。

## <a name="prerequisites"></a>必要條件

[Visual Studio 2019](https://www.visualstudio.com/downloads/) 或更新版本的安裝。

## <a name="create-a-blank-database-in-azure-sql-database"></a>在 Azure SQL Database 中建立空白的資料庫

使用一組定義的計算和儲存體資源建立 Azure SQL Database 的資料庫。 此資料庫建立於 [Azure 資源群組](../../active-directory-b2c/overview.md)內，並使用[邏輯 SQL 伺服器](logical-servers.md)進行管理。

遵循以下步驟來建立空白資料庫。

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 在 [新增] 頁面上，選取 [Azure Marketplace] 區段中的 [資料庫]，然後按一下 [精選] 區段中的 [SQL Database]。

   ![建立空白資料庫](./media/design-first-database-csharp-tutorial/create-empty-database.png)

3. 使用下列資訊填寫 **SQL Database** 表單，如上圖所示︰

    | 設定       | 建議的值 | 描述 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **資料庫名稱** | *yourDatabase* | 如需有效的資料庫名稱，請參閱[資料庫識別碼](/sql/relational-databases/databases/database-identifiers)。 |
    | **訂用帳戶** | *yourSubscription*  | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
    | **資源群組** | *yourResourceGroup* | 如需有效的資源群組名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。 |
    | **選取來源** | 空白資料庫 | 指定應建立空白資料庫。 |

4. 按一下 [伺服器] 來使用現有伺服器，或建立及設定新的伺服器。 選取現有的伺服器，或按一下 [建立新伺服器] 並且在 [新伺服器] 表單中填寫下列資訊︰

    | 設定       | 建議的值 | 描述 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **伺服器名稱** | 任何全域唯一名稱 | 如需有效的伺服器名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。 |
    | **伺服器管理員登入** | 任何有效名稱 | 如需有效的登入名稱，請參閱[資料庫識別碼](/sql/relational-databases/databases/database-identifiers)。 |
    | **密碼** | 任何有效密碼 | 您的密碼至少要有 8 個字元，而且必須使用下列幾種字元的其中三種︰大寫字元、小寫字元、數字和非英數字元。 |
    | **位置** | 任何有效位置 | 如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。 |

    ![建立資料庫伺服器](./media/design-first-database-csharp-tutorial/create-database-server.png)

5. 按一下 [選取]。
6. 按一下 [定價層] 可指定服務層、DTU 或虛擬核心的數目，以及儲存體數量。 您可以瀏覽 DTU/虛擬核心數目的選項，以及可供您每個服務層級使用的儲存體。

    在選取服務層、DTU 或 vCore 數目和儲存體數量之後，按一下 [套用]。

7. 為空白資料庫輸入 [定序]\(在此教學課程中使用預設值)。 如需定序的詳細資訊，請參閱[定序](/sql/t-sql/statements/collations)。

8. 您現在已完成 **SQL Database** 表單，請按一下 [建立] 來佈建資料庫。 這個步驟可能需要幾分鐘的時間。

9. 在工具列上，按一下 [通知] 以監視部署程序。

   ![此螢幕擷取畫面顯示正在進行部署的 Azure 入口網站中的通知。](./media/design-first-database-csharp-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>建立伺服器層級 IP 防火牆規則

SQL Database 會在伺服器層級建立 IP 防火牆。 此防火牆會防止外部應用程式和工具連線到伺服器及伺服器上的任何資料庫，除非防火牆規則允許其 IP 通過防火牆。 若要啟用對資料庫的外部連線，您必須先新增 IP 位址 (或 IP 位址範圍) 的 IP 防火牆規則新增。 依照下列步驟來建立[伺服器層級的 IP 防火牆規則](firewall-configure.md)。

> [!IMPORTANT]
> SQL Database 會透過連接埠 1433 通訊。 如果您嘗試從公司網路連線到這項服務，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 若情況如此，除非系統管理員開啟連接埠 1433，否則您無法連線至您的資料庫。

1. 部署完成之後，按一下左側功能表中的 [SQL 資料庫]，然後按一下 [SQL 資料庫] 頁面上的 [yourDatabase]。 資料庫的概觀頁面隨即開啟，其中會顯示完整 **伺服器名稱** (例如 *yourserver.database.windows.net* )，並提供進一步的組態選項。

2. 請複製此完整伺服器名稱，以便從 SQL Server Management Studio 連線到伺服器和資料庫。

   ![伺服器名稱](./media/design-first-database-csharp-tutorial/server-name.png)

3. 在工具列上按一下 [設定伺服器防火牆]。 伺服器的 [防火牆設定] 頁面會隨即開啟。

   ![伺服器層級 IP 防火牆規則](./media/design-first-database-csharp-tutorial/server-firewall-rule.png)

4. 按一下工具列上的 [新增用戶端 IP]，將目前的 IP 位址新增至新的 IP 防火牆規則。 IP 防火牆規則可以針對單一 IP 位址或 IP 位址範圍開啟連接埠 1433。

5. 按一下 [檔案] 。 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則，以便在伺服器上開啟連接埠 1433。

6. 依序按一下 [確定]，然後關閉 [防火牆設定] 頁面。

您的 IP 位址現在可以通過 IP 防火牆。 您現在可以使用 SQL Server Management Studio 或您選擇的其他工具來連線至您的資料庫。 務必使用先前建立的伺服器管理帳戶。

> [!IMPORTANT]
> 根據預設，已對所有 Azure 服務啟用透過 SQL Database IP 防火牆存取。 按一下此頁面上的 [關閉] 即可對所有 Azure 服務停用存取權。

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解基本的資料庫工作，例如建立資料庫和資料表、連線至資料庫、載入資料，以及執行查詢。 您已了解如何︰

> [!div class="checklist"]
>
> * 使用 Azure 入口網站建立資料庫
> * 使用 Azure 入口網站設定伺服器層級的 IP 防火牆規則
> * 使用 ADO.NET 和 Visual Studio 連線到資料庫
> * 使用 ADO.NET 建立資料表
> * 使用 ADO.NET 插入、更新和刪除資料
> * 使用 ADO.NET 查詢資料

請前進到下一個教學課程，以了解資料移轉。

> [!div class="nextstepaction"]
> [使用 DMS 在離線狀態下將 SQL Server 遷移至 Azure SQL Database](../../dms/tutorial-sql-server-to-azure-sql.md)