---
title: 驗證
description: 瞭解如何使用 Azure 活動目錄 (Azure AD) 或 SQL Server 身份驗證對 Azure 同步分析進行身份驗證。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: fcd9607cd630f41c67c655c2e8016254600b52e2
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745385"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>對 Azure 同步分析進行認證

瞭解如何使用 Azure 活動目錄 (AAD) 或 SQL Server 身份驗證對 Azure 突觸中的 SQL 分析進行身份驗證。

要連接到 SQL 池,必須傳遞安全認證以進行身份驗證。 建立連線時，會設定特定的連線設定，以做為建立查詢工作階段的一部分。  

有關安全性以及如何啟用與資料倉儲的連線的詳細資訊,請參閱[保護資料庫文件](sql-data-warehouse-overview-manage-security.md)。

## <a name="sql-authentication"></a>SQL 驗證

要連接到 SQL 池,必須提供以下資訊:

* 完整伺服器名稱
* 指定 SQL 驗證
* 使用者名稱
* 密碼
* 預設資料庫 (選擇性)

預設情況下,您的連接連接到*主*資料庫,而不是用戶資料庫。 若要連線到您的使用者資料庫，您可以選擇執行下列兩個動作之一：

* 使用伺服器註冊 SSDT、SSMS 或應用程式連接字串中的 SQL Server 物件總管時，請指定預設資料庫。 例如，包含 ODBC 連線的 InitialCatalog 參數。
* 在 SSDT 中建立工作階段之前，先反白顯示使用者資料庫。

> [!NOTE]
> 變更連線的資料庫時，Transact-SQL 陳述式 **USE MyDatabase;** 不受支援。 有關使用 SSDT 連接到 SQL 池的指導,請參閱[使用 Visual Studio 的查詢](sql-data-warehouse-query-visual-studio.md)一文。

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) 驗證

[Azure 活動目錄](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)身份驗證是使用 Azure 活動目錄 (Azure AD) 中的識別連接到 SQL 池的機制。 您可以使用 Azure Active Directory 驗證，在單一中央位置集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 中央 ID 管理提供了管理 Azure 突觸使用者的單一位置,並簡化了許可權管理。

### <a name="benefits"></a>優點

Azure Active Directory 的優點包括：

* 提供 SQL Server 驗證的替代方案。
* 協助停止跨資料庫伺服器使用過多的使用者身分識別。
* 允許在單一位置的密碼替換
* 使用外部 (Azure AD) 組管理資料庫許可權。
* 藉由啟用整合式 Windows 驗證和 Azure Active Directory 支援的其他形式驗證來避免儲存密碼。
* 使用自主資料庫使用者，在資料庫層級驗證身分。
* 支援連接到 SQL 池的應用程式基於權杖的身份驗證。
* 支援透過主動目錄通用身份驗證對各種工具(包括 SQL[伺服器管理工作室](../../sql-database/sql-database-ssms-mfa-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)和[SQL Server 資料工具](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest))進行多重身份驗證。

> [!NOTE]
> Azure Active Directory 相對來說仍較新，且具有一些限制。 若要確定 Azure Active Directory 適合您的環境，請參閱 [Azure AD 功能和限制](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations)，特別是＜其他考量＞。

### <a name="configuration-steps"></a>組態步驟

請遵循下列步驟來設定 Azure Active Directory 驗證。

1. 建立和填入 Azure Active Directory
2. 選用：和目前與您的 Azure 訂用帳戶相關聯的 active directory 產生關聯並加以變更
3. 為 Azure 突觸建立 Azure 活動目錄管理員
4. 設定用戶端電腦
5. 在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者
6. 使用 Azure AD 識別連線到 SQL 池

Azure Active Directory 使用者目前不會顯示在 SSDT 物件總管中。 解決方法是在 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#azure-ad-features-and-limitations) 中檢視使用者。

### <a name="find-the-details"></a>尋找詳細資料

* 在 Azure Synaps 中配置和使用 Azure 活動目錄身份驗證的步驟幾乎相同。 按照主題"通過使用 Azure[活動目錄身份驗證連接到 SQL 資料庫或 SQL 池中的](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)詳細步驟。
* 建立自訂資料庫角色，並加入使用者至角色。 然後授與角色細微的權限。 如需詳細資訊，請參閱 [資料庫引擎權限使用者入門](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="next-steps"></a>後續步驟

要開始使用 Visual Studio 和其他應用程式查詢,請參閱[使用 Visual Studio 查詢](sql-data-warehouse-query-visual-studio.md)。
