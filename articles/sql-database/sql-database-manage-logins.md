---
title: 使用登錄名和使用者帳戶授權伺服器和資料庫訪問
description: 瞭解 Azure SQL 資料庫和 Azure 突觸分析如何使用登錄名和使用者帳戶對使用者進行訪問進行身份驗證。 還瞭解如何資料庫角色和明確權限來授權登錄和使用者執行操作和查詢資料。
keywords: sql 資料庫安全性, 資料庫安全性管理, 登入安全性, 資料庫安全性, 資料庫存取權
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/23/2020
ms.openlocfilehash: 98c15fe11b64e8c177e60a2ea1eb7c50eaf69353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124782"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>使用登錄名和使用者帳戶授權經過身份驗證的使用者訪問 SQL 資料庫和 Azure 同步分析

在本文中，您將瞭解：

- 用於配置 Azure SQL 資料庫和 Azure 突觸分析（以前的 Azure SQL 資料倉儲）的選項，使使用者能夠執行管理工作並訪問存儲在這些資料庫中的資料。
- 最初創建新 Azure SQL 資料庫後的訪問和授權配置
- 如何在主資料庫和使用者帳戶中添加登錄名和使用者帳戶，然後授予這些帳戶管理許可權
- 如何添加使用者資料庫中的使用者帳戶（與登錄關聯或作為包含的使用者帳戶）
- 使用資料庫角色和明確權限配置具有使用者資料庫中許可權的使用者帳戶

> [!IMPORTANT]
> 在本文的其餘部分中，Azure SQL 資料庫和 Azure 突觸分析（以前的 Azure SQL 資料倉儲）中的資料庫統稱為資料庫或 Azure SQL（為了簡單起見）。

## <a name="authentication-and-authorization"></a>驗證和授權

[**身份驗證**](sql-database-security-overview.md#authentication)是證明使用者是他們聲稱是誰的過程。 使用者使用使用者帳戶連接到資料庫。
當使用者嘗試連接到資料庫時，他們提供使用者帳戶和身份驗證資訊。 使用以下兩種身份驗證方法之一對使用者進行身份驗證：

- [SQL 身份驗證](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)。

  使用此身份驗證方法，使用者提交使用者帳戶名稱和相關密碼以建立連接。 此密碼存儲在主資料庫中，用於連結到登錄名或存儲在資料庫中的使用者帳戶，其中包含未連結到登錄的使用者帳戶的使用者帳戶。
- [Azure Active Directory 驗證](sql-database-aad-authentication.md)

  使用此身份驗證方法，使用者提交使用者帳戶名稱，並要求服務使用存儲在 Azure 活動目錄中的憑據資訊。

**登錄和使用者**：在 Azure SQL 中，資料庫中的使用者帳戶可以與存儲在主資料庫中的登錄名相關聯，也可以是存儲在單個資料庫中的使用者名。

- **登錄**名是主資料庫中的單個帳戶，可以連結到一個或多個資料庫中的使用者帳戶。 使用登錄名，使用者帳戶的憑據資訊將隨登錄一起存儲。
- **使用者帳戶**是任何資料庫中可能但不必連結到登錄的單個帳戶。 對於未連結到登錄的使用者帳戶，憑據資訊將隨使用者帳戶一起存儲。

[**Authorization**](sql-database-security-overview.md#authorization)使用資料庫角色和明確權限管理訪問資料和執行各種操作的授權。 授權是指分配給使用者的許可權，並確定允許使用者做什麼。 授權由使用者帳戶的資料庫[角色成員身份](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)和[物件級許可權](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)控制。 最好的作法是，您應該授與使用者所需的最低權限。

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>創建新資料庫後的現有登錄名和使用者帳戶

創建第一個 Azure SQL 部署時，會為該登錄名指定管理員登錄名和關聯的密碼。 此管理帳戶稱為**伺服器管理員**。在部署期間，主資料庫和使用者資料庫中的登錄名和使用者進行以下配置：

- 使用指定的登錄名創建具有管理許可權的 SQL 登錄名。 [登錄](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login)名是登錄到 SQL 資料庫的單個使用者帳戶。
- 此登錄作為[伺服器級主體](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)被授予所有資料庫的完整管理許可權。 此登錄具有 SQL 資料庫中的所有可用許可權，並且不能受到限制。 在託管實例中，此登錄將添加到[sysadmin 固定伺服器角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)（單個或池資料庫不存在此角色）。
- 為每個[user account](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users)使用者資料庫中的`dbo`此登錄創建調用的使用者帳戶。 [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)使用者具有資料庫中的所有資料庫許可權，並映射到`db_owner`固定資料庫角色。 本文稍後將討論其他固定資料庫角色。

要標識資料庫的管理員帳戶，請打開 Azure 門戶，然後導航到伺服器或託管實例的 **"屬性**"選項卡。

![SQL Server 系統管理員](media/sql-database-manage-logins/sql-admins.png)

![SQL Server 系統管理員](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> 創建管理員登錄名後無法更改。 要重置邏輯伺服器管理員的密碼，請轉到 Azure[門戶](https://portal.azure.com)，按一下**SQL 伺服器**，從清單中選擇伺服器，然後按一下"**重置密碼**"。 要重置託管實例伺服器的密碼，請轉到 Azure 門戶，按一下實例，然後按一下 **"重置密碼**"。 您還可以使用 PowerShell 或 Azure CLI。

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>創建其他登錄名和具有管理許可權的使用者

此時，Azure SQL 實例僅配置為使用單個 SQL 登錄名和使用者帳戶進行訪問。 要創建具有完全或部分管理許可權的其他登錄名，請提供以下選項（具體取決於部署模式）：

- **創建具有完全管理許可權的 Azure 活動目錄管理員帳戶**

  啟用 Azure 活動目錄身份驗證並創建 Azure AD 管理員登錄名。 一個 Azure 活動目錄帳戶可以配置為具有完全管理許可權的 SQL 資料庫部署的管理員。 此帳戶可以是個人或安全性群組帳戶。 如果要使用 Azure AD 帳戶連接到 SQL 資料庫，**則必須**配置 Azure AD 管理員。 有關為所有 SQL 資料庫部署類型啟用 Azure AD 身份驗證的詳細資訊，請參閱以下文章：

  - [使用適用於 SQL 驗證的 Azure Active Directory Authentication](sql-database-aad-authentication.md)
  - [使用 SQL 設定及管理 Azure Active Directory 驗證](sql-database-aad-authentication-configure.md)

- **在託管實例部署中，創建具有完全管理許可權的 SQL 登錄**

  - 在託管實例中創建其他 SQL Server 登入名
  - 使用[ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)語句將登錄添加到[系統管理員固定伺服器角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)。 此登錄將具有完全的管理許可權。
  - 或者，使用<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">創建登錄</a>語法創建 Azure AD[登錄名](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi)。

- **在單個或池部署中，創建具有有限管理許可權的 SQL 登錄**

  - 為單個或池資料庫部署或託管實例部署在主資料庫中創建其他 SQL 登錄
  - 在主資料庫中創建與此新登錄關聯的使用者帳戶
  - 使用[ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) `loginmanager`語句將使用者帳戶添加到`dbmanager`、`master`角色或資料庫中的兩者（對於 Azure Synapse 分析，請使用[sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)語句）。

  > [!NOTE]
  > `dbmanager`角色`loginmanager`與託管實例部署**無關**。

  這些用於單個或池資料庫[的特殊主資料庫角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-)的成員使使用者可以有權創建和管理資料庫或創建和管理登錄。 在由`dbmanager`作為角色成員的使用者創建的資料庫中，成員映射到`db_owner`固定資料庫角色，並可以使用`dbo`使用者帳戶登錄和管理該資料庫。 這些角色在主資料庫之外沒有明確權限。

  > [!IMPORTANT]
  > 您不能在單個或池資料庫中創建具有完全管理許可權的其他 SQL 登錄。

## <a name="create-accounts-for-non-administrator-users"></a>為非管理員使用者創建帳戶

您可以使用兩種方法之一為非管理使用者創建帳戶：

- **創建登錄名**

  在主資料庫中創建 SQL 登錄名。 然後在每個資料庫中創建一個使用者帳戶，該使用者需要訪問該使用者帳戶，並將使用者帳戶與該登錄名相關聯。 當使用者必須訪問多個資料庫並且您希望保持密碼同步時，此方法是首選。 但是，當與異地複製一起使用時，此方法非常複雜，因為登錄必須在主伺服器和次要伺服器上創建。 有關詳細資訊，請參閱[配置和管理 Azure SQL 資料庫安全性，以便進行異地還原或容錯移轉](sql-database-geo-replication-security-config.md)。
- **建立使用者帳戶**

  在資料庫中創建使用者需要訪問的使用者帳戶（也稱為[包含使用者](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)）。

  - 使用單個或池資料庫，您始終可以創建這種類型的使用者帳戶。
  - 使用不支援[Azure AD 伺服器主體](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)的託管實例資料庫，只能在[包含的資料庫中](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)創建這種類型的使用者帳戶。 使用支援[Azure AD 伺服器主體](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)的託管實例，您可以創建使用者帳戶以對託管實例進行身份驗證，而無需將資料庫使用者創建為包含的資料庫使用者。

  使用此方法，使用者身份驗證資訊存儲在每個資料庫中，並自動複製到異地複製的資料庫。 但是，如果同一帳戶存在於多個資料庫中，並且您使用的是 SQL 身份驗證，則必須手動保持密碼同步。 此外，如果使用者在不同的資料庫中具有不同的密碼，記住這些密碼可能會成為一個問題。

> [!IMPORTANT]
> 要創建映射到 Azure AD 標識的包含使用者，必須使用 SQL 資料庫中的管理員的 Azure AD 帳戶登錄。 在託管實例中，具有`sysadmin`許可權的 SQL 登錄名也可以創建 Azure AD 登錄名或使用者。

有關如何創建登錄名和使用者的示例，請參閱：

- [為單個或池資料庫創建登錄名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [為託管實例資料庫創建登錄名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [為 Azure 突觸分析資料庫創建登錄名](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [創建使用者](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [創建 Azure AD 包含的使用者](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> 有關安全教程，其中包括在單個或池資料庫中創建 SQL Server 包含的使用者，請參閱[教程：保護單個或池資料庫](sql-database-security-tutorial.md)。

## <a name="using-fixed-and-custom-database-roles"></a>使用固定和自訂資料庫角色

在資料庫中創建使用者帳戶後（基於登錄名或作為包含使用者）後，可以授權該使用者執行各種操作並訪問特定資料庫中的資料。 您可以使用以下方法授權訪問：

- **固定資料庫角色**

  將使用者帳戶添加到[固定資料庫角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)。 有 9 個固定資料庫角色，每個角色都有一組定義的許可權。 最常見的固定資料庫角色是：db_owner、db_ddladmin、db_datawriter、db_datareader、db_denydatawriter和**db_denydatawriter****db_denydatareader。** **db_owner** **db_ddladmin** **db_datawriter** **db_datareader** **db_owner** 通常是用來將完整權限授與少數幾個使用者。 其他固定的資料庫角色適用於快速開發簡單的資料庫，但不建議用於大多數實際執行資料庫。 例如 **，db_datareader**固定資料庫角色授予對資料庫中每個表的讀取存取許可權，這比絕對必要的許可權要大。

  - 要將使用者添加到固定資料庫角色：

    - 在 Azure SQL 資料庫中，使用[ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)語句。 有關示例，請參閱[ALTER ROLE 示例](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure 同步分析，使用[sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)語句。 有關示例，請參閱[sp_addrolemember示例](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)。

- **自訂資料庫角色**

  使用[CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)語句創建自訂資料庫角色。 自訂角色使您能夠創建自己的使用者定義的資料庫角色，並仔細為每個角色授予業務需求所需的最少許可權。 然後，您可以將使用者添加到自訂角色。 當使用者是多個角色的成員時，會集所有這些角色的權限在一身。
- **直接授予許可權**

  直接授予使用者帳戶[許可權](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)。 有超過 100 個權限可在 SQL Database 中分別授與或拒絕。 這些權限有許多為巢狀。 例如，結構描述上的 `UPDATE` 權限包括該結構描述中每個資料表的 `UPDATE` 權限。 如同大多數的權限系統，拒絕權限會覆寫授與權限。 因為權限的巢狀本質和數目，可能需要仔細研究，設計適當的權限系統以便適當地保護您的資料庫。 請從[權限 (Database Engine)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) 的權限清單開始著手，然後檢閱[海報大小的權限圖](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png)。

## <a name="using-groups"></a>使用群組

高效訪問管理使用分配給 Active Directory 安全性群組的許可權以及固定或自訂角色，而不是單個使用者。

- 使用 Azure 活動目錄身份驗證時，將 Azure 活動目錄使用者放入 Azure 活動目錄安全性群組。 建立群組的自主資料庫使用者。 將一個或多個資料庫使用者放入具有適合該使用者組的特定許可權的自訂資料庫角色中。

- 使用 SQL 身份驗證時，在資料庫中創建包含的資料庫使用者。 將一個或多個資料庫使用者放入具有適合該使用者組的特定許可權的自訂資料庫角色中。

  > [!NOTE]
  > 您還可以將組用於非包含的資料庫使用者。

您應該熟悉下列功能，這些功能可用來限制或提高權限︰

- [模擬](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server)和[模組簽署](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)可用來安全地暫時提升權限。
- [資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) 可用於使用者可存取資料列的限制。
- [資料遮罩](sql-database-dynamic-data-masking-get-started.md) 可用來限制公開機密資料。
- [預存程序](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 可用來限制對資料庫可採取的動作。

## <a name="next-steps"></a>後續步驟

如需所有 SQL Database 安全性功能的概觀，請參閱 [SQL 安全性概觀](sql-database-security-overview.md)。
