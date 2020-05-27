---
title: 使用受控識別來存取 Azure SQL Database - Azure 串流分析
description: 本文說明如何使用受控識別向 Azure SQL DB 輸出驗證 Azure 串流分析作業。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595837"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>使用受控識別從 Azure 串流分析作業 (預覽) 存取 Azure SQL Database

Azure 串流分析支援 Azure SQL Database 輸出接收的[受控識別驗證](../active-directory/managed-identities-azure-resources/overview.md)。 受控識別消除了以使用者為基礎的驗證方法限制，比如因為密碼變更或使用者權杖每 90 天到期一次而需要重新驗證。 當您移除手動驗證的需求時，您的串流分析部署可以完全自動化。

受控識別是在 Azure Active Directory 中註冊的受控應用程式，代表了指定的串流分析作業。 受控應用程式用來向目標資源進行驗證。 本文說明如何透過 Azure 入口網站，為串流分析作業的 Azure SQL Database 輸出啟用受控識別。

## <a name="prerequisites"></a>Prerequisites

這項功能需要下列各項：

- Azure 串流分析作業。

- Azure SQL Database 資源。

## <a name="create-a-managed-identity"></a>建立受控識別

首先，您會建立 Azure 串流分析作業的受控識別。

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您的 Azure 串流分析作業。

1. 從左側導覽功能表中，選取位於 [設定] 之下的 [受控識別]。 然後，核取 [使用系統指派的受控識別] 旁的方塊，然後選取 [儲存]。

   ![選取系統指派的受控識別](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   Azure Active Directory 中已建立串流分析作業的識別服務主體。 Azure 會管理新建立的身分識別生命週期。 當串流分析作業刪除時，Azure 會自動刪除已與其建立關聯的身分識別 (亦即服務主體)。 

1. 當您儲存組態時，服務主體的物件識別碼 (OID) 會列為主體識別碼，如下所示： 

   ![顯示為主體識別碼的物件識別碼](./media/sql-db-output-managed-identity/principal-id.png)

   服務主體與串流分析作業的名稱相同。 例如，如果作業的名稱是 *MyASAJob*，則服務主體的名稱也會是 *MyASAJob*。

## <a name="select-an-active-directory-admin"></a>選取 Active Directory 管理員

建立受控識別之後，請選取 Active Directory 管理員。

1. 瀏覽至您的 Azure SQL Database 資源，然後選取資料庫所在的 SQL Server。 您可以在資源概觀頁面上，找到 [伺服器名稱] 旁邊的 SQL Server 名稱。 

1. 選取 [設定] 之下的 [Active Directory 管理員]。 接著，選取 [設定管理員]。 

   ![Active Directory 管理員頁面](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. 在 [Active Directory 管理員] 頁面上，搜尋要成為 SQL Server 管理員的使用者或群組，然後按一下 [選取]。  

   ![新增 Active Directory 管理員](./media/sql-db-output-managed-identity/add-admin.png)

1. 在 [Active Directory 管理員] 頁面上選取 [儲存]。 變更管理員的程序需要幾分鐘的時間。  

## <a name="create-a-database-user"></a>建立資料庫使用者

接下來，您會在 SQL Database 中建立對應至 Azure Active Directory 身分識別的自主資料庫使用者。 自主資料庫使用者沒有 master 資料庫的登入，但是會對應至目錄中與資料庫相關聯的身分識別。 Azure Active Directory 身分識別可以是個別的使用者帳戶或群組。 在此情況下，您想要為串流分析作業建立自主資料庫使用者。 

1. 使用 SQL Server Management Studio 連線至 SQL 資料庫。 [使用者名稱] 是具有 [改變任何使用者] 權限的 Azure Active Directory 使用者。 您在 SQL Server 上設定的管理員即為範例。 使用 [Azure Active Directory - MFA 通用] 驗證。 

   ![連接至 SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   伺服器名稱 `<SQL Server name>.database.windows.net` 在不同的區域中可能不同。 例如，中國地區應該使用 `<SQL Server name>.database.chinacloudapi.cn`。
 
   您可以移至 [選項] > [連線屬性] > [連線到資料庫]，指定特定 SQL Database。  

   ![SQL Server 連線屬性](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. 當您第一次連線時，可能會遇到下列視窗：

   ![新增防火牆規則視窗](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. 若是如此，移至 Azure 入口網站上您的 SQL Server 資源。 在 [安全性] 區段下，開啟 [防火牆和虛擬網路] 頁面。 
   1. 使用任何規則名稱新增規則。
   1. 使用 [新增防火牆規則] 視窗中 [開始 IP] 的 [起始] IP 位址。
   1. 使用 [新增防火牆規則] 視窗中 [結束 IP] 的 [終止] IP 位址。 
   1. 選取 [儲存]，然後再次嘗試從 SQL Server Management Studio 連線。 

1. 連線之後，請建立自主資料庫使用者。 下列 SQL 命令會建立具有與您的串流分析作業同名的自主資料庫使用者。 務必將 *ASA_JOB_NAME* 放入括弧中。 使用下列 T-SQL 語法並執行查詢。 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

## <a name="grant-stream-analytics-job-permissions"></a>授與串流分析作業權限

串流分析作業有權限從受控識別**連線**到您的 SQL Database 資源。 最可能的情況是，允許串流分析作業執行 **SELECT** 等命令會很有效。 您可以使用 SQL Server Management Studio，將這些權限授予串流分析作業。 如需詳細資訊，請參閱 [GRANT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15) 參考。

或者，您可以在 SQL Server Management Studio 中以滑鼠右鍵按一下您的 SQL 資料庫，然後選取 [屬性] > [權限]。 從 [權限] 功能表中，您可以看到先前新增的串流分析作業，而且可以視需要手動授與或拒絕權限。

## <a name="create-an-azure-sql-database-output"></a>建立 Azure SQL Database 輸出

您的受控識別現已設定，因此可將 Azure SQL Database 當作輸出新增到您的串流分析作業。

1. 回到您的串流分析作業，然後瀏覽至 [作業拓撲] 下的 [輸出] 頁面。 

1. 選取 [新增] > [SQL Database]。 在 SQL Database 輸出接收的輸出屬性視窗中，選取驗證模式下拉式清單中的 [受控識別]。

1. 填寫其餘屬性。 若要深入了解如何建立 SQL Database 輸出，請參閱[使用串流分析建立 SQL Database 輸出](stream-analytics-define-outputs.md#sql-database)。 完成後，請選取 [儲存]。 

## <a name="next-steps"></a>後續步驟

* [了解來自 Azure 串流分析的輸出](stream-analytics-define-outputs.md)
* [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)
