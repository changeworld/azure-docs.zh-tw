---
title: '專用 SQL 集區的驗證 (先前為 SQL DW) '
description: 瞭解如何使用 Azure Active Directory (Azure AD) 或 SQL Server 驗證，在 Azure Synapse Analytics 中 (先前的 SQL DW) 驗證專用的 SQL 集區。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 80bc9f6fc6af94ba2a5ade77cc1d53b3fc29f1ea
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685339"
---
# <a name="authenticate-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中 (先前的 SQL DW) 驗證專用的 SQL 集區

瞭解如何使用 Azure Active Directory (Azure AD) 或 SQL Server 驗證，在 Azure Synapse 中驗證專用 SQL 集區 (先前的 SQL DW) 。

若要連線到專用的 SQL 集區 (先前的 SQL DW) ，您必須傳入安全性認證以供驗證之用。 建立連線時，會設定特定的連線設定，以做為建立查詢工作階段的一部分。  

如需安全性的詳細資訊，以及如何啟用與您專用的 SQL 集區的連接 (先前為 SQL DW) ，請參閱 [保護資料庫檔案](sql-data-warehouse-overview-manage-security.md)集。

## <a name="sql-authentication"></a>SQL 驗證

若要連接到先前 SQL DW)  (專用 SQL 集區，您必須提供下列資訊：

* 完整伺服器名稱
* 指定 SQL 驗證
* 使用者名稱
* 密碼
* 預設資料庫 (選擇性)

根據預設，連線會連線至「主要」資料庫，而非使用者資料庫。 若要連線到您的使用者資料庫，您可以選擇執行下列兩個動作之一：

* 使用伺服器註冊 SSDT、SSMS 或應用程式連接字串中的 SQL Server 物件總管時，請指定預設資料庫。 例如，包含 ODBC 連線的 InitialCatalog 參數。
* 在 SSDT 中建立工作階段之前，先反白顯示使用者資料庫。

> [!NOTE]
> 變更連線的資料庫時，Transact-SQL 陳述式 **USE MyDatabase;** 不受支援。 如需使用 SSDT 連線到 SQL 集區的指引，請參閱[使用 Visual Studio 查詢](sql-data-warehouse-query-visual-studio.md)一文。

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 驗證

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 驗證機制使用 Azure Active Directory (Azure AD) 中的身分識別連線至 SQL 集區。 您可以使用 Azure Active Directory 驗證，在單一中央位置集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 中央識別碼管理提供單一位置來管理專用的 SQL 集區 (先前為 SQL DW) 使用者，並簡化版權管理。

### <a name="benefits"></a>優點

Azure Active Directory 的優點包括：

* 提供 SQL Server 驗證的替代方案。
* 協助停止跨伺服器使用過多的使用者身分識別。
* 允許在單一位置的密碼替換
* 使用外部 (Azure AD) 群組來管理資料庫權限。
* 藉由啟用整合式 Windows 驗證和 Azure Active Directory 支援的其他形式驗證來避免儲存密碼。
* 使用自主資料庫使用者，在資料庫層級驗證身分。
* 針對連線到 SQL 集區的應用程式支援權杖型驗證。
* 針對包括 [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 和 [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 的各種工具，透過 Active Directory 通用驗證支援多重要素驗證。

> [!NOTE]
> Azure Active Directory 相對來說仍較新，且具有一些限制。 若要確定 Azure Active Directory 適合您的環境，請參閱 [Azure AD 功能和限制](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations)，特別是＜其他考量＞。

### <a name="configuration-steps"></a>組態步驟

請遵循下列步驟來設定 Azure Active Directory 驗證。

1. 建立和填入 Azure Active Directory
2. 選擇性：和目前與 Azure 訂用帳戶相關聯的 Active Directory 產生關聯並加以變更
3. 建立 Azure Synapse 的 Azure Active Directory 系統管理員
4. 設定用戶端電腦
5. 在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者
6. 使用 Azure AD 身分識別連線到 SQL 集區

Azure Active Directory 使用者目前不會顯示在 SSDT 物件總管中。 解決方法是在 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 中檢視使用者。

### <a name="find-the-details"></a>尋找詳細資料

* 針對 Azure SQL Database 及 Azure Synapse 中的 Synapse SQL，設定並使用 Azure Active Directory 驗證的步驟幾乎完全相同。 請遵循 [使用 Azure Active Directory 驗證連線到 SQL Database 或 SQL 集區](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)主題中的詳細步驟。
* 建立自訂資料庫角色，並加入使用者至角色。 然後授與角色細微的權限。 如需詳細資訊，請參閱 [資料庫引擎權限使用者入門](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

## <a name="next-steps"></a>後續步驟

若要透過 Visual Studio 和其他應用程式開始查詢，請參閱[使用 Visual Studio 查詢](sql-data-warehouse-query-visual-studio.md)。
