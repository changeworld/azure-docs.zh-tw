---
title: 使用客戶管理的金鑰進行資料加密-適用於 MySQL 的 Azure 資料庫
description: 使用客戶管理的金鑰適用於 MySQL 的 Azure 資料庫資料加密，可讓您攜帶您自己的金鑰（BYOK）以進行待用資料保護。 它也可讓組織在金鑰和資料的管理中，執行責任分離。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: a97fee619858aa024ff208b72d3b2594c30d2fd5
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299119"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>使用客戶管理的金鑰適用於 MySQL 的 Azure 資料庫資料加密

> [!NOTE]
> 此時，您必須要求存取權才能使用這項功能。 若要這麼做，請聯絡 AskAzureDBforMySQL@service.microsoft.com。

適用於 MySQL 的 Azure 資料庫使用客戶管理的金鑰進行資料加密，可讓您攜帶自己的金鑰（BYOK）來保護待用資料。 它也可讓組織在金鑰和資料的管理中，執行責任分離。 有了客戶管理的加密，您就必須負責並完全掌控金鑰的生命週期、金鑰使用許可權，以及對金鑰進行作業的審核。

針對適用於 MySQL 的 Azure 資料庫以客戶管理的金鑰進行資料加密，是在伺服器層級設定。 針對指定的伺服器，稱為「金鑰加密金鑰」（KEK）的客戶管理金鑰會用來加密服務所使用的資料加密金鑰（DEK）。 KEK 是儲存在客戶擁有和客戶管理[Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md)實例中的非對稱金鑰。 金鑰加密金鑰（KEK）和資料加密金鑰（DEK）稍後會在本文中詳細說明。

Key Vault 是以雲端為基礎的外部金鑰管理系統。 它具有高可用性，並為 RSA 密碼編譯金鑰提供可擴充且安全的儲存體，並選擇性地由 FIPS 140-2 Level 2 驗證的硬體安全性模組（Hsm）支援。 它不允許直接存取預存金鑰，但會提供加密和解密的服務給已授權的實體。 Key Vault 可以產生金鑰、將其匯入，或[將它從內部部署 HSM 裝置進行傳輸](../key-vault/key-Vault-hsm-protected-keys.md)。

> [!NOTE]
> 這項功能適用于所有 Azure 區域，其中適用於 MySQL 的 Azure 資料庫支援「一般用途」和「記憶體優化」定價層。

## <a name="benefits"></a>優點

適用於 MySQL 的 Azure 資料庫的資料加密提供下列優點：

* 資料存取是由您完全控制，您可以移除索引鍵並使資料庫無法存取 
* 完整控制金鑰生命週期，包括輪替金鑰以配合公司原則
* 集中管理和 Azure Key Vault 中的金鑰組織
* 能夠在安全人員與 DBA 和系統管理員之間實施責任分隔


## <a name="terminology-and-description"></a>術語和描述

**資料加密金鑰（DEK）** ：用來加密分割區或資料區塊的對稱 AES256 金鑰。 使用不同金鑰將每個資料區塊進行加密，會使密碼編譯分析攻擊更加困難。 資源提供者或要將特定區塊加密和解密的應用程式執行個體都需要存取 DEK。 當您以新的金鑰取代 DEK 時，只有其相關聯區塊中的資料必須以新的金鑰重新加密。

**金鑰加密金鑰（KEK）** ：用來加密 dek 的加密金鑰。 永遠不會離開 Key Vault 的 KEK 允許加密和控制 Dek 本身。 具有 KEK 存取權的實體可能不同于需要 DEK 的實體。 因為需要 KEK 才能將 DEK 解密，KEK 實際上就是單一點，藉以透過刪除 KEK 來有效地刪除 DEK。

以 Kek 加密的 Dek 會分別儲存。 只有具有 KEK 存取權的實體可以解密這些 Dek。 如需詳細資訊，請參閱[靜態加密中的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>使用客戶管理的金鑰進行資料加密的運作方式

![顯示攜帶您自己的金鑰總覽的圖表](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

若要讓 MySQL 伺服器使用儲存在 Key Vault 中的客戶管理金鑰來加密 DEK，Key Vault 系統管理員會提供伺服器的下列存取權限：

* **get**：用於抓取金鑰保存庫中金鑰的公用部分和屬性。
* **wrapKey**：能夠加密 DEK。
* **unwrapKey**：能夠解密 DEK。

金鑰保存庫系統管理員也可以[啟用 Key Vault audit 事件的記錄](../azure-monitor/insights/azure-key-vault.md)，以便稍後進行審核。

當伺服器設定為使用儲存在金鑰保存庫中的客戶管理金鑰時，伺服器會將 DEK 傳送至金鑰保存庫以進行加密。 Key Vault 會傳回加密的 DEK，這會儲存在使用者資料庫中。 同樣地，在需要時，伺服器會將受保護的 DEK 傳送至金鑰保存庫以進行解密。 如果已啟用記錄功能，則審計員可以使用 Azure 監視器來審查 Key Vault audit 事件記錄檔。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫資料加密的需求

以下是設定 Key Vault 的需求：

* Key Vault 和適用於 MySQL 的 Azure 資料庫必須屬於相同的 Azure Active Directory （Azure AD）租使用者。 不支援跨租使用者 Key Vault 和伺服器互動。 之後移動資源需要您重新設定資料加密。
* 您必須在金鑰保存庫上啟用虛刪除功能，以在發生意外的金鑰（或 Key Vault）刪除時防止資料遺失。 虛刪除的資源會保留90天，除非使用者同時復原或清除。 「復原」和「清除」動作在 Key Vault 存取原則中具有相關聯的許可權。 虛刪除功能預設為關閉，但您可以透過 PowerShell 或 Azure CLI 啟用它（請注意，您無法透過 Azure 入口網站加以啟用）。
* 使用 get、wrapKey 和 unwrapKey 許可權，適用於 MySQL 的 Azure 資料庫授與金鑰保存庫的存取權，方法是使用其唯一的受控識別。 在 Azure 入口網站中，在 MySQL 上啟用資料加密時，會自動建立唯一的身分識別。 如需使用 Azure 入口網站時的詳細逐步指示，請參閱[設定 MySQL 的資料加密](howto-data-encryption-portal.md)。

* 當您使用 Key Vault 的防火牆時，必須啟用 [**允許受信任的 Microsoft 服務略過防火牆**] 選項。

以下是設定客戶管理的金鑰的需求：

* 用來加密 DEK 的客戶管理金鑰只能是非對稱的 RSA 2028。
* 金鑰啟用日期（若已設定）必須是過去的日期和時間。 到期日（如果已設定）必須是未來的日期和時間。
* 金鑰必須處於 [*已啟用*] 狀態。
* 如果您要將現有的金鑰匯入金鑰保存庫，請務必以支援的檔案格式（`.pfx`、`.byok`、`.backup`）提供它。

## <a name="recommendations"></a>建議

當您使用由客戶管理的金鑰進行資料加密時，以下是設定 Key Vault 的建議：

* 在 Key Vault 上設定資源鎖定，以控制誰可以刪除此重要資源，並防止意外或未經授權的刪除。
* 啟用所有加密金鑰的審核和報告功能。 Key Vault 提供容易插入其他安全性資訊和事件管理工具的記錄檔。 Azure 監視器 Log Analytics 是已整合之服務的其中一個範例。

* 請確定 Key Vault 和適用於 MySQL 的 Azure 資料庫位於相同的區域，以確保更快存取 DEK 包裝和解除封裝作業。

以下是設定客戶管理的金鑰的建議：

* 將客戶管理的金鑰複本保存在安全的位置，或將其提供給憑證服務。

* 如果 Key Vault 產生金鑰，請在第一次使用金鑰之前，先建立金鑰備份。 您只能將備份還原到 Key Vault。 如需 backup 命令的詳細資訊，請參閱[AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)。

## <a name="inaccessible-customer-managed-key-condition"></a>無法存取客戶管理的金鑰狀況

當您在 Key Vault 中使用客戶管理的金鑰來設定資料加密時，伺服器必須能夠持續存取此金鑰才能保持線上狀態。 如果伺服器無法存取 Key Vault 中客戶管理的金鑰，伺服器就會在10分鐘內開始拒絕所有連線。 伺服器會發出對應的錯誤訊息，並將伺服器狀態變更為「*無法存取*」。 在此狀態的資料庫上，唯一允許的動作是刪除它。

### <a name="accidental-key-access-revocation-from-key-vault"></a>從 Key Vault 意外的金鑰存取撤銷

這可能是因為具有足夠存取權限 Key Vault 的人不小心停用伺服器對金鑰的存取權：

* 從伺服器撤銷 key vault 的 get、wrapKey 和 unwrapKey 許可權。
* 正在刪除索引鍵。
* 正在刪除金鑰保存庫。
* 變更金鑰保存庫的防火牆規則。

* 在 Azure AD 中刪除伺服器的受控識別。

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>在 Key Vault 中監視客戶管理的金鑰

若要監視資料庫狀態，並啟用遺失透明資料加密保護裝置存取的警示，請設定下列 Azure 功能：

* [Azure 資源健康狀態](../service-health/resource-health-overview.md)：拒絕存取客戶金鑰的資料庫，在第一次連接到資料庫之後，會顯示為「無法存取」。
* [活動記錄](../service-health/alerts-activity-log-service-notifications.md)：當客戶管理的 Key Vault 中的客戶金鑰存取失敗時，會將專案新增至活動記錄。 如果您為這些事件建立警示，您可以儘快恢復存取。

* [動作群組](../azure-monitor/platform/action-groups.md)：定義這些內容，以根據您的喜好設定傳送通知和警示。

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>在 Key Vault 中使用客戶的受控金鑰進行還原和複寫

適用於 MySQL 的 Azure 資料庫使用儲存在 Key Vault 中的客戶管理金鑰加密之後，任何新建立的伺服器複本也會一併加密。 您可以透過本機或異地還原作業，或透過讀取複本來建立這個新複本。 不過，您可以變更複本來反映新客戶的加密管理金鑰。 當客戶管理的金鑰變更時，伺服器的舊備份會開始使用最新的金鑰。

若要避免在還原或讀取複本期間設定客戶管理的資料加密時的問題，請務必在主要和還原/複本伺服器上執行下列步驟：

* 從主要適用於 MySQL 的 Azure 資料庫起始還原或讀取複本建立程式。
* 將新建立的伺服器（還原/複本）保留為無法存取的狀態，因為它的唯一身分識別尚未獲得 Key Vault 的許可權。
* 在還原/複本伺服器上，重新驗證資料加密設定中客戶管理的金鑰。 這可確保新建立的伺服器會針對儲存在 Key Vault 中的金鑰提供包裝和解除封裝許可權。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用 Azure 入口網站，以客戶管理的 Azure database For MySQL 的金鑰設定資料加密](howto-data-encryption-portal.md)。
