---
title: 客戶管理的透明資料加密 （TDE）
description: 使用用於 SQL 資料庫和 Azure 突觸的 Azure 金鑰保存庫，為透明資料加密 （TDE） 提供您自己的金鑰 （BYOK） 支援。 搭配使用 TDE 與 BYOK 的概觀、優點、運作方式、考量和建議。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 462326fb16663a6f25ff4b51ea11791201086fd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528723"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>使用客戶管理的金鑰進行 Azure SQL 透明資料加密

使用客戶管理的金鑰的 Azure SQL[透明資料加密 （TDE）](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)支援自帶金鑰 （BYOK） 方案以進行靜態資料保護，並允許組織在金鑰和資料管理中實現職責分離。 通過客戶管理的透明資料加密，客戶負責並完全控制關鍵生命週期管理（金鑰創建、上傳、旋轉、刪除）、金鑰使用許可權和對金鑰的操作的審核。

在這種情況下，用於加密資料庫加密金鑰 （DEK） 的金鑰（稱為 TDE 保護器）是存儲在客戶擁有和客戶管理的[Azure 金鑰保存庫 （AKV）（](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)基於雲的外部金鑰管理系統）中的客戶管理的不對稱金鑰。 金鑰保存庫是 RSA 加密金鑰的高度可用和可擴展的安全存儲，可選由 FIPS 140-2 級驗證的硬體安全模組 （HSM） 支援。 它不允許直接存取存儲的金鑰，但使用授權實體的金鑰提供加密/解密服務。 金鑰可以由金鑰保存庫生成、導入金鑰或[從上預 HSM 設備傳輸到金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)。

對於 Azure SQL 資料庫和 Azure 突觸，TDE 保護程式在邏輯伺服器級別設置，並由與該伺服器關聯的所有加密資料庫繼承。 對於 Azure SQL 託管實例，TDE 保護器在實例級別設置，並由該實例上的所有加密資料庫繼承。 術語*伺服器*在整個文檔中都引用 SQL 資料庫邏輯伺服器和託管實例，除非以不同方式說明。

> [!IMPORTANT]
> 對於那些希望開始使用客戶管理的 TDE 服務管理 TDE 的使用者，資料在切換過程中保持加密狀態，並且資料庫檔案沒有停機或重新加密。 從服務管理金鑰切換到客戶管理的金鑰只需要重新加密 DEK，這是一個快速的線上操作。

## <a name="benefits-of-the-customer-managed-tde"></a>客戶管理的 TDE 的優勢

客戶管理的 TDE 為客戶提供以下好處：

- 全面、精細地控制 TDE 保護器的使用和管理;

- TDE 保護器使用的透明度;

- 能夠在組織內金鑰和資料管理中實現職責分離;

- 金鑰保存庫管理員可以撤銷金鑰存取權限，以使加密資料庫無法訪問;

- AKV 金鑰的集中管理;

- 增強最終客戶的信任，因為 AKV 的設計使 Microsoft 無法查看或提取加密金鑰;

## <a name="how-customer-managed-tde-works"></a>客戶管理的 TDE 的工作原理

![客戶管理的 TDE 的設置和運行](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

要使伺服器能夠使用存儲在 AKV 中的 TDE 保護器對 DEK 進行加密，金鑰保存庫管理員需要使用其唯一的 AAD 標識向伺服器授予以下存取權限：

- **獲取**- 用於檢索金鑰保存庫中金鑰的公共部分和屬性

- **包裝鍵**- 能夠保護（加密）DEK

- **解包鍵**- 能夠取消保護（解密）DEK

金鑰保存庫管理員還可以[啟用金鑰保存庫審核事件的日誌記錄](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)，以便以後可以審核這些事件。

當伺服器配置為使用 AKV 中的 TDE 保護器時，伺服器會將每個啟用 TDE 的資料庫的 DEK 發送到金鑰保存庫進行加密。 金鑰保存庫返回加密的 DEK，然後存儲在使用者資料庫中。

如果需要，伺服器會將受保護的 DEK 發送到金鑰保存庫進行解密。

如果啟用了日誌記錄，審核員可以使用 Azure 監視器查看金鑰保存庫 AuditEvent 日誌。

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>配置客戶管理的 TDE 的要求

### <a name="requirements-for-configuring-akv"></a>配置 AKV 的要求

- 金鑰保存庫和 SQL 資料庫/託管實例必須屬於同一 Azure 活動目錄租戶。 不支援跨租用戶金鑰保存庫與伺服器的互動。 要在之後移動資源，必須重新配置帶有 AKV 的 TDE。 瞭解有關[移動資源的更多資訊](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。

- 必須在金鑰保存庫上啟用[虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)功能，以防止發生資料丟失意外金鑰（或金鑰保存庫）刪除。 虛刪除的資源將保留 90 天，除非客戶在此期間恢復或清除。 *復原*和*清除*動作本身的權限已在金鑰保存庫的存取原則中建立關聯。 虛刪除功能預設處於關閉狀態，可通過[PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete)或[CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)啟用。 無法通過 Azure 門戶啟用它。  

- 使用 Azure 活動目錄標識授予 SQL 資料庫伺服器或託管實例對金鑰保存庫（獲取、包裝金鑰、解包金鑰）的訪問。 使用 Azure 門戶時，將自動創建 Azure AD 標識。 使用 PowerShell 或 CLI 時，必須顯式創建 Azure AD 標識，並驗證完成。 請參閱[設定具有 BYOK 的 TDE](transparent-data-encryption-byok-azure-sql-configure.md)和[針對受控執行個體設定具有 BYOK 的 TDE](https://aka.ms/sqlmibyoktdepowershell)，以取得使用 PowerShell 時的詳細逐步指示。

- 將防火牆與 AKV 一起使用時，必須啟用選項 *"允許受信任的 Microsoft 服務繞過防火牆*"。

### <a name="requirements-for-configuring-tde-protector"></a>配置 TDE 保護器的要求

- TDE 保護器只能是非對稱的 RSA 2048 或 RSA HSM 2048 金鑰。

- 金鑰啟動日期（如果設置）必須是過去的日期和時間。 到期日期（如果設置）必須是將來的日期和時間。

- 金鑰必須處於 *"已啟用"* 狀態。

- 如果要將現有金鑰導入金鑰保存庫，請確保以受支援的檔案格式（.pfx、.byok 或 .backup）提供。

## <a name="recommendations-when-configuring-customer-managed-tde"></a>配置客戶管理的 TDE 時的建議

### <a name="recommendations-when-configuring-akv"></a>配置 AKV 時的建議

- 在單個訂閱中，最多將 500 個通用資料庫或 200 個業務關鍵資料庫與金鑰保存庫相關聯，以確保伺服器訪問金鑰保存庫中的 TDE 保護器時的高可用性。 這些數位是基於經驗，並記錄在[關鍵的保存庫服務限制](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)。 此處的目的是防止伺服器容錯移轉後出現問題，因為它將觸發與該伺服器中有資料庫一樣多的針對保存庫的關鍵操作。

- 在金鑰保存庫上設置資源鎖，以控制誰可以刪除此關鍵資源並防止意外或未經授權的刪除。 瞭解有關[資源鎖](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)的更多資訊。

- 啟用對所有加密金鑰的審核和報告：金鑰保存庫提供易於注入到其他安全資訊和事件管理工具中的日誌。 操作管理套件[日誌分析](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)是已集成的服務的一個示例。

- 將每台伺服器與駐留在不同區域並保存相同金鑰材料的兩個金鑰保存庫連結，以確保加密資料庫的高可用性。 僅將金鑰與 TDE 保護器位於同一區域的金鑰保存庫標記。 如果同一區域中的金鑰保存庫出現中斷，系統將自動切換到遠端區域中的金鑰保存庫。

### <a name="recommendations-when-configuring-tde-protector"></a>配置 TDE 保護器時的建議
- 將 TDE 保護器的副本保存在安全的地方，或將其託管到託管服務。

- 如果在金鑰保存庫中生成金鑰，則在首次使用 AKV 中的金鑰之前創建金鑰備份。 備份只能還原到 Azure 金鑰保存庫。 詳細瞭解[備份-AzKeyVaultKey 命令](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)。

- 每當對金鑰進行任何更改（例如鍵屬性、標記、ACL）時，創建新備份。

- 在輪替金鑰時**將舊版保留**在金鑰保存庫中，以便在還原較舊的資料庫備份時使用。 當資料庫的 TDE 保護程式更改時，資料庫的舊備份**不會更新**以使用最新的 TDE 保護器。 在還原時，每個備份都需要在創建時加密的 TDE 保護器。 您可以依照[使用 PowerShell 輪替透明資料加密保護裝置](transparent-data-encryption-byok-azure-sql-key-rotation.md)中的指示，來執行金鑰輪替。

- 即使在切換到服務管理金鑰後，也能將所有以前使用過的金鑰保留在 AKV 中。 它可確保資料庫備份可以使用存儲在 AKV 中的 TDE 保護器進行還原。  必須維護使用 Azure 金鑰保存庫創建的 TDE 保護器，直到使用服務管理的金鑰創建所有剩餘的存儲備份。 使用[備份-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)製作這些金鑰的可恢復備份副本。

- 要在安全事件期間刪除可能洩露的金鑰，而不會造成資料丟失，請按照[刪除可能受損的金鑰](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)中的步驟操作。

## <a name="inaccessible-tde-protector"></a>無法訪問的 TDE 保護器

當透明資料加密配置為使用客戶管理的金鑰時，資料庫需要持續訪問 TDE 保護器才能保持線上狀態。 如果伺服器無法訪問 AKV 中的客戶託管 TDE 保護程式，則資料庫將開始拒絕具有相應錯誤訊息的所有連接，並將其狀態更改為*不可訪問*。 在不可訪問狀態的資料庫上允許的唯一操作是刪除它。

> [!NOTE]
> 如果資料庫由於間歇性網路中斷而無法訪問，則無需執行任何操作，資料庫將自動重新連線。

恢復對金鑰的訪問後，將資料庫重新連線需要額外的時間和步驟，這可能因無法訪問金鑰所經過的時間和資料庫中的資料大小而異：

- 如果在 8 小時內修復金鑰訪問，資料庫將在下一小時內自動修復。

- 如果金鑰訪問在 8 小時後恢復，則無法自動修復，並且將資料庫帶回來需要在門戶上執行其他步驟，並且根據資料庫的大小可能需要大量時間。 資料庫重新連線後，以前配置的伺服器級設置（如[容錯移轉組](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group)配置、時間點還原歷史記錄和標記）**將丟失**。 因此，建議實施一個通知系統，允許您在 8 小時內識別和解決基礎金鑰訪問問題。

### <a name="accidental-tde-protector-access-revocation"></a>意外 TDE 保護器訪問吊銷

對金鑰保存庫具有足夠存取權限的人可能會通過以下情況意外禁用伺服器對金鑰的訪問：

- 從伺服器調用金鑰保存庫的*獲取*、*包裝金鑰*、*解包金鑰*許可權

- 刪除金鑰

- 刪除金鑰保存庫

- 更改金鑰保存庫的防火牆規則

- 刪除 Azure 活動目錄中伺服器的託管標識

詳細瞭解[資料庫無法訪問的常見原因](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)。

## <a name="monitoring-of-the-customer-managed-tde"></a>監控客戶管理的 TDE

要監視資料庫狀態並啟用 TDE 保護器訪問丟失警報，請配置以下 Azure 功能：
- [Azure 資源運行狀況](https://docs.microsoft.com/azure/service-health/resource-health-overview)。 在拒絕與資料庫的第一次連接後，無法訪問的資料庫已失去對 TDE 保護器的存取權限，將顯示為"不可用"。
- 當客戶託管的金鑰保存庫中對 TDE 保護器的訪問失敗時，[活動日誌](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications)會將條目添加到活動日誌中。  為這些事件創建警報將使您能夠儘快恢復訪問。
- [可以定義操作組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)，以便根據您的偏好向您發送通知和警報，例如電子郵件/短信/推送/語音、邏輯應用、Webhook、ITSM 或自動化 Runbook。

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>使用客戶管理的 TDE 進行資料庫備份和還原

使用金鑰保存庫中的金鑰使用 TDE 加密資料庫後，任何新生成的備份也會使用相同的 TDE 保護程式進行加密。 更改 TDE 保護器時，**不會更新**資料庫的舊備份以使用最新的 TDE 保護程式。

要還原使用金鑰保存庫中的 TDE 保護器加密的備份，請確保目標伺服器可以使用金鑰材料。 因此，我們建議您將所有舊版本的 TDE 保護程式保留在金鑰保存庫中，以便可以還原資料庫備份。

> [!IMPORTANT]
> 在任何時候，伺服器只能設置一個 TDE 保護器。 它是 Azure 門戶邊欄選項卡中標有"使金鑰成為預設 TDE 保護器"的金鑰。 但是，可以將多個附加金鑰連結到伺服器，而無需將其標記為 TDE 保護器。 這些金鑰不用於保護 DEK，但如果備份檔案使用相應的指紋對金鑰進行加密，則可以在從備份還原期間使用。

如果還原備份所需的金鑰不再可用於目標伺服器，則還原嘗試時將返回以下錯誤訊息："目標伺服器`<Servername>`無權存取時間戳#1>和\<\<時間戳記#2>之間創建的所有 AKV URI。 請在還原所有 AKV URI 後重試操作。

要緩解這種情況，請運行目標 SQL 資料庫邏輯伺服器的[Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) Cmdlet，或為目標託管實例[運行 Get-AzSqlInstanceKeyVaultKeyKeykey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey)鍵，以返回可用金鑰的清單並標識丟失的金鑰。 為了確保可以還原所有備份，請確保還原的目標伺服器有權訪問所需的所有金鑰。 這些金鑰不需要標記為 TDE 保護器。

若要深入了解 SQL Database 的備份復原，請參閱[復原 Azure SQL Database](sql-database-recovery-using-backups.md)。 要瞭解有關 SQL 池備份恢復的更多內容，請參閱[恢復 SQL 池](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)。 有關 SQL Server 的本機備份/使用託管實例還原，請參閱[快速入門：將資料庫還原到託管實例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

日誌檔的其他注意事項：備份的日誌檔仍使用原始 TDE 保護程式進行加密，即使它已旋轉且資料庫現在使用新的 TDE 保護程式也是如此。  在還原時，必須要有這兩個金鑰才能還原資料庫。  如果日誌檔使用存儲在 Azure 金鑰保存庫中的 TDE 保護程式，則還原時將需要此金鑰，即使同時將資料庫更改為使用服務管理的 TDE 也是如此。

## <a name="high-availability-with-customer-managed-tde"></a>通過客戶管理的 TDE 提供高可用性

即使伺服器沒有配置的異地冗余，強烈建議將伺服器配置為在具有相同金鑰材質的兩個不同區域中使用兩個不同的金鑰保存庫。 可以通過使用與伺服器位於同一區域的主金鑰保存庫創建 TDE 保護器，並將金鑰克隆到其他 Azure 區域中的金鑰保存庫中來實現，這樣，如果主金鑰保存庫是主金鑰保存庫，則伺服器有權訪問第二個金鑰保存庫資料庫啟動並運行時遇到中斷。

使用備份-AzKeyVaultKey Cmdlet 從主金鑰保存庫中檢索加密格式的金鑰，然後使用還原-AzKeyVaultKey Cmdlet 並指定第二個區域中的金鑰保存庫來克隆金鑰。 或者，使用 Azure 門戶備份和還原金鑰。 其他區域中的輔助金鑰保存庫中的金鑰不應標記為 TDE 保護器，甚至不允許。

 如果存在影響主金鑰保存庫的中斷，並且僅到那時，系統將自動切換到輔助金鑰保存庫中具有相同指紋的其他連結金鑰（如果存在）。 請注意，如果 TDE 保護器由於吊銷的存取權限而無法訪問，或者因為金鑰或金鑰保存庫被刪除，則不會發生該切換，因為它可能表明客戶有意限制伺服器訪問金鑰。

![單伺服器 HA](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>地理 DR 和客戶管理的 TDE

在[活動異地複製](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)和[容錯移轉組](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group)方案中，所涉及的每個伺服器都需要一個單獨的金鑰保存庫，該金鑰保存庫必須與同一 Azure 區域中的伺服器共存。 客戶負責保持金鑰庫之間的金鑰材料一致，以便異地次要資料庫處於同步狀態，如果由於區域中的中斷而無法訪問主金鑰庫，並且觸發容錯移轉，客戶可以使用來自其本地金鑰保存庫的同一金鑰接管. 最多可以配置四個次要資料庫，並且不支援連結（次要資料庫的次要資料庫）。

為了避免在建立或在異地複製過程中由於金鑰材料不完整而出現問題，在配置客戶管理的 TDE 時，請務必遵循這些規則：

- 涉及的所有金鑰保存庫必須具有相同的屬性，並且對相應的伺服器具有相同的存取權限。

- 涉及的所有金鑰保存庫必須包含相同的金鑰材料。 它不僅適用于當前的 TDE 保護器，還適用于備份檔案中可能使用的所有以前的 TDE 保護器。

- TDE 保護器的初始設置和旋轉都必須先在次要保護器上完成，然後再在主保護器上完成。

![容錯移轉群組和異地災害復原](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

要測試容錯移轉，請按照[活動異地複製概述](sql-database-geo-replication-overview.md)中的步驟操作。 應定期執行這一操作，以確認 SQL 對兩個金鑰保存庫的存取權限已維護。

## <a name="next-steps"></a>後續步驟

您可能還需要檢查以下 PowerShell 示例腳本，瞭解與客戶管理的 TDE 的常見操作：

- [使用 PowerShell 旋轉 SQL 資料庫的透明資料加密保護程式](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [使用 PowerShell 刪除 SQL 資料庫的透明資料加密 （TDE） 保護程式](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [使用 PowerShell 使用您自己的金鑰管理託管實例中的透明資料加密](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
