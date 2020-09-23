---
title: 使用客戶管理的金鑰加密資料 - 適用於 MySQL 的 Azure 資料庫
description: 適用於 MySQL 的 Azure 資料庫資料加密 (使用客戶管理的金鑰) 可讓您攜帶您自己的金鑰 (BYOK) 來保護待用資料。 此外也可讓組織在金鑰和資料的管理中實作職責區分。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 71657d45ce9c4cc6fb103b61235a282b3005b924
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884912"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>適用於 MySQL 的 Azure 資料庫資料加密 (使用客戶管理的金鑰)

適用於 MySQL 的 Azure 資料庫使用客戶管理金鑰的資料加密，可讓您攜帶自己的金鑰 (BYOK) 來保護待用資料。 此外也可讓組織在金鑰和資料的管理中實作職責區分。 使用客戶管理的加密時，您將負責處理並全權掌控金鑰的生命週期、金鑰使用權限，以及金鑰的作業稽核。

適用於 MySQL 的 Azure 資料庫資料加密 (使用客戶管理的金鑰) 是在伺服器層級設定。 針對指定的伺服器，系統會使用客戶管理的金鑰 (稱為「金鑰加密金鑰」(KEK)) 來加密服務所使用的資料加密金鑰 (DEK)。 KEK 是儲存在客戶擁有及客戶所管理 [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) 執行個體中的非對稱金鑰。 金鑰加密金鑰 (KEK) 和資料加密金鑰 (DEK) 稍後會在此文章中詳細說明。

Key Vault 是雲端式外部金鑰管理系統。 其具有高可用性，並為 RSA 密碼編譯金鑰提供可調整且安全的儲存體，並可選擇由 FIPS 140-2 層級 2 驗證的硬體安全性模組 (HSM) 加以支援。 其不允許直接存取儲存的金鑰，但會提供加密和解密服務給已授權的實體。 Key Vault 可以產生金鑰、將其匯入，或[從內部部署 HSM 裝置進行傳輸](../key-vault/key-Vault-hsm-protected-keys.md)。

> [!NOTE]
> 「適用於 MySQL 的 Azure 資料庫」支援「一般用途」和「記憶體最佳化」定價層，其中所有 Azure 區域都可使用此功能。 如需其他限制，請參閱「 [限制](concepts-data-encryption-mysql.md#limitations) 」一節。

## <a name="benefits"></a>優點

使用客戶管理的金鑰進行適用於 MySQL 的 Azure 資料庫的資料加密提供下列優點：

* 完全由您控制資料存取，您可以移除金鑰且讓資料庫無法存取 
* 完全控制金鑰生命週期，包括輪替金鑰以配合公司原則
* 集中管理和組織 Azure Key Vault 中的金鑰
* 能夠在安全人員、DBA 和系統管理員之間實作職責區分


## <a name="terminology-and-description"></a>術語和描述

**資料加密金鑰 (DEK)** ：用於將分割區或資料區塊加密的對稱 AES256 金鑰。 使用不同金鑰將每個資料區塊進行加密，會使密碼編譯分析攻擊更加困難。 資源提供者或要將特定區塊加密和解密的應用程式執行個體都需要存取 DEK。 當新的金鑰取代 DEK 時，只有在相關聯區塊中的資料才需要使用新的金鑰重新加密。

**金鑰加密金鑰 (KEK)** ：用來加密 DEK 的加密金鑰。 永遠不會離開 Key Vault 的 KEK 允許加密和控制 DEK 本身。 可存取 KEK 的實體可能不同於需要 DEK 的實體。 因為需要 KEK 才能將 DEK 解密，KEK 實際上就是單一點，藉以透過刪除 KEK 來有效地刪除 DEK。

以 KEK 加密的 DEK 會個別儲存。 只有具有 KEK 存取權限的實體可以解密這些 DEK。 如需詳細資訊，請參閱[靜態加密的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>使用客戶管理的金鑰加密資料的方式

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="顯示「攜帶您自己的金鑰」概觀的圖表":::

針對使用儲存於 Key Vault 中的客戶管理金鑰來加密 DEK 的 MySQL 伺服器，Key Vault 系統管理員會授與伺服器下列存取權限：

* **get**：用於在金鑰保存庫中，擷取金鑰的公開部分和屬性。
* **wrapKey**：可以加密 DEK。 加密的 DEK 會儲存在適用於 MySQL 的 Azure 資料庫中。
* **unwrapKey**：可以解密 DEK。 適用於 MySQL 的 Azure 資料庫需要解密 DEK 來加密/解密資料

金鑰保存庫管理員也可以[啟用 Key Vault 稽核事件的記錄](../azure-monitor/insights/key-vault-insights-overview.md)，以便稍後再進行稽核。

當伺服器設定為使用儲存在金鑰保存庫中的客戶管理金鑰時，伺服器會將 DEK 傳送至金鑰保存庫以加密。 Key Vault 會傳回加密的 DEK，並將其儲存在使用者資料庫中。 同樣地，在有需要時，伺服器會將受保護的 DEK 傳送至金鑰保存庫以解密。 如果已啟用記錄，則稽核員可以使用 Azure 監視器檢閱 Key Vault 稽核事件記錄檔。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫的資料加密設定需求

下列是設定 Key Vault 的需求：

* Key Vault 和適用於 MySQL 的 Azure 資料庫必須屬於相同的 Azure Active Directory (Azure AD) 租用戶。 目前不支援跨租用戶 Key Vault 與伺服器互動。 之後移動 Key Vault 資源會要求您重新設定資料加密。
* 在金鑰保存庫上啟用虛刪除功能，可在意外刪除金鑰 (或 Key Vault) 時防止資料遺失。 除非使用者同時復原或清除虛刪除的資源，否則這些資源將會保留 90 天。 Key Vault 存取原則中已建立復原和清除動作本身權限的關聯。 虛刪除功能預設為關閉，但可透過 PowerShell 或 Azure CLI 啟用 (請注意，您無法透過 Azure 入口網站啟用)。
* 使用其唯一的受控識別授與「適用於 MySQL 的 Azure 資料庫」對金鑰保存庫的存取權限，使其具有 get、wrapKey 和 unwrapKey 權限。 在 Azure 入口網站中，在 MySQL 上啟用資料加密時，會自動建立唯一的「服務」身分識別。 如需使用 Azure 入口網站的詳細逐步指示，請參閱[設定 MySQL 的資料加密](howto-data-encryption-portal.md)。

下列是設定客戶管理金鑰的需求：

* 用來加密 DEK 的客戶管理金鑰，只能是非對稱的 RSA 2048。
* 金鑰啟用日期 (若已設定) 必須是過去的日期和時間。 到期日 (若已設定) 必須是未來的日期和時間。
* 金鑰必須處於「已啟用」狀態。
* 如果您要將 [現有金鑰匯入](https://docs.microsoft.com/rest/api/keyvault/ImportKey/ImportKey) 至金鑰保存庫，請務必以支援的檔案格式提供， (`.pfx` 、 `.byok` `.backup`) 。

## <a name="recommendations"></a>建議

當您使用客戶管理的金鑰進行資料加密時，設定 Key Vault 的建議如下：

* 在 Key Vault 上設定資源鎖定，藉此控制可刪除這個重要資源的人員，並防止意外或未經授權的刪除發生。
* 啟用所有加密金鑰的稽核和報告功能。 Key Vault 提供可輕易插入其他安全性資訊和事件管理工具中的記錄。 例如，Azure 監視器 Log Analytics 即是已整合的服務之一。
* 請確定 Key Vault 和適用於 MySQL 的 Azure 資料庫都位於相同的區域，以確保 DEK 包裝和解除包裝作業的存取速度更快。
* 鎖定 Azure KeyVault 為只限**私人端點和選取的網路**，並僅允許「信任的 Microsoft」服務以保護資源。

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

以下是設定客戶管理的金鑰的建議：

* 將客戶管理的金鑰複本置於安全的位置，或將其提供給委付服務。

* 如果 Key Vault 產生金鑰，請在第一次使用該金鑰之前，先建立金鑰備份。 您只能將備份還原到 Key Vault。 如需備份命令的詳細資訊，請參閱 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey) \(英文\)。

## <a name="inaccessible-customer-managed-key-condition"></a>無法存取客戶管理金鑰的狀況

當您在 Key Vault 中使用客戶管理的金鑰來設定資料加密時，伺服器必須能夠持續存取此金鑰，才能保持線上狀態。 如果伺服器無法存取 Key Vault 中客戶管理的金鑰，就會在 10 分鐘內開始拒絕所有連線。 伺服器會發出對應的錯誤訊息，並將狀態變更為「無法存取」。 伺服器會出現此狀態的部分原因如下：

* 如果我們為您的適用於 MySQL 的 Azure 資料庫 (且其已啟用資料加密) 建立還原時間點伺服器，新建立的伺服器將會處於「無法存取」的狀態。 您可以透過 [Azure 入口網站](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers)或 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) 來修正此問題。
* 如果我們為您的適用於 MySQL 的 Azure 資料庫 (且已啟用資料加密) 建立讀取複本，則複本伺服器將會處於「無法存取」的狀態。 您可以透過 [Azure 入口網站](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers)或 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) 來修正此問題。
* 如果您刪除 KeyVault，適用於 MySQL 的 Azure 資料庫將無法存取該金鑰，且會移至「無法存取」的狀態。 復原 [Key Vault](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects) 並重新驗證資料加密，讓伺服器呈現「可用狀態」。
* 如果我們從 KeyVault 刪除金鑰，適用於 MySQL 的 Azure 資料庫將無法存取該金鑰，且會移至「無法存取」的狀態。 復原該[金鑰](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects)並重新驗證資料加密，讓伺服器呈現「可用狀態」。
* 如果儲存在 Azure KeyVault 中的金鑰過期，則會變成無效金鑰，且適用於 MySQL 的 Azure 資料庫會轉換成「無法存取」的狀態。 使用 [CLI](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-set-attributes) \(英文\) 延長金鑰到期日，然後重新驗證資料加密，讓伺服器呈現「可用狀態」。

### <a name="accidental-key-access-revocation-from-key-vault"></a>從 Key Vault 意外撤銷金鑰存取權限

這種情況可能會因具有足夠權限可存取 Key Vault 的人員，透過執行以下項目，不小心停用了伺服器存取金鑰的權限：

* 從伺服器撤銷金鑰保存庫的 get、wrapKey 和 unwrapKey 權限。
* 刪除金鑰。
* 刪除金鑰保存庫。
* 變更金鑰保存庫的防火牆規則。
* 在 Azure AD 中刪除伺服器的受控識別。

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>在 Key Vault 中監視客戶管理的金鑰

若要監視資料庫狀態，以及啟用透明資料加密保護裝置存取的遺失警示，請設定 Azure 的下列功能：

* [Azure 資源健康狀態](../service-health/resource-health-overview.md)：一個無法存取且其已失去客戶金鑰存取權的資料庫，會在首次連線至資料庫遭拒後顯示為「無法存取」。
* [活動記錄](../service-health/alerts-activity-log-service-notifications.md)：當客戶管理的 Key Vault 中的客戶金鑰存取失敗時，系統會將這些項目新增至活動記錄中。 如果您為這些事件建立警示，則可以盡快恢復存取。

* [動作群組](../azure-monitor/platform/action-groups.md)：定義這些群組，並依據您的偏好設定傳送通知和警示。

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>在 Key Vault 中使用客戶管理的金鑰進行還原和複寫

在「適用於 MySQL 的 Azure 資料庫」使用儲存於 Key Vault 的客戶管理金鑰加密之後，任何新建立的伺服器複本也會一併加密。 您可以透過本機或異地還原作業，或者是讀取複本來建立這個新複本。 不過，複本可能會變更以反映新的客戶管理加密金鑰。 客戶管理的金鑰變更時，伺服器的舊備份會開始使用最新的金鑰。

若要避免在還原或讀取複本建立期間，設定客戶管理的資料加密時所發生的問題，請務必在主要和還原/複本伺服器上遵循下列步驟：

* 從主要「適用於 MySQL 的 Azure 資料庫」起始還原或讀取複本建立程序。
* 將新建立的伺服器 (還原/複本) 保留在無法存取的狀態，因為其唯一身分識別尚未授與 Key Vault 權限。
* 在還原/複本伺服器上，重新驗證資料加密設定中客戶管理的金鑰，以確保會針對新建立的伺服器提供 Key Vault 中儲存之金鑰的包裝和解除包裝權限。

## <a name="limitations"></a>限制

針對適用於 MySQL 的 Azure 資料庫，使用客戶管理的金鑰 (CMK) 加密待用資料的支援有少數限制-

* 這項功能的支援僅限於 **一般用途** 和 **記憶體優化** 定價層。
* 只有在支援最多 16 TB 儲存體的區域和伺服器上，才支援這項功能。 如需支援儲存體最多16TB 的 Azure 區域清單，請參閱[此處](concepts-pricing-tiers.md#storage)檔中的儲存體一節。

    > [!NOTE]
    > - 在上欄區域中建立的所有新 MySQL 伺服器，都有 **提供**使用客戶管理金鑰加密的支援。 還原的時間點 (PITR) 伺服器或讀取複本將不會有資格，但理論上是「新的」。
    > - 若要驗證您布建的伺服器是否支援最多16TB，您可以移至入口網站中的 [定價層] 分頁，並查看您布建的伺服器所支援的儲存體大小上限。 如果您可以將滑杆移至4TB，則您的伺服器可能不支援使用客戶管理的金鑰進行加密。 不過，資料會隨時使用服務管理的金鑰進行加密。 AskAzureDBforMySQL@service.microsoft.com如果您有任何問題，請與您聯繫。

* 只有 RSA 2048 密碼編譯金鑰支援加密。

## <a name="next-steps"></a>後續步驟

了解如何[使用 Azure 入口網站，針對適用於 MySQL 的 Azure 資料庫使用客戶管理的金鑰設定資料加密](howto-data-encryption-portal.md)。
