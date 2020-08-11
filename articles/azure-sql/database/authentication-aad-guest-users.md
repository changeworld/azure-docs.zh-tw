---
title: 建立 Azure AD 來賓使用者
description: 如何建立 Azure AD 來賓使用者並將其設定為 Azure AD 系統管理員，而不使用 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse 分析中的 Azure AD 群組
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 2c8aa39b6819e135181e2d153825e89686359538
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053757"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>建立 Azure AD 來賓使用者，並將其設定為 Azure AD 管理員

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 此文章目前為**公開預覽狀態**。

Azure Active Directory (Azure AD) 中的來賓使用者是已從其他 Azure Active Directory 或其外部匯入到目前 Azure AD 的使用者。 例如，來賓使用者可以包含來自其他 Azure Active directory 的使用者，或是來自* \@ outlook.com*、 * \@ hotmail.com*、 * \@ live.com*或* \@ gmail.com*等帳戶。 本文將示範如何建立 Azure AD 來賓使用者，並將該使用者設定為 Azure SQL 邏輯伺服器的 Azure AD 管理員，而不需要讓該來賓使用者屬於 Azure AD 內的群組。

## <a name="feature-description"></a>功能說明

這項功能會將目前的限制帶到，當來賓使用者是在 Azure AD 中建立之群組的成員時，才可以連接到 Azure SQL Database、SQL 受控執行個體或 Azure Synapse 分析。 群組需要使用指定資料庫中的[CREATE user (transact-sql) ](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)語句，以手動方式對應至使用者。 一旦為包含來賓使用者的 Azure AD 群組建立資料庫使用者之後，來賓使用者就可以使用 Azure Active Directory 搭配 MFA 驗證來登入資料庫。 在此**公開預覽**中，您可以建立來賓使用者，並直接連接到 SQL DATABASE、SQL 受控執行個體或 Azure Synapse，而不需要先將它們新增至 Azure AD 群組，然後建立該 Azure AD 群組的資料庫使用者。

在此功能中，您也能夠將 Azure AD 來賓使用者直接設定為 Azure SQL 邏輯伺服器的 AD 系統管理員。 現有的功能，其中來賓使用者可以是 Azure AD 群組的一部分，然後該群組可以設定為 Azure SQL 邏輯伺服器的 Azure AD 管理員不會受到影響。 屬於 Azure AD 群組一部分之資料庫中的來賓使用者，也不會受到這項變更的影響。

如需有關使用 Azure AD 群組的來賓使用者現有支援的詳細資訊，請參閱[使用多重要素 Azure Active Directory 驗證](authentication-mfa-ssms-overview.md)。

## <a name="prerequisite"></a>必要條件

- 使用 PowerShell 將來賓使用者設定為 Azure SQL 邏輯伺服器的 Azure AD 管理員時，需要[Az. Sql 2.9.0 版](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)module 或更高版本。

## <a name="create-database-user-for-azure-ad-guest-user"></a>為 Azure AD 來賓使用者建立資料庫使用者 

請遵循下列步驟，使用 Azure AD 來賓使用者來建立資料庫使用者。

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>在 SQL Database 和 Azure Synapse 中建立來賓使用者

1. 請確定來賓使用者 (例如，) 已 `user1@gmail.com` 新增至您的 Azure AD，而且已針對資料庫伺服器設定 Azure AD 系統管理員。 Azure Active Directory 驗證需要有 Azure AD 系統管理員。

1. 以 Azure AD 管理員或具有足夠 SQL 許可權的 Azure AD 使用者身分，連接到 SQL database 以建立使用者，然後在需要新增來賓使用者的資料庫上執行下列命令：

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. 現在應該會有為來賓使用者建立的資料庫使用者 `user1@gmail.com` 。

1. 執行下列命令來確認資料庫使用者已成功建立：

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. 以來賓使用者的身分，使用 SQL Server Management Studio (SSMS) 來中斷連線並登入資料庫，其方式是 `user1@gmail.com` 使用驗證方法**Azure Active Directory-通用搭配 MFA**。 [SQL Server Management Studio (SSMS) ](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 如需詳細資訊，請參閱[使用多因素 Azure Active Directory 驗證](authentication-mfa-ssms-overview.md)。

### <a name="create-guest-user-in-sql-managed-instance"></a>在 SQL 受控執行個體中建立來賓使用者

> [!NOTE]
> SQL 受控執行個體支援 Azure AD 使用者的登入，以及 Azure AD 自主資料庫使用者的登入。 下列步驟示範如何為 SQL 受控執行個體中的 Azure AD 來賓使用者建立登入和使用者。 您也可以選擇使用在[SQL Database 和 Azure Synapse 中建立來賓使用者](#create-guest-user-in-sql-database-and-azure-synapse)一節中的方法，在 SQL 受控執行個體中建立自主[資料庫使用者](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)。

1. 請確定來賓使用者 (例如，) 已 `user1@gmail.com` 新增至您的 Azure AD，而且已針對 SQL 受控執行個體伺服器設定 Azure AD 系統管理員。 Azure Active Directory 驗證需要有 Azure AD 系統管理員。

1. 以 Azure AD 管理員或具有足夠 SQL 許可權的 Azure AD 使用者身分，連接到 SQL 受控執行個體伺服器以建立使用者，並在資料庫上執行下列命令， `master` 以建立來賓使用者的登入：

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. 現在應該會在資料庫中建立來賓使用者的登 `user1@gmail.com` 入 `master` 。

1. 執行下列命令，確認已成功建立登入：

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. 在需要新增來賓使用者的資料庫上執行下列命令： 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. 現在應該會有為來賓使用者建立的資料庫使用者 `user1@gmail.com` 。

1. 以來賓使用者的身分，使用 SQL Server Management Studio (SSMS) 來中斷連線並登入資料庫，其方式是 `user1@gmail.com` 使用驗證方法**Azure Active Directory-通用搭配 MFA**。 [SQL Server Management Studio (SSMS) ](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 如需詳細資訊，請參閱[使用多因素 Azure Active Directory 驗證](authentication-mfa-ssms-overview.md)。

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>將來賓使用者設定為 Azure AD 系統管理員

請遵循下列步驟，將 Azure AD 來賓使用者設定為 SQL 邏輯伺服器的 Azure AD 系統管理員。

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>設定 SQL Database 和 Azure Synapse 的 Azure AD 系統管理員

1. 請確定來賓使用者 (例如，) 已 `user1@gmail.com` 新增至您的 Azure AD。

1. 執行下列 PowerShell 命令，將來賓使用者新增為 Azure SQL 邏輯伺服器的 Azure AD 系統管理員：

    - `<ResourceGroupName>`將取代為包含 AZURE SQL 邏輯伺服器的 Azure 資源組名。
    - `<ServerName>`以您的 AZURE SQL 邏輯伺服器名稱取代。 如果您的伺服器名稱是 `myserver.database.windows.net`，使用 `myserver` 取代 `<Server Name>`。
    - `<DisplayNameOfGuestUser>`以您的來賓使用者名稱取代。

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    您也可以使用 Azure CLI 命令[az sql server ad-admin](https://docs.microsoft.com/cli/azure/sql/server/ad-admin) ，將來賓使用者設定為 Azure sql 邏輯伺服器的 Azure AD 系統管理員。

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>設定 SQL 受控執行個體的 Azure AD 管理員

1. 請確定來賓使用者 (例如，) 已 `user1@gmail.com` 新增至您的 Azure AD。

1. 移至 [ [Azure 入口網站](https://portal.azure.com)]，然後移至您的**Azure Active Directory**資源。 在 [**管理**] 下，移至 [**使用者**] 窗格。 選取您的來賓使用者，並記錄 `Object ID` 。 

1. 執行下列 PowerShell 命令，將來賓使用者新增為 SQL 受控執行個體的 Azure AD 系統管理員：

    - `<ResourceGroupName>`以包含 SQL 受控執行個體的 Azure 資源組名取代。
    - `<ManagedInstanceName>`將取代為您的 SQL 受控執行個體名稱。
    - `<DisplayNameOfGuestUser>`以您的來賓使用者名稱取代。
    - `<AADObjectIDOfGuestUser>`以稍早所搜集的取代 `Object ID` 。

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    您也可以使用 Azure CLI 命令[az sql mi ad-admin](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin) ，將來賓使用者設定為 sql 受控執行個體的 Azure AD 系統管理員。

## <a name="limitations"></a>限制

Azure 入口網站的限制會導致無法選取 Azure AD 來賓使用者作為 SQL 受控執行個體的 Azure AD 管理員。 對於 Azure AD 外部的來賓帳戶，例如* \@ outlook.com*、 * \@ hotmail.com*、 * \@ live.com*或* \@ gmail.com*，AD 系統管理員選取器會顯示這些帳戶，但它們會呈現灰色且無法選取。 使用上述的[PowerShell 或 CLI 命令](#setting-a-guest-user-as-an-azure-ad-admin)來設定 Azure AD 系統管理員。或者，您可以將包含來賓使用者的 Azure AD 群組設定為 SQL 受控執行個體的 Azure AD 系統管理員。

此功能將在此功能正式推出之前，啟用 SQL 受控執行個體。

## <a name="next-steps"></a>後續步驟

- [使用 Azure SQL 設定和管理 Azure AD 驗證](authentication-aad-configure.md)
- [使用多重要素 Azure Active Directory 驗證](authentication-mfa-ssms-overview.md)
- [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
