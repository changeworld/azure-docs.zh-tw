---
title: 安全性概觀
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 瞭解 Azure SQL Database 和 Azure SQL 受控執行個體中的安全性，包括其與 SQL Server 有何不同。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 09/21/2020
ms.openlocfilehash: f3ae5e1ef4dc2968724daeafb32f26cf445b0d2f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885287"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Azure SQL Database 與 SQL 受控執行個體安全性功能的總覽
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文概述使用 [Azure SQL Database](sql-database-paas-overview.md) 和 [Azure SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md)保護應用程式資料層的基本概念。 所述的安全性策略會遵循下圖所示的多層式深度防禦方法，並從外部移入：

![sql 安全性 layer.png](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>網路安全性

Microsoft Azure SQL Database 和 SQL 受控執行個體為雲端和企業應用程式提供關係資料庫服務。 為了協助保護客戶資料，防火牆會在根據 IP 位址或 Azure 虛擬網路流量來源明確授與存取權之前，防止對伺服器的網路存取。

### <a name="ip-firewall-rules"></a>IP 防火牆規則

IP 防火牆規則會根據每個要求的來源 IP 位址授與資料庫存取權。 如需詳細資訊，請參閱 [Azure SQL Database 和 Azure Synapse Analytics 防火牆規則的總覽](firewall-configure.md)。

### <a name="virtual-network-firewall-rules"></a>虛擬網路防火牆規則

[虛擬網路服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)會透過 Azure 骨幹擴充您的虛擬網路連線能力，並且讓 Azure SQL Database 找出流量源自的虛擬網路子網路。 若要允許流量觸達 Azure SQL Database，請使用 SQL[服務標籤](../../virtual-network/security-overview.md)，允許透過網路安全性群組輸出流量。

[虛擬網路規則](vnet-service-endpoint-rule-overview.md) 可讓 Azure SQL Database 只接受從虛擬網路內所選子網傳送的通訊。

> [!NOTE]
> 使用防火牆規則來控制存取 *權並不適* 用於 **SQL 受控執行個體**。 如需有關所需網路設定的詳細資訊，請參閱 [連接到受控實例](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>存取管理

> [!IMPORTANT]
> 在 Azure 內管理資料庫和伺服器，是由入口網站使用者帳戶的角色指派所控制。 如需本文的詳細資訊，請參閱 [Azure 入口網站中的角色型存取控制](../../role-based-access-control/overview.md)。

### <a name="authentication"></a>驗證

驗證是證明使用者宣告身分的程序。 Azure SQL Database 和 SQL 受控執行個體支援兩種類型的驗證：

- **SQL 驗證**：

    SQL 驗證指的是使用使用者名稱和密碼連接到 Azure SQL Database 或 Azure SQL 受控執行個體時的使用者驗證。 建立伺服器時，必須指定具有使用者名稱和密碼的 **伺服器管理員** 登入。 **伺服器管理員**可以使用這些認證，以資料庫擁有者身分向該伺服器或實例上的任何資料庫進行驗證。 在那之後，伺服器管理員可以建立其他 SQL 登入和使用者，可讓使用者使用使用者名稱和密碼進行連線。

- **Azure Active Directory authentication**：

    Azure Active Directory authentication 是使用 Azure Active Directory (Azure AD) 中的身分識別連線到 [Azure SQL Database](sql-database-paas-overview.md)、 [Azure SQL 受控執行個體](../managed-instance/sql-managed-instance-paas-overview.md) 和 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 的機制。 Azure AD authentication 可讓系統管理員集中管理資料庫使用者的身分識別和許可權，以及在一個集中位置的其他 Azure 服務。 這包括密碼儲存體的最小化，並啟用集中式密碼輪替原則。

     必須建立稱為 **Active Directory 系統管理員**的伺服器管理員，才能使用 Azure AD 驗證搭配 SQL Database。 如需詳細資訊，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database](authentication-aad-overview.md)。 Azure AD 驗證支援受控和同盟帳戶。 同盟帳戶支援與 Azure AD 同盟的客戶網域的 Windows 使用者和群組。

    其他可用的 Azure AD 驗證選項為[適用於 SQL Server Management Studio 的 Active Directory 通用驗證](authentication-mfa-ssms-overview.md)連線，包括 [Multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) 和[條件式存取](conditional-access-configure.md)。

> [!IMPORTANT]
> 在 Azure 內管理資料庫和伺服器，是由入口網站使用者帳戶的角色指派所控制。 如需有關此文章的詳細資訊，請參閱 [Azure 入口網站中的角色型存取控制](../../role-based-access-control/overview.md)。 使用防火牆規則來控制存取 *權並不適* 用於 **SQL 受控執行個體**。 如需有關所需網路設定的詳細資訊，請參閱下列文章， [以瞭解如何連線到受控實例](../managed-instance/connect-application-instance.md) 。

## <a name="authorization"></a>授權

授權是指指派給 Azure SQL Database 或 Azure SQL 受控執行個體中資料庫內的使用者的許可權，並決定允許使用者執行的動作。 許可權的控制方式是將使用者帳戶加入至 [資料庫角色](/sql/relational-databases/security/authentication-access/database-level-roles) ，並將資料庫層級許可權指派給這些角色，或授與使用者某些 [物件層級許可權](/sql/relational-databases/security/permissions-database-engine)。 如需詳細資訊，請參閱[登入與使用者](logins-create-manage.md)

最佳做法是在需要時建立自訂角色。 將使用者新增至具有工作功能所需最低許可權的角色。 請勿直接將許可權指派給使用者。 伺服器系統管理員帳戶是內建 db_owner 角色的成員，其具有廣泛的許可權，而且只能授與給具有系統管理責任的少數使用者。 若為應用程式，請使用 [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) 來指定被呼叫模組的執行內容，或使用具有有限許可權的 [應用程式角色](/sql/relational-databases/security/authentication-access/application-roles) 。 這種作法可確保連接至資料庫的應用程式具有應用程式所需的最低許可權。 遵循這些最佳作法也會促進責任的分隔。

### <a name="row-level-security"></a>資料列層級安全性

資料列層級安全性可讓客戶根據執行查詢的使用者特性 (例如群組成員資格或執行內容)，來控制對資料庫資料表中資料列的存取。 資料列層級安全性也可以用來執行自訂的標籤式安全性概念。 如需詳細資訊，請參閱資料 [列層級安全性](/sql/relational-databases/security/row-level-security)。

![azure 資料庫 rls.png](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>威脅保護

SQL Database 和 SQL 受控執行個體提供審核和威脅偵測功能，來保護客戶資料。

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Azure 監視器記錄和事件中樞中的 SQL 審核

SQL Database 和 SQL 受控執行個體 audit 會將資料庫事件記錄至客戶擁有的 Azure 儲存體帳戶中的 audit 記錄，以追蹤資料庫活動並協助維護安全性標準的合規性。 稽核可讓使用者監視進行中的資料庫活動，以及分析和調查歷史活動，以找出潛在威脅或可疑的濫用和安全性違規。 如需詳細資訊，請參閱開始使用 [SQL Database 的審核](../../azure-sql/database/auditing-overview.md)。  

### <a name="advanced-threat-protection"></a>進階威脅防護

先進的威脅防護會分析您的記錄，以偵測不尋常的行為，以及可能有害的存取或惡意探索資料庫的嘗試。 系統會針對可疑的活動建立警示，例如 SQL 插入式攻擊、可能的資料滲透，以及暴力密碼破解攻擊或存取模式中的異常情況，以攔截許可權提升和違反的認證使用。 系統會從  [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)中查看警示，其中會提供可疑活動的詳細資料，並提供進一步調查的建議以及減輕威脅的動作。 您可以針對每部伺服器啟用 Advanced 威脅防護，以產生額外費用。 如需詳細資訊，請參閱 [SQL Database Advanced 威脅防護入門](threat-detection-configure.md)。

![azure 資料庫 td.jpg](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>資訊保護和加密

### <a name="transport-layer-security-encryption-in-transit"></a>傳輸層安全性 (加密傳輸中) 

SQL Database 和 SQL 受控執行個體透過 [傳輸層安全性 (TLS) ](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)來加密移動中的資料，以保護客戶資料。

SQL Database 和 SQL 受控執行個體對所有連線強制執行加密 (SSL/TLS) 。 這可確保不論連接字串中的 **加密** 或 **TrustServerCertificate** 設定，所有資料都會在用戶端與伺服器之間加密「傳輸中」。

最佳做法是，建議您在應用程式所使用的連接字串中，指定加密的連接，而 _**不**_ 信任伺服器憑證。 這會強制您的應用程式驗證伺服器憑證，從而防止您的應用程式容易遭受中間人攻擊。

例如，使用 ADO.NET 驅動程式時，這是透過  **Encrypt = True** 和 **TrustServerCertificate = False**來完成。 如果您從 Azure 入口網站取得連接字串，則它將具有正確的設定。

> [!IMPORTANT]
> 請注意，某些非 Microsoft 驅動程式預設可能不會使用 TLS，或依賴舊版的 TLS ( # B0 1.2) 才能運作。 在此情況下，伺服器仍可讓您連接到您的資料庫。 不過，我們建議您評估允許這類驅動程式和應用程式連接到 SQL Database 的安全性風險，特別是在您儲存機密資料的情況下。
>
> 如需 TLS 和連線能力的詳細資訊，請參閱 [tls 考慮](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>透明資料加密 (待用資料加密)

[透明資料加密 (TDE) for Azure SQL Database 和 SQL 受控執行個體](transparent-data-encryption-tde-overview.md) 增加了一層安全性，以協助保護待用資料免于未經授權或離線存取原始檔案或備份。 常見的案例包括資料中心遭竊或不安全的硬體或媒體（例如磁片磁碟機和備份磁帶）處置。TDE 會使用 AES 加密演算法來加密整個資料庫，而不需要應用程式開發人員對現有的應用程式進行任何變更。

在 Azure 中，預設會加密所有新建立的資料庫，而且資料庫加密金鑰是由內建的伺服器憑證所保護。  憑證維護和輪替是由服務所管理，而且不需要使用者輸入。 想要掌控加密金鑰的客戶可以在 [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md) 中管理金鑰。

### <a name="key-management-with-azure-key-vault"></a>Azure Key Vault 的金鑰管理

[攜帶您自己的金鑰](transparent-data-encryption-byok-overview.md) (BYOK)  [透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE 的支援，可讓客戶使用 [) ](../../key-vault/general/secure-your-key-vault.md)Azure 的雲端式外部金鑰管理系統來取得金鑰管理與輪替的擁有權。 如果撤銷了資料庫對金鑰保存庫的存取權，資料庫即無法解密並讀取到記憶體中。 Azure Key Vault 可提供集中金鑰管理平台、使用嚴密監控的硬體安全性模組 (HSM)，並可區分管理金鑰和資料的職責，以利符合安全性合規性需求。

### <a name="always-encrypted-encryption-in-use"></a>一律加密 (使用中加密)

![azure 資料庫 ae.png](./media/security-overview/azure-database-ae.png)

[一律加密](/sql/relational-databases/security/encryption/always-encrypted-database-engine)功能的設計訴求是要保護特定資料庫資料行中儲存的敏感性資料以防存取 (例如，信用卡號碼、身分證號碼，或「必須知道」__ 的資料)。 這包括資料庫管理員或其他特殊權限的使用者，該使用者經授權存取資料庫以執行管理工作，但沒有存取已加密資料行中特定資料的商務需求。 資料一律會加密，這表示加密的資料會解密，僅供可存取加密金鑰的用戶端應用程式進行處理。 加密金鑰永遠不會公開給 SQL Database 或 SQL 受控執行個體，而且可以儲存在 [Windows 憑證存放區](always-encrypted-certificate-store-configure.md) 或 [Azure Key Vault](always-encrypted-azure-key-vault-configure.md)中。

### <a name="dynamic-data-masking"></a>動態資料遮罩

![azure 資料庫 ddm.png](./media/security-overview/azure-database-ddm.png)

動態資料遮罩會對不具權限的使用者遮罩機密資料，從而限制其曝光。 動態資料遮罩會自動在 Azure SQL Database 和 SQL 受控執行個體中探索潛在的敏感性資料，並提供可採取動作的建議以遮罩這些欄位，對應用層的影響最低。 其運作方式為針對指定的資料庫欄位隱匿查詢結果集中的敏感性資料，而不變更資料庫中的資料。 如需詳細資訊，請參閱 [開始使用 SQL Database 和 SQL 受控執行個體動態資料遮罩](dynamic-data-masking-overview.md)。

## <a name="security-management"></a>安全性管理

### <a name="vulnerability-assessment"></a>弱點評估

[弱點評量](sql-vulnerability-assessment.md)是容易設定的服務，可探索、追蹤及協助修復潛在的資料庫弱點，其目標在於主動改善整體資料庫安全性。 弱點評估 (VA) 是 Azure Defender for SQL 供應專案的一部分，這是適用于 advanced SQL 安全性功能的整合套件。 您可以透過中央 Azure Defender for SQL 入口網站來存取和管理弱點評定。

### <a name="data-discovery-and-classification"></a>資料探索與分類

資料探索與分類 (目前為預覽狀態) 提供內建于 Azure SQL Database 和 SQL 受控執行個體的先進功能，可用於探索、分類、標記及保護資料庫中的敏感性資料。 探索與分類您最具敏感性的資料 (商業/財務、醫療保健、個人資料等)，可在您組織的資訊保護方面扮演著關鍵角色。 它可以作為以下的基礎結構：

- 各種安全性案例，例如針對敏感性資料異常存取的監視 (稽核) 及警示。
- 對包含高度敏感性資料的資料庫進行存取控制並強化安全性。
- 協助符合資料隱私標準和法規合規性需求。

如需詳細資訊，請參閱 [開始使用資料探索和分類](data-discovery-and-classification-overview.md)。

### <a name="compliance"></a>合規性

除了上述可協助您的應用程式符合各種安全性需求的特色和功能之外，Azure SQL Database 也定期參與稽核，並且經過認證符合許多法規標準。 如需詳細資訊，請參閱 [Microsoft Azure 信任中心](https://www.microsoft.com/trust-center/compliance/compliance-overview) ，您可以在此找到最新的 SQL Database 合規性認證清單。

## <a name="next-steps"></a>下一步

- 如需使用登入、使用者帳戶、資料庫角色，以及 SQL Database 和 SQL 受控執行個體中之許可權的討論，請參閱管理登入 [和使用者帳戶](logins-create-manage.md)。
- 如需資料庫審核的討論，請參閱「 [審核](../../azure-sql/database/auditing-overview.md)」。
- 如需威脅偵測的討論，請參閱 [威脅偵測](threat-detection-configure.md)。
