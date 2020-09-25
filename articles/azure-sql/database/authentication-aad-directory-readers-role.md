---
title: Azure Active Directory 中適用於 Azure SQL 的 Directory 讀者角色
description: 深入瞭解 Azure SQL Azure AD 中的目錄讀取者角色。
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: 5a6fb2c1c539c5b8e353f5c3720cb9d001dcbbc9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91277935"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Azure Active Directory 中適用於 Azure SQL 的 Directory 讀者角色

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 本文中的這項功能目前為 **公開預覽**狀態。

Azure Active Directory (Azure AD) 引進了 [使用雲端群組來管理 Azure Active Directory (preview) 中的角色指派 ](../../active-directory/users-groups-roles/roles-groups-concept.md)。 這可讓 Azure AD 角色指派給群組。

針對 Azure SQL Database、Azure SQL 受控執行個體或 Azure Synapse Analytics 啟用 [受控識別](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 時，必須將 Azure AD [**目錄讀者**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 角色指派給身分識別，以允許 [Azure AD 圖形 API](../../active-directory/develop/active-directory-graph-api.md)的讀取權限。 SQL Database 和 Azure Synapse 的受控識別稱為伺服器身分識別。 SQL 受控執行個體的受控識別稱為「受控實例」身分識別，並會在建立實例時自動指派。 如需將伺服器身分識別指派給 SQL Database 或 Azure Synapse 的詳細資訊，請參閱 [啟用服務主體以建立 Azure AD 使用者](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)。

需要 **目錄讀者** 角色才能：

- 建立 SQL 受控執行個體的 Azure AD 登入
- 在 Azure SQL 中模擬 Azure AD 使用者
- 使用 [ALTER USER (transact-sql (](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) 命令，將使用受控執行個體 Windows 驗證的 SQL Server 使用者與 Azure AD authentication) 進行遷移) 
- 變更 SQL 受控執行個體的 Azure AD 管理員
- 允許 [服務主體 (應用程式) ](authentication-aad-service-principal.md) 在 Azure SQL 中建立 Azure AD 使用者

## <a name="assigning-the-directory-readers-role"></a>指派目錄讀取者角色

為了將 [**目錄讀取**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 者角色指派給身分識別，需要具有 [全域管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) 或特殊許可權 [角色管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 許可權的使用者。 通常管理或部署 SQL Database、SQL 受控執行個體或 Azure Synapse 的使用者，可能無法存取這些高度特殊許可權的角色。 這通常會讓建立未規劃的 Azure SQL 資源的使用者變得複雜，或需要具有高特殊許可權角色成員的協助，而大型組織通常無法存取這些成員。

針對 SQL 受控執行個體，必須先將**目錄讀者**角色指派給受控執行個體身分識別，才能[設定受控執行個體的 Azure AD 管理員](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。 

設定邏輯伺服器的 Azure AD 管理員時，對於 SQL Database 或 Azure Synapse，不需要將**目錄讀者**角色指派給伺服器身分識別。 不過，若要在 SQL Database 或 Azure Synapse 中代表 Azure AD 應用程式啟用 Azure AD 物件建立，則需要**目錄讀者**角色。 如果角色未指派給 SQL 邏輯伺服器身分識別，將無法在 Azure SQL 中建立 Azure AD 使用者。 如需詳細資訊，請參閱[使用 Azure SQL 的 Azure Active Directory 服務主體](authentication-aad-service-principal.md)。

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>將目錄讀取者角色授與 Azure AD 群組

目前處於 **公開預覽**狀態，您現在可以讓 [全域管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) 或特殊 [許可權角色管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 建立 Azure AD 群組，並將 [**目錄讀取**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 者許可權指派給該群組。 這可讓您存取此群組成員的 Azure AD 圖形 API。 此外，也允許擁有此群組擁有者的 Azure AD 使用者指派此群組的新成員，包括 Azure SQL 邏輯伺服器的身分識別。

此解決方案仍然需要高許可權的使用者 (全域管理員或特殊許可權角色管理員) 建立群組，並將使用者指派為一次性的活動，但 Azure AD 群組擁有者將能夠指派其他成員繼續進行。 如此一來，未來就不需要使用高許可權的使用者來設定其 Azure AD 租使用者中的所有 SQL 資料庫、SQL 受控實例或 Azure Synapse 伺服器。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：將目錄讀取者角色指派給 Azure AD 群組並管理角色指派](authentication-aad-directory-readers-role-tutorial.md)
