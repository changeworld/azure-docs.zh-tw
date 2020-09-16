---
title: '客戶管理的透明資料加密 (TDE) '
description: 攜帶您自己的金鑰 (BYOK) 透明資料加密支援 (和) 的 Azure Key Vault SQL Database TDE Azure Synapse Analytics。 搭配使用 TDE 與 BYOK 的概觀、優點、運作方式、考量和建議。
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
ms.openlocfilehash: 4e17af8289c68ded282a9c4a9ca2d400d31ca30d
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602664"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>搭配使用 Azure SQL 透明資料加密與客戶管理的金鑰
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL [透明資料加密 (TDE) ](/sql/relational-databases/security/encryption/transparent-data-encryption) 與客戶管理的金鑰，可讓您攜帶您自己的金鑰 (適用于待用資料保護的 BYOK) 案例，並可讓組織在金鑰和資料的管理中執行責任區隔。 使用客戶管理的透明資料加密時，客戶會負責並完全掌控金鑰生命週期管理 (金鑰的建立、上傳、輪替、刪除) 、金鑰使用方式許可權，以及對金鑰的作業審核。

在此案例中，用來加密資料庫加密金鑰的金鑰 (DEK) （稱為 TDE 保護裝置）是客戶管理的非對稱金鑰，儲存在客戶擁有和客戶管理的 [Azure Key Vault (AKV) ](../../key-vault/general/secure-your-key-vault.md)（雲端式外部金鑰管理系統）中。 Key Vault 是適用于 RSA 密碼編譯金鑰的高度可用且可調整的安全儲存體，可透過 FIPS 140-2 層級2驗證的硬體安全性模組，選擇性地支援 (Hsm) 。 它不允許直接存取已儲存的金鑰，但會使用授權實體的金鑰提供加密/解密的服務。 金鑰可以由金鑰保存庫產生、匯入，或 [從內部內部部署 HSM 裝置傳輸至金鑰保存庫](../../key-vault/keys/hsm-protected-keys.md)。

針對 Azure SQL Database 和 Azure Synapse Analytics，TDE 保護裝置會在伺服器層級設定，並由與該伺服器相關聯的所有加密資料庫繼承。 針對 Azure SQL 受控執行個體，TDE 保護裝置會在實例層級設定，並由該實例上的所有加密資料庫繼承。 「 *伺服器* 」一詞指的是 SQL Database 和 Azure Synapse 中的伺服器，以及在本檔的 SQL 受控執行個體中的受控實例，除非有不同的陳述。

> [!IMPORTANT]
> 對於使用服務管理的 TDE，而想要開始使用客戶管理的 TDE，資料會在切換過程中保持加密狀態，而且不會有停機時間或重新加密資料庫檔案。 從服務管理的金鑰切換至客戶管理的金鑰時，只需要重新加密 DEK，這是一項快速且線上的操作。

> [!NOTE]
> 若要為 Azure SQL 客戶提供兩層的待用資料加密，使用 AES-256 加密演算法) 的基礎結構加密 (正在推出平臺管理的金鑰。這會提供待用加密的一層，以及 TDE 與客戶管理的金鑰（已提供）。 目前，客戶必須要求存取這項功能。 如果您對這項功能有興趣，請聯絡 AzureSQLDoubleEncryptionAtRest@service.microsoft.com 。

## <a name="benefits-of-the-customer-managed-tde"></a>客戶管理 TDE 的優點

客戶管理的 TDE 為客戶提供下列優點：

- 完整且細微地控制 TDE 保護裝置的使用方式和管理;

- TDE 保護裝置使用方式的透明度;

- 能夠在管理組織中的金鑰和資料時，將職責區隔分開。

- Key Vault 系統管理員可以撤銷金鑰存取權限，讓加密的資料庫無法存取;

- 集中管理 AKV 中的金鑰;

- 由於 AKV 的設計是為了讓 Microsoft 看不到或無法解壓縮加密金鑰，因此您的終端客戶可獲得更高的信任;

## <a name="how-customer-managed-tde-works"></a>客戶管理的 TDE 如何運作

![客戶管理 TDE 的設定和運作](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

若要讓伺服器能夠使用儲存在 AKV 中的 TDE 保護裝置來加密 DEK，key vault 系統管理員必須使用其唯一 Azure Active Directory (Azure AD) 身分識別，授與伺服器的下列存取權限：

- **取得** -以在 Key Vault 中取得索引鍵的公用部分和屬性

- **wrapKey** -能夠保護 (加密) DEK

- **unwrapKey** -若要能夠取消保護 (解密) DEK

Key vault 系統管理員也可以 [啟用金鑰保存庫審核事件的記錄](../../azure-monitor/insights/key-vault-insights-overview.md)，以便日後可以進行審核。

當伺服器設定為使用來自 AKV 的 TDE 保護裝置時，伺服器會將每個已啟用 TDE 之資料庫的 DEK 傳送至金鑰保存庫進行加密。 Key vault 會傳回加密的 DEK，然後儲存在使用者資料庫中。

當需要時，伺服器會將受保護的 DEK 傳送至金鑰保存庫進行解密。

如果啟用記錄功能，則審計員可以使用 Azure 監視器來檢查 key vault AuditEvent 記錄。

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>設定客戶管理 TDE 的需求

### <a name="requirements-for-configuring-akv"></a>設定 AKV 的需求

- Key vault 和 SQL Database/受控實例必須屬於相同的 Azure Active Directory 租使用者。 不支援跨租用戶金鑰保存庫與伺服器的互動。 若要在之後移動資源，則必須重新設定 TDE with AKV。 深入瞭解如何 [移動資源](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

- 必須在金鑰保存庫上啟用虛[刪除](../../key-vault/general/soft-delete-overview.md)功能，以防止資料遺失意外的金鑰 (或金鑰保存庫) 刪除發生。 除非客戶同時復原或清除虛刪除的資源，否則會保留90天。 *復原*和*清除*動作本身的權限已在金鑰保存庫的存取原則中建立關聯。 虛刪除功能預設為關閉，並可透過 [PowerShell](../../key-vault/general/soft-delete-powershell.md#enabling-soft-delete) 或 [CLI](../../key-vault/general/soft-delete-cli.md#enabling-soft-delete)來啟用。 無法透過 Azure 入口網站啟用。  

- 將金鑰保存庫 (get、wrapKey、unwrapKey) 的存取權授與伺服器或受控實例，並使用其 Azure Active Directory 身分識別。 使用 Azure 入口網站時，會自動建立 Azure AD 身分識別。 使用 PowerShell 或 CLI 時，必須明確建立 Azure AD 身分識別，並驗證完成。 如需使用 PowerShell 時的詳細逐步指示，請參閱使用 [BYOK 設定 TDE](transparent-data-encryption-byok-configure.md) 和 [設定 TDE WITH BYOK for SQL 受控執行個體](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) 。

- 搭配使用防火牆與 AKV 時，您必須啟用 *[允許信任的 Microsoft 服務] 選項來略過防火牆*。

### <a name="requirements-for-configuring-tde-protector"></a>設定 TDE 保護裝置的需求

- TDE 保護裝置只能是非對稱、RSA 或 RSA HSM 金鑰。 支援的金鑰長度為2048和3072個位元組。

- 金鑰啟用日期 (若已設定) 必須是過去的日期和時間。 到期日 (如果設定的) 必須是未來的日期和時間。

- 金鑰必須處於「已啟用」狀態。

- 如果您要將現有金鑰匯入至金鑰保存庫，請務必以支援的檔案格式提供 ( .pfx、byok 或. 備份) 。

> [!NOTE]
> Azure SQL 現在支援使用以 TDE 保護裝置形式儲存在受控 HSM 中的 RSA 金鑰。 這項功能處於 **公開預覽**狀態。 Azure Key Vault 受控 HSM 是完全受控、高可用性、單一租使用者、符合標準的雲端服務，可讓您使用 FIPS 140-2 層級3驗證的 Hsm 來保護雲端應用程式的密碼編譯金鑰。 深入瞭解 [受管理的 hsm](https://aka.ms/mhsm)。


## <a name="recommendations-when-configuring-customer-managed-tde"></a>設定客戶管理的 TDE 時的建議

### <a name="recommendations-when-configuring-akv"></a>設定 AKV 時的建議

- 使用單一訂用帳戶中的金鑰保存庫，與最多500一般用途或200個商務關鍵資料庫產生關聯，以確保伺服器存取金鑰保存庫中的 TDE 保護裝置時的高可用性。 這些數位是以 [金鑰保存庫服務限制](../../key-vault/general/service-limits.md)的經驗為基礎。 這裡的目的是要防止伺服器容錯移轉後的問題，因為該伺服器中有資料庫，所以會對保存庫觸發許多重要的作業。

- 設定金鑰保存庫的資源鎖定，以控制誰可以刪除這項重要資源，並防止意外或未經授權的刪除。 深入瞭解 [資源鎖定](../../azure-resource-manager/management/lock-resources.md)。

- 啟用所有加密金鑰的審核和報告： Key vault 提供容易插入其他安全性資訊和事件管理工具的記錄檔。 Operations Management Suite [Log Analytics](../../azure-monitor/insights/azure-key-vault.md) 是已整合之服務的其中一個範例。

- 將每個伺服器連結到位於不同區域的兩個金鑰保存庫，並保留相同的金鑰內容，以確保加密資料庫的高可用性。 只將金鑰保存庫中的金鑰，標示為與 TDE 保護裝置相同的區域。 如果中斷影響相同區域中的金鑰保存庫，系統將會自動切換到遠端區域中的金鑰保存庫。

### <a name="recommendations-when-configuring-tde-protector"></a>設定 TDE 保護裝置時的建議

- 將 TDE 保護裝置的複本保存在安全的位置，或將它提供給「加密」服務。

- 如果金鑰是在金鑰保存庫中產生的，則在第一次使用 AKV 中的金鑰之前，請先建立金鑰備份。 備份只能還原至 Azure Key Vault。 深入瞭解 [AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey) 命令。

- 每當對 (金鑰進行任何變更時（例如，索引鍵屬性、標記、Acl) ），就會建立新的備份。

- 在輪替金鑰時**將舊版保留**在金鑰保存庫中，以便在還原較舊的資料庫備份時使用。 當資料庫的 TDE 保護裝置變更時，資料庫的舊備份 **不會更新** 為使用最新的 TDE 保護裝置。 在還原時，每個備份都需要在建立時加密的 TDE 保護裝置。 您可以依照[使用 PowerShell 輪替透明資料加密保護裝置](transparent-data-encryption-byok-key-rotation.md)中的指示，來執行金鑰輪替。

- 即使在切換到服務管理的金鑰之後，仍在 AKV 中保留所有先前使用的金鑰。 它可確保可以使用儲存在 AKV 中的 TDE 保護裝置來還原資料庫備份。  使用 Azure Key Vault 建立的 TDE 保護裝置必須保持在所有剩餘的預存備份都以服務管理的金鑰建立之前。 使用 [備份 AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey)來建立這些金鑰的可復原備份複本。

- 若要在安全性事件期間移除可能遭盜用的金鑰，而不會遺失資料的風險，請依照 [移除可能遭盜用的金鑰](transparent-data-encryption-byok-remove-tde-protector.md)中的步驟執行。

## <a name="inaccessible-tde-protector"></a>無法存取 TDE 保護裝置

當透明資料加密設定為使用客戶管理的金鑰時，資料庫必須持續存取 TDE 保護裝置才能保持在線上狀態。 如果伺服器無法存取 AKV 中的客戶管理 TDE 保護裝置，則在最多10分鐘內，資料庫將會開始拒絕所有具有對應錯誤訊息的連線，並將其狀態變更為 *無法*存取。 資料庫處於無法存取狀態的唯一允許的動作是刪除它。

> [!NOTE]
> 如果因為間歇性的網路中斷而無法存取資料庫，則不需要採取任何動作，而且資料庫將會自動復原上線。

還原金鑰的存取權之後，讓資料庫重新上線需要額外的時間和步驟，這可能會因經過的時間而異，而不需要存取資料庫中的索引鍵和資料大小：

- 如果在8小時內還原金鑰存取，資料庫會在接下來的一小時內自動修復。

- 如果在超過 8 個小時後還原金鑰存取，則無法自動修復，且讓資料庫重新上線需要在入口網站上採取額外步驟，視資料庫大小而定可能需要相當長的時間。 一旦資料庫重新上線，先前設定的伺服器層級設定（例如 [容錯移轉群組](auto-failover-group-overview.md) 設定、時間點還原歷程記錄，以及標記）將會 **遺失**。 因此，建議您執行通知系統，讓您識別並解決8小時內的基礎金鑰存取問題。

以下是在入口網站上顯示無法存取的資料庫重新上線所需的其他步驟。

![TDE BYOK 無法存取的資料庫](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>意外 TDE 保護裝置存取撤銷

有足夠的許可權可存取金鑰保存庫時，可能會意外停用伺服器對金鑰的存取：

- 從伺服器撤銷金鑰保存庫的 *get*、 *wrapKey*、 *unwrapKey* 許可權

- 正在刪除金鑰

- 正在刪除金鑰保存庫

- 變更金鑰保存庫的防火牆規則

- 在 Azure Active Directory 中刪除伺服器的受控識別

深入瞭解 [資料庫變成無法存取的常見原因](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)。

## <a name="monitoring-of-the-customer-managed-tde"></a>監視客戶管理的 TDE

若要監視資料庫狀態，並針對遺失 TDE 保護裝置存取啟用警示，請設定下列 Azure 功能：

- [Azure 資源健康狀態](../../service-health/resource-health-overview.md)。 在資料庫的第一次連線遭到拒絕之後，無法存取且無法存取 TDE 保護裝置的資料庫將會顯示為「無法使用」。
- 當存取客戶管理的金鑰保存庫中的 TDE 保護裝置失敗時，[活動記錄](../../service-health/alerts-activity-log-service-notifications.md)會將專案新增至活動記錄。  建立這些事件的警示可讓您儘快恢復存取。
- 您可以定義[動作群組](../../azure-monitor/platform/action-groups.md)，以根據您的喜好設定傳送通知和警示，例如電子郵件/SMS/推播/語音、邏輯應用程式、WEBHOOK、ITSM 或自動化 Runbook。

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>使用客戶管理的 TDE 進行資料庫備份和還原

使用 Key Vault 中的金鑰從 TDE 加密資料庫之後，任何新產生的備份也會以相同的 TDE 保護裝置加密。 當 TDE 保護裝置變更時，資料庫的舊備份 **不會更新** 為使用最新的 TDE 保護裝置。

若要從 Key Vault 還原以 TDE 保護裝置加密的備份，請確定目標伺服器可以使用金鑰內容。 因此，我們建議您將所有舊版的 TDE 保護裝置保留在金鑰保存庫中，以便能夠還原資料庫備份。

> [!IMPORTANT]
> 在任何時候，伺服器上都不能設定一個以上的 TDE 保護裝置。 這是在 Azure 入口網站分頁中標示為「將金鑰設為預設 TDE 保護裝置」的金鑰。 不過，您可以將多個額外的金鑰連結到伺服器，而不需要將它們標示為 TDE 保護裝置。 這些金鑰不會用來保護 DEK，但是如果備份檔案是使用具有對應指紋的金鑰進行加密，就可以在從備份還原時使用。

如果還原備份所需的金鑰無法再供目標伺服器使用，則 restore try 會傳回下列錯誤訊息：「目標伺服器 `<Servername>` 無法存取與之間建立的所有 AKV uri \<Timestamp #1> \<Timestamp #2> 。 請在還原所有 AKV Uri 之後重試操作」。

若要減輕此問題，請執行目標伺服器的 [AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) 指令程式，或 [AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) 目標受控實例，以傳回可用金鑰的清單，並找出遺漏的金鑰。 為確保可以還原所有備份，請確定還原的目標伺服器可以存取所需的所有金鑰。 這些金鑰不需要標示為 TDE 保護裝置。

若要深入瞭解 SQL Database 的備份復原，請參閱 [SQL Database 中的復原資料庫](recovery-using-backups.md)。 若要深入瞭解 SQL 集區的備份復原，請參閱 [復原 Sql 集](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md)區。 如 SQL Server 的原生備份/還原與 SQL 受控執行個體，請參閱 [快速入門：將資料庫還原至 sql 受控執行個體](../managed-instance/restore-sample-database-quickstart.md)

記錄檔的其他考慮：已備份的記錄檔仍會以原始 TDE 保護裝置加密，即使已輪替，且資料庫目前使用新的 TDE 保護裝置。  在還原時，必須要有這兩個金鑰才能還原資料庫。  如果記錄檔使用儲存在 Azure Key Vault 中的 TDE 保護裝置，則在還原時將會需要此金鑰，即使在此同時，資料庫已變更為使用服務管理的 TDE 也是一樣。

## <a name="high-availability-with-customer-managed-tde"></a>客戶管理 TDE 的高可用性

即使在沒有為伺服器設定異地複寫的情況下，強烈建議您將伺服器設定為在兩個不同區域中使用兩個不同的金鑰保存庫（具有相同的金鑰內容）。 您可以使用與伺服器位於相同區域中的主要 key vault 建立 TDE 保護裝置，然後將金鑰複製到不同 Azure 區域中的金鑰保存庫，讓伺服器可以存取第二個金鑰保存庫，因為主要金鑰保存庫在資料庫已啟動並執行時發生中斷。

使用 AzKeyVaultKey Cmdlet，從主要金鑰保存庫取出加密格式的金鑰，然後使用 AzKeyVaultKey 指令 Cmdlet 並指定第二個區域中的金鑰保存庫來複製金鑰。 或者，您也可以使用 Azure 入口網站來備份和還原金鑰。 另一個區域的次要金鑰保存庫中的金鑰不應標示為 TDE 保護裝置，而且甚至不允許。

如果中斷影響到主要金鑰保存庫，則系統會自動切換至次要金鑰保存庫中具有相同指紋的其他連結金鑰（如果有的話）。 請注意，如果因為撤銷存取權限而無法存取 TDE 保護裝置，或因為金鑰或金鑰保存庫遭到刪除，則不會發生該交換器，因為這可能表示客戶刻意想要限制伺服器存取金鑰。

![單一伺服器 HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>異地 DR 和客戶管理的 TDE

在 [主動式異地](active-geo-replication-overview.md) 複寫和 [容錯移轉群組](auto-failover-group-overview.md) 案例中，每個涉及的伺服器都需要個別的金鑰保存庫，且必須與伺服器共置於相同的 Azure 區域中。 客戶須負責讓金鑰資料保存在不同的金鑰保存庫中，如此一來，異地次要資料庫就會同步，而且如果因為區域發生中斷而無法使用相同的金鑰，則可以從本機密鑰保存庫使用相同的金鑰，並觸發容錯移轉。 最多可以設定四個次要資料庫，且不支援 (次要的次要資料庫) 。

若要避免在由於金鑰材質未完成時建立或異地複寫期間發生問題，請務必在設定客戶管理的 TDE 時遵循這些規則：

- 所有相關的金鑰保存庫都必須具有相同的屬性，而且個別伺服器的存取權限也相同。

- 所有相關的金鑰保存庫都必須包含相同的金鑰內容。 它不只會套用至目前的 TDE 保護裝置，還會套用至備份檔案中所有先前的 TDE 保護裝置。

- TDE 保護裝置的初始安裝和旋轉必須先在次要上完成，然後在主要裝置上執行。

![容錯移轉群組和異地災害復原](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

若要測試容錯移轉，請遵循 [主動式異地複寫總覽](active-geo-replication-overview.md)中的步驟。 應定期進行測試容錯移轉，以驗證 SQL Database 已維持對兩個金鑰保存庫的存取權限。

## <a name="next-steps"></a>後續步驟

您也可能想要使用客戶管理的 TDE 來檢查下列 PowerShell 範例腳本，以進行一般作業：

- [使用 PowerShell 輪替 SQL Database 的透明資料加密保護裝置](transparent-data-encryption-byok-key-rotation.md)

- [使用 PowerShell 移除透明資料加密 (TDE) 保護裝置 SQL Database](transparent-data-encryption-byok-remove-tde-protector.md)

- [使用 PowerShell 以您自己的金鑰管理 SQL 受控執行個體中的透明資料加密](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)
