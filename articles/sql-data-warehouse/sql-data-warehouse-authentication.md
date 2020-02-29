---
title: 驗證
description: 瞭解如何使用 Azure Active Directory （AAD）或 SQL Server authentication 來驗證 Azure Synapse 分析。
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 235d13cfd6f33830d832a6a79e3bc1c78bbfe53e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195969"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>向 Azure Synapse 分析進行驗證
瞭解如何使用 Azure Active Directory （AAD）或 SQL Server authentication，在 Azure Synapse 中向 SQL 分析進行驗證。

若要連接到 SQL 集區，您必須傳入安全性認證以供驗證之用。 建立連線時，會設定特定的連線設定，以做為建立查詢工作階段的一部分。  

如需安全性以及如何啟用資料倉儲連接的詳細資訊，請參閱[保護資料庫檔案](sql-data-warehouse-overview-manage-security.md)。

## <a name="sql-authentication"></a>SQL 驗證
若要連接到 SQL 集區，您必須提供下列資訊：

* 完整伺服器名稱
* 指定 SQL 驗證
* 使用者名稱
* Password
* 預設資料庫 (選擇性)

根據預設，您的連線會連接到*master*資料庫，而非您的使用者資料庫。 若要連線到您的使用者資料庫，您可以選擇執行下列兩個動作之一：

* 使用伺服器註冊 SSDT、SSMS 或應用程式連接字串中的 SQL Server 物件總管時，請指定預設資料庫。 例如，包含 ODBC 連線的 InitialCatalog 參數。
* 在 SSDT 中建立工作階段之前，先反白顯示使用者資料庫。

> [!NOTE]
> 變更連線的資料庫時，Transact-SQL 陳述式 **USE MyDatabase;** 不受支援。 如需使用 SSDT 連接到 SQL 集區的指引，請參閱[使用 Visual Studio 的查詢](sql-data-warehouse-query-visual-studio.md)一文。
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) 驗證
[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)驗證是使用 Azure Active Directory （Azure AD）中的身分識別連接到 SQL 集區的機制。 您可以使用 Azure Active Directory 驗證，在單一中央位置集中管理資料庫使用者和其他 Microsoft 服務的身分識別。 中央識別碼管理提供單一位置來管理 Azure Synapse 使用者，並簡化版權管理。 

### <a name="benefits"></a>優點
Azure Active Directory 的優點包括：

* 提供 SQL Server 驗證的替代方案。
* 協助停止跨資料庫伺服器使用過多的使用者身分識別。
* 允許在單一位置的密碼替換
* 使用外部 (AAD) 群組管理資料庫權限。
* 藉由啟用整合式 Windows 驗證和 Azure Active Directory 支援的其他形式驗證來避免儲存密碼。
* 使用自主資料庫使用者，在資料庫層級驗證身分。
* 針對連線到 SQL 集區的應用程式，支援以權杖為基礎的驗證。
* 透過 Active Directory 適用于各種工具（包括[SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md)和[SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json)）的通用驗證，支援多重要素驗證。

> [!NOTE]
> Azure Active Directory 相對來說仍較新，且具有一些限制。 若要確定 Azure Active Directory 適合您的環境，請參閱 [Azure AD 功能和限制](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)，特別是＜其他考量＞。
> 
> 

### <a name="configuration-steps"></a>組態步驟
請遵循下列步驟來設定 Azure Active Directory 驗證。

1. 建立和填入 Azure Active Directory
2. 選用：和目前與您的 Azure 訂用帳戶相關聯的 active directory 產生關聯並加以變更
3. 建立 Azure Synapse 的 Azure Active Directory 系統管理員
4. 設定用戶端電腦
5. 在對應至 Azure AD 身分識別的資料庫中建立自主資料庫使用者
6. 使用 Azure AD 身分識別連接到您的 SQL 集區

Azure Active Directory 使用者目前不會顯示在 SSDT 物件總管中。 解決方法是在 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx) 中檢視使用者。

### <a name="find-the-details"></a>尋找詳細資料
* 針對 Azure Synapse 中的 Azure SQL Database 和 SQL 分析，設定及使用 Azure Active Directory 驗證的步驟幾乎完全相同。 請遵循[使用 Azure Active Directory Authentication 來連接到 SQL Database 或 SQL 集](../sql-database/sql-database-aad-authentication.md)區主題中的詳細步驟。
* 建立自訂資料庫角色，並加入使用者至角色。 然後授與角色細微的權限。 如需詳細資訊，請參閱 [資料庫引擎權限使用者入門](https://msdn.microsoft.com/library/mt667986.aspx)。

## <a name="next-steps"></a>後續步驟
若要使用 Visual Studio 和其他應用程式開始查詢，請參閱[使用 Visual Studio 進行查詢](sql-data-warehouse-query-visual-studio.md)。
