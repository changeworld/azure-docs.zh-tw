---
title: 保護資料庫
description: 本教學課程將教導您保護 Azure SQL Database (無論是單一資料庫或集區資料庫) 的技術和功能。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 09/03/2019
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: 12c3a35e12e3f432345ea788893d0d0ae6e6433f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496911"
---
# <a name="tutorial-secure-a-database-in-azure-sql-database"></a>教學課程：保護 Azure SQL Database 中的資料庫
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在本教學課程中，您將了解如何：

> [!div class="checklist"]
>
> - 建立伺服器層級和資料庫層級防火牆規則
> - 設定 Azure Active Directory (Azure AD) 管理員
> - 使用 SQL 驗證、Azure AD 驗證和安全的連接字串來管理使用者存取
> - 啟用安全性功能，例如進階資料安全性、稽核、資料遮罩和加密

Azure SQL Database 可讓您以下列方式保護資料：

- 使用防火牆規則限制存取
- 使用需要身分識別的驗證機制
- 使用授權搭配角色型成員資格和權限
- 啟用安全性功能

> [!NOTE]
> 如 [Azure SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)和[連線架構](../managed-instance/connectivity-architecture-overview.md)所述，使用網路安全性群組規則和私人端點來保護 Azure SQL 受控執行個體。

若要深入了解，請參閱 [Azure SQL Database 安全性概觀](/azure/sql-database/sql-database-security-index)和[功能](security-overview.md)文章。

> [!TIP]
> 下列 Microsoft Learn 課程模組可協助您免費學習如何[保護 Azure SQL Database 中的資料庫](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您具有下列必要條件：

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- [伺服器](logical-servers.md)和單一資料庫
  - 透過 [Azure 入口網站](single-database-create-quickstart.md)、[CLI](az-cli-script-samples-content-guide.md) 或 [PowerShell](powershell-script-content-guide.md) 加以建立

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

若要取得此教學課程中的所有步驟，請登入 [Azure 入口網站](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>建立防火牆規則

Azure 中 SQL Database 中的資料庫是由防火牆保護。 依預設，伺服器與資料庫的所有連線皆會遭拒。 若要深入了解，請參閱[伺服器層級和資料庫層級防火牆規則](firewall-configure.md)。

最安全的設定是將 [允許存取 Azure 服務] 設為 [關閉]。 接著，針對需要連線的資源 (例如 Azure VM 或雲端服務)，建立[保留的 IP (傳統部署)](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip)，而且只允許透過防火牆的 IP 位址存取。 如果您使用[資源管理員](/azure/virtual-network/virtual-network-ip-addresses-overview-arm)部署模型，則每個資源都需要專用的公用 IP 位址。

> [!NOTE]
> SQL Database 會透過連接埠 1433 通訊。 如果您嘗試從公司網路連線，您網路的防火牆可能不允許透過連接埠 1433 的連出流量。 若情況如此，除非系統管理員開啟連接埠 1433，否則您無法連線至伺服器。

### <a name="set-up-server-level-firewall-rules"></a>設定伺服器層級的防火牆規則

伺服器層級的 IP 防火牆規則適用於相同伺服器內的所有資料庫。

若要設定伺服器層級的防火牆規則：

1. 在 Azure 入口網站中，從左側功能表中選取 [SQL 資料庫]，然後在 [SQL 資料庫] 頁面上選取您的資料庫。

    ![伺服器防火牆規則](./media/secure-database-tutorial/server-name.png)

    > [!NOTE]
    > 請務必複製完整的伺服器名稱 (例如 *yourserver.database.windows.net*)，以便稍後在本教學課程中使用。

1. 在 [概觀] 頁面上，選取 [設定伺服器防火牆]。 伺服器的 [防火牆設定] 頁面隨即開啟。

   1. 選取工具列上的 [新增用戶端 IP]，以將目前的 IP 位址新增至新的防火牆規則。 此規則可以針對單一 IP 位址或 IP 位址範圍開啟連接埠 1433。 選取 [儲存]。

      ![設定伺服器防火牆規則](./media/secure-database-tutorial/server-firewall-rule2.png)

   1. 選取 [確定] 並關閉 [防火牆設定] 頁面。

您現在可以連線到資料庫中任何具有指定 IP 位址或 IP 位址範圍的伺服器。

### <a name="setup-database-firewall-rules"></a>設定資料庫防火牆規則

資料庫層級的防火牆規則只適用於個別的資料庫。 伺服器容錯移轉期間，資料庫將會保留這些規則。 資料庫層級的防火牆規則只能使用 Transact-SQL (T-SQL) 陳述式設定，且僅可在您設定伺服器層級的防火牆規則之後進行設定。

設定資料庫層級防火牆規則：

1. 連線至資料庫，例如使用 [SQL Server Management Studio](connect-query-ssms.md)。

1. 在 [物件總管] 中，以滑鼠右鍵按一下資料庫，然後選取 [新增查詢]。

1. 在查詢視窗中，新增此陳述式並將 IP 位址修改為您的公用 IP 位址：

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. 在工具列上，選取 [執行] 以建立防火牆規則。

> [!NOTE]
> 您也可使用 [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) 命令在 SSMS 中以建立伺服器層級的防火牆規則，但必須連線到 *master* 資料庫。

## <a name="create-an-azure-ad-admin"></a>建立 Azure AD 管理員

確定您使用適當的 Azure Active Directory (AD) 受控網域。 若要選取 AD 網域，請使用 Azure 入口網站的右上角。 此程序可確認將相同的訂用帳戶用於裝載資料庫或資料倉儲的 Azure AD 和邏輯 SQL 伺服器。

   ![choose-ad](./media/secure-database-tutorial/8choose-ad.png)

若要設定 Azure AD 管理員：

1. 在 Azure 入口網站的 [SQL Server] 頁面上，選取 [Active Directory 系統管理員]。接下來，選取 [設定管理員]。

    ![選取 Active Directory](./media/secure-database-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > 您必須是「公司系統管理員」或「全域管理員」，才能執行此工作。

1. 在 [新增管理員] 頁面上，搜尋並選取的 AD 使用者或群組，然後選擇 [選取]。 Active Directory 的所有成員和群組都會列出，而且不支援將呈現灰色的項目作為 Azure AD 管理員。 請參閱 [Azure AD 功能和限制](authentication-aad-overview.md#azure-ad-features-and-limitations)。

    ![選取管理員](./media/secure-database-tutorial/admin-select.png)

    > [!IMPORTANT]
    > 角色型存取控制 (RBAC) 僅適用於入口網站，不會傳播至 SQL Server。

1. 在 [Active Directory 管理員] 頁面頂端，選取 [儲存]。

    變更管理員的程序可能需要幾分鐘的時間。 新的管理員會出現在 [Active Directory 管理員] 方塊中。

> [!NOTE]
> 設定 Azure AD 管理員時，新的管理員名稱 (使用者或群組) 不能在 master 資料庫中作為 SQL Server 登入使用者存在。 如果存在，此設定會失敗並復原變更，表示這樣的管理員名稱已經存在。 由於 SQL Server 登入使用者並非 Azure AD 的成員，因此使用 Azure AD 驗證來連線到使用者的一切努力都會失敗。

如需有關設定 Azure AD 的詳細資訊，請參閱：

- [整合內部部署身分識別與 Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [將自有的網域名稱新增至 Azure AD](../../active-directory/fundamentals/add-custom-domain.md)
- [Microsoft Azure 現在支援與 Windows Server AD 同盟](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [管理 Azure AD 目錄](../../active-directory/fundamentals/active-directory-whatis.md)
- [使用 PowerShell 管理 Azure AD](/powershell/azure/?view=azureadps-2.0)
- [混合式身分識別所需的連接埠和通訊協定](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>管理資料庫存取

將使用者新增至資料庫，或允許使用者使用安全的連接字串存取，以管理資料庫存取權。 連接字串適合用於外部應用程式。 若要深入了解，請參閱[管理登入和使用者帳戶](logins-create-manage.md)和 [AD 驗證](authentication-aad-overview.md)。

若要新增使用者，請選擇資料庫驗證類型：

- **SQL 驗證**，使用登入作業的使用者名稱和密碼，而且僅在伺服器內的特定資料庫環境中有效。

- **Azure AD 驗證**，使用 Azure AD 所管理的身分識別

### <a name="sql-authentication"></a>SQL 驗證

若要使用 SQL 驗證新增使用者：

1. 連線至資料庫，例如使用 [SQL Server Management Studio](connect-query-ssms.md)。

1. 在 [物件總管] 中，以滑鼠右鍵按一下資料庫，然後選擇 [新增查詢]。

1. 在查詢視窗中，輸入下列命令︰

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. 在工具列上，選取 [執行] 以建立使用者。

1. 依預設，使用者可與資料庫連線，但沒有權限可讀取或寫入資料。 若要授與這些權限，請在新的查詢視窗中執行下列命令：

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> 在資料庫層級建立非系統管理員帳戶，除非他們需要執行如建立新使用者的系統管理員工作。

### <a name="azure-ad-authentication"></a>Azure AD 驗證

Azure Active Directory 驗證要求將資料庫使用者建立為自主資料庫使用者。 自主資料庫使用者會對應至 Azure AD 目錄中與資料庫關聯的身分識別，而沒有 master 資料庫的登入。 Azure AD 身分識別可用於個別的使用者或群組。 如需詳細資訊，請參閱[自主資料庫使用者，使資料庫可攜](/sql/relational-databases/security/contained-database-users-making-your-database-portable)，並檢閱 [Azure AD 教學課程](authentication-aad-configure.md)以了解如何使用 Azure AD 進行驗證。

> [!NOTE]
> 您無法使用 Azure 入口網站建立資料庫使用者 (排除系統管理員)。 Azure 角色不會傳播至 SQL Server、資料庫或資料倉儲。 這類角色只用來管理 Azure 資源，並不會套用到資料庫權限。
>
> 例如，「SQL Server 參與者」角色不會授與可連線到資料庫或資料倉儲的存取權。 必須使用 Transact-SQL 陳述式在資料庫內授與此權限。

> [!IMPORTANT]
> 不支援在 T-SQL `CREATE LOGIN` 和 `CREATE USER` 陳述式中的使用者名稱內使用冒號 `:` 或 `&` 等特殊字元。

若要使用 Azure AD 驗證來新增使用者：

1. 使用至少具有 *ALTER ANY USER* 權限的 Azure AD 帳戶連線至 Azure 中的伺服器。

1. 在 [物件總管] 中，以滑鼠右鍵按一下資料庫，然後選取 [新增查詢]。

1. 在查詢視窗中輸入下列命令，並將 `<Azure_AD_principal_name>` 修改為 Azure AD 使用者的主體名稱或 Azure AD 群組的顯示名稱：

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Azure AD 使用者會在資料庫中繼資料中標示為 `E (EXTERNAL_USER)` 類型，而群組則標示為 `X (EXTERNAL_GROUPS)` 類型。 如需詳細資訊，請參閱 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)。

### <a name="secure-connection-strings"></a>安全的連接字串

若要確保用戶端應用程式與 SQL 資料庫之間有安全且加密的連線，連接字串必須設定為：

- 要求加密的連線
- 不信任伺服器憑證

使用傳輸層安全性 (TLS) 建立連線，並降低發生攔截式攻擊的風險。 每個資料庫都有可用的連接字串，並已預先設定為支援用戶端驅動程式，例如 ADO.NET、JDBC、ODBC 和 PHP。 如需 TLS 和連線能力的相關資訊，請參閱 [TLS 考量](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)。

若要複製安全的連接字串：

1. 在 Azure 入口網站中，從左側功能表中選取 [SQL 資料庫]，然後在 [SQL 資料庫] 頁面上選取您的資料庫。

1. 在 [概觀] 頁面上，選取 [顯示資料庫連接字串]。

1. 選取驅動程式索引標籤並複製完整的連接字串。

    ![ADO.NET 連接字串](./media/secure-database-tutorial/connection.png)

## <a name="enable-security-features"></a>啟用安全性功能

Azure SQL Database 會提供可使用 Azure 入口網站存取的安全性功能。 除了僅適用於資料庫的資料遮罩以外，這些功能均可供資料庫和伺服器使用。 若要深入了解，請參閱[進階資料安全性](advanced-data-security.md)、[稽核](../../azure-sql/database/auditing-overview.md)、[動態資料遮罩](dynamic-data-masking-overview.md)，以及[透明資料加密](transparent-data-encryption-tde-overview.md)。

### <a name="advanced-data-security"></a>進階資料安全性

進階資料安全性功能可讓您偵測潛在威脅，並且對異常活動提供安全性警示。 使用者可以使用稽核功能探索可疑的事件，並判斷事件的原因是否為有人嘗試存取、破壞或利用資料庫中的資料。 使用者也會提供包含弱點評量及資料探索和分類工具的安全性概觀。

> [!NOTE]
> 範例威脅為 SQL 插入，這是攻擊者將惡意 SQL 插入應用程式輸入的程序。 然後應用程式會在不知情的情況下執行惡意 SQL，並允許攻擊者存取、破壞或修改資料庫中的資料。

若要啟用進階資料安全性：

1. 在 Azure 入口網站中，從左側功能表中選取 [SQL 資料庫]，然後在 [SQL 資料庫] 頁面上選取您的資料庫。

1. 在 [概觀] 頁面上，選取 [伺服器名稱] 連結。 將會開啟伺服器頁面。

1. 在 [SQL 伺服器] 頁面上，尋找 [安全性] 區段並選取 [進階資料安全性]。

   1. 選取 [進階資料安全性] 下的 [開啟] 來啟用此功能。 選擇儲存弱點評定結果的儲存體帳戶。 然後選取 [儲存]。

      ![瀏覽窗格](./media/secure-database-tutorial/threat-settings.png)

      您也可以設定電子郵件來接收安全性警示、儲存體詳細資料及威脅偵測類型。

1. 返回資料庫的 [SQL 資料庫] 頁面，然後選取 [安全性] 區段下的 [進階資料安全性]。 您可以在這裡找到資料庫可用的各種安全性指標。

    ![威脅狀態](./media/secure-database-tutorial/threat-status.png)

如果偵測到異常活動，您會收到一封含有事件相關資訊的電子郵件。 其中包含活動性質、資料庫、伺服器、事件時間、可能原因和建議動作，以協助您調查並減輕潛在威脅。 如果收到這類電子郵件，請選取 [Azure SQL 稽核記錄] 連結以啟動 Azure 入口網站，並顯示事件時間的相關稽核記錄。

   ![威脅偵測電子郵件](./media/secure-database-tutorial/threat-email.png)

### <a name="auditing"></a>稽核

稽核功能會追蹤資料庫事件，並將事件寫入 Azure 儲存體或 Azure 監視器記錄中的稽核記錄，或寫入事件中樞。 稽核有助於保持法規合規性、了解資料庫活動，以及深入了解可能指出潛在安全違規的不一致和異常。

若要啟用稽核：

1. 在 Azure 入口網站中，從左側功能表中選取 [SQL 資料庫]，然後在 [SQL 資料庫] 頁面上選取您的資料庫。

1. 在 [安全性] 區段中，選取 [稽核]。

1. 在 [稽核] 設定之下，設定下列值：

   1. 將 [稽核] 設定為 [開啟]。

   1. 選取 [稽核記錄目的地] 作為下列任何一項：

       - **儲存體**，其中儲存事件記錄並可下載為 *.xel* 檔案的 Azure 儲存體帳戶。

          > [!TIP]
          > 讓所有稽核的資料庫都使用相同的儲存體帳戶，以充分利用稽核報告範本。

       - **Log Analytics**，可自動儲存事件以供查詢或進一步分析

           > [!NOTE]
           > 需要有 **Log Analytics 工作區**才可支援進階功能，例如分析、自訂警示規則以及 Excel 或 Power BI 匯出。 沒有工作區，只可使用查詢編輯器。

       - **事件中樞**，允許路由傳送事件，以便在其他應用程式中使用

   1. 選取 [儲存]。

      ![稽核設定](./media/secure-database-tutorial/audit-settings.png)

1. 現在您可以選取 [檢視稽核記錄] 來檢視資料庫事件資料。

    ![稽核記錄](./media/secure-database-tutorial/audit-records.png)

> [!IMPORTANT]
> 請參閱 [SQL Database 稽核](../../azure-sql/database/auditing-overview.md)，了解如何使用 PowerShell 或 REST API 進一步自訂稽核事件。

### <a name="dynamic-data-masking"></a>動態資料遮罩

資料遮罩功能會自動隱藏您資料庫中的敏感性資料。

若要啟用資料遮罩：

1. 在 Azure 入口網站中，從左側功能表中選取 [SQL 資料庫]，然後在 [SQL 資料庫] 頁面上選取您的資料庫。

1. 在 [安全性] 區段中，選取 [動態資料遮罩]。

1. 在 [動態資料遮罩] 設定之下，選取 [新增遮罩] 以新增遮罩規則。 Azure 會自動填入可用的資料庫結構描述、資料表和資料行，以供選擇。

    ![遮罩設定](./media/secure-database-tutorial/mask-settings.png)

1. 選取 [儲存]。 選取的資訊現在已遮罩，以提供隱私權。

    ![遮罩範例](./media/secure-database-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>透明資料加密

加密功能會自動為您的待用資料加密，而且應用程式不須做任何變更來存取加密的資料庫。 新資料庫預設會開啟加密。 您也可以使用 SSMS 和[一律加密](always-encrypted-certificate-store-configure.md)功能將資料加密。

若要啟用或確認加密：

1. 在 Azure 入口網站中，從左側功能表中選取 [SQL 資料庫]，然後在 [SQL 資料庫] 頁面上選取您的資料庫。

1. 在 [安全性] 區段中，選取 [透明資料加密]。

1. 如有必要，請將 [資料加密] 設為 [開啟]。 選取 [儲存]。

    ![透明資料加密](./media/secure-database-tutorial/encryption-settings.png)

> [!NOTE]
> 若要檢視加密狀態，請使用 [SSMS](connect-query-ssms.md) 連線到資料庫，以及查詢 [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) 檢視的 `encryption_state` 資料行。 狀態 `3` 表示資料庫已加密。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解只要幾個簡單步驟，就可以改善資料庫的安全性。 您已了解如何︰

> [!div class="checklist"]
>
> - 建立伺服器層級和資料庫層級防火牆規則
> - 設定 Azure Active Directory (AD) 管理員
> - 使用 SQL 驗證、Azure AD 驗證和安全的連接字串來管理使用者存取
> - 啟用安全性功能，例如進階資料安全性、稽核、資料遮罩和加密

前進至下一個教學課程，以了解如何實作地理分散。

> [!div class="nextstepaction"]
>[實作異地分散式資料庫](geo-distributed-application-configure-tutorial.md)
