---
title: 使用 Azure SQL 的 Azure Active Directory 服務主體
description: Azure AD 應用程式 (服務主體) 支援 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 中 Azure AD 使用者建立
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: d8268ebf89bed6b67919e77576118343b58edb6c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516617"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>使用 Azure SQL 的 Azure Active Directory 服務主體

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

支援 Azure Active Directory (Azure AD) Azure SQL Database (的 SQL DB) Azure Synapse Analytics Azure AD (的應用 [程式) 的](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 的應用程式中建立使用者，目前處於 **公開預覽**狀態。

> [!NOTE]
> SQL 受控執行個體已支援這項功能。

## <a name="service-principal-azure-ad-applications-support"></a>服務主體 (Azure AD 應用程式) 支援

本文適用于與 Azure AD 整合的應用程式，而且是 Azure AD 註冊的一部分。 這些應用程式通常需要對 Azure SQL 進行驗證和授權存取，才能執行各種工作。 此 **公開預覽** 功能現在可讓服務主體在 SQL Database 和 Azure Synapse 中建立 Azure AD 使用者。 有一項限制會代表已移除 Azure AD 的應用程式，而導致 Azure AD 物件建立。

使用 Azure 入口網站或 PowerShell 命令註冊 Azure AD 的應用程式時，Azure AD 租使用者中會建立兩個物件：

- 應用程式物件
- 服務主體物件

如需 Azure AD 應用程式的詳細資訊，請參閱 [Azure Active Directory 中的應用程式和服務主體物件](../../active-directory/develop/app-objects-and-service-principals.md) ，並 [使用 Azure PowerShell 建立 Azure 服務主體](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.2.0)。

SQL Database、Azure Synapse 和 SQL 受控執行個體支援下列 Azure AD 物件：

- Azure AD 的使用者 (受控、同盟和來賓) 
- Azure AD 群組 (managed 和同盟) 
-  Azure AD 應用程式 

`CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER`代表 Azure AD 應用程式的 t-sql 命令現在支援 SQL Database 和 Azure Synapse。

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>使用服務主體 Azure AD 使用者建立的功能

支援這項功能對於 Azure AD 應用程式自動化程式很有用，其中 Azure AD 物件是在 SQL Database 和 Azure Synapse 中建立和維護，而不需要人為互動。 服務主體可以是 SQL 邏輯伺服器的 Azure AD 系統管理員，屬於群組或個別使用者。 應用程式可以在以系統管理員身分執行時，自動在 SQL Database 和 Azure Synapse 中建立 Azure AD 物件，而且不需要任何額外的 SQL 許可權。 這可讓您完整自動化資料庫使用者建立。 系統指派的受控識別和使用者指派的受控識別也支援此功能。 如需詳細資訊，請參閱 [什麼是適用于 Azure 資源的受控識別？](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>啟用服務主體以建立 Azure AD 使用者

若要在 SQL Database 和 Azure Synapse 中代表 Azure AD 應用程式啟用 Azure AD 物件建立，需要下列設定：

1. 指派伺服器身分識別
    - 針對新的 Azure SQL 邏輯伺服器，請執行下列 PowerShell 命令：
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    如需詳細資訊，請參閱 [>new-azsqlserver](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) 命令。

    - 針對現有的 Azure SQL 邏輯伺服器，請執行下列命令：
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    如需詳細資訊，請參閱 [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) \(英文\) 命令。

    - 若要檢查伺服器身分識別是否已指派給伺服器，請執行 >new-azsqlserver 命令。

    > [!NOTE]
    > 您也可以使用 CLI 命令來指派伺服器身分識別。 如需詳細資訊，請參閱 [az sql server create](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) 和 [az sql server update](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-update)。

2. 將所建立或指派給伺服器的伺服器身分識別授與 Azure AD [**Directory 讀取器**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 許可權。
    - 若要授與此許可權，請依照下列文章中提供的 SQL 受控執行個體描述：布建 [Azure AD admin (sql 受控執行個體) ](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - 授與此許可權的 Azure AD 使用者必須是 Azure AD **全域管理員** 或特殊 **許可權角色系統管理員** 角色的一部分。

> [!IMPORTANT]
> 您必須以上述循序執行步驟1和2。 首先，建立或指派伺服器身分識別，然後再授與 [**目錄讀取**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 者許可權。 省略上述其中一個步驟，或兩者都會在 Azure SQL 中代表 Azure AD 應用程式建立 Azure AD 物件時，造成執行錯誤。 如需代表 Azure AD 應用程式建立 Azure AD 使用者的逐步指示，請參閱 [教學課程：使用 Azure AD 應用程式建立 Azure AD 的使用者](authentication-aad-service-principal-tutorial.md)。
>
> 在 **公開預覽**中，您可以將 **目錄讀取** 者角色指派給 Azure AD 中的群組。 然後，群組擁有者可以將受控識別新增為此群組的成員，這會略過 **全域管理員** 或特殊 **許可權角色管理員** 授與 **目錄讀取** 者角色的需求。 如需這項功能的詳細資訊，請參閱 [AZURE SQL Azure Active Directory 中的目錄讀取者角色](authentication-aad-directory-readers-role.md)。

## <a name="troubleshooting-and-limitations-for-public-preview"></a>公開預覽的疑難排解和限制

- 代表 Azure AD 的應用程式在 Azure SQL 中建立 Azure AD 物件，而不啟用伺服器身分識別和授與 **目錄讀取** 者許可權時，此作業將會失敗，並出現下列可能的錯誤。 下列範例錯誤適用于 PowerShell 命令執行，以 `myapp` 在文章教學課程中建立 SQL Database 使用者 [：使用 Azure AD 應用程式建立 Azure AD 使用者](authentication-aad-service-principal-tutorial.md)。
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - 針對上述錯誤，請遵循下列步驟，將身分 [識別指派給 AZURE sql 邏輯伺服器](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) ，並 [將目錄讀取者許可權指派給 SQL 邏輯伺服器身分識別](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity)。
    > [!NOTE]
    > 上述的錯誤訊息將在功能 GA 之前變更，以清楚找出 Azure AD 應用程式支援所缺少的設定需求。
- 只有使用 CLI 命令以及 [2.9.0 版](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 或更高版本的 PowerShell 命令，才能將 Azure AD 應用程式設定為 SQL 受控執行個體的 Azure AD 管理員。 如需詳細資訊，請參閱 [az sql mi ad-admin create](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin?view=azure-cli-latest#az-sql-mi-ad-admin-create) 和 [Set-AzSqlInstanceActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) 命令。 
    - 如果您想要使用 SQL 受控執行個體的 Azure 入口網站設定 Azure AD 系統管理員，可能的因應措施是建立 Azure AD 群組。 然後，將服務主體 (Azure AD 應用程式) 新增至此群組，然後將此群組設定為 SQL 受控執行個體的 Azure AD 管理員。
    - 使用 SQL Database、 [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)和 [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) 命令，可支援將服務主體 (Azure AD 應用程式) 為 Azure 入口網站的 Azure AD 系統管理員和 Azure Synapse。
- 存取在不同租使用者中建立的 SQL Database 或 SQL 受控執行個體時，從另一個 Azure AD 租使用者使用具有服務主體的 Azure AD 應用程式將會失敗。 指派給此應用程式的服務主體必須是與 SQL 邏輯伺服器或受控執行個體的相同租使用者。
- 使用 PowerShell 以針對 Azure SQL 將個別 Azure AD 應用程式設定為 Azure AD 管理員時，需要 [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) \(英文\) 模組或更新版本。 確定您已升級到最新模組。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [教學課程：使用 Azure AD 應用程式建立 Azure AD 使用者](authentication-aad-service-principal-tutorial.md)


