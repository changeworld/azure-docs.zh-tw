---
title: 使用受控識別來存取 Azure SQL Database 或 Azure Synapse Analytics-Azure 串流分析
description: 本文說明如何使用受控識別來驗證您的 Azure 串流分析作業，以 Azure SQL Database 或 Azure Synapse Analytics 輸出。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 244f8a446e33102e018881b3ec8f32ba9d8cbcc6
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014140"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>使用受控識別來存取 Azure 串流分析作業的 Azure SQL Database 或 Azure Synapse Analytics (預覽) 

Azure 串流分析支援 Azure SQL Database 和 Azure Synapse Analytics 輸出接收的 [受控識別驗證](../active-directory/managed-identities-azure-resources/overview.md) 。 受控識別消除了以使用者為基礎的驗證方法限制，比如因為密碼變更或使用者權杖每 90 天到期一次而需要重新驗證。 當您移除手動驗證的需求時，您的串流分析部署可以完全自動化。

受控識別是在 Azure Active Directory 中註冊的受控應用程式，代表了指定的串流分析作業。 受控應用程式用來向目標資源進行驗證。 本文說明如何透過 Azure 入口網站為 Azure SQL Database 或 Azure Synapse Analytics 輸出 (串流分析作業的) 啟用受控識別。

## <a name="prerequisites"></a>Prerequisites

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

使用這項功能需要下列各項：

- Azure 串流分析作業。

- Azure SQL Database 資源。

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

使用這項功能需要下列各項：

- Azure 串流分析作業。

- Azure Synapse Analytics SQL 集區。

- [設定為您的串流分析作業](azure-synapse-analytics-output.md)的 Azure 儲存體帳戶。

---

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

1. 流覽至您的 Azure SQL Database 或 Azure Synapse Analytics 資源，然後選取資料庫所在的 SQL Server。 您可以在資源概觀頁面上，找到 [伺服器名稱] 旁邊的 SQL Server 名稱。

1. 選取 [設定] 之下的 [Active Directory 管理員]。 接著，選取 [設定管理員]。

   ![Active Directory 管理員頁面](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. 在 [Active Directory 管理員] 頁面上，搜尋要成為 SQL Server 管理員的使用者或群組，然後按一下 [選取]。

   ![新增 Active Directory 管理員](./media/sql-db-output-managed-identity/add-admin.png)

   [Active Directory 系統管理員] 頁面會顯示您 Active Directory 的所有成員和群組。 無法選取灰色的使用者或群組，因為這些使用者或群組不受 Azure Active Directory 系統管理員的支援。 請參閱 ****    [使用 Azure Active Directory 驗證 SQL Database 或 Azure Synapse 的驗證](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)Azure Active Directory 功能和限制一節中支援的系統管理員清單。 Azure 角色型存取控制 (Azure RBAC) 只適用于入口網站，不會傳播至 SQL Server。 此外，選取的使用者或群組就是在下一節中能夠建立 **自主資料庫使用者** 的使用者。

1. 在 [Active Directory 管理員] 頁面上選取 [儲存]。 變更管理員的程序需要幾分鐘的時間。

   當您設定 Azure Active Directory 管理員時，新的系統管理員名稱 (使用者或群組) 不能以 SQL Server authentication 使用者的身分存在於虛擬主資料庫中。 如果有的話，Azure Active Directory 管理員安裝程式將會失敗，並復原其建立，表示) 的系統管理員 (名稱已存在。 由於 SQL Server authentication 使用者不是 Azure Active Directory 的一部分，因此在使用者失敗時使用 Azure Active Directory 驗證連接到伺服器的工作。 

## <a name="create-a-contained-database-user"></a>建立自主資料庫使用者

接下來，您要在 Azure SQL 或 Azure Synapse 資料庫中建立對應至 Azure Active Directory 身分識別的自主資料庫使用者。 自主資料庫使用者沒有主資料庫的登入，但它會對應到與資料庫相關聯的目錄中的身分識別。 Azure Active Directory 身分識別可以是個別的使用者帳戶或群組。 在此情況下，您想要為串流分析作業建立自主資料庫使用者。 

1. 使用 SQL Server Management Studio 連接到您的 Azure SQL 或 Azure Synapse 資料庫。 [使用者名稱] 是具有 [改變任何使用者] 權限的 Azure Active Directory 使用者。 您在 SQL Server 上設定的管理員即為範例。 使用 [Azure Active Directory - MFA 通用] 驗證。 

   ![連接至 SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   伺服器名稱 `<SQL Server name>.database.windows.net` 在不同的區域中可能不同。 例如，中國地區應該使用 `<SQL Server name>.database.chinacloudapi.cn`。
 
   您可以前往 **[選項 > 連接屬性] > [連接到資料庫]**，以指定特定的 azure SQL 或 azure Synapse 資料庫。  

   ![SQL Server 連線屬性](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. 當您第一次連線時，可能會遇到下列視窗：

   ![新增防火牆規則視窗](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. 若是如此，移至 Azure 入口網站上您的 SQL Server 資源。 在 [安全性] 區段下，開啟 [防火牆和虛擬網路] 頁面。 
   1. 使用任何規則名稱新增規則。
   1. 使用 [新增防火牆規則] 視窗中 [開始 IP] 的 [起始] IP 位址。
   1. 使用 [新增防火牆規則] 視窗中 [結束 IP] 的 [終止] IP 位址。 
   1. 選取 [儲存]，然後再次嘗試從 SQL Server Management Studio 連線。 

1. 連接之後，請建立自主資料庫使用者。 下列 SQL 命令會建立具有與您的串流分析作業同名的自主資料庫使用者。 務必將 *ASA_JOB_NAME* 放入括弧中。 使用下列 T-SQL 語法並執行查詢。 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

1. 為了讓 Microsoft 的 Azure Active Directory 確認串流分析作業是否可存取 SQL Database，我們必須給予 Azure Active Directory 與資料庫通訊的權限。 若要這樣做，請再次移至 Azure 入口網站中的 [防火牆和虛擬網路] 頁面，然後啟用 [允許 Azure 服務和資源存取這部伺服器]。 

   ![防火牆與虛擬網路](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>授與串流分析作業權限

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

當您在入口網站中建立自主資料庫使用者並授與 Azure 服務的存取權（如上一節所述）時，您的串流分析作業具有受控識別的許可權 **，可透過受控識別來連線** 到您的 azure SQL database 資源。 建議您將 SELECT 和 INSERT 權限授與串流分析作業，因為稍後在串流分析工作流程中需要用到。 **SELECT** 許可權可讓作業測試與 Azure SQL 資料庫中資料表的連接。 當您設定輸入和 Azure SQL database 輸出之後， **INSERT** 許可權可讓您測試端對端串流分析查詢。

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

當您在入口網站中建立自主資料庫使用者並授與 Azure 服務的存取權（如上一節所述）時，您的串流分析作業具有受控 **識別的許可權，可透過** 受控識別連線至您的 azure Synapse 資料庫資源。 建議您進一步將「選取」、「插入」和「管理資料庫大量作業」許可權授與串流分析作業，因為稍後會在串流分析工作流程中需要這些許可權。 **SELECT** 許可權可讓作業測試其與 Azure Synapse 資料庫中資料表的連接。 當您設定輸入和 Azure Synapse 資料庫輸出之後， **插入** 和 **管理資料庫大量作業** 許可權可讓您測試端對端串流分析查詢。

若要授與「管理資料庫大量作業」許可權，您必須將標示為「資料庫」許可權所 [隱含](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest#remarks)的擁有權限 **授與串流** 分析作業。 您需要此許可權，因為串流分析作業會執行 COPY 語句，這需要 [管理資料庫大量作業和插入](/sql/t-sql/statements/copy-into-transact-sql)。

---

您可以使用 SQL Server Management Studio，將這些權限授予串流分析作業。 如需詳細資訊，請參閱 GRANT (Transact-SQL) 參考。

若只要授與資料庫中特定資料表或物件的權限，請使用下列 T-SQL 語法並執行查詢。 

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT [PERMISSION NAME] OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

或者，您可以在 SQL Server Management Studio 中的 Azure SQL 或 Azure Synapse 資料庫上按一下滑鼠右鍵，然後選取 [ **屬性] > 許可權**。 從 [權限] 功能表中，您可以看到先前新增的串流分析作業，而且可以視需要手動授與或拒絕權限。

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>建立 Azure SQL Database 或 Azure Synapse 輸出

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

現在您已設定受控識別，您已準備好將 Azure SQL Database 或 Azure Synapse 輸出新增至您的串流分析作業。

請確定您已在 SQL Database 中建立具有適當輸出架構的資料表。 這個資料表的名稱，是當您將 SQL Database 輸出新增至串流分析作業時，必須填寫的其中一個必要屬性。 此外，請確定作業有 **SELECT** 和 **INSERT** 權限，才能測試連線及執行串流分析查詢。 如果您尚未這麼做，請參閱[授與串流分析作業權限](#grant-stream-analytics-job-permissions)一節。 

1. 回到您的串流分析作業，然後瀏覽至 [作業拓撲] 下的 [輸出] 頁面。 

1. 選取 [新增] > [SQL Database]。 在 SQL Database 輸出接收的輸出屬性視窗中，選取驗證模式下拉式清單中的 [受控識別]。

1. 填寫其餘屬性。 若要深入了解如何建立 SQL Database 輸出，請參閱[使用串流分析建立 SQL Database 輸出](sql-database-output.md)。 完成後，請選取 [儲存]。

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

既然您已設定受控識別和儲存體帳戶，您就可以將 Azure SQL Database 或 Azure Synapse 輸出新增至您的串流分析作業。

確定您已使用適當的輸出架構在 Azure Synapse 資料庫中建立資料表。 此資料表的名稱是您將 Azure Synapse 輸出新增至串流分析作業時必須填寫的其中一個必要屬性。 此外，請確定作業有 **SELECT** 和 **INSERT** 權限，才能測試連線及執行串流分析查詢。 如果您尚未這麼做，請參閱[授與串流分析作業權限](#grant-stream-analytics-job-permissions)一節。

1. 回到您的串流分析作業，然後瀏覽至 [作業拓撲] 下的 [輸出] 頁面。

1. 選取 [ **新增 > Azure Synapse Analytics**。 在 SQL Database 輸出接收的輸出屬性視窗中，選取驗證模式下拉式清單中的 [受控識別]。

1. 填寫其餘屬性。 若要深入瞭解如何建立 Azure Synapse 輸出，請參閱 [Azure Synapse Analytics Azure 串流分析的輸出](azure-synapse-analytics-output.md)。 完成後，請選取 [儲存]。

---

## <a name="remove-managed-identity"></a>移除受控識別

只有在作業刪除時，才會刪除為串流分析作業建立的受控識別。 沒有任何方法可刪除受控識別，而不刪除工作。 如果您不想再使用受控識別，您可以變更輸出的驗證方法。 受控識別會持續存在，直到刪除作業為止，如果您決定再次使用受控識別驗證，則會使用此身分識別。

## <a name="next-steps"></a>後續步驟

* [了解來自 Azure 串流分析的輸出](stream-analytics-define-outputs.md)
* [Azure 串流分析輸出至 Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Azure 串流分析的 Azure Synapse Analytics 輸出](azure-synapse-analytics-output.md)
