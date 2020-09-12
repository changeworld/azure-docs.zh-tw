---
title: 使用登入和使用者帳戶授權伺服器和資料庫存取
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: 瞭解 Azure SQL Database、SQL 受控執行個體和 Azure Synapse 如何使用登入和使用者帳戶來驗證使用者的存取權。 同時瞭解如何授與資料庫角色和明確許可權，以授權登入和使用者執行動作和查詢資料。
keywords: sql 資料庫安全性, 資料庫安全性管理, 登入安全性, 資料庫安全性, 資料庫存取權
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/23/2020
ms.openlocfilehash: 891d5907ee8c964ebe7e281f6298205712ce1186
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441165"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>授權資料庫存取 SQL Database、SQL 受控執行個體和 Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

在本文中，您將瞭解：

- 設定 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics (先前的 SQL 資料) 倉儲的選項，可讓使用者執行系統管理工作，以及存取儲存在這些資料庫中的資料。
- 初次建立新伺服器之後的存取和授權設定。
- 如何在 master 資料庫和使用者帳戶中新增登入和使用者帳戶，然後將系統管理許可權授與這些帳戶。
- 如何新增使用者資料庫中的使用者帳戶（與登入相關聯或以包含的使用者帳戶建立關聯）。
- 使用資料庫角色和明確許可權，在使用者資料庫中設定具有許可權的使用者帳戶。

> [!IMPORTANT]
> 本文的其餘部分會將 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse 中的資料庫統稱為資料庫，而且伺服器會參考管理資料庫的 [伺服器](logical-servers.md) ，以進行 Azure SQL Database 和 Azure Synapse。

## <a name="authentication-and-authorization"></a>驗證和授權

[**驗證**](security-overview.md#authentication) 是證明使用者是他們宣稱的流程。 使用者會使用使用者帳戶連接至資料庫。
當使用者嘗試連接至資料庫時，會提供使用者帳戶和驗證資訊。 使用者會使用下列兩種驗證方法的其中一種進行驗證：

- [SQL 驗證](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)。

  使用此驗證方法時，使用者會提交使用者帳戶名稱和相關聯的密碼以建立連線。 此密碼會儲存在 master 資料庫中，以用於連結至登入的使用者帳戶，或儲存在包含 *未* 連結至登入之使用者帳戶的資料庫中。
- [Azure Active Directory 驗證](authentication-aad-overview.md)

  使用此驗證方法，使用者會提交使用者帳戶名稱，並要求服務使用儲存在 Azure Active Directory (Azure AD) 中的認證資訊。

登入**和使用者**：資料庫中的使用者帳戶可以與儲存在 master 資料庫中的登入相關聯，也可以是儲存在個別資料庫中的使用者名稱。

- **登**入是 master 資料庫中的個別帳戶，可在其中連結一或多個資料庫中的使用者帳戶。 登入時，使用者帳戶的認證資訊會與登入一起儲存。
- **使用者帳戶**是任何資料庫中的個別帳戶，但不一定要連結到登入。 使用未連結至登入的使用者帳戶時，認證資訊會與使用者帳戶一起儲存。

使用資料庫角色和明確許可權來管理存取資料和執行各種動作的[**授權**](security-overview.md#authorization)。 授權是指指派給使用者的許可權，並決定該使用者允許執行的動作。 授權是由使用者帳戶的資料庫 [角色成員資格](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) 和 [物件層級許可權](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)所控制。 最好的作法是，您應該授與使用者所需的最低權限。

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>建立新資料庫之後的現有登入和使用者帳戶

當您第一次部署 Azure SQL 時，您會為該登入指定管理員登入和相關聯的密碼。 此系統管理帳戶稱為「 **伺服器管理員**」。在部署期間，主資料庫和使用者資料庫中的登入和使用者會進行下列設定：

- 系統會使用您指定的登入名稱來建立具有系統管理許可權的 SQL 登入。 [登](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login)入是個別的使用者帳戶，用於登入 SQL DATABASE、SQL 受控執行個體和 Azure Synapse。
- 這個登入是以 [伺服器層級主體](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)的形式，授與所有資料庫的完整系統管理許可權。 登入具有所有可用的許可權，而且不受限制。 在 SQL 受控執行個體中，此登入會新增至 [系統管理員（sysadmin）固定伺服器角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) ， (此角色不存在於 Azure SQL Database) 中。
- [user account](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) `dbo` 針對每個使用者資料庫中的此登入，建立名為的使用者帳戶。 [Dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)使用者擁有資料庫中的所有資料庫許可權，而且會對應至 `db_owner` 固定資料庫角色。 本文稍後將討論其他固定資料庫角色。

若要識別資料庫的系統管理員帳戶，請開啟 Azure 入口網站，然後流覽至伺服器或受控實例的 [ **屬性** ] 索引標籤。

![SQL Server 系統管理員](./media/logins-create-manage/sql-admins.png)

![SQL Server 系統管理員](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> 系統管理員登入名稱在建立後即無法變更。 若要重設伺服器管理員的密碼，請移至 [Azure 入口網站](https://portal.azure.com)、按一下 [SQL Server]****、從清單中選取伺服器，然後按一下 [重設密碼]****。 若要重設 SQL 受控執行個體的密碼，請移至 [Azure 入口網站]，按一下該實例，然後按一下 [ **重設密碼**]。 您也可以使用 PowerShell 或 Azure CLI。

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>建立具有系統管理許可權的其他登入和使用者

此時，您的伺服器或受控實例僅設定為使用單一 SQL 登入和使用者帳戶進行存取。 若要建立具有完整或部分系統管理許可權的其他登入，您可以根據部署模式) ， (下列選項：

- **建立具有完整系統管理許可權的 Azure Active Directory 系統管理員帳戶**

  啟用 Azure Active Directory authentication，並建立 Azure AD 系統管理員登入。 您可以使用完整的系統管理許可權，將一個 Azure Active Directory 帳戶設定為 Azure SQL 部署的系統管理員。 此帳戶可以是個別或安全性群組帳戶。 如果您想要使用 Azure AD 帳戶連接到 SQL Database、SQL 受控執行個體或 Azure Synapse，則 **必須** 設定 Azure AD 系統管理員。 如需針對所有 Azure SQL 部署類型啟用 Azure AD 驗證的詳細資訊，請參閱下列文章：

  - [使用 Azure Active Directory authentication 進行 SQL 驗證](authentication-aad-overview.md)
  - [使用 SQL 設定及管理 Azure Active Directory 驗證](authentication-aad-configure.md)

- **在 SQL 受控執行個體中，建立具有完整系統管理許可權的 SQL 登入**

  - 在 master 資料庫中建立額外的 SQL 登入。
  - 使用[ALTER SERVER role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)語句，將登入加入至[系統管理員（sysadmin）固定伺服器角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)。 此登入將擁有完整的系統管理許可權。
  - 或者，使用[CREATE login](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)語法建立[Azure AD 登](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)入。

- **在 SQL Database 中，建立具有有限系統管理許可權的 SQL 登入**

  - 在 master 資料庫中建立額外的 SQL 登入。
  - 在與這個新登入相關聯的 master 資料庫中建立使用者帳戶。
  - `dbmanager` `loginmanager` `master` 使用適用于 Azure SYNAPSE 的[ALTER role](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)語句 (，在資料庫中將使用者帳戶加入至或角色，然後使用[sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)語句) 。

  > [!NOTE]
  > `dbmanager`和 `loginmanager` 角色與**not** SQL 受控執行個體部署無關。

  這些 [特殊 master 資料庫角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) 的成員，Azure SQL Database 有權建立和管理資料庫，或是建立和管理登入。 在屬於角色成員之使用者所建立的資料庫中 `dbmanager` ，該成員會對應至 `db_owner` 固定資料庫角色，而且可以使用使用者帳戶登入並管理該資料庫 `dbo` 。 這些角色沒有 master 資料庫外的明確許可權。

  > [!IMPORTANT]
  > 您無法在 SQL Database 中建立具有完整系統管理許可權的其他 SQL 登入。

## <a name="create-accounts-for-non-administrator-users"></a>為非系統管理員的使用者建立帳戶

您可以使用下列兩種方法之一，為非系統管理使用者建立帳戶：

- **建立登入**

  在 master 資料庫中建立 SQL 登入。 然後，在該使用者需要存取的每個資料庫中建立使用者帳戶，並將使用者帳戶與該登入產生關聯。 當使用者必須存取多個資料庫，而且您想要讓密碼保持同步時，建議使用此方法。 不過，與異地複寫搭配使用時，此方法會很複雜，因為必須在主伺服器和次要伺服器上建立登入， (s) 。 如需詳細資訊，請參閱 [設定和管理異地還原或容錯移轉的 Azure SQL Database 安全性](active-geo-replication-security-configure.md)。
- **建立使用者帳戶**

  在使用者需要存取的資料庫中建立使用者帳戶 (也稱為 [包含的使用者](/sql/relational-databases/security/contained-database-users-making-your-database-portable)) 。

  - 使用 SQL Database，您一律可以建立這種類型的使用者帳戶。
  - 使用支援 [Azure AD 伺服器主體](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)的 SQL 受控執行個體時，您可以建立使用者帳戶來向 sql 受控執行個體進行驗證，而不需要將資料庫使用者建立為自主資料庫使用者。

  使用這個方法時，使用者驗證資訊會儲存在每個資料庫中，並自動複寫到異地複寫的資料庫。 但是，如果多個資料庫中有相同的帳戶，而您使用的是 Azure SQL 驗證，則您必須將密碼手動同步處理。 此外，如果使用者在不同的資料庫中擁有不同密碼的帳戶，請記住這些密碼可能會變成問題。

> [!IMPORTANT]
> 若要建立對應至 Azure AD 身分識別的內含使用者，您必須使用 Azure SQL Database 的資料庫中的系統管理員 Azure AD 帳戶登入。 在 SQL 受控執行個體中，具有許可權的 SQL 登入 `sysadmin` 也可以建立 Azure AD 登入或使用者。

如需顯示如何建立登入和使用者的範例，請參閱：

- [建立 Azure SQL Database 的登入](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [建立 Azure SQL 受控執行個體的登入](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [為 Azure Synapse 建立登入](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [建立使用者](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [建立 Azure AD 包含的使用者](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> 如需包含在 Azure SQL Database 中建立使用者的安全性教學課程，請參閱 [教學課程：安全 Azure SQL Database](secure-database-tutorial.md)。

## <a name="using-fixed-and-custom-database-roles"></a>使用固定和自訂資料庫角色

在資料庫中建立使用者帳戶之後，您可以根據登入或包含的使用者，授權該使用者執行各種動作，以及存取特定資料庫中的資料。 您可以使用下列方法來授權存取：

- **固定資料庫角色**

  將使用者帳戶加入至 [固定資料庫角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)。 有9個固定資料庫角色，每個角色都有一組已定義的許可權。 最常見的固定資料庫角色包括： **db_owner**、 **db_ddladmin**、 **db_datawriter**、 **db_datareader**、 **db_denydatawriter**和 **db_denydatareader**。 **db_owner** 通常是用來將完整權限授與少數幾個使用者。 其他固定的資料庫角色適用於快速開發簡單的資料庫，但不建議用於大多數實際執行資料庫。 例如， **db_datareader** 固定資料庫角色會授與資料庫中每個資料表的讀取存取權，這是絕對必要的。

  - 若要將使用者加入至固定資料庫角色：

    - 在 Azure SQL Database 中，請使用 [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) 語句。 如需範例，請參閱 [ALTER ROLE 範例](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse，請使用 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) 語句。 如需範例，請參閱 [sp_addrolemember 範例](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)。

- **自訂資料庫角色**

  使用 [CREATE role](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) 語句來建立自訂資料庫角色。 自訂角色可讓您建立自己的使用者定義資料庫角色，並為每個角色謹慎授與商務需求所需的最低許可權。 然後，您可以將使用者新增至自訂角色。 當使用者是多個角色的成員時，會集所有這些角色的權限在一身。
- **直接授與許可權**

  直接授與使用者帳戶 [許可權](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) 。 有超過 100 個權限可在 SQL Database 中分別授與或拒絕。 這些權限有許多為巢狀。 例如，結構描述上的 `UPDATE` 權限包括該結構描述中每個資料表的 `UPDATE` 權限。 如同大多數的權限系統，拒絕權限會覆寫授與權限。 因為權限的巢狀本質和數目，可能需要仔細研究，設計適當的權限系統以便適當地保護您的資料庫。 請從[權限 (Database Engine)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) 的權限清單開始著手，然後檢閱[海報大小的權限圖](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png)。

## <a name="using-groups"></a>使用群組

有效率的存取管理會使用指派給 Active Directory 安全性群組和固定或自訂角色的許可權，而不是個別使用者。

- 使用 Azure Active Directory authentication 時，請將 Azure Active Directory 使用者放入 Azure Active Directory 的安全性群組。 建立群組的自主資料庫使用者。 將一或多個資料庫使用者新增為自訂或內建資料庫角色的成員，並具有適用于該使用者群組的特定許可權。

- 使用 SQL 驗證時，請在資料庫中建立自主資料庫使用者。 將一或多個資料庫使用者放入具有適用于該使用者群組之特定許可權的自訂資料庫角色。

  > [!NOTE]
  > 您也可以使用非自主資料庫使用者的群組。

您應該熟悉下列功能，這些功能可用來限制或提高權限︰

- [模擬](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server)和[模組簽署](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)可用來安全地暫時提升權限。
- [資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) 可用於使用者可存取資料列的限制。
- [資料遮罩](dynamic-data-masking-overview.md) 可用來限制公開機密資料。
- [預存程序](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 可用來限制對資料庫可採取的動作。

## <a name="next-steps"></a>接下來的步驟

如需所有 Azure SQL Database 和 SQL 受控執行個體安全性功能的總覽，請參閱 [安全性總覽](security-overview.md)。
