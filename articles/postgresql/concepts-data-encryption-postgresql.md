---
title: 使用客戶管理金鑰進行資料加密 - 用於 PostgreSQL 的 Azure 資料庫 - 單個伺服器
description: 使用客戶管理的金鑰進行 PostgreSQL 單一伺服器資料加密的 Azure 資料庫使您能夠自帶金鑰 （BYOK） 以進行靜態資料保護。 它還允許組織在金鑰和資料管理方面實現職責分離。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 20e01e681c382e3c9c69f76c95a90f709f409d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297017"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>使用客戶管理的金鑰進行 PostgreSQL 單一伺服器資料加密的 Azure 資料庫

> [!NOTE]
> 此時，您必須請求訪問才能使用此功能。 為此，請聯繫AskAzureDBforPostgreSQL@service.microsoft.com。

使用用於 PostgreSQL 單個伺服器的 Azure 資料庫的客戶管理金鑰進行資料加密，使您能夠自帶金鑰 （BYOK） 以進行靜態資料保護。 它還允許組織在金鑰和資料管理方面實現職責分離。 使用客戶管理的加密，您可以負責並完全控制金鑰的生命週期、金鑰使用許可權和對金鑰的操作的審核。

在伺服器級別設置具有用於 PostgreSQL 單一伺服器的 Azure 資料庫的客戶管理金鑰的資料加密。 對於給定伺服器，客戶管理的金鑰（稱為金鑰加密金鑰 （KEK））用於加密服務使用的資料加密金鑰 （DEK）。 KEK 是存儲在客戶擁有和客戶管理的[Azure 金鑰保存庫](../key-vault/key-Vault-secure-your-key-Vault.md)實例中的非對稱金鑰。 金鑰加密金鑰 （KEK） 和資料加密金鑰 （DEK） 在本文後面進行了更詳細的介紹。

金鑰保存庫是一個基於雲的外部金鑰管理系統。 它高度可用，為 RSA 加密金鑰提供可擴展、安全的存儲，由 FIPS 140-2 級驗證的硬體安全模組 （HSM） 提供可選的支援。 它不允許直接存取存儲的金鑰，但確實向授權實體提供加密和解密服務。 金鑰保存庫可以生成金鑰、導入金鑰或[將其從本地 HSM 設備傳輸](../key-vault/key-Vault-hsm-protected-keys.md)。

> [!NOTE]
> 此功能在所有 Azure 區域都可用，其中用於 PostgreSQL 單個伺服器的 Azure 資料庫都支援"通用"和"記憶體優化"定價層。

## <a name="benefits"></a>優點

後格雷SQL單一伺服器 Azure 資料庫的資料加密提供了以下優點：

* 資料訪問完全由您通過刪除金鑰和使資料庫無法訪問的能力控制 
*    完全控制關鍵生命週期，包括旋轉金鑰以符合公司策略
*    Azure 金鑰保存庫中金鑰的集中管理和組織
*    能夠在安全官員、DBA 和系統管理員之間實現職責分離

## <a name="terminology-and-description"></a>術語和描述

**資料加密金鑰 （DEK）**：用於加密資料分區或資料塊的對稱 AES256 金鑰。 使用不同金鑰將每個資料區塊進行加密，會使密碼編譯分析攻擊更加困難。 資源提供者或要將特定區塊加密和解密的應用程式執行個體都需要存取 DEK。 使用新金鑰替換 DEK 時，只能使用新金鑰重新加密其關聯塊中的資料。

**金鑰加密金鑰 （KEK）：** 用於加密 SDK 的加密金鑰。 從不離開金鑰保存庫的 KEK 允許對 SDK 本身進行加密和控制。 有權訪問 KEK 的實體可能與需要 DEK 的實體不同。 因為需要 KEK 才能將 DEK 解密，KEK 實際上就是單一點，藉以透過刪除 KEK 來有效地刪除 DEK。

與 KEK 加密的 SDK 單獨存儲。 只有有權訪問 KEK 的實體才能解密這些 SDK。 有關詳細資訊，請參閱[靜態加密的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>使用客戶管理金鑰進行資料加密的工作原理

![顯示自帶金鑰概述的圖表](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

對於 PostgreSQL 伺服器使用存儲在金鑰保存庫中的客戶託管金鑰進行 DEK 加密，金鑰保存庫管理員向伺服器授予以下存取權限：

* **獲取**： 用於檢索金鑰保存庫中金鑰的公共部分和屬性。
* **包裝鍵**：能夠加密DEK。
* **解包鍵**：能夠解密DEK。

金鑰保存庫管理員還可以[啟用金鑰保存庫審核事件的日誌記錄](../azure-monitor/insights/azure-key-vault.md)，以便以後可以審核這些事件。

當伺服器配置為使用存儲在金鑰保存庫中的客戶託管金鑰時，伺服器會將 DEK 發送到金鑰保存庫進行加密。 金鑰保存庫返回存儲在使用者資料庫中的加密 DEK。 同樣，在需要時，伺服器將受保護的 DEK 發送到金鑰保存庫進行解密。 如果啟用了日誌記錄，審核員可以使用 Azure 監視器查看金鑰保存庫審核事件日誌。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>為 PostgreSQL 單伺服器配置 Azure 資料庫的資料加密要求

以下是配置金鑰保存庫的要求：

* PostgreSQL 單個伺服器的金鑰保存庫和 Azure 資料庫必須屬於同一 Azure 活動目錄 （Azure AD） 租戶。 不支援跨租戶金鑰保存庫和伺服器交互。 之後移動資源需要重新配置資料加密。
* 您必須在金鑰保存庫上啟用虛刪除功能，以便在意外刪除金鑰（或金鑰保存庫）時防止資料丟失。 虛刪除的資源將保留 90 天，除非使用者在此期間恢復或清除它們。 恢復和清除操作具有它們自己的許可權，在金鑰保存庫訪問策略中關聯。 虛刪除功能預設處於關閉狀態，但您可以通過 PowerShell 或 Azure CLI 將其啟用（請注意，無法通過 Azure 門戶啟用它）。
* 使用獲取、包裝金鑰和取消包裝金鑰許可權，授予 PostgreSQL 單個伺服器對金鑰保存庫的 Azure 資料庫存取權限，並使用其唯一的託管標識。 在 Azure 門戶中，在 PostgreSQL 單台伺服器上啟用資料加密時，將自動創建唯一標識。 請參閱使用 Azure 門戶時使用 Azure 門戶獲得詳細、分步說明[的 PostgreSQL 單一伺服器的 Azure 資料庫資料加密](howto-data-encryption-portal.md)。

* 當您使用帶有金鑰保存庫的防火牆時，必須啟用 **"允許受信任的 Microsoft 服務繞過防火牆**"選項。

以下是配置客戶管理金鑰的要求：

* 用於加密 DEK 的客戶管理金鑰只能是不對稱的 RSA 2028。
* 金鑰啟動日期（如果設置）必須是過去的日期和時間。 到期日期（如果設置）必須是將來的日期和時間。
* 金鑰必須處於 *"已啟用"* 狀態。
* 如果要將現有金鑰導入金鑰保存庫，請確保以受支援的檔案格式 （、`.pfx` `.byok` `.backup`. ） 提供它。

## <a name="recommendations"></a>建議

使用客戶管理的金鑰使用資料加密時，以下是配置金鑰保存庫的建議：

* 在金鑰保存庫上設置資源鎖，以控制誰可以刪除此關鍵資源並防止意外或未經授權的刪除。
* 啟用對所有加密金鑰的審核和報告。 金鑰保存庫提供易於注入其他安全資訊和事件管理工具的日誌。 Azure 監視器日誌分析是已集成的服務的一個示例。

* 確保 PostgreSQL 單個伺服器的金鑰保存庫和 Azure 資料庫駐留在同一區域中，以確保 DEK 換行和解包操作的存取速度更快。

以下是配置客戶管理金鑰的建議：

* 將客戶管理的金鑰副本保存在安全的地方，或將其託管到託管服務。

* 如果金鑰保存庫生成金鑰，則在首次使用金鑰之前創建金鑰備份。 您只能將備份還原到金鑰保存庫。 有關備份命令的詳細資訊，請參閱[備份 -AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)。

## <a name="inaccessible-customer-managed-key-condition"></a>無法訪問的客戶管理的關鍵條件

使用金鑰保存庫中的客戶管理金鑰配置資料加密時，伺服器需要持續訪問此金鑰才能保持線上狀態。 如果伺服器無法訪問金鑰保存庫中的客戶管理金鑰，伺服器將在 10 分鐘內開始拒絕所有連接。 伺服器發出相應的錯誤訊息，並將伺服器狀態更改為*不可訪問*。 在此狀態下資料庫上允許的唯一操作是刪除它。

### <a name="accidental-key-access-revocation-from-key-vault"></a>金鑰保存庫的意外金鑰訪問吊銷

對金鑰保存庫具有足夠存取權限的人員可能會通過以下情況意外禁用伺服器對金鑰的訪問：

* 從伺服器撤銷金鑰保存庫的獲取、包裝金鑰和取消包金鑰許可權。
* 刪除金鑰。
* 刪除金鑰保存庫。
* 更改金鑰保存庫的防火牆規則。

* 刪除 Azure AD 中伺服器的託管標識。

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>監視金鑰保存庫中的客戶管理金鑰

要監視資料庫狀態，並啟用對丟失透明資料加密保護程式訪問的警報，請配置以下 Azure 功能：

* [Azure 資源運行狀況](../service-health/resource-health-overview.md)：在拒絕第一個與資料庫的連接後，無法訪問的資料庫已無法訪問客戶金鑰，顯示為"無法訪問"。
* [活動日誌](../service-health/alerts-activity-log-service-notifications.md)：當客戶託管的金鑰保存庫中的客戶金鑰訪問失敗時，條目將添加到活動日誌中。 如果為這些事件創建警報，則可以儘快恢復訪問。

* [操作組](../azure-monitor/platform/action-groups.md)：定義這些組，以便根據您的首選項向您發送通知和警報。

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>使用金鑰保存庫中的客戶託管金鑰還原和複製

在使用存儲在金鑰保存庫中的客戶託管金鑰組 PostgreSQL 單個伺服器的 Azure 資料庫進行加密後，對新創建的伺服器複本也進行加密。 您可以通過本地或異地還原操作或通過讀取副本製作此新副本。 但是，可以更改副本以反映新客戶的託管金鑰進行加密。 更改客戶管理的金鑰時，伺服器的舊備份將開始使用最新金鑰。

為了避免在還原或讀取複本建立期間設置客戶管理的資料加密時出現問題，請務必在主伺服器和還原/副本伺服器上執行以下步驟：

* 從 PostgreSQL 單個伺服器的主 Azure 資料庫啟動還原或讀取複本建立過程。
* 將新創建的伺服器（還原/複製副本）保持在不可訪問狀態，因為它的唯一標識尚未被授予金鑰保存庫的許可權。
* 在還原/複製伺服器上，在資料加密設置中重新驗證客戶管理的金鑰。 這可確保為新創建的伺服器授予金鑰保存庫中存儲的金鑰的換行和解包許可權。

## <a name="next-steps"></a>後續步驟

瞭解如何使用[Azure 門戶為 PostgreSQL 單一伺服器的 Azure 資料庫設置資料加密](howto-data-encryption-portal.md)。

