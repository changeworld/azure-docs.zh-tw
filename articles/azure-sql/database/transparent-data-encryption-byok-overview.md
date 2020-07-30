---
title: 客戶管理的透明資料加密（TDE）
description: 攜帶您自己的金鑰（BYOK）支援透明資料加密（TDE）與 SQL Database 和 Azure Synapse Analytics 的 Azure Key Vault。 搭配使用 TDE 與 BYOK 的概觀、優點、運作方式、考量和建議。
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: cf0fec1f081a232abc88941e3dd785fb7617fb57
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387110"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>搭配使用 Azure SQL 透明資料加密與客戶管理的金鑰
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL[透明資料加密（TDE）](/sql/relational-databases/security/encryption/transparent-data-encryption)與客戶管理的金鑰，可讓攜帶您自己的金鑰（BYOK）案例進行待用資料保護，並可讓組織在金鑰和資料的管理中執行責任分離。 有了客戶管理的透明資料加密，客戶就能以完全控制金鑰生命週期管理（金鑰建立、上傳、輪替、刪除）、金鑰使用許可權，以及對金鑰進行作業的審核。

在此案例中，用來加密資料庫加密金鑰（DEK）的金鑰（稱為 TDE 保護裝置）是以雲端為基礎的外部金鑰管理系統，儲存在客戶擁有和客戶管理的[Azure Key Vault （AKV）](../../key-vault/general/secure-your-key-vault.md)中的客戶管理的非對稱金鑰。 Key Vault 是適用于 RSA 密碼編譯金鑰的高可用性和可擴充的安全儲存體，並可選擇性地由 FIPS 140-2 Level 2 驗證的硬體安全性模組（Hsm）支援。 它不允許直接存取預存金鑰，而是使用授權實體的金鑰提供加密/解密服務。 金鑰可由金鑰保存庫產生、匯入，或[從內部內部部署 HSM 裝置傳輸至金鑰保存庫](../../key-vault/keys/hsm-protected-keys.md)。

針對 Azure SQL Database 和 Azure Synapse 分析，TDE 保護裝置會在伺服器層級設定，並由與該伺服器相關聯的所有加密資料庫繼承。 針對 Azure SQL 受控執行個體，TDE 保護裝置會在實例層級設定，並由該實例上所有加密的資料庫繼承。 「*伺服器*」一詞指的是 SQL Database 和 Azure Synapse 中的伺服器，以及本檔中 SQL 受控執行個體的受控實例，除非另有不同的指定。

> [!IMPORTANT]
> 對於使用服務管理的 TDE 而想要開始使用客戶管理的 TDE，資料會在切換的過程中保持加密，而且不會有停機時間或重新加密資料庫檔案。 從服務管理的金鑰切換到客戶管理的金鑰，只需要重新加密 DEK，這是快速且線上的操作。

> [!NOTE]
> 若要為 Azure SQL 客戶提供兩層待用資料加密，則會推出具有平臺管理金鑰的基礎結構加密（使用 AES-256 加密演算法）。這會提供待用加密的一層，以及 TDE 與客戶管理的金鑰（已可供使用）。 目前，客戶必須要求存取這項功能。 如果您對這項功能有興趣，請聯絡 AzureSQLDoubleEncryptionAtRest@service.microsoft.com 。

## <a name="benefits-of-the-customer-managed-tde"></a>客戶管理 TDE 的優點

客戶管理的 TDE 為客戶提供下列優點：

- TDE 保護裝置的使用方式和管理的完整和細微控制;

- TDE 保護裝置使用方式的透明度;

- 能夠在組織內的金鑰和資料的管理中執行責任區隔，

- Key Vault 系統管理員可以撤銷金鑰存取權限，使加密資料庫無法存取;

- 集中管理 AKV 中的金鑰;

- 與您的終端客戶較大的信任，因為 AKV 的設計是讓 Microsoft 無法看到也不會解壓縮加密金鑰;

## <a name="how-customer-managed-tde-works"></a>客戶管理的 TDE 如何運作

![客戶管理 TDE 的設定和運作](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

若要讓伺服器能夠使用儲存在 AKV 中的 TDE 保護裝置進行 DEK 加密，key vault 系統管理員必須使用其唯一的 Azure Active Directory （Azure AD）身分識別，授與伺服器的下列存取權限：

- **get** -用於在 Key Vault 中抓取金鑰的公用元件和屬性

- **wrapKey** -能夠保護（加密） DEK

- **unwrapKey** -能夠解除保護（解密） DEK

金鑰保存庫系統管理員也可以[啟用金鑰保存庫 audit 事件的記錄](../../azure-monitor/insights/key-vault-insights-overview.md)，以便稍後進行審核。

當伺服器設定為使用 AKV 的 TDE 保護裝置時，伺服器會將每個已啟用 TDE 之資料庫的 DEK 傳送至金鑰保存庫以進行加密。 Key vault 會傳回加密的 DEK，然後儲存在使用者資料庫中。

如有需要，伺服器會將受保護的 DEK 傳送至金鑰保存庫以進行解密。

如果啟用記錄功能，則審計員可以使用 Azure 監視器來審查金鑰保存庫 AuditEvent 記錄。

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>設定客戶管理 TDE 的需求

### <a name="requirements-for-configuring-akv"></a>設定 AKV 的需求

- 金鑰保存庫和 SQL Database/受控實例必須屬於相同的 Azure Active Directory 租使用者。 不支援跨租用戶金鑰保存庫與伺服器的互動。 若要之後移動資源，則必須重新設定具有 AKV 的 TDE。 深入瞭解如何[移動資源](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

- 必須在金鑰保存庫上啟用虛[刪除](../../key-vault/general/soft-delete-overview.md)功能，以防止意外遺失金鑰（或金鑰保存庫）刪除。 虛刪除的資源會保留90天，除非客戶在此期間復原或清除。 *復原*和*清除*動作本身的權限已在金鑰保存庫的存取原則中建立關聯。 虛刪除功能預設為關閉，並可透過[PowerShell](../../key-vault/general/soft-delete-powershell.md#enabling-soft-delete)或[CLI](../../key-vault/general/soft-delete-cli.md#enabling-soft-delete)啟用。 它無法透過 Azure 入口網站啟用。  

- 使用 Azure Active Directory 身分識別，將存取金鑰保存庫（get、wrapKey、unwrapKey）的許可權授與伺服器或受控實例。 使用 Azure 入口網站時，會自動建立 Azure AD 身分識別。 使用 PowerShell 或 CLI 時，必須明確地建立 Azure AD 身分識別，並驗證完成。 如需使用 PowerShell 的詳細逐步指示，請參閱[CONFIGURE TDE WITH BYOK](transparent-data-encryption-byok-configure.md)和[CONFIGURE TDE WITH BYOK for SQL 受控執行個體](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md)。

- 使用防火牆搭配 AKV 時，您必須啟用 *[允許受信任的 Microsoft 服務略過防火牆*] 選項。

### <a name="requirements-for-configuring-tde-protector"></a>設定 TDE 保護裝置的需求

- TDE 保護裝置只能是非對稱、RSA 或 RSA HSM 金鑰。 支援的金鑰長度為2048和3072個位元組。

- 金鑰啟用日期 (若已設定) 必須是過去的日期和時間。 到期日（如果設定）必須是未來的日期和時間。

- 金鑰必須處於「已啟用」狀態。

- 如果您要將現有的金鑰匯入金鑰保存庫，請務必以支援的檔案格式（.pfx、byok 或 backup）提供它。

## <a name="recommendations-when-configuring-customer-managed-tde"></a>設定客戶管理的 TDE 時的建議

### <a name="recommendations-when-configuring-akv"></a>設定 AKV 時的建議

- 當伺服器存取金鑰保存庫中的 TDE 保護裝置時，在單一訂用帳戶中，最多可將500一般用途或200個業務關鍵資料庫與金鑰保存庫建立關聯，以確保高可用性。 這些數位是以[金鑰保存庫服務限制](../../key-vault/general/service-limits.md)中的經驗為基礎。 這裡的目的是要避免伺服器容錯移轉後的問題，因為它會對保存庫觸發多個金鑰作業，因為該伺服器上有資料庫。

- 在金鑰保存庫上設定資源鎖定，以控制可以刪除此重要資源的人員，並防止意外或未經授權的刪除作業。 深入瞭解[資源鎖定](../../azure-resource-manager/management/lock-resources.md)。

- 啟用所有加密金鑰的審核和報告： Key vault 提供的記錄可輕鬆地插入其他安全性資訊和事件管理工具。 Operations Management Suite [Log Analytics](../../azure-monitor/insights/azure-key-vault.md)是已整合之服務的其中一個範例。

- 將每部伺服器與位於不同區域且持有相同金鑰內容的兩個金鑰保存庫連結在一起，以確保加密資料庫的高可用性。 只將金鑰保存庫中的金鑰，從與 TDE 保護裝置相同的區域中標記。 如果中斷會影響相同區域中的金鑰保存庫，系統將會自動切換至遠端區域中的金鑰保存庫。

### <a name="recommendations-when-configuring-tde-protector"></a>設定 TDE 保護裝置時的建議

- 將 TDE 保護裝置的複本保留在安全的位置，或將它證書處理到憑證服務。

- 如果金鑰是在金鑰保存庫中產生，請在第一次使用 AKV 中的金鑰之前，先建立金鑰備份。 只能將備份還原到 Azure Key Vault。 深入瞭解[AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey)命令。

- 每當對金鑰進行任何變更時（例如索引鍵屬性、標記、Acl），建立新的備份。

- 在輪替金鑰時**將舊版保留**在金鑰保存庫中，以便在還原較舊的資料庫備份時使用。 當資料庫的 TDE 保護裝置變更時，資料庫的舊備份**不會更新**為使用最新的 TDE 保護裝置。 在還原時，每個備份都需要在建立時加密的 TDE 保護裝置。 您可以依照[使用 PowerShell 輪替透明資料加密保護裝置](transparent-data-encryption-byok-key-rotation.md)中的指示，來執行金鑰輪替。

- 將所有先前使用的金鑰保留在 AKV 中，即使在切換到服務管理的金鑰之後也一樣。 它可確保您可以使用儲存在 AKV 中的 TDE 保護裝置來還原資料庫備份。  使用 Azure Key Vault 建立的 TDE 保護裝置，必須等到所有剩餘的已儲存備份都已使用服務管理的金鑰建立後，才能進行維護。 使用[AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey)，為這些金鑰建立可復原的備份複本。

- 若要在安全性事件期間移除可能遭盜用的金鑰，而不會有資料遺失的風險，請遵循[移除可能遭盜用的金鑰](transparent-data-encryption-byok-remove-tde-protector.md)中的步驟。

## <a name="inaccessible-tde-protector"></a>無法存取 TDE 保護裝置

當透明資料加密設定為使用客戶管理的金鑰時，需要持續存取 TDE 保護裝置，資料庫才會保持線上狀態。 如果伺服器無法存取 AKV 中客戶管理的 TDE 保護裝置，在10分鐘內，資料庫將會開始拒絕所有具有對應錯誤訊息的連接，並將其狀態變更為*無法存取*。 在無法存取狀態的資料庫上，唯一允許的動作是刪除它。

> [!NOTE]
> 如果因為間歇網路中斷而無法存取資料庫，則不需要採取任何動作，而且資料庫將會自動復原上線。

還原金鑰的存取權後，使資料庫恢復上線需要額外的時間和步驟，而這可能會根據經過的時間而有所不同，而不需存取資料庫中的金鑰和資料大小：

- 如果在8小時內還原金鑰存取，資料庫將會在下一個小時內自動修復。

- 如果在超過 8 個小時後還原金鑰存取，則無法自動修復，且讓資料庫重新上線需要在入口網站上採取額外步驟，視資料庫大小而定可能需要相當長的時間。 一旦資料庫恢復上線，先前設定的伺服器層級設定（例如[容錯移轉群組](auto-failover-group-overview.md)設定、時間點還原歷程記錄，以及標記）將會**遺失**。 因此，建議您執行通知系統，讓您在8小時內識別並處理基礎金鑰存取問題。

### <a name="accidental-tde-protector-access-revocation"></a>意外 TDE 保護裝置存取撤銷

可能的情況是，具有金鑰保存庫的足夠存取權限的人不小心停用伺服器存取金鑰的方法：

- 從伺服器撤銷 key vault 的*get*、 *wrapKey*、 *unwrapKey*許可權

- 刪除金鑰

- 刪除金鑰保存庫

- 變更金鑰保存庫的防火牆規則

- 在 Azure Active Directory 中刪除伺服器的受控識別

深入瞭解[資料庫變成無法存取的常見原因](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)。

## <a name="monitoring-of-the-customer-managed-tde"></a>監視客戶管理的 TDE

若要監視資料庫狀態並啟用遺失 TDE 保護裝置存取的警示，請設定下列 Azure 功能：

- [Azure 資源健康狀態](../../service-health/resource-health-overview.md)。 當資料庫的第一次連接遭到拒絕之後，無法存取 TDE 保護裝置的資料庫會顯示為「無法使用」。
- [活動記錄](../../service-health/alerts-activity-log-service-notifications.md)當客戶管理的金鑰保存庫中的 TDE 保護裝置存取失敗時，會將專案新增至活動記錄。  建立這些事件的警示可讓您儘快恢復存取。
- 您可以定義[動作群組](../../azure-monitor/platform/action-groups.md)，以根據您的喜好設定（例如電子郵件/SMS/推播/語音、邏輯應用程式、WEBHOOK、ITSM 或自動化 Runbook）傳送通知和警示。

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>使用客戶管理的 TDE 進行資料庫備份和還原

當資料庫使用 Key Vault 的金鑰從 TDE 加密時，任何新產生的備份也會以相同的 TDE 保護裝置加密。 當 TDE 保護裝置變更時，資料庫的舊備份**不會更新**為使用最新的 TDE 保護裝置。

若要從 Key Vault 還原以 TDE 保護裝置加密的備份，請確定目標伺服器有可用的金鑰內容。 因此，建議您將所有舊版的 TDE 保護裝置保留在金鑰保存庫中，以便還原資料庫備份。

> [!IMPORTANT]
> 在任何時候，伺服器都不能設定一個以上的 TDE 保護裝置。 這是在 Azure 入口網站] 分頁中，標示為「將金鑰設為預設 TDE 保護裝置」的金鑰。 不過，您可以將多個額外的金鑰連結到伺服器，而不需要將它們標示為 TDE 保護裝置。 這些金鑰不會用來保護 DEK，但如果備份檔案是以具有對應指紋的金鑰加密，則可以在從備份還原期間使用。

如果還原備份所需的金鑰已無法再供目標伺服器使用，還原嘗試：「目標伺服器 `<Servername>` 無法存取在和之間建立的所有 AKV uri，就會傳回下列錯誤訊息 \<Timestamp #1> \<Timestamp #2> 。 請在還原所有 AKV 的 Uri 之後重試操作」。

若要減輕此問題，請針對目標伺服器執行[AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) Cmdlet，或針對目標受控實例執行[AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) ，以傳回可用的金鑰清單，並找出遺漏的索引鍵。 為確保所有備份都能還原，請確定還原的目標伺服器可以存取所需的所有金鑰。 這些金鑰不需要標示為 TDE 保護裝置。

若要深入瞭解 SQL Database 的備份復原，請參閱[在 SQL Database 中復原資料庫](recovery-using-backups.md)。 若要深入瞭解 SQL 集區的備份復原，請參閱[復原 Sql 集](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md)區。 如需 SQL Server 的原生備份/還原與 SQL 受控執行個體，請參閱[快速入門：將資料庫還原至 SQL 受控執行個體](../managed-instance/restore-sample-database-quickstart.md)

記錄檔的其他考慮：已備份的記錄檔仍會以原始 TDE 保護裝置加密，即使已輪替，且資料庫現在正在使用新的 TDE 保護裝置。  在還原時，必須要有這兩個金鑰才能還原資料庫。  如果記錄檔使用儲存在 Azure Key Vault 中的 TDE 保護裝置，即使資料庫已變更為在同時使用服務管理的 TDE，仍會在還原時需要此金鑰。

## <a name="high-availability-with-customer-managed-tde"></a>客戶管理的 TDE 高可用性

即使在沒有為伺服器設定異地複寫的情況下，強烈建議您將伺服器設定為在兩個不同區域中使用兩個不同的金鑰保存庫，並具有相同的金鑰內容。 您可以使用與伺服器位於相同區域的主要金鑰保存庫來建立 TDE 保護裝置，並將金鑰複製到不同 Azure 區域中的金鑰保存庫，讓伺服器能夠存取第二個金鑰保存庫，如此一來，當資料庫啟動並執行時，主要金鑰保存庫就會發生中斷。

使用 AzKeyVaultKey Cmdlet，從主要金鑰保存庫取出加密格式的金鑰，然後使用 AzKeyVaultKey Cmdlet 並在第二個區域中指定金鑰保存庫，以複製金鑰。 或者，使用 Azure 入口網站來備份和還原金鑰。 另一個區域中次要金鑰保存庫中的金鑰不應標示為 TDE 保護裝置，也不允許使用。

如果影響主要金鑰保存庫的中斷，而且只有在此情況下，系統將會自動切換到次要金鑰保存庫中具有相同指紋的其他連結金鑰（如果有的話）。 請注意，如果 TDE 保護裝置因為撤銷存取權限而無法存取，或因為金鑰或金鑰保存庫遭到刪除，則不會發生此參數，因為這可能表示客戶刻意想要限制伺服器存取金鑰。

![單一伺服器 HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>異地 DR 和客戶管理的 TDE

在「[主動式異地](active-geo-replication-overview.md)複寫」和「[容錯移轉群組](auto-failover-group-overview.md)」案例中，每個涉及的伺服器都需要個別的金鑰保存庫，且必須與伺服器共置於相同的 Azure 區域中。 客戶須負責讓金鑰保存庫中的金鑰資料保持一致，以便讓異地次要資料庫保持同步，而且如果主要複本因為區域中斷而無法存取，而且會觸發容錯移轉，就可以從其本機密鑰保存庫使用相同的金鑰。 最多可以設定四個次要資料庫，且不支援連結（次要次要資料庫）。

若要避免在因為金鑰材料不完整而在異地複寫期間發生問題，請務必在設定客戶管理的 TDE 時遵循下列規則：

- 所有相關的金鑰保存庫都必須具有相同的屬性，以及個別伺服器的相同存取權限。

- 所有相關的金鑰保存庫都必須包含相同的金鑰內容。 它不只會套用至目前的 TDE 保護裝置，也適用于備份檔案中可能使用的所有先前的 TDE 保護裝置。

- TDE 保護裝置的初始設定和輪替必須先在次要複本上完成，然後才在主要複本上進行。

![容錯移轉群組和異地災害復原](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

若要測試容錯移轉，請遵循[主動式異地複寫總覽](active-geo-replication-overview.md)中的步驟。 應定期進行測試容錯移轉，以驗證 SQL Database 已維護這兩個金鑰保存庫的存取權限。

## <a name="next-steps"></a>後續步驟

您也可能想要查看下列 PowerShell 範例腳本，以取得客戶管理 TDE 的一般作業：

- [使用 PowerShell 輪替 SQL Database 的透明資料加密保護裝置](transparent-data-encryption-byok-key-rotation.md)

- [使用 PowerShell 移除 SQL Database 的透明資料加密（TDE）保護裝置](transparent-data-encryption-byok-remove-tde-protector.md)

- [使用 PowerShell 以您自己的金鑰管理 SQL 受控執行個體中的透明資料加密](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)
